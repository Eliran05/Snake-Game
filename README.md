# Snake-Game
My first game 

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.LinkedList;
import java.util.Random;

public class SnakeGame extends JFrame implements ActionListener, KeyListener
{

    private static final int GRID_SIZE = 20;
    private static final int TILE_SIZE = 20;
    private static final int GAME_SPEED = 100;
    private static int SCORE = -1;

    private enum Direction
    {
        UP, DOWN, LEFT, RIGHT
    }

    private LinkedList<Point> snake;
    private Point fruit;
    private Direction direction;

    public SnakeGame()
    {
        setTitle("Snake Game");
        setSize(GRID_SIZE * TILE_SIZE, GRID_SIZE * TILE_SIZE);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        snake = new LinkedList<>();
        direction = Direction.RIGHT;

        addKeyListener(this);

        Timer timer = new Timer(GAME_SPEED, this);
        timer.start();
//        timer.stop();

        initializeGame();
    }

    private void initializeGame()
    {
        SCORE = -1;
        snake.clear();
        snake.add(new Point(8, 8));
        direction = Direction.RIGHT;

        spawnFruit();
    }

    private void spawnFruit()
    {
        SCORE++;
        Random random = new Random();
        int x = random.nextInt(GRID_SIZE - 1) + 1;
        int y = random.nextInt(GRID_SIZE - 2) + 2;
        fruit = new Point(x, y);

        while (snake.contains(fruit))
        {
            x = random.nextInt(GRID_SIZE - 1) + 1;
            y = random.nextInt(GRID_SIZE - 2) + 2;
            fruit.setLocation(x, y);
        }
    }

    private void move()
    {
        Point head = snake.getFirst();

        switch (direction)
        {
            case UP:
                head = new Point(head.x, (head.y - 1 + GRID_SIZE) % GRID_SIZE);
                break;
            case DOWN:
                head = new Point(head.x, (head.y + 1) % GRID_SIZE);
                break;
            case LEFT:
                head = new Point((head.x - 1 + GRID_SIZE) % GRID_SIZE, head.y);
                break;
            case RIGHT:
                head = new Point((head.x + 1) % GRID_SIZE, head.y);
                break;
        }

        if (direction == Direction.DOWN)
        {
            if (head.y == 0)
            {
                head.y = 1;
            }
        }
        if (direction == Direction.UP)
        {
            if (head.y == 0)
            {
                head.y = (head.y - 1 + GRID_SIZE) % GRID_SIZE;
            }
        }

        snake.addFirst(head);
        if (head.equals(fruit))
        {
            spawnFruit();
        } else
        {
            snake.removeLast();
        }

        checkCollision();
    }

    private void checkCollision()
    {
        Point head = snake.getFirst();

        if (snake.size() > 1 && snake.subList(1, snake.size()).contains(head))
        {
            // Snake collided with itself
            gameOver();
        }
        if (head.equals(fruit))
        {
            spawnFruit();
        }
    }

    private void gameOver()
    {
        JOptionPane.showMessageDialog(this, "Game Over!", "Game Over", JOptionPane.INFORMATION_MESSAGE);
        initializeGame();
    }

    private void paintComponent(Graphics g)
    {
        g.clearRect(0, 0, getWidth(), getHeight());
        g.setFont(new Font(Font.MONOSPACED, Font.BOLD, 20));
        // Draw snake
        g.setColor(Color.GREEN);
        snake.forEach((point) ->
        {
            if (snake.getFirst() == point)
            {
                g.setColor(Color.ORANGE);
                g.fillOval(point.x * TILE_SIZE, point.y * TILE_SIZE, TILE_SIZE, TILE_SIZE);
            } else
            {
                g.setColor(Color.GREEN);
                g.fillRect(point.x * TILE_SIZE, point.y * TILE_SIZE, TILE_SIZE, TILE_SIZE);
            }
        });
        // Draw fruit
        g.setColor(Color.RED);
        g.fillRect(fruit.x * TILE_SIZE, fruit.y * TILE_SIZE, TILE_SIZE, TILE_SIZE);

        Toolkit.getDefaultToolkit().sync();
        //score on the top
        g.setColor(Color.BLACK);
        g.drawString("" + SCORE, getWidth() - 80, 60);
        Toolkit.getDefaultToolkit().sync();

    }

    @Override
    public void actionPerformed(ActionEvent e)
    {
        move();
        repaint();
    }

    @Override
    public void keyPressed(KeyEvent e)
    {
        switch (e.getKeyCode())
        {
            case KeyEvent.VK_UP:
                if (direction != Direction.DOWN)
                {
                    direction = Direction.UP;
                }
                break;
            case KeyEvent.VK_DOWN:
                if (direction != Direction.UP)
                {
                    direction = Direction.DOWN;
                }
                break;
            case KeyEvent.VK_LEFT:
                if (direction != Direction.RIGHT)
                {
                    direction = Direction.LEFT;
                }
                break;
            case KeyEvent.VK_RIGHT:
                if (direction != Direction.LEFT)
                {
                    direction = Direction.RIGHT;
                }
                break;
        }
    }

    @Override
    public void keyTyped(KeyEvent e)
    {
    }

    @Override
    public void keyReleased(KeyEvent e)
    {
    }

    @Override
    public void paint(Graphics g)
    {
        super.paint(g);
        paintComponent(g);
    }

    public static void main(String[] args)
    {
        SwingUtilities.invokeLater(() ->
        {
            SnakeGame snakeGame = new SnakeGame();
            snakeGame.setVisible(true);
        });
    }
}
