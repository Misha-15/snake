#include <iostream>
#include <conio.h>
#include <windows.h>
#include <vector>
#include <ctime>
using namespace std;

const int width = 40;
const int height = 20;
bool gameOver;
int x, y, fruitX, fruitY, score;
vector<pair<int, int>> tail;
int nTail;
enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };
eDirection dir;
int speed = 100;
vector<pair<int, int>> obstacles;

void Setup() {
    gameOver = false;
    dir = STOP;
    x = width / 2;
    y = height / 2;
    fruitX = rand() % width;
    fruitY = rand() % height;
    score = 0;
    nTail = 0;
    tail.clear();
    obstacles.clear();

    for (int i = 0; i < 10; i++) {
        int obsX = rand() % width;
        int obsY = rand() % height;
        obstacles.push_back(make_pair(obsX, obsY));
    }
}

void Draw() {
    system("cls");

    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << endl;

    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {
            if (j == 0)
                cout << "#";

            if (i == y && j == x)
                cout << "O";
            else if (i == fruitY && j == fruitX)
                cout << "F";
            else {
                bool print = false;

                for (int k = 0; k < nTail; k++) {
                    if (tail[k].first == j && tail[k].second == i) {
                        cout << "o";
                        print = true;
                    }
                }

                for (auto& obs : obstacles) {
                    if (obs.first == j && obs.second == i) {
                        cout << "X";
                        print = true;
                    }
                }

                if (!print)
                    cout << " ";
            }

            if (j == width - 1)
                cout << "#";
        }
        cout << "\n";
    }

    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << "\n";

    cout << "score: " << score << "\n";
}

void Input() {
    if (_kbhit()) {
        switch (_getch()) {
        case 'a':
            if (dir != RIGHT) dir = LEFT;
            break;
        case 'd':
            if (dir != LEFT) dir = RIGHT;
            break;
        case 'w':
            if (dir != DOWN) dir = UP;
            break;
        case 's':
            if (dir != UP) dir = DOWN;
            break;
        case 'x':
            gameOver = true;
            break;
        }
    }
}

void Logic() {
    pair<int, int> prev = tail.empty() ? make_pair(x, y) : tail[0];
    pair<int, int> prev2;
    if (nTail > 0) {
        tail[0] = make_pair(x, y);
        for (int i = 1; i < nTail; i++) {
            prev2 = tail[i];
            tail[i] = prev;
            prev = prev2;
        }
    }

    switch (dir) {
    case LEFT:
        x--;
        break;
    case RIGHT:
        x++;
        break;
    case UP:
        y--;
        break;
    case DOWN:
        y++;
        break;
    default:
        break;
    }

    if (x >= width) x = 0; else if (x < 0) x = width - 1;
    if (y >= height) y = 0; else if (y < 0) y = height - 1;

    for (int i = 0; i < nTail; i++)
        if (tail[i].first == x && tail[i].second == y)
            gameOver = true;

    for (auto& obs : obstacles)
        if (obs.first == x && obs.second == y)
            gameOver = true;

    if (x == fruitX && y == fruitY) {
        score += 10;
        fruitX = rand() % width;
        fruitY = rand() % height;
        nTail++;
        tail.push_back(make_pair(0, 0));

        if (speed > 30) speed -= 5;
    }
}

void Menu() {
    cout << "Welcome to Snake!" << "\n";
    cout << "1.play " << "\n";
    cout << "2.exit" << "\n";
    cout << "choose an option:";
    int choice;
    cin >> choice;

    if (choice == 2) exit(0);

    cout << "Select Difficulty Level:" << "\n";
    cout << "1.easy" << "\n";
    cout << "2.medium" << "\n";
    cout << "3.hard" << "\n";
    cout << "enter choice: ";
    cin >> choice;

    switch (choice) {
    case 1:
        speed = 150;
        break;
    case 2:
        speed = 100;
        break;
    case 3:
        speed = 50;
        break;
    default:
        speed = 100;
        break;
    }

    Setup();
}

int main() {
    system("title Snake");
    srand(time(0));
    Menu();

    while (!gameOver) {
        Draw();
        Input();
        Logic();
        Sleep(speed);
    }

    cout << "game over!your score: " << score << "\n";
    system("pause");
    return 0;
}
