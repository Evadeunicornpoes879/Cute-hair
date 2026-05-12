from pydub import AudioSegment
from pydub.generators import Sine
from pathlib import Path

# Load uploaded voice recording
voice_path = "/mnt/data/Spraak 260510_183716.m4a"
voice = AudioSegment.from_file(voice_path)

# Normalize and trim a bit
voice = voice.normalize()

# Create a simple pop beat
beat_length = len(voice)
beat = AudioSegment.silent(duration=beat_length)

kick = Sine(60).to_audio_segment(duration=180).apply_gain(-3)
snare = Sine(220).to_audio_segment(duration=120).apply_gain(-8)

tempo = 118
beat_ms = int(60000 / tempo)

for i in range(0, beat_length, beat_ms):
    beat = beat.overlay(kick, position=i)
    if (i // beat_ms) % 2 == 1:
        beat = beat.overlay(snare, position=i)

# Add a soft synth drone
pad = Sine(440).to_audio_segment(duration=beat_length).apply_gain(-18)

# Mix everything
song = beat.overlay(pad).overlay(voice + 3)

# Fade in/out
song = song.fade_in(1000).fade_out(2000)

# Export
output_path = "/mnt/data/your_voice_pop_song.mp3"
song.export(output_path, format="mp3")

print(f"Created song: {output_path}")
