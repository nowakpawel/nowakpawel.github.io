---
layout: post
date: 2025-02-06
categories: [Java, Design Patterns]
tags: [Java Design Patterns]
title: Observer Design Pattern

---
# Introduction
The Observer design pattern is a behavioral software design pattern that defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. This pattern is particularly useful in scenarios where a clear separation of concerns and loose coupling between objects are desired.

# Understanding the Observer Pattern
At its core, the Observer pattern revolves around the concept of a subject and its observers. The subject maintains a list of its observers and provides methods for attaching and detaching observers. When the subject's state changes, it notifies all its observers, typically by calling a method defined in the observer interface.

## Key Components

- ***Subject***: The object that maintains a list of observers and notifies them of state changes.
- ***Observer***: An interface or abstract class that defines the update method, which is called when the subject's state changes.
- ***ConcreteSubject***: A specific implementation of the subject that maintains its state and notifies observers.
- ***ConcreteObserver***: A specific implementation of the observer that reacts to state changes in the subject.


### Implementation in Java:

```java
import java.util.ArrayList;
import java.util.List;

// Subject interface: Defines methods for registering, unregistering, and notifying observers.
interface Subject {
    void registerObserver(Observer observer);
    void unregisterObserver(Observer observer);
    void notifyObservers();
}

// Observer interface: Defines the update method that observers must implement.
interface Observer {
    void update(String state);
}

// ConcreteSubject: Manages state and notifies observers upon changes.
class ConcreteSubject implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String state;

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
        // Notify observers when the state changes.
        notifyObservers();
    }

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void unregisterObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        // Iterate through all registered observers and call their update method.
        for (Observer observer : observers) {
            observer.update(state);
        }
    }
}

// ConcreteObserver: Reacts to state changes in the subject.
class ConcreteObserver implements Observer {
    private String observerState;

    @Override
    public void update(String state) {
        observerState = state;
        System.out.println("Observer state updated: " + observerState);
    }
}

public class Main {
    public static void main(String[] args) {
        ConcreteSubject subject = new ConcreteSubject();
        ConcreteObserver observer1 = new ConcreteObserver();
        ConcreteObserver observer2 = new ConcreteObserver();

        subject.registerObserver(observer1);
        subject.registerObserver(observer2);

        subject.setState("New State");
    }
}
```

### Additional Practical Example

For a more comprehensive understanding, consider the example available on [my GitHub](https://github.com/nowakpawel/java-playground/tree/patterns) This repository showcases a scenario involving a weather station and its observers. This example demonstrates how the Observer pattern can be used to distribute real-time weather data to multiple display units. It features a WeatherData subject and DisplayElement observers. This example shows an application of the pattern in a real life situation.

Applications of the Observer Pattern

### The Observer pattern finds extensive use in:

- Event-driven systems
- Model-View-Controller (MVC) architectures
- Spreadsheet applications
- Distributed event systems
- GUI development, for event handling.

### Conclusion
The Observer design pattern is a fundamental tool for constructing loosely coupled and adaptable systems. By grasping its principles and implementation, developers can effectively create robust and maintainable software architectures.