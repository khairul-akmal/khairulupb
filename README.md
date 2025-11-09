"""
==============================================================
Tic Tac Toe: Pemain vs Potter (AI dengan Minimax Algorithm)
==============================================================
Deskripsi:
    Game Tic Tac Toe berbasis terminal, di mana manusia ('X') 
    bermain melawan AI ('O') bernama Potter. AI menggunakan 
    algoritma Minimax untuk menentukan langkah optimal.

Fitur:
    - Interaksi langsung di terminal (CLI)
    - AI selalu mengambil langkah terbaik
    - Papan diperbarui setiap giliran
    - Sistem skor (jumlah kemenangan)
    - Pesan hasil (menang, kalah, atau seri)

Dibuat oleh: angga
Versi: 1.0
Lisensi: MIT
"""

import math
import random
import time
import os

# === Utility Functions ===
def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')


def print_board(board):
    """Menampilkan papan Tic Tac Toe ke layar"""
    print("\n")
    for i in range(3):
        print(" " + " | ".join(board[i*3:(i+1)*3]))
        if i < 2:
            print("---+---+---")
    print("\n")


def available_moves(board):
    """Mengembalikan daftar indeks sel kosong"""
    return [i for i, val in enumerate(board) if val == ' ']


def check_winner(board, player):
    """Memeriksa apakah pemain tertentu menang"""
    win_patterns = [
        [0,1,2], [3,4,5], [6,7,8],
        [0,3,6], [1,4,7], [2,5,8],
        [0,4,8], [2,4,6]
    ]
    return any(all(board[pos] == player for pos in pattern) for pattern in win_patterns)


def is_full(board):
    """Memeriksa apakah papan penuh"""
    return all(cell != ' ' for cell in board)


# === AI menggunakan Minimax ===
def minimax(board, depth, is_maximizing):
    """Algoritma Minimax untuk menentukan langkah terbaik AI"""
    if check_winner(board, 'O'):
        return 1
    if check_winner(board, 'X'):
        return -1
    if is_full(board):
        return 0

    if is_maximizing:
        best_score = -math.inf
        for move in available_moves(board):
            board[move] = 'O'
            score = minimax(board, depth + 1, False)
            board[move] = ' '
            best_score = max(best_score, score)
        return best_score
    else:
        best_score = math.inf
        for move in available_moves(board):
            board[move] = 'X'
            score = minimax(board, depth + 1, True)
            board[move] = ' '
            best_score = min(best_score, score)
        return best_score


def ai_move(board):
    """Menentukan langkah terbaik AI"""
    best_score = -math.inf
    best_move = None
    for move in available_moves(board):
        board[move] = 'O'
        score = minimax(board, 0, False)
        board[move] = ' '
        if score > best_score:
            best_score = score
            best_move = move
    return best_move


# === Game Loop ===
def play_game():
    """Fungsi utama untuk menjalankan permainan"""
    board = [' ' for _ in range(9)]
    player_name = "Pemain"
    ai_name = "Potter"
    current_player = 'X'
    player_wins = 0
    ai_wins = 0

    print(f"\nSelamat datang di Tic Tac Toe: {player_name} vs {ai_name}!")
    print("Anda adalah 'X' dan Potter adalah 'O'.\n")
    print("Pilih posisi 1–9 sesuai layout:\n")
    print(" 1 | 2 | 3 ")
    print("---+---+---")
    print(" 4 | 5 | 6 ")
    print("---+---+---")
    print(" 7 | 8 | 9 \n")

    while True:
        clear_screen()
        print_board(board)

        # Giliran pemain
        if current_player == 'X':
            move = input("Masukkan posisi (1-9) atau 'q' untuk keluar: ").strip()
            if move.lower() == 'q':
                print("Permainan dihentikan.")
                break
            if not move.isdigit() or int(move) not in range(1, 10):
                print("Input tidak valid. Coba lagi.")
                time.sleep(1)
                continue
            move = int(move) - 1
            if board[move] != ' ':
                print("Posisi sudah terisi. Coba lagi.")
                time.sleep(1)
                continue
            board[move] = 'X'

            if check_winner(board, 'X'):
                clear_screen()
                print_board(board)
                print(f"🎉 Selamat {player_name}, kamu menang!")
                player_wins += 1
                break

        # Giliran AI
        else:
            print("🤖 Potter sedang berpikir...")
            time.sleep(1)
            move = ai_move(board)
            board[move] = 'O'
            if check_winner(board, 'O'):
                clear_screen()
                print_board(board)
                print(f"😢 Potter menang! Coba lagi, {player_name}.")
                ai_wins += 1
                break

        # Cek seri
        if is_full(board):
            clear_screen()
            print_board(board)
            print("🤝 Hasil seri!")
            break

        # Ganti giliran
        current_player = 'O' if current_player == 'X' else 'X'

    print(f"\nSkor akhir -> {player_name}: {player_wins} | {ai_name}: {ai_wins}\n")


# === Jalankan Game ===
if __name__ == "__main__":
    play_game()
