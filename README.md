# eucalypsih_rcrapsqa

```python
import urllib.parse
import random
import time

# daftar soal: kanji + level (N5–N1)
kanji_list = [
    {"kanji": "済", "level": "N3"},
    {"kanji": "訳", "level": "N1"},
    {"kanji": "翻", "level": "N1"}
]

# acak soal
random.shuffle(kanji_list)

score = 0
total = len(kanji_list)
time_limit = 15  # detik per soal

print("=== Kuis Kanji (Jawab dengan URL-encoded / hex) ===")
print(f"Setiap soal ada batas waktu {time_limit} detik!\n")

for i, item in enumerate(kanji_list, 1):
    kanji = item["kanji"]
    level = item["level"]
    print(f"Soal {i} (Level {level}): Apa URL-encoded dari kanji {kanji}?")
    
    start_time = time.time()
    try:
        user_input = input("Jawaban: ")
    except KeyboardInterrupt:
        print("\nKuis dihentikan.")
        break
    
    elapsed = time.time() - start_time
    
    if elapsed > time_limit:
        print(f"Waktu habis! (lebih dari {time_limit} detik)")
        decoded = None
    else:
        decoded = urllib.parse.unquote(user_input)
    
    if decoded == kanji:
        print("Benar!\n")
        score += 1
    else:
        correct_hex = urllib.parse.quote(kanji)
        print("Salah!")
        if decoded is not None:
            print("Decode jawaban kamu:", decoded)
        print("Jawaban benar:", correct_hex, "\n")

print("=== Hasil Kuis ===")
print(f"Skor: {score}/{total}")

if score == total:
    print("Sempurna! 🔥")
elif score >= total // 2:
    print("Lumayan 👍")
else:
    print("Perlu latihan lagi 💪")

```

```python
import curses
import urllib.parse
import random
import time

kanji_list = [
    {"kanji": "済", "level": "N3"},
    {"kanji": "学", "level": "N5"},
    {"kanji": "水", "level": "N5"},
    {"kanji": "火", "level": "N5"},
    {"kanji": "山", "level": "N5"},
    {"kanji": "経", "level": "N2"},
    {"kanji": "調", "level": "N2"},
    {"kanji": "策", "level": "N1"},
    {"kanji": "詳", "level": "N1"}
]

random.shuffle(kanji_list)
score = 0
total = len(kanji_list)
time_limit = 15
lives = 3

def main(stdscr):
    global score, lives
    curses.curs_set(1)
    stdscr.clear()

    stdscr.addstr(0, 0, "=== Kuis Kanji (URL-encoded) ===")
    stdscr.addstr(1, 0, f"Waktu per soal: {time_limit} detik | Nyawa: {lives}")
    stdscr.refresh()

    for i, item in enumerate(kanji_list, 1):
        if lives <= 0:
            stdscr.addstr(10, 0, "Nyawa habis! Game Over 💀")
            break

        kanji = item["kanji"]
        level = item["level"]

        stdscr.addstr(3, 0, f"Soal {i} (Level {level}) | Nyawa: {lives}            ")
        stdscr.addstr(4, 0, f"Apa URL-encoded dari kanji {kanji}?               ")
        stdscr.addstr(5, 0, "Jawaban: ")
        stdscr.clrtoeol()
        stdscr.refresh()

        stdscr.move(5, 9)
        stdscr.clrtoeol()
        stdscr.refresh()

        user_input = ""
        start_time = time.time()

        # Mode input non-blok dan update realtime
        stdscr.nodelay(True)
        stdscr.timeout(100)

        while True:
            elapsed = time.time() - start_time
            remaining = int(time_limit - elapsed)

            # Update countdown
            stdscr.addstr(6, 0, f"Sisa waktu: {remaining} detik   ")
            stdscr.clrtoeol()

            # Update input secara realtime
            stdscr.addstr(5, 9, user_input)
            stdscr.clrtoeol()

            stdscr.refresh()

            if remaining <= 0:
                user_input = None
                break

            ch = stdscr.getch()

            if ch == -1:
                continue
            elif ch in (10, 13):  # Enter
                break
            elif ch in (8, 127):  # Backspace
                if len(user_input) > 0:
                    user_input = user_input[:-1]
            elif 0 <= ch <= 255:
                c = chr(ch)
                user_input += c

        stdscr.nodelay(False)  # kembali ke mode blocking

        if user_input is None:
            stdscr.addstr(7, 0, "⏰ Waktu habis!                        ")
            lives -= 1
            decoded = None
        else:
            decoded = urllib.parse.unquote(user_input)

        if decoded == kanji:
            stdscr.addstr(7, 0, "✅ Benar!                                 ")
            score += 1
        else:
            lives -= 1 if user_input is not None else 0
            correct_hex = urllib.parse.quote(kanji)
            stdscr.addstr(7, 0, f"❌ Salah! Jawaban benar: {correct_hex}     ")
        stdscr.addstr(8, 0, "Tekan Enter untuk lanjut...")
        stdscr.refresh()

        # Tunggu Enter
        while True:
            key = stdscr.getch()
            if key in (10, 13):
                break

    # Hasil akhir
    stdscr.clear()
    stdscr.addstr(0, 0, "=== Hasil Kuis ===")
    stdscr.addstr(1, 0, f"Skor: {score}/{total}")
    stdscr.addstr(2, 0, f"Sisa nyawa: {lives}")

    if score == total:
        stdscr.addstr(3, 0, "Sempurna! 🔥")
    elif score >= total // 2:
        stdscr.addstr(3, 0, "Lumayan 👍")
    else:
        stdscr.addstr(3, 0, "Perlu latihan lagi 💪")

    stdscr.addstr(5, 0, "Tekan Enter untuk keluar...")
    stdscr.refresh()
    while True:
        key = stdscr.getch()
        if key in (10, 13):
            break

curses.wrapper(main)

```

```python
import curses
import urllib.parse
import random
import time

kanji_list = [
    {"kanji": "済", "level": "N3"},
    {"kanji": "学", "level": "N5"},
    {"kanji": "水", "level": "N5"},
    {"kanji": "火", "level": "N5"},
    {"kanji": "山", "level": "N5"},
    {"kanji": "経", "level": "N2"},
    {"kanji": "調", "level": "N2"},
    {"kanji": "策", "level": "N1"},
    {"kanji": "詳", "level": "N1"}
]

random.shuffle(kanji_list)
score = 0
total = len(kanji_list)
time_limit = 15
lives = 3

def main(stdscr):
    global score, lives
    curses.curs_set(1)
    stdscr.clear()

    stdscr.addstr(0, 0, "=== Kuis Kanji (URL-encoded) ===")
    stdscr.addstr(1, 0, f"Waktu per soal: {time_limit} detik | Nyawa: {lives}")
    stdscr.refresh()

    for i, item in enumerate(kanji_list, 1):
        if lives <= 0:
            stdscr.addstr(10, 0, "Nyawa habis! Game Over 💀")
            break

        kanji = item["kanji"]
        level = item["level"]

        stdscr.addstr(3, 0, f"Soal {i} (Level {level}) | Nyawa: {lives}            ")
        stdscr.addstr(4, 0, f"Apa URL-encoded dari kanji {kanji}?               ")
        stdscr.addstr(5, 0, "Jawaban: ")
        stdscr.refresh()

        user_input = ""
        start_time = time.time()

        # Mode input non-blocking
        stdscr.nodelay(True)
        stdscr.timeout(100)

        while True:
            elapsed = time.time() - start_time
            remaining = int(time_limit - elapsed)

            # Update countdown
            stdscr.addstr(6, 0, f"Sisa waktu: {remaining:2} detik   ")

            # Update input
            stdscr.addstr(5, 9, user_input + " " * (50 - len(user_input)))
            stdscr.move(5, 9 + len(user_input))
            stdscr.refresh()

            if remaining <= 0:
                user_input = None
                break

            ch = stdscr.getch()
            if ch == -1:
                continue
            elif ch in (10, 13):  # Enter
                break
            elif ch in (8, 127, 263):  # Backspace
                if len(user_input) > 0:
                    user_input = user_input[:-1]
            elif 0 <= ch <= 255:
                user_input += chr(ch)

        stdscr.nodelay(False)

        if user_input is None:
            stdscr.addstr(7, 0, "⏰ Waktu habis!                        ")
            lives -= 1
            decoded = None
        else:
            decoded = urllib.parse.unquote(user_input)

        correct_encoded = urllib.parse.quote(kanji)
        if decoded == kanji:
            stdscr.addstr(7, 0, "✅ Benar!                                 ")
            score += 1
        else:
            if user_input is not None:
                lives -= 1
            stdscr.addstr(7, 0, f"❌ Salah! Jawaban benar: {correct_encoded}     ")

        stdscr.addstr(8, 0, "Tekan Enter untuk lanjut...")
        stdscr.refresh()

        # Tunggu Enter
        while True:
            key = stdscr.getch()
            if key in (10, 13):
                break

    # Hasil akhir
    stdscr.clear()
    stdscr.addstr(0, 0, "=== Hasil Kuis ===")
    stdscr.addstr(1, 0, f"Skor: {score}/{total}")
    stdscr.addstr(2, 0, f"Sisa nyawa: {lives}")

    if score == total:
        stdscr.addstr(3, 0, "Sempurna! 🔥")
    elif score >= total // 2:
        stdscr.addstr(3, 0, "Lumayan 👍")
    else:
        stdscr.addstr(3, 0, "Perlu latihan lagi 💪")

    stdscr.addstr(5, 0, "Tekan Enter untuk keluar...")
    stdscr.refresh()
    while True:
        key = stdscr.getch()
        if key in (10, 13):
            break

curses.wrapper(main)

```
