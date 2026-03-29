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
    {"kanji": "訳", "level": "N1"},
    {"kanji": "翻", "level": "N1"}
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
    {"kanji": "訳", "level": "N1"},
    {"kanji": "翻", "level": "N1"}
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
```python
import curses
import random
import urllib.parse
import time

# ===========================
# 1. DATA KUIS
# ===========================
sentences = [
    {
        "kanji": "済",
        "level": "N3",
        "strokes": 11,
        "meaning": {
            "id": "selesai",
            "en": "finish",
            "ar": "انتهى",
            "jv": "rampung"
        },
        "contexts": [
            {
                "theme": {
                    "id": "sesuatu sudah selesai",
                    "en": "something is completed",
                    "ar": "شيء قد انتهى",
                    "jv": "wis rampung"
                },
                "examples": [
                    {
                        "jp": "支払いが済む",
                        "id": "pembayaran selesai",
                        "en": "payment is completed",
                        "ar": "تم الدفع",
                        "jv": "pambayaran wis rampung",
                        "distractors": {
                            "id": ["belum selesai", "sedang berjalan", "akan diproses"],
                            "en": ["not finished", "in progress", "will be processed"],
                            "ar": ["لم ينته", "قيد المعالجة", "سيتم معالجته"],
                            "jv": ["durung rampung", "lagi proses", "arep diproses"]
                        }
                    },
                    {
                        "jp": "手続きが済む",
                        "id": "prosedur selesai",
                        "en": "procedure is completed",
                        "ar": "تمت الإجراءات",
                        "jv": "prosedur wis rampung",
                        "distractors": {
                            "id": ["belum selesai", "sedang berjalan", "akan diproses"],
                            "en": ["not finished", "in progress", "will be processed"],
                            "ar": ["لم ينته", "قيد المعالجة", "سيتم معالجته"],
                            "jv": ["durung rampung", "lagi proses", "arep diproses"]
                        }
                    }
                ]
            }
        ]
    }
]

# ===========================
# 2. HELPER FUNCTIONS
# ===========================

def normalize(text):
    return text.lower().strip()

def check_answer(user, correct_list):
    """Flexible check for typing mode"""
    user = normalize(user)
    return any(normalize(ans) in user for ans in correct_list)

def generate_choices(correct, distractors):
    """Randomize options for MC"""
    options = distractors + [correct]
    random.shuffle(options)
    return options

def get_input(stdscr, prompt=""):
    curses.echo()
    stdscr.addstr(prompt)
    stdscr.refresh()
    inp = stdscr.getstr().decode("utf-8")
    curses.noecho()
    return inp

# ===========================
# 3. MAIN FUNCTION
# ===========================
def main(stdscr):
    global sentences
    score = 0
    total = len(sentences)
    lives = 3
    time_limit = 20  # per soal

    curses.curs_set(1)
    stdscr.clear()

    # Mode select
    stdscr.addstr(0, 0, "Pilih Mode: 1. Multiple Choice  2. Typing  3. Hybrid")
    key = stdscr.getch()
    if key == ord("1"):
        mode = "mc"
    elif key == ord("2"):
        mode = "typing"
    else:
        mode = "hybrid"

    stdscr.clear()
    stdscr.addstr(0, 0, f"Mode: {mode.upper()}  | Nyawa: {lives}")
    stdscr.refresh()
    time.sleep(1)

    random.shuffle(sentences)

    for idx, item in enumerate(sentences, 1):
        if lives <= 0:
            stdscr.addstr(10, 0, "Nyawa habis! Game Over 💀")
            break

        context = random.choice(item["contexts"])
        theme_text = context["theme"]["id"]
        examples = context["examples"]

        stdscr.clear()
        stdscr.addstr(0, 0, f"Soal {idx} | Tema: {theme_text} | Nyawa: {lives}")
        stdscr.addstr(1, 0, "-"*50)

        # ===========================
        #  Loop setiap example
        # ===========================
        for ex in examples:
            stdscr.addstr(3, 0, f"Kalimat JP: {ex['jp']}")
            stdscr.refresh()

            for lang in ["id","en","ar","jv"]:
                # Tentukan mode per bahasa
                current_mode = mode
                if mode=="hybrid":
                    current_mode = random.choice(["mc","typing"])

                correct = ex[lang]
                distractors = ex["distractors"][lang]

                if current_mode == "mc":
                    options = generate_choices(correct, distractors)
                    stdscr.addstr(4, 0, f"[{lang.upper()}] Pilih jawaban:")
                    for i, opt in enumerate(options,1):
                        stdscr.addstr(5+i, 0, f"{i}. {opt}")
                    stdscr.addstr(10, 0, "> ")
                    stdscr.refresh()
                    try:
                        choice = int(stdscr.getstr().decode("utf-8"))
                        if options[choice-1] == correct:
                            stdscr.addstr(11, 0, "✅ Benar!")
                            score += 1
                        else:
                            stdscr.addstr(11, 0, f"❌ Salah! Jawaban: {correct}")
                            lives -= 1
                    except:
                        stdscr.addstr(11, 0, f"❌ Salah! Jawaban: {correct}")
                        lives -= 1
                    stdscr.refresh()
                    stdscr.getch()
                else:
                    user_input = get_input(stdscr, f"[{lang.upper()}] Ketik jawaban: ")
                    if check_answer(user_input, [correct]):
                        stdscr.addstr(12, 0, "✅ Benar!")
                        score += 1
                    else:
                        stdscr.addstr(12, 0, f"❌ Salah! Jawaban: {correct}")
                        lives -= 1
                    stdscr.refresh()
                    stdscr.getch()

        # ===========================
        # Kanji & Tema
        # ===========================
        stdscr.addstr(14, 0, "Tebak Kanji yang digunakan:")
        user_kanji = get_input(stdscr, "> ")
        if normalize(user_kanji) == normalize(item["kanji"]):
            stdscr.addstr(15, 0, "✅ Benar!")
            score += 3
        else:
            stdscr.addstr(15, 0, f"❌ Salah! Jawaban: {item['kanji']}")
            lives -= 1
        stdscr.refresh()
        stdscr.getch()

        # Tema
        stdscr.addstr(17, 0, "Tebak Tema dari semua contoh:")
        stdscr.addstr(18, 0, f"1. {context['theme']['id']}")
        stdscr.addstr(19, 0, "2. emosi")
        stdscr.addstr(20, 0, "3. pergerakan")
        stdscr.addstr(21, 0, "4. perubahan")
        user_theme = get_input(stdscr, "> ")
        if user_theme == "1":
            stdscr.addstr(22, 0, "✅ Benar!")
            score += 2
        else:
            stdscr.addstr(22, 0, f"❌ Salah! Tema: {context['theme']['id']}")
            lives -= 1
        stdscr.refresh()
        stdscr.getch()

    # ===========================
    # Hasil Akhir
    # ===========================
    stdscr.clear()
    stdscr.addstr(0, 0, "=== Hasil Kuis ===")
    stdscr.addstr(1, 0, f"Skor: {score}/{total*10}")
    stdscr.addstr(2, 0, f"Sisa nyawa: {lives}")
    if score == total*10:
        stdscr.addstr(3, 0, "Sempurna! 🔥")
    elif score >= total*5:
        stdscr.addstr(3, 0, "Lumayan 👍")
    else:
        stdscr.addstr(3, 0, "Perlu latihan lagi 💪")
    stdscr.addstr(5, 0, "Tekan Enter untuk keluar...")
    stdscr.refresh()
    stdscr.getch()


if __name__ == "__main__":
    curses.wrapper(main)

```
