using System;
using System.Drawing;
using System.Windows.Forms;

namespace EducationalTetris
{
    public partial class TetrisGame : Form
    {
        private const int GridWidth = 10;
        private const int GridHeight = 20;
        private int[,] grid = new int[GridHeight, GridWidth];
        private Timer gameTimer;
        private Block currentBlock;

        public TetrisGame()
        {
            InitializeComponent();
            InitGame();
        }

        private void InitGame()
        {
            this.ClientSize = new Size(300, 600);
            gameTimer = new Timer();
            gameTimer.Interval = 500; // Block falls every 500 ms
            gameTimer.Tick += GameTick;
            gameTimer.Start();

            currentBlock = new Block();
            this.Paint += new PaintEventHandler(RenderGame);
            this.KeyDown += new KeyEventHandler(OnKeyPress);
        }

        private void GameTick(object sender, EventArgs e)
        {
            if (!MoveBlockDown())
            {
                LockBlockInGrid();
                CheckForCompleteLines();
                AskQuestion();
                currentBlock = new Block(); // Generate new block
            }

            this.Invalidate(); // Redraw the screen
        }

        private bool MoveBlockDown()
        {
            if (currentBlock.CanMove(0, 1, grid))
            {
                currentBlock.Move(0, 1);
                return true;
            }
            return false;
        }

        private void LockBlockInGrid()
        {
            for (int i = 0; i < currentBlock.Shape.GetLength(0); i++)
            {
                for (int j = 0; j < currentBlock.Shape.GetLength(1); j++)
                {
                    if (currentBlock.Shape[i, j] == 1)
                    {
                        grid[currentBlock.Y + i, currentBlock.X + j] = 1;
                    }
                }
            }
        }

        private void CheckForCompleteLines()
        {
            for (int i = 0; i < GridHeight; i++)
            {
                bool isLineComplete = true;
                for (int j = 0; j < GridWidth; j++)
                {
                    if (grid[i, j] == 0)
                    {
                        isLineComplete = false;
                        break;
                    }
                }

                if (isLineComplete)
                {
                    RemoveLine(i);
                }
            }
        }

        private void RemoveLine(int row)
        {
            for (int i = row; i > 0; i--)
            {
                for (int j = 0; j < GridWidth; j++)
                {
                    grid[i, j] = grid[i - 1, j];
                }
            }
        }

        private void AskQuestion()
        {
            string question = "What does 'OOP' stand for?";
            string correctAnswer = "Object-Oriented Programming";
            
            string userAnswer = Microsoft.VisualBasic.Interaction.InputBox(question, "Question");

            if (userAnswer.Trim().ToLower() != correctAnswer.ToLower())
            {
                MessageBox.Show("Incorrect answer. The block is removed!", "Wrong Answer");
                currentBlock = null; // Remove the block if the answer is wrong
            }
        }

        private void RenderGame(object sender, PaintEventArgs e)
        {
            Graphics g = e.Graphics;

            // Render the grid
            for (int i = 0; i < GridHeight; i++)
            {
                for (int j = 0; j < GridWidth; j++)
                {
                    if (grid[i, j] == 1)
                    {
                        g.FillRectangle(Brushes.Blue, j * 30, i * 30, 30, 30);
                    }
                }
            }

            // Render current block
            if (currentBlock != null)
            {
                for (int i = 0; i < currentBlock.Shape.GetLength(0); i++)
                {
                    for (int j = 0; j < currentBlock.Shape.GetLength(1); j++)
                    {
                        if (currentBlock.Shape[i, j] == 1)
                        {
                            g.FillRectangle(Brushes.Red, (currentBlock.X + j) * 30, (currentBlock.Y + i) * 30, 30, 30);
                        }
                    }
                }
            }
        }

        private void OnKeyPress(object sender, KeyEventArgs e)
        {
            if (e.KeyCode == Keys.Left && currentBlock.CanMove(-1, 0, grid))
            {
                currentBlock.Move(-1, 0);
            }
            else if (e.KeyCode == Keys.Right && currentBlock.CanMove(1, 0, grid))
            {
                currentBlock.Move(1, 0);
            }
            else if (e.KeyCode == Keys.Down && currentBlock.CanMove(0, 1, grid))
            {
                currentBlock.Move(0, 1);
            }
            else if (e.KeyCode == Keys.Up)
            {
                currentBlock.Rotate(grid)
                using System;

namespace EducationalTetris
{
    public class Block
    {
        public int[,] Shape { get; private set; }
        public int X { get; private set; }
        public int Y { get; private set; }

        public Block()
        {
            Shape = new int[,]
            {
                { 1, 1, 0 },
                { 0, 1, 1 }
            };

            X = 4; // Start at the center
            Y = 0;
        }

        public void Move(int xDelta, int yDelta)
        {
            X += xDelta;
            Y += yDelta;
        }

        public void Rotate(int[,] grid)
        {
            int[,] rotatedShape = new int[Shape.GetLength(1), Shape.GetLength(0)];
            for (int i = 0; i < Shape.GetLength(0); i++)
            {
                for (int j = 0; j < Shape.GetLength(1); j++)
                {
                    rotatedShape[j, Shape.GetLength(0) - i - 1] = Shape[i, j];
                }
            }

            Shape = rotatedShape;
        }

        public bool CanMove(int xDelta, int yDelta, int[,] grid)
        {
            for (int i = 0; i < Shape.GetLength(0); i++)
            {
                for (int j = 0; j < Shape.GetLength(1); j++)
                {
                    if (Shape[i, j] == 1)
                    {
                        int newX = X + j + xDelta;
                        int newY = Y + i + yDelta;

                        if (newX < 0 || newX >= grid.GetLength(1) || newY >= grid.GetLength(0) || grid[newY, newX] == 1)
                        {
                            return false;
                        }
                    }
                }
            }
            return true;
        }
    }
}
