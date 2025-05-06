# desafio-dio-jogo-dama

#include <iostream>
#include <string>
#include <cmath>

enum class Player { NONE, WHITE, BLACK };
enum class PieceType { NORMAL, KING };

struct Piece {
    Player player;
    PieceType type;
    Piece(Player p = Player::NONE, PieceType t = PieceType::NORMAL)
        : player(p), type(t) {}
};

Piece board[8][8];

void initializeBoard() {
    for (int row = 0; row < 8; ++row) {
        for (int col = 0; col < 8; ++col) {
            board[row][col] = Piece();
            if ((row + col) % 2 == 1) {
                if (row < 3) board[row][col] = Piece(Player::BLACK);
                else if (row > 4) board[row][col] = Piece(Player::WHITE);
            }
        }
    }
}

void printBoard() {
    std::cout << "  a b c d e f g h\n";
    for (int i = 0; i < 8; ++i) {
        std::cout << 8 - i << " ";
        for (int j = 0; j < 8; ++j) {
            if ((i + j) % 2 == 0) std::cout << ".";
            else {
                Piece& p = board[i][j];
                if (p.player == Player::WHITE)
                    std::cout << (p.type == PieceType::KING ? 'W' : 'w');
                else if (p.player == Player::BLACK)
                    std::cout << (p.type == PieceType::KING ? 'B' : 'b');
                else
                    std::cout << "_";
            }
            std::cout << " ";
        }
        std::cout << 8 - i << "\n";
    }
    std::cout << "  a b c d e f g h\n";
}

bool isValidMove(int x1, int y1, int x2, int y2, Player player) {
    if (x2 < 0 || x2 >= 8 || y2 < 0 || y2 >= 8) return false;
    Piece& p = board[x1][y1];
    Piece& dest = board[x2][y2];

    if (dest.player != Player::NONE) return false;

    int dx = x2 - x1;
    int dy = y2 - y1;

    if (std::abs(dy) != 1) return false;

    if (p.type == PieceType::NORMAL) {
        int direction = (player == Player::WHITE) ? -1 : 1;
        return dx == direction;
    } else {
        return std::abs(dx) == 1;
    }
}

void playGame() {
    Player currentPlayer = Player::WHITE;
    std::string from, to;

    while (true) {
        printBoard();
        std::cout << (currentPlayer == Player::WHITE ? "Branco" : "Preto") << " joga (ex: b6 c5): ";
        std::cin >> from >> to;

        if (from.length() != 2 || to.length() != 2) {
            std::cout << "Formato inválido.\n";
            continue;
        }

        int x1 = 8 - (from[1] - '0');
        int y1 = from[0] - 'a';
        int x2 = 8 - (to[1] - '0');
        int y2 = to[0] - 'a';

        if (x1 < 0 || x1 >= 8 || y1 < 0 || y1 >= 8 || x2 < 0 || x2 >= 8 || y2 < 0 || y2 >= 8) {
            std::cout << "Coordenadas fora do tabuleiro.\n";
            continue;
        }

        Piece& p = board[x1][y1];
        if (p.player != currentPlayer) {
            std::cout << "Essa não é sua peça.\n";
            continue;
        }

        if (!isValidMove(x1, y1, x2, y2, currentPlayer)) {
            std::cout << "Movimento inválido.\n";
            continue;
        }

        board[x2][y2] = p;
        board[x1][y1] = Piece();

        // Promoção
        if (p.player == Player::WHITE && x2 == 0)
            board[x2][y2].type = PieceType::KING;
        else if (p.player == Player::BLACK && x2 == 7)
            board[x2][y2].type = PieceType::KING;

        currentPlayer = (currentPlayer == Player::WHITE) ? Player::BLACK : Player::WHITE;
    }
}

int main() {
    initializeBoard();
    playGame();
    return 0;
}
