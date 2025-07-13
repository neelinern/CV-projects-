# CV-projects-
// C++ program for the above approach
#include <bits/stdc++.h>
using namespace std;

// Stores the number of rows
// and columns of the matrix
int N, M;

// Stores the final generated input
int arr[100][100];

// Direction arrays
int dx[9] = { -1, 0, 1, -1, 0, 1, -1, 0, 1 };
int dy[9] = { 0, 0, 0, -1, -1, -1, 1, 1, 1 };

// Function to check if the
// cell location is valid
bool isValid(int x, int y)
{
    // Returns true if valid
    return (x >= 0 && y >= 0
            && x < N && y < M);
}

// Function to generate a valid minesweeper
// matrix of size ROW * COL with P being
// the probability of a cell being a mine
void generateMineField(int ROW, int COL, int P)
{
    // Generates the random
    // number every time
    srand(time(NULL));

    int rand_val;

    // Stores whether a cell
    // contains a mine or not
    int mines[ROW][COL];

    // Iterate through each cell
    // of the matrix mine
    for (int x = 0; x < ROW; x++) {
        for (int y = 0; y < COL; y++) {
            // Generate a random value
            // from the range [0, 100]
            rand_val = rand() % 100;

            // If rand_val is less than P
            if (rand_val < P)

                // MArk mines[x][y] as True
                mines[x][y] = true;

            // Otherwise, mark
            // mines[x][y] as False
            else
                mines[x][y] = false;
        }
    }

    cout << "Generated Input:\n";

    // Iterate through each cell (x, y)
    for (int x = 0; x < ROW; x++) {
        for (int y = 0; y < COL; y++) {
            arr[x][y] = 0;

            // Count the number of mines
            // around the cell (x, y)
            // and store in arr[x][y]
            for (int k = 0; k < 9; k++) {
                // If current adjacent cell is valid
                if (isValid(x + dx[k], y + dy[k])
                    && (mines[x + dx[k]][y + dy[k]]))
                    arr[x][y]++;
            }

            // Print the value at
            // the current cell
            cout << arr[x][y] << " ";
        }
        cout << endl;
    }
}

// Driver Code
int main()
{
    N = 7, M = 7;
    int P = 20;

    // Function call to generate
    // a valid minesweeper matrix
    generateMineField(N, M, 15);
}// C++ program for the above approach
#include <bits/stdc++.h>
using namespace std;

// Stores the number of rows
// and columns in given matrix
int N, M;

// Maximum number of rows
// and columns possible
#define MAXM 100
#define MAXN 100

// Directional Arrays
int dx[9] = { -1, 0, 1, -1, 0, 1, -1, 0, 1 };
int dy[9] = { 0, 0, 0, -1, -1, -1, 1, 1, 1 };

// Function to check if the
// cell (x, y) is valid or not
bool isValid(int x, int y)
{
    return (x >= 0 && y >= 0 && x < N && y < M);
}

// Function to print the matrix grid[][]
void printGrid(bool grid[MAXN][MAXM])
{
    for (int row = 0; row < N; row++) {
        for (int col = 0; col < M; col++) {
            if (grid[row][col])
                cout << "x ";
            else
                cout << "_ ";
        }
        cout << endl;
    }
}

// Function to check if the cell (x, y)
// is valid to have a mine or not
bool isSafe(int arr[MAXN][MAXM], int x, int y)
{

    // Check if the cell (x, y) is a
    // valid cell or not
    if (!isValid(x, y))
        return false;

    // Check if any of the neighbouring cell
    // of (x, y) supports (x, y) to have a mine
    for (int i = 0; i < 9; i++) {

        if (isValid(x + dx[i], y + dy[i])
            && (arr[x + dx[i]][y + dy[i]] - 1 < 0))
            return (false);
    }

    // If (x, y) is valid to have a mine
    for (int i = 0; i < 9; i++) {
        if (isValid(x + dx[i], y + dy[i]))

            // Reduce count of mines in
            // the neighboring cells
            arr[x + dx[i]][y + dy[i]]--;
    }

    return true;
}

// Function to check if there
// exists any unvisited cell or not
bool findUnvisited(bool visited[MAXN][MAXM], int& x, int& y)
{
    for (x = 0; x < N; x++)
        for (y = 0; y < M; y++)
            if (!visited[x][y])
                return (true);
    return (false);
}

// Function to check if all the cells
// are visited or not and the input array
// is satisfied with the mine assignments
bool isDone(int arr[MAXN][MAXM], bool visited[MAXN][MAXM])
{
    bool done = true;
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < M; j++) {
            done
                = done && (arr[i][j] == 0) && visited[i][j];
        }
    }

    return (done);
}

// Function to solve the minesweeper matrix
bool SolveMinesweeper(bool grid[MAXN][MAXM],
                      int arr[MAXN][MAXM],
                      bool visited[MAXN][MAXM])
{

    // Function call to check if each cell
    // is visited and the solved grid is
    // satisfying the given input matrix
    bool done = isDone(arr, visited);

    // If the solution exists and
    // and all cells are visited
    if (done)
        return true;

    int x, y;

    // Function call to check if all
    // the cells are visited or not
    if (!findUnvisited(visited, x, y))
        return false;

    // Mark cell (x, y) as visited
    visited[x][y] = true;

    // Function call to check if it is
    // safe to assign a mine at (x, y)
    if (isSafe(arr, x, y)) {

        // Mark the position with a mine
        grid[x][y] = true;

        // Recursive call with (x, y) having a mine
        if (SolveMinesweeper(grid, arr, visited))

            // If solution exists, then return true
            return true;

        // Reset the position x, y
        grid[x][y] = false;
        for (int i = 0; i < 9; i++) {
            if (isValid(x + dx[i], y + dy[i]))
                arr[x + dx[i]][y + dy[i]]++;
        }
    }

    // Recursive call without (x, y) having a mine
    if (SolveMinesweeper(grid, arr, visited))

        // If solution exists then return true
        return true;

    // Mark the position as unvisited again
    visited[x][y] = false;

    // If no solution exists
    return false;
}

void minesweeperOperations(int arr[MAXN][MAXN], int N,
                           int M)
{

    // Stores the final result
    bool grid[MAXN][MAXM];

    // Stores whether the position
    // (x, y) is visited or not
    bool visited[MAXN][MAXM];

    // Initialize grid[][] and
    // visited[][] to false
    memset(grid, false, sizeof(grid));
    memset(visited, false, sizeof(visited));

    // If the solution to the input
    // minesweeper matrix exists
    if (SolveMinesweeper(grid, arr, visited)) {

        // Function call to print the grid[][]
        printGrid(grid);
    }

    // No solution exists
    else
        printf("No solution exists\n");
}

// Driver Code
int main()
{
    // Given input
    N = 7;
    M = 7;
    int arr[MAXN][MAXN] = {
        { 1, 1, 0, 0, 1, 1, 1 }, { 2, 3, 2, 1, 1, 2, 2 },
        { 3, 5, 3, 2, 1, 2, 2 }, { 3, 6, 5, 3, 0, 2, 2 },
        { 2, 4, 3, 2, 0, 1, 1 }, { 2, 3, 3, 2, 1, 2, 1 },
        { 1, 1, 1, 1, 1, 1, 0 }
    };

    // Function call to perform
    // generate and solve a minesweeper
    minesweeperOperations(arr, N, M);

    return 0;
}
