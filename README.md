# eucalypsih_rcrapsqa

```python
import urllib.parse
import random
import time

# daftar soal: kanji + level (N5–N1)
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
