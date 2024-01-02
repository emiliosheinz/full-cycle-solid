# Full Cycle SOLID

SOLID is an acronym for five principles that help software developers design maintainable and extendable classes. It was introduced by Robert C. Martin (Uncle Bob) in his 2000 paper Design Principles and Design Patterns.

- S: Single Responsibility Principle
- O: Open/Closed Principle
- L: Liskov Substitution Principle
- I: Interface Segregation Principle
- D: Dependency Inversion Principle

## Single Responsibility Principle (SRP)

A class should have one and only one reason to change, meaning that a class should have only one job. If a class has more than one responsibility, it becomes coupled. A change to one responsibility results to modification of the other responsibility too.

In the example below, the `User` class violates SRP by having both responsibilities of displaying user details and saving to the database. The refactored version consists of two separate classes (`User` and `UserDatabase`), each with a single responsibility. This adheres to the Single Responsibility Principle and makes the code more modular and maintainable.

**Violation of SRP**:
```js
// Class with multiple responsibilities (violating SRP)
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // Responsibility 1: Display user details
  displayDetails() {
    console.log(`Name: ${this.name}, Age: ${this.age}`);
  }

  // Responsibility 2: Save user to the database
  saveToDatabase() {
    console.log(`Saving user ${this.name} to the database...`);
    // Code to save user to the database
  }
}
```

**Adhering to SRP**:
```js
// Class with responsibility for user information
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  displayDetails() {
    console.log(`Name: ${this.name}, Age: ${this.age}`);
  }
}

// Class with responsibility for saving user to the database
class UserDatabase {
  static saveToDatabase(user) {
    console.log(`Saving user ${user.name} to the database...`);
    // Code to save user to the database
  }
}
```

**Usage example:**
```js
const user = new User("John Doe", 25);
user.displayDetails();

UserDatabase.saveToDatabase(user);
```

## Open/Closed Principle (OCP)

Objects or entities should be open for extension but closed for modification. This simply means that a class should be easily extendable without modifying the class itself.

One way to achieve this is by using a combination of the Factory Pattern and the Strategy Pattern. The Factory Pattern handles the object creation and the Strategy Pattern handles the object composition. Together, they can be used to create objects based on some conditions.

In this example:

1. `Payment` is the strategy interface that defines the common pay method.
1. `CreditCardPayment` and `PayPalPayment` are concrete strategy classes implementing the payment logic for credit card and PayPal, respectively.
1. `PaymentFactory` is a factory class responsible for creating the appropriate payment strategy based on the provided payment method.
1. `ShoppingCart` is the client class that uses the selected payment strategy to process the checkout.
1. This setup adheres to the Open/Closed Principle as you can easily extend the system by adding new payment strategies without modifying existing code.

```js
// Strategy Pattern: Define different strategies
abstract class Payment {
  pay(amount) {
    throw new Error("This method should be overridden by concrete strategies");
  }
}

class CreditCardPayment extends Payment {
  pay(amount) {
    console.log(`Paid $${amount} using Credit Card.`);
  }
}

class PayPalPayment extends Payment {
  pay(amount) {
    console.log(`Paid $${amount} using PayPal.`);
  }
}

// Factory Pattern: Create a factory to generate payment strategies
class PaymentFactory {
  static createPayment(paymentMethod) {
    switch (paymentMethod) {
      case "creditCard":
        return new CreditCardPayment();
      case "paypal":
        return new PayPalPayment();
      default:
        throw new Error("Invalid payment method");
    }
  }
}

// Client code using the strategies and factory
class ShoppingCart {
  constructor(paymentMethod) {
    this.payment = PaymentFactory.createPayment(
      paymentMethod
    );
  }

  checkout(amount) {
    this.payment.pay(amount);
  }
}

// Example usage
const shoppingCartCreditCard = new ShoppingCart("creditCard");
shoppingCartCreditCard.checkout(100);

const shoppingCartPayPal = new ShoppingCart("paypal");
shoppingCartPayPal.checkout(50);
```


## Liskov Substitution Principle (LSP)

Subclasses should be substitutable for their base classes. This means that a subclass should override the parent class methods in a way that does not break functionality from a client’s point of view.

In the example below, we have a base class `Shape` with a method area. The `Square` and `Circle` classes are subclasses that override the area method, ensuring that they can be substituted for the base class without breaking the client code (the `printArea` function). The `printArea` function takes a `Shape` as a parameter and prints its area, demonstrating the Liskov Substitution Principle in action.

```js    
class Shape {
  constructor() {}

  area() {
    throw new Error("Method not implemented");
  }
}

class Square extends Shape {
  constructor(side) {
    super();
    this.side = side;
  }

  area() {
    return this.side * this.side;
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }

  area() {
    return Math.PI * this.radius * this.radius;
  }
}

function printArea(shape) {
  console.log(`Area: ${shape.area()}`);
}

// Using Liskov Substitution Principle
const square = new Square(5);
const circle = new Circle(3);

printArea(square); // Output: Area: 25
printArea(circle); // Output: Area: 28.274333882308138
```

## Interface Segregation Principle (ISP)

A class should never be forced to implement an interface that it doesn’t use or classes shouldn’t be forced to depend on methods they do not use. This principle deals with the disadvantages of implementing big interfaces and promotes splitting them into smaller ones.

In the example below, the `Worker` class violates the Interface Segregation Principle by having a single interface with both `work` and `eat` methods. The improved version uses two separate interfaces (`Workable` and `Eatable`), and the improved `Worker` class implements these interfaces separately. The client code (`performTasks` function) now depends only on the specific interfaces it needs, avoiding unnecessary dependencies on methods that are not used.

**Violation of ISP:**
```js
class Worker {
  work() {
    // Working...
  }

  eat() {
    // Eating...
  }
}
```

**Adhering to ISP:**
```js
class Workable {
  work() {
    // Working...
  }
}

class Eatable {
  eat() {
    // Eating...
  }
}

// Implementing interfaces separately
class Worker implements Workable, Eatable {
  work() {
    // Working...
  }

  eat() {
    // Eating...
  }
}
```

**Usage example:**
```js
function performTasks(worker) {
  if (worker instanceof Workable) {
    worker.work();
  }

  if (worker instanceof Eatable) {
    worker.eat();
  }
}

const worker = new Worker();
performTasks(worker);
```

## Dependency Inversion Principle (DIP)

Entities must depend on abstractions, not on implementations. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

In the provided example, a violation of the Dependency Inversion Principle is initially demonstrated by a high-level module, `NotificationService`, directly depending on low-level modules (`EmailNotificationSender` and `SMSNotificationSender`). The improvement section addresses this violation by introducing an abstraction, `NotificationSender`, acting as an interface. Concrete implementations (`EmailNotificationSender` and `SMSNotificationSender`) inherit from this interface, establishing a clear separation of concerns. The final usage example illustrates how instances of the concrete implementations are created and injected into the NotificationService, showcasing a design that adheres to the Dependency Inversion Principle. This adherence ensures that high-level and low-level modules depend on abstractions, fostering flexibility and maintainability in the codebase.

**Violation of DIP:**
```js
// Low-level module
class EmailNotificationSender {
  sendEmail(message) {
    console.log(`Sending email notification: ${message}`);
    // Actual email sending logic
  }
}

// Another low-level module
class SMSNotificationSender {
  sendSMS(message) {
    console.log(`Sending SMS notification: ${message}`);
    // Actual SMS sending logic
  }

}

// High-level module depending on low-level modules
class NotificationService {
  constructor() {
    this.emailSender = new EmailNotificationSender();
    this.smsSender = new SMSNotificationSender();
  }

  notifyUserViaEmail(message) {
    // High-level logic
    this.emailSender.sendEmail(message);
  }

  notifyUserViaSMS(message) {
    // High-level logic
    this.smsSender.sendSMS(message);
  }
}
```

**Adhering to DIP:**
```js
// Abstraction (Interface)
class NotificationSender {
  send(message) {
    // To be implemented by concrete implementations
  }
}

// Low-level module implementing the abstraction
class EmailNotificationSender extends NotificationSender {
  send(message) {
    console.log(`Sending email notification: ${message}`);
    // Actual email sending logic
  }
}

// Another low-level module implementing the abstraction
class SMSNotificationSender extends NotificationSender {
  send(message) {
    console.log(`Sending SMS notification: ${message}`);
    // Actual SMS sending logic
  }
}

// High-level module depending on the abstraction
class NotificationService {
  constructor(notificationSender) {
    this.notificationSender = notificationSender;
  }

  notifyUser(message) {
    // High-level logic
    this.notificationSender.send(message);
  }
}
```

**Usage example:**
```js
const emailSender = new EmailNotificationSender();
const smsSender = new SMSNotificationSender();

const emailNotificationService = new NotificationService(emailSender);
const smsNotificationService = new NotificationService(smsSender);

emailNotificationService.notifyUser("Hello, this is an email notification.");
smsNotificationService.notifyUser("Hello, this is an SMS notification.");
```