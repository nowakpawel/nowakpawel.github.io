---
layout: post
date: 2025-09-25
categories: [Java, Basics]
tags: [Java Basics]
title: Wielowątkowość

---
Wielozadaniowość pozawala wielu aktywnościom na równoległe wykonywanie swoich zadań. 
Wielozadaniowość możemy podzielić na dwie kategorie:
- Process-based multitasking - Pozwala na równoległe włączanie różnych procesów (odtwarzanie muzyki z Winampa, w czasie kiedy edytujemy tekst w MS Word).
- Thread-based multitasking -  Pozwala modułom tego samego programu na równoległe wykonywanie różnych czynności (Drukowanie oraz edycja dokumentu w tym samym czasie).

# Wątek vs Proces
- Dwa wątki współdzielą ten am adres w pamięci.
- Zmiana kontekstu pomiędzy wątkami jest zazwyczaj "tańsza" niż pomiędzy procesami.
- "Koszt" komunikacji pomiędzy wątkami jest również relatywnie mniejszy. 


# Dlaczego warto używać wielowątkowiści?
- W środowisku składającym się tylko z jednego wątku, tylko jedno zadaniee w tym ssanym czasie może być wykonywane.
- Cykle pracy procesora są marnowane, w momencie, kiedy na przykład czekamu na wprowadzenie danych.
- Wielozadaniowość pozwala na efektywne wykorzystanie czasu bezczynnego czasu procesora.

# Wątek
Zatem czym jest wątek? 
- Wątek to niezależna część jednego procesu (programu), która może zostać wykonana w separacji od innej częśći - tak jak w przykładzie w MS Word.
- Wiele wątków w programie może być wykonywanych niezależnie od siebie.
- W czasie wykonywania, wątki programu istnieją we wspólnej przestrzeni adresowej pamięci komputera. Dzięki temu mogę współdzielić dane i instrukcje (co sprawia również problemy, ale o tym za chwilę).
- Wątki są dużo "lżejsze" w porównaniu do procesów.
- Współdzielą one także ten sam proces, w którym zostały uruchomione.

# Główny wątek programu
Kiedy aplikacja jest uruchomiona z metody `main()` - uruchamia się głowny wątek programu. Jeśli w czasie wykonywania, żadnen inny wątek nie zostanie stworzony, po zakończemiu wykomywania instrukcji, metoda `main()` zakończy swoje działanie. Tym samym głowny wątek programu zostanie zakończony.
Wszystkie stworzone w czasie wykomywania programu wątki zostają uruchomione z `main()` pośrenio lub bezpośrednio. To oznacza, że metoda `main()` może zakończyć swoje działanie, ale aplikacja (program) będzie działała dalej do momentu, aż wszystkie wątko nie zakończą swojego działania.Dodatkowo środowisko uruchomieniowe Javy rozróżnia pomiędzy wątki jako dzieci, oraz Daemony (utworzone poprzez `setDaemon(boolean)` przed wystartowaniem wątku).
Daemon jest zależny od głównego wątku. Oznacza to, że jego działanie zostanie przerwane w momencie kiedy wszystkie wątki potomne się zakończą.

# Przykład 
Dużo już powiedziałem o wątkach. Przejdźmy zatem do konkretnego przykładu.
Po pierwsze jak stworzyć nowy wątek w Javie?

## Tworzenie nowego wąatku
### Rozszerzamy klasę `Thread`

```java
public class MyExtendedThread extends Thread {
    @Override
    public run() {
        for (int i = 1; i <= 10; i++) {
            System.out.printf("MyExtendedThread: %d\n", i);
            try {
                Thread.sleep(1000); //Symulujemy na przykład odpowiedź z serwera
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```
Aby odpalić wątek, tworzymy obiekt naszej nowo utworzonej klasyy i wywołujemy metodę `start()`. Dlaczego `start()`, a nie nadpisaną wcześniej `run()`? Otóż sama odpalenie metody `run()` zadziała, ale nie wykona instrukcji w nowym wątku.

```java
public static void main(String[] args) {
    MyThread thread = new MyThread();
    thread.start();
}
```

## Implementujemy interfejs funkcjomalny `Runnable`
Innym sposobem na stworzenie nowego wątku jest zaimplementowanie metody `run()` interface'u `Runnable`. Ponieważ jest to interface funkcjonalny, możemy go zaimplenentować poprzez Lambdę.
```java
Runnable lambdaThread = () -> {
            for (int i = 1; i <= 10; i++) {
                System.out.printf("runnableThread: %d\n", i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        };
```
Tworzymy nowy obiekt o typie `Runnable`. Teraz by rozpocząć pracę takiego wątku, musimy stworzyć nowy wątek, a w konstruktorze przekazać przed chwilą stworzony obiekt. Nasza metoda `main`(), wraz z poprzednim przykładem może zatem wyglądać tak:
```java
    public static void main(String[] args) {
    MyThread thread = new MyThread();

    Runnable lambdaThread = () -> {
            for (int i = 1; i <= 10; i++) {
                System.out.printf("runnableThread: %d\n", i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        };
    
    Thread lambda = new Thread(lambdaThread, "Lambda Thread");
    thread.start();
    lambda.start();
}
```
Ten drugi parametr to opcjonalna nazwa wątku.

Mamy zatem dwa sposoby na stworzenie wątku. Który wybrać?
W przeważającej większości przypadków najlepiej jest wybrać implementację interface'u. Powód jest bardzo prosty. W Javie możemy dziedziczyć tylko po jednej klasie (W odróżnieniu od, np. C++). Zatem, jeśli sworzylibyśmy wątek bezpośrednio od klasy `Thread`, stracilibyśmy możliwość dziedziczenia dla tego konkretnego wątku. Tego sposobu używa się tylko w szczególnych przypadkach.

Wiemy już o wątkach naprawdę sporo. Spróbujmy więc zasymulować prawdziwy program. 
Powiedzmy, że nasz klient wymaga, aby program w osobnych wątkach zliczał jakieś zasoby.
Mamy dwie klasy

```java
public class CounterClass {
    private int counter = 0;

    public void increase() {
        this.counter++;
        System.out.printf("%d\n", counter);
    }
}

```
oraz

```java
public class CounterThread {
    public static void main(String[] args) {
        CounterClass counter = new CounterClass();
        Runnable thread1 = () -> {
            for (int i = 1; i <= 10; i++) {
                counter.increase();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        };

        Runnable thread2 = () -> {
            for (int i = 1; i <= 10; i++) {
                counter.increase();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        };

        Thread newThread = new Thread(thread1, "First Counter");
        Thread anotherThread = new Thread(thread2, "Second Counter");

        newThread.start();
        anotherThread.start();
    }
}
```

Po odpaleniu aplikacji spodziewamy się, że wartośc pola counter wynosić będzie 20. - Jeden wtek zwiększy wartość pola counter do 10, a drugi zacznie liczyć od 10 i zwiększy do 20. Prosta sprawa...Otóż nie.
Kiedy odpalimy program kilka razy zobaczymy, że wartość `counter` nie zawsze dochodzi do wartości do 20. (W jednym z przypadków, kiedy testowałem to rozwiązanie cunter zatrzymał się na 16\*).

Dzieje się tak dlatego, że oba wątki współdzielą ten sam zasób. O co chodzi? O to, że w pewnym momencie, kiedy jeden wątek pobiera wartość pola i ją zwiększa. Drugi w tym czasie robi to samo. W efekcie podczas swojego działania oba wątki pobierają np. wartość counter = 15 i oba ją zwiększają, Takie operacje mogą wydarzyć się kilka razy w ciągu działania programu. Na szczęście twórcy Javy nie pozostawili programistów bezbronnych w takiej sytuacji. 
Rozwiązaniem jest słówko kluczowe `synchronized`. Zatem klasa `CounterClass` wyglądać będzie tak:
```java
public class CounterClass {
    private int counter = 0;

    public synchronized void increase() {
        this.counter++;
        System.out.printf("%d\n", counter);
    }
}
```
Zastosowanie `synchronized` w odniesieniu do metody powoduje dwie rzeczy:
1. Tylko jeden wątek ma możliwość wykonania bloku kodu oznaczonego `synchronized`
2. Zmiany wprowadzone do bloku kodu oznaczonego `synchronized`, przez jeden wątek, są od razu widoczne dla innych wątków. 

Problem można rozwiązać za pomocą `volatile`, jednak w odróżnieniu od `synchronized`, gwarantuje to tylko , że wartość zapisana z/do pamięci będzie od razu widoczna dla innych wątków, bez uzwględnienia atomowości (ang. atomicity).




`volatile` - może być użyte tylko w odniesieniu do pola klasy. 


\* <sup>Co ciekawe, kiedy skompilowałem program używając Javy 21, problem praktycznie nie występuję. Dzieje się tak za sprawą optymalizacji Javy oraz zapewnie wątków virtualnych, które zostały wprowadzone w tej wersji języka. (Choć tego dokładnie nie testowałem)</sup>