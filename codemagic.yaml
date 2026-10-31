import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(const CodeManagerApp());
}

class CodeManagerApp extends StatelessWidget {
  const CodeManagerApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Code Manager',
      theme: ThemeData(
        colorSchemeSeed: Colors.deepPurple,
        useMaterial3: true,
      ),
      home: const HomePage(),
    );
  }
}

class Entry {
  final String code;
  final String amount;

  Entry(this.code, this.amount);
}

List<String> cyclic(String s) {
  return {s, s.substring(1) + s[0], s.substring(2) + s.substring(0, 2)}
      .toList();
}

List<String> permutations(String s) {
  final result = <String>{};

  void generate(String prefix, String remaining) {
    if (remaining.isEmpty) {
      result.add(prefix);
      return;
    }

    for (int i = 0; i < remaining.length; i++) {
      generate(
        prefix + remaining[i],
        remaining.substring(0, i) + remaining.substring(i + 1),
      );
    }
  }

  generate('', s);
  return result.toList();
}

List<Entry>? parseRecord(String text) {
  final r = text.trim();
  var m = RegExp(
    r'^(\d{3})\s+(\d+)\s+(3r|r)\s*(\d+)$',
    caseSensitive: false,
  ).firstMatch(r);

  if (m != null) {
    final base = m[1]!;
    final codes = m[3]!.toLowerCase() == '3r'
        ? cyclic(base)
        : permutations(base);

    return [
      Entry(base, m[2]!),
      ...codes.where((c) => c != base).map((c) => Entry(c, m[4]!)),
    ];
  }

  m = RegExp(r'^(\d{3})\s+3r\s*(\d+)$',
      caseSensitive: false).firstMatch(r);

  if (m != null) {
    return cyclic(m[1]!).map((c) => Entry(c, m[2]!)).toList();
  }

  m = RegExp(r'^(\d{3})\s+r\s*(\d+)$',
      caseSensitive: false).firstMatch(r);

  if (m != null) {
    return permutations(m[1]!).map((c) => Entry(c, m[2]!)).toList();
  }

  m = RegExp(r'^(\d{3})[.\s*=\-:;#]+(\d+)$').firstMatch(r);

  if (m != null) {
    return [Entry(m[1]!, m[2]!)];
  }

  m = RegExp(r'^(\d{3})(\d+)$').firstMatch(r);

  if (m != null) {
    return [Entry(m[1]!, m[2]!)];
  }

  return null;
}

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  final input = TextEditingController();
  List<Entry> results = [];

  void message(String text) {
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: Text(text)),
    );
  }

  Future<void> copy(String text) async {
    await Clipboard.setData(ClipboardData(text: text));
    message('Copy လုပ်ပြီးပါပြီ။');
  }

  void process() {
    final raw = input.text.trim();

    if (raw.isEmpty) {
      message('Code နှင့် Amount ထည့်ပါ။');
      return;
    }

    final records = raw
        .split(RegExp(r'[,\n]+'))
        .map((e) => e.trim())
        .where((e) => e.isNotEmpty);

    final output = <Entry>[];

    for (final record in records) {
      final parsed = parseRecord(record);

      if (parsed == null) {
        message('ပုံစံမမှန်ပါ: $record');
        return;
      }

      output.addAll(parsed);
    }

    setState(() {
      results = output;
    });

    message('အောင်မြင်ပါပြီ။');
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('CODE MANAGER'),
      ),
      body: ListView(
        padding: const EdgeInsets.all(16),
        children: [
          TextField(
            controller: input,
            minLines: 5,
            maxLines: 10,
            decoration: const InputDecoration(
              labelText: 'Code နှင့် Amount ထည့်ပါ',
              hintText: '459 50\n123 3r 100\n789 r 20',
              border: OutlineInputBorder(),
            ),
          ),
          const SizedBox(height: 12),
          Row(
            children: [
              Expanded(
                child: FilledButton(
                  onPressed: process,
                  child: const Text('SUBMIT'),
                ),
              ),
              const SizedBox(width: 8),
              Expanded(
                child: OutlinedButton(
                  onPressed: () {
                    input.clear();
                  },
                  child: const Text('CLEAR'),
                ),
              ),
            ],
          ),
          const SizedBox(height: 20),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              const Text(
                'RESULTS',
                style: TextStyle(fontWeight: FontWeight.bold),
              ),
              TextButton(
                onPressed: results.isEmpty
                    ? null
                    : () => copy(
                          results
                              .map((e) => '${e.code} ${e.amount}')
                              .join('\n'),
                        ),
                child: const Text('COPY ALL'),
              ),
            ],
          ),
          ...results.map(
            (e) => Card(
              child: ListTile(
                title: Text(
                  e.code,
                  style: const TextStyle(fontWeight: FontWeight.bold),
                ),
                subtitle: Text('Amount: ${e.amount}'),
                trailing: IconButton(
                  tooltip: 'Copy Code + Amount',
                  icon: const Icon(Icons.content_copy),
                  onPressed: () => copy('${e.code} ${e.amount}'),
                ),
              ),
            ),
          ),
        ],
      ),
    );
  }
}
