# ARSW Lab 2 Immortal Case
Laboratorio 2 de ARSW Immortal Case
# Group:
+ **PEDRO JOSE MAYORGA NAVARRETE** - *Initial work* - [unknownmastercoder](https://github.com/unknownmastercoder)
+ **ANDRES DAVID VASQUEZ IBAÑEZ** - *Initial work* - [VASHIGO](https://github.com/vashigo)
----
                
+ # **Immortal Case**
    + ## Part 1:
        **Thread control with wait/notify. Producer/consumer**
        
        + **Check the operation of the program and run it. While this occurs, run jVisualVM and check the CPU consumption of the corresponding process.** 
             + **Why is this consumption?**

                > El consumo de la CPU se está generando porque el thread del Consumer en todo momento está verificando si la cola de productores no está vacia para poder **"consumir"** los elemtentos producidos. Es por esta razón que mientras que el thread Producer solo crea un nuevo elemento nuevo cada segundo y su consumo de CPU no es tan fuerte, el Consumer en todo momento está verificando si se han creado nuevos elementos que él pueda consumir, como se aprecia en el siguiente fragmento de código. 
                ```java
                while (true) {
                        if (queue.size() > 0) {}
                }
                ```

             + **Which is the responsible class?**

                > La clase responsable del mayor consumo de CPU es la clase **"Consumer"** por las razones explicadas anteriormente.
        

        + **Make the necessary adjustments so that the solution uses the CPU more efficiently, taking into account that - for now - production is slow and consumption is fast. Verify with JVisualVM that the CPU consumption is reduced.**

            > Antes de la implementación:

            //inserte imagen 1

            > Despues de la implementación:

            //inserte imagen 1
        ```java
        public void run() {
                while (true) {
                        if (queue.size() > 0) {
                                int elem = queue.poll();
                                System.out.println("Consumer consumes " + elem);
                                try {
                                Thread.sleep(1000);
                                } catch (InterruptedException ex) {
                                Logger.getLogger(StartProduction.class.getName()).log(Level.SEVERE, null, ex);
                                }
                        } else {
                                try {
                                queue.wait();
                                } catch (InterruptedException ex) {
                                Logger.getLogger(Consumer.class.getName()).log(Level.SEVERE, null, ex);
                                }
                        }
                        synchronized (queue) {
                                queue.notify();
                        }
                }
        }
        ```    

        + **Make the producer now produce very fast, and the consumer consumes slow. Taking into account that the producer knows a Stock limit (how many elements he should have, at most in the queue), make that limit be respected. Review the API of the collection used as a queue to see how to ensure that this limit is not exceeded. Verify that, by setting a small limit for the 'stock', there is no high CPU consumption or errors.**

            > Despues de la implementación:

            //inserte imagen 1
            ```java
            public void run() {
                while (true) {

                        if (queue.size() < stockLimit) {
                                dataSeed = dataSeed + rand.nextInt(100);
                                System.out.println("Producer added " + dataSeed);
                                queue.add(dataSeed);
                                try {
                                Thread.sleep(1000);
                                } catch (InterruptedException ex) {
                                Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                                }
                        } else {
                                try {
                                queue.wait();
                                } catch (InterruptedException ex) {
                                Logger.getLogger(Producer.class.getName()).log(Level.SEVERE, null, ex);
                                }
                        }
                        synchronized (queue) {
                                queue.notify();
                        }

                }
            }
            ```