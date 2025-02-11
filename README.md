# Word_game

import 'dart:math';
import 'package:flutter/material.dart';

class GuessTheWordGame extends StatefulWidget {
  const GuessTheWordGame({super.key});

  @override
  // ignore: library_private_types_in_public_api
  _GuessTheWordGameState createState() => _GuessTheWordGameState();
}

class _GuessTheWordGameState extends State<GuessTheWordGame> {
  final List<String> words = ["flutter", "dart", "developer", "mobile", "application"];
  final List<String> hints = [
    "A UI toolkit for building natively compiled apps.",
    "A programming language for web and mobile.",
    "A person who creates applications.",
    "Relating to handheld devices.",
    "A software designed to perform specific tasks."
  ];
  late String selectedWord;
  late List<String> guessedLetters;
  int attemptsLeft = 6;

  @override
  void initState() {
    super.initState();
    startNewGame();
  }

  void startNewGame() {
    selectedWord = words[Random().nextInt(words.length)];
    guessedLetters = [];
    attemptsLeft = 6;
  }

  void guessLetter(String letter) {
    if (!guessedLetters.contains(letter)) {
      guessedLetters.add(letter);
      if (!selectedWord.contains(letter)) {
        setState(() {
          attemptsLeft--;
        });
      }
    }
    if (attemptsLeft == 0) {
      showDialogResult("Game Over! The word was '$selectedWord'.");
    } else if (isWordGuessed()) {
      showDialogResult("Congratulations! You guessed the word '$selectedWord'!");
    }
    setState(() {});
  }

  bool isWordGuessed() {
    return selectedWord.split('').every((letter) => guessedLetters.contains(letter));
  }

  void showDialogResult(String message) {
    showDialog(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: Text(message, style: const TextStyle(fontSize: 18)),
          actions: [
            TextButton(
              style: TextButton.styleFrom(
                foregroundColor: Colors.white, backgroundColor: Colors.blue,
              ),
              child: const Text('Play Again'),
              onPressed: () {
                Navigator.of(context).pop();
                startNewGame();
                setState(() {});
              },
            ),
          ],
        );
      },
    );
  }

  void showHint() {
    showDialog(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: const Text('Hint', selectionColor: Color.fromARGB(255, 255, 255, 255),),
          content: Text(hints[words.indexOf(selectedWord)]),
          actions: [
            TextButton(
              child: const Text('Close'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Guess the Word Game'),
        centerTitle: true,
      ),
      body: Container(
        decoration: const BoxDecoration(
          gradient: LinearGradient(
            colors: [Colors.blueAccent, Colors.lightBlueAccent],
            begin: Alignment.topLeft,
            end: Alignment.bottomRight,
          ),
        ),
        padding: const EdgeInsets.all(16.0),
        child: Center(
          child: Card(
            elevation: 10,
            child: Padding(
              padding: const EdgeInsets.all(16.0),
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: [
                  const Text(
                    'Guess the Word!',
                    style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 20),
                  Text(
                    'Attempts Left: $attemptsLeft',
                    style: const TextStyle(fontSize: 18),
                  ),
                  const SizedBox(height: 20),
                  Row(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: selectedWord.split('').map((letter) {
                      return Padding(
                        padding: const EdgeInsets.symmetric(horizontal: 4.0),
                        child: Text(
                          guessedLetters.contains(letter) ? letter : '_',
                          style: const TextStyle(fontSize: 30, fontWeight: FontWeight.bold),
                        ),
                      );
                    }).toList(),
                  ),
                  const SizedBox(height: 20),
                  ElevatedButton(
                    onPressed: attemptsLeft > 0 && !isWordGuessed() ? showHint : null,
                    style: ElevatedButton.styleFrom(
                      backgroundColor: Colors.blue,
                      padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 10),
                    ),
                    child: const Text('Show Hint'),
                  ),
                  const SizedBox(height: 20),
                  Wrap(
                    spacing: 8.0,
                    children: 'abcdefghijklmnopqrstuvwxyz'.split('').map((letter) {
                      return ElevatedButton(
                        style: ElevatedButton.styleFrom(
                          foregroundColor: Colors.white, backgroundColor: Colors.blue,
                          padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 5),
                          shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(5)),
                          elevation: 5,
                        ),
                        onPressed: attemptsLeft > 0 && !isWordGuessed()
                            ? () => guessLetter(letter)
                            : null,
                        child: Text(letter.toUpperCase()),
                      );
                    }).toList(),
                  ),
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}

import 'package:flutter/material.dart';
import 'game.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Guess the Word Game',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const GuessTheWordGame(),
    );
  }
}
