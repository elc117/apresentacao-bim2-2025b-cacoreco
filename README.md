# Apresentação 18/10 - Metódos join() e sleep() 
## Método join()
O método `join()` permite que uma thread "pause" sua execução até que outra thread finalize sua execução (a que está utilizando `join()`). Isso significa que `join()` faz com que a thread que o invoca (a thread "atual") espere até que a thread a thread "alvo" termine sua execução.
- É um método importante para controlar o fluxo de execução das threads. Por vezes, é necessário esperar o resultado da execução de um thread para executar outra corretamente.
- É importante notar também que as threads não são executadas em padrões previsíveis, por isso o método `join()` é tão importante.
- Também é possível adicionar o parâmetro `join(long millis)` para definir um tempo limite, em milisegundos, para a thread terminar sua execução. Se o tempo terminar a thread não tiver terminado sua execução, a thread "waiting" vai retomar a execução.
  
### Exemplo:
```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(1000);  // "Pausa" a execução por um segundo
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        }
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread thread1 = new MyThread();
        MyThread thread2 = new MyThread();
        
        thread1.start();  // Start thread1
        try {
            thread1.join();  // Main thread espera a thread 1 terminar
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted");
        }

        thread2.start();  // thread2 começa somente quando a thread1 termina
    }
}
```

## Método sleep()
O método `sleep()` é um método conceitualmente simples e fácil de entender. Ele pode ser usado para pausar (colocar em `WAITING`) a execução da thread atual por um tempo dado por milisegundos. O método, junto com seu parâmetro de tempo, fica `sleep(long millis)`.
- Esse é outro método que auxilia no controle de execução das threads.

### Exemplo

```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(1000);  // "Pausa" a execução por um segundo
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        }
    }
}
```
- Trecho do exemplo anterior que já demonstra o funcionamento de `sleep(1000)`. Outros exemplos simples seguem um padrão similiar.

## Código Exemplo

```java
class WorkerThread extends Thread {
    private final String nome;

    public WorkerThread(String nome) {
        this.nome = nome;
    }

    @Override
    public void run() {
        System.out.println("-> [" + nome + "] Começou a trabalhar...");
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
        }
        System.out.println("-> [" + nome + "] Terminou o trabalho.");
    }
}

public class ExemploJoin {
    public static void main(String[] args) {
        System.out.println("[Main] Iniciando a Thread Trabalhadora.");
        WorkerThread t1 = new WorkerThread("Worker-1");
        WorkerThread t2 = new WorkerThread("Worker-2");
        t1.start(); // A thread t1 começa a executar em paralelo

        System.out.println("[Main] Chamando t1.join(). A Main vai esperar t1 terminar.");

        try {
            t1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("[Main] t1 terminou. t2 pode começar a trabalhar.");

        t2.start();

        try {
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("[Main] t2 terminou. Main pode continuar e finalizar.");
    }
}
```
- O código utiliza o método `sleep()` para simular a execucação de um trabalho e o método `join()` para controlar o fluxo das threads `t1`, `t2` e a própria `main`

O output é:
```
[Main] Iniciando a Thread Trabalhadora.
[Main] Chamando t1.join(). A Main vai esperar t1 terminar.
-> [Worker-1] Começou a trabalhar...
-> [Worker-1] Terminou o trabalho.
[Main] t1 terminou. t2 pode começar a trabalhar.
-> [Worker-2] Começou a trabalhar...
-> [Worker-2] Terminou o trabalho.
[Main] t2 terminou. Main pode continuar e finalizar.
```
Se, por exemplo, removermos o `t1.join()`, a ordem de execução se bagunça com as outras threads, fazendo com que as mensagens também não façam sentido:
```
[Main] Iniciando a Thread Trabalhadora.
[Main] Chamando t1.join(). A Main vai esperar t1 terminar.
[Main] t1 terminou. t2 pode começar a trabalhar.
-> [Worker-1] Começou a trabalhar...
-> [Worker-2] Começou a trabalhar...
-> [Worker-1] Terminou o trabalho.
-> [Worker-2] Terminou o trabalho.
[Main] t2 terminou. Main pode continuar e finalizar.
```

### Vídeo de execução

https://github.com/user-attachments/assets/db6a036e-c53e-4a51-a6f7-2c2039d00dd3

## Conclusão
Para mim, os métodos `join()` e `sleep()` foram fáceis de entender e parecem extremamente úteis para qualquer aplicação que utilize threads e precise controlar a ordem de execução dessas threads. Esse conteúdo se assemelha muito ao funcionamento de processadores, que estamos aprendendo em Arquitetura de Computadores, coisa que facilita o entendimento. 
Os pontos que demoraram para eu entender foram mais relacionados ao método `join()`, já que o nome não é tão intuitivo (faz parecer que é apenas uma thread a mais entrando na execução), e também pelo fato de que, em um programa que utiliza threads, a classe `main` também se torna uma thread que em alguns casos precisa que classes alvo utilizem o método `join()` para garantir o fluxo correto do código.

## Referências:
https://medium.com/@AlexanderObregon/javas-thread-join-method-explained-6475b822e16c - Java’s Thread.join() Method Explained  
https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Thread.html#join() - Class Thread  
https://www.digitalocean.com/community/tutorials/thread-sleep-java - How To Use Thread.sleep() in Java with Examples  
https://youtu.be/r_MbozD32eo?si=W8HBzsPtKbKXe0V8 - Multithreading em Java explicado em 10 minutos 
