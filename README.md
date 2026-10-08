import sys
from collections import Counter


def analyze_file(filename):
    line_count = 0
    word_count = 0
    char_count = 0
    char_count_no_spaces = 0
    longest_word = ""
    word_freq = Counter()

    with open(filename, "r", encoding="utf-8") as file:
        for line in file:
            line_count += 1
            char_count += len(line)
            char_count_no_spaces += len("".join(line.split()))

            words = line.split()
            word_count += len(words)

            for word in words:
                clean = word.strip(".,!?;:\"'()[]{}").lower()
                if clean:
                    word_freq[clean] += 1
                    if len(clean) > len(longest_word):
                        longest_word = clean

    return {
        "lines": line_count,
        "words": word_count,
        "characters": char_count,
        "characters_no_spaces": char_count_no_spaces,
        "longest_word": longest_word,
        "top_words": word_freq.most_common(5),
    }


def format_report(filename, stats):
    report = [
        "=" * 40,
        f"  TEXT FILE STATISTICS: {filename}",
        "=" * 40,
        f"Line count                 : {stats['lines']}",
        f"Word count                 : {stats['words']}",
        f"Character count            : {stats['characters']}",
        f"Characters (without spaces): {stats['characters_no_spaces']}",
        f"Longest word               : {stats['longest_word']}",
        "",
        "Top 5 most common words:",
    ]
    for word, count in stats["top_words"]:
        report.append(f"  {word:<12} -> {count}")
    return "\n".join(report)


def main():
    filename = sys.argv[1] if len(sys.argv) > 1 else "input.txt"

    try:
        stats = analyze_file(filename)
    except FileNotFoundError:
        print(f"Error: '{filename}' naam ki file nahi mili. Naam check karein.")
        return
    except PermissionError:
        print(f"Error: '{filename}' ko parhne ki permission nahi hai.")
        return
    except UnicodeDecodeError:
        print(f"Error: '{filename}' valid UTF-8 text file nahi hai.")
        return

    report = format_report(filename, stats)
    print(report)

    with open("statistics.txt", "w", encoding="utf-8") as out:
        out.write(report + "\n")
    print("\nStatistics 'statistics.txt' me save ho gayi.")


if __name__ == "__main__":
    main()
