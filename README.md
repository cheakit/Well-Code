# Content should include

## 1. Beautiful and the art of coding
## 2. Design Patterns
## 3. Software Design Principles

---

# Writing Clean Code in Flutter

## Introduction

In Flutter development, writing clean and maintainable code is essential for the long-term success of a project. Clean code not only makes the codebase easier to understand but also facilitates collaboration among team members and reduces the likelihood of bugs. This document outlines best practices and guidelines for writing clean code in Flutter.

### 1. Beautiful and the art of coding

#### 1.1 File Structure

- Organize files logically within the project directory.
- Use folders to group related files (e.g., screens, models, services).

#### 1.2 Meaningful Names

#### 1.2.1 Variable Names

- Use descriptive names that convey the purpose of the variable.
- Avoid abbreviations and single-letter variable names.

```dart
// Bad: Using non-descriptive variable names
var a = 10;

// Good: Using descriptive variable names
var numberOfItems = 10;
```

#### 1.2.2 Class Names

Class names should be nouns and written in UpperCamelCase.
Use descriptive names that reflect the class's responsibility.

```dart
// Bad: Non-descriptive class name
class XYZ {}

// Good: Descriptive class name
class UserRepository {}
```

#### 1.2.3 Searchable Names

Use searchable names instead of calling it inside the function

```dart
//! BAD 

Future.delayed(const Duration(minutes: 30), () { 
  debugPrint('some logic here');
}); 

//* GOOD 

const MINUTES_DURATION = 30;

Future.delayed(const Duration(minutes: MINUTES_DURATION), () { 
  debugPrint('some logic');
}); 
```

#### 1.2.4 API call

Use Future.wait to make concurrent API calls
By using Future.wait, you can initiate multiple async tasks at the same time. Thereby reducing the overall execution time

```dart
Future callMultipleApis() async { 
  await getUserInfo(); 
  await getLocations();
} 

//* GOOD 

Future callMultipleApis() async { 
  await Future.wait([
    getUserInfo(), 
    getLocations(), 
  ]);
}
```

#### 1.2.5 Don't make duplicate code

Avoid duplicate code as much as possible. Duplications can complicate maintenance and updates, leading to inconsistencies. Instead, create a solid abstraction that can handle multiple scenarios with a single function or module. However, ensure that the abstraction is well-designed and follows the SOLID principles.

```dart
//! BAD

Widget _buildAvatarStudent() {
  return Image.asset('assets/images/student.png');
}

Widget _buildAvatarTeacher() {
  return Image.asset('assets/images/teacher.png');
}

//! GOOD

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

### 2. Design Patterns

### 3. Software Design Principles

## Conclusion

Adhering to clean code principles in Flutter development leads to a more maintainable and scalable codebase. By following the guidelines outlined in this document, developers can write code that is easier to understand, test, and extend, ultimately improving the quality of Flutter applications.

## References

-[https://medium.com/@flutterdynasty/design-patterns-in-flutter-25191278149c
](url)

-[https://blog.stackademic.com/solid-principles-in-flutter-crafting-robust-apps-0e1d1bcefeca](url)
