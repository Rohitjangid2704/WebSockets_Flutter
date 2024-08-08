```markdown
# WebSocket Flutter App

## How to Run

1. **Clone the Repository**

   ```bash
   git clone https://github.com/Rohitjangid2704/WebSockets_Flutter.git
   ```

2. **Navigate to the Project Directory**

   ```bash
   cd WebSockets_Flutter
   ```

3. **Install Dependencies**

   Make sure you have Flutter installed. Run the following command to get the required packages:

   ```bash
   flutter pub get
   ```

4. **Run the App**

   To run the `main.dart` file, execute the following command from the root directory of the project:

   ```bash
   flutter run lib/main.dart
   ```

   This will launch the app on an emulator or physical device.

## Additional Information

- Ensure your development environment is properly set up with Flutter.
- The app connects to the WebSocket endpoint `wss://stream.binance.com:9443/ws/!miniTicker@arr` to fetch and display real-time stock prices.

## `pubspec.yaml`

```yaml
name: websocket_app
description: "A new Flutter project."
publish_to: 'none' # Remove this line if you wish to publish to pub.dev

version: 1.0.0+1

environment:
  sdk: ^3.5.0

dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.8
  web_socket_channel: ^2.2.0  # Add this line

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0

flutter:
  uses-material-design: true
```

## `main.dart`

```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:web_socket_channel/web_socket_channel.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'WebSocket Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  final WebSocketChannel channel = WebSocketChannel.connect(
    Uri.parse('wss://stream.binance.com:9443/ws/!miniTicker@arr'),
  );

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('WebSocket Data'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: StreamBuilder(
          stream: channel.stream,
          builder: (context, snapshot) {
            if (snapshot.hasError) {
              return Center(child: Text('Error: ${snapshot.error}'));
            }
            if (snapshot.connectionState == ConnectionState.waiting) {
              return Center(child: CircularProgressIndicator());
            }
            if (snapshot.hasData) {
              try {
                List<dynamic> data = jsonDecode(snapshot.data.toString());
                return ListView.builder(
                  itemCount: data.length,
                  itemBuilder: (context, index) {
                    final item = data[index];
                    return Card(
                      margin: const EdgeInsets.symmetric(vertical: 8.0),
                      elevation: 4.0,
                      child: ListTile(
                        contentPadding: const EdgeInsets.all(16.0),
                        leading: Icon(Icons.attach_money, color: Colors.green),
                        title: Text(
                          item['s'],
                          style: TextStyle(
                            fontWeight: FontWeight.bold,
                            fontSize: 18.0,
                          ),
                        ),
                        subtitle: Text(
                          'Price: ${item['c']}',
                          style: TextStyle(
                            fontSize: 16.0,
                            color: Colors.black54,
                          ),
                        ),
                      ),
                    );
                  },
                );
              } catch (e) {
                return Center(child: Text('Error parsing data: $e'));
              }
            }
            return Center(child: Text('No data available'));
          },
        ),
      ),
    );
  }

  @override
  void dispose() {
    channel.sink.close();
    super.dispose();
  }
}
```
