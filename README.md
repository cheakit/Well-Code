# Content should include

* Beautiful and the art of coding
* Design Patterns
* Software Design Principles

---

# Writing Clean Code in Flutter

## Introduction

In Flutter development, writing clean and maintainable code is essential for the long-term success of a project. Clean code not only makes the codebase easier to understand but also facilitates collaboration among team members and reduces the likelihood of bugs. This document outlines best practices and guidelines for writing clean code in Flutter.

### 1. Beautiful and the art of coding

#### 1.1 File Structure

- Organize files logically within the project directory.
- Use folders to group related files (e.g., screens, models, services ..etc).

```dart
// BAD:
project_directory/
│
├── lib/
│   ├── home_screen.dart
│   ├── profile_screen.dart
│   ├── settings_screen.dart
│   ├── user_model.dart
│   ├── product_model.dart
│   ├── order_model.dart
│   ├── authentication_service.dart
│   ├── database_service.dart
│   └── analytics_service.dart
│
└── main.dart

// GOOD:
project_directory/
│
├── lib/
│   ├── screens/
│   │   ├── home_screen.dart
│   │   ├── profile_screen.dart
│   │   └── settings_screen.dart
│   │
│   ├── models/
│   │   ├── user_model.dart
│   │   ├── product_model.dart
│   │   └── order_model.dart
│   │
│   └── services/
│       ├── authentication_service.dart
│       ├── database_service.dart
│       └── analytics_service.dart
│
└── main.dart
```

#### 1.2 Meaningful Names

##### 1.2.1 Variable Names

- Use descriptive names that convey the purpose of the variable.
- Avoid abbreviations and single-letter variable names.

```dart
// BAD: Using non-descriptive variable names
var a = 10;

// GOOD: Using descriptive variable names
var numberOfItems = 10;
```

##### 1.2.2 Class Names

Class names should be nouns and written in UpperCamelCase.
Use descriptive names that reflect the class's responsibility.

```dart
// BAD: Non-descriptive class name
class XYZ {}

// GOOD: Descriptive class name
class UserRepository {}
```

##### 1.2.3 Searchable Names

Use searchable names instead of calling it inside the function

```dart
// BAD:

Future.delayed(const Duration(minutes: 30), () { 
  debugPrint('some logic here');
}); 

// GOOD:

const MINUTES_DURATION = 30;

Future.delayed(const Duration(minutes: MINUTES_DURATION), () { 
  debugPrint('some logic');
}); 
```

#### 1.2.4 Make sure folders and files are properly named.

```dart
// BAD: these are all wrong
loginView.dart
LoginView.dart
loginview.dart
Login_View.dart

// GOOD: this is the proper way to name a file
login_view.dart
```

#### 1.3 API call

Use Future.wait to make concurrent API calls
By using Future.wait, you can initiate multiple async tasks at the same time. Thereby reducing the overall execution time

```dart
// BAD:
Future callMultipleApis() async { 
  await getUserInfo(); 
  await getLocations();
} 

// GOOD:
Future callMultipleApis() async { 
  await Future.wait([
    getUserInfo(), 
    getLocations(), 
  ]);
}
```

#### 1.4 Don't make duplicate code

Avoid duplicate code as much as possible. Duplications can complicate maintenance and updates, leading to inconsistencies. Instead, create a solid abstraction that can handle multiple scenarios with a single function or module. However, ensure that the abstraction is well-designed and follows the SOLID principles.

```dart
// BAD:
Widget _buildAvatarStudent() {
  return Image.asset('assets/images/student.png');
}

Widget _buildAvatarTeacher() {
  return Image.asset('assets/images/teacher.png');
}

// GOOD:
Widget _buildAvatar(Person person) {
  String image = 'assets/images/placeholder.png';
  if (person is Student) {
    image = 'assets/images/student.png';
  } else if (person is Teacher) {
    image = 'assets/iamges/teacher.png';
  }
  return Image.asset(image);
}
```

---

### 2. Design Patterns (S.O.L.I.D Principle)

#### 2.1 S — Single Responsibility Principle (SRP)

The idea behind the SRP is that every class, module, or function in a program should have one responsibility/purpose in a program. As a commonly used definition, "every class should have only one reason to change".

Consider the example below:

```dart
// DON'T:
class Student {
  void registerStudent() {
    // some logic
  }

  void calculateStudentResults() {
    // some logic
  }

  void sendEmail() {
    // some logic
  }
}
```

The class above violates the single responsibility principle. Why?
This Student class has three responsibilities – registering students, calculating their results, and sending out emails to students.

The code above will work perfectly but will lead to some challenges. We cannot make this code reusable for other classes or objects. The class has a whole lot of logic interconnected that we would have a hard time fixing errors.

Imagine a new developer joining a team with this sort of logic with a codebase of about two thousand lines of code all congested into one class.

Now let's fix this

```dart
// DO:
class StudentRegister {
  void registerStudent() {
    // some logic
  }
}

class StudentResult{
  void calculateStudentResults() {
    // some logic
  }
}

class StudentEmails{
  void sendEmail() {
    // some logic
  }
}
```

Also imagine a widget handling UI rendering, data retrieval, and business logic. Sounds messy, right? SRP advocates for splitting responsibilities. For instance, rather than a monolithic widget, use dedicated widgets for UI, providers, blocs or GetX for state management, and repositories for data operations.

```dart
// DON'T:
class UserProfile extends StatelessWidget {
  final User user;
  //... constructor and other methods

  Future<UserData> fetchUserData() async {
    // Fetch logic here (asynchronous)
    // ...
  }

  @override
  Widget build(BuildContext context) {
    // Fetching user data (Not a UI responsibility!)
    user = fetchUserData(user.id);

    return Text(data.name);
  }
}
```

Instead, separate responsibilities:

```dart
class UserController extend GetxController {

Final user= Rxn<User>(null);

@override
  void onInit() {
    fetchUserData();
  }


  // Handles data fetching logic
  Future<UserData> fetchUserData(int id) async {
    // Fetch logic here (asynchronous) from service
    // ...
	user.value = result;
  }
}

class UserProfile extends StatelessWidget {

  UserProfile({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final controller = Get.put(UserController());
    return Text(controller.user.value.name)
    );
  }
}
```

#### 2.2 O — Open/Closed Principle (OCP)

The open-closed principle states that software entities should be open for extension, but closed for modification.
This implies that such entities – classes, functions, and so on – should be created in a way that their core functionalities can be extended to other entities without altering the initial entity's source code.

What if you now need to draw squares? Instead of altering the widget, design it in a way that allows adding new shapes without changing the existing code. Maybe introduce a base Shape class and extend it for various forms.

Instead of:
```dart
// DON'T
class ShapePainter {
  final String shapeType;

  draw() {
    if (shapeType == 'circle') {
      // Draw circle
    } else if (shapeType == 'square') {
      // Draw square
    }
  }
}
```

Opt for:

```dart
// DO
abstract class Shape {
  void draw();
}

class Circle implements Shape {
  void draw() {
    // Draw circle
  }
}

class Square implements Shape {
  void draw() {
    // Draw square
  }
}

class ShapePainter {
  final Shape shape;
  void draw() => shape.draw();
}
```

#### 2.3 L — Liskov Substitution Principle (LSP)

According to Barbara Liskov and Jeannette Wing, the Liskov substitution principle states that:

Don't worry if you find that confusing, it will all make sense soon. Let's simplify this principle below:

The Liskov substitution principle simply implies that when an instance of a class is passed/extended to another class, the inheriting class should have a use case for all the properties and behavior of the inherited class.

Imagine having a base Bird class and a derived Penguin class. If the Bird class has a fly method, it wouldn't fit the Penguin. LSP suggests that derived classes should perfectly fit the behaviors of their base classes. Here, we might need a rethinking of our class design.

Instead of:

```dart
// DON'T
class Bird {
  void fly() {}
}

class Penguin extends Bird {} // Penguin can't fly!
```

Refactor to:

```dart
// DO
class Bird {
  void move() {
    // Common implementation for all birds
  }
}

class Sparrow extends Bird {
  void move() {
    // Some Logic here
  }
  // The move method from Bird is reused, no new methods are added
}

class Penguin extends Bird {
  void move() {
    // Some Logic here
  }
  // The move method from Bird is reused, no new methods are added
}
```

#### 2.4 I — Interface Segregation Principle (ISP)

---

### 3. Software Design Principles

---

## Conclusion

Adhering to clean code principles in Flutter development leads to a more maintainable and scalable codebase. By following the guidelines outlined in this document, developers can write code that is easier to understand, test, and extend, ultimately improving the quality of Flutter applications.

---

## References

-[https://medium.com/@flutterdynasty/design-patterns-in-flutter-25191278149c
](url)

-[https://blog.stackademic.com/solid-principles-in-flutter-crafting-robust-apps-0e1d1bcefeca](url)

-[https://www.freecodecamp.org/news/solid-principles-single-responsibility-principle-explained/](url)
