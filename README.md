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

            <p align="center">
            <img src="https://drive.google.com/uc?export=view&id=1XWMmWzQPHzCafh0lGlWsafkiGkJu9mJZ" />
            </p>

            > Despues de la implementación:

            <p align="center">
            <img src="https://drive.google.com/uc?export=view&id=1AQkpQVzu-nb8c6x-UASQS2q_6NVemh_t" />
            </p>

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

            > Despues de la implementación, con el productor rapido pero el consumidor lento osea sin el cambio, se vuelve el mal uso de cpu porque el consumidor e el que tiene el poder de reducir aunque consumidor si se puede optimizar pero no varia mucho

            <p align="center">
            <img src="https://drive.google.com/uc?export=view&id=1un5Qe66ixn31B56L-PTen9GIcZg-sLKA" />
            </p>

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
            > Consumidor y Productor Mejorados

            <p align="center">
            <img src="https://drive.google.com/uc?export=view&id=1vAE4RilImYmcUCGyMQj5J_s37glEnI87" />
            </p>
        ----

    + ## **Part 2:**
        + **1-2. Review the code and identify how the functionality indicated above was implemented**
            + **You have N immortal players.**

                > En la clase controlador **ControlFrame** especificamente aca:
                
                ```java
                JLabel lblNumOfImmortals = new JLabel("num. of immortals:");
                toolBar.add(lblNumOfImmortals); //adiciona los n inmortales que se necesiten
                ```

            + **Each player knows the remaining N-1 player.**

                > En la clase  **Inmortal** especificamente aca:
                
                ```java
                int myIndex = immortalsPopulation.indexOf(this); //saca el indice donde esta ese inmortal en especifico

                int nextFighterIndex = r.nextInt(immortalsPopulation.size()); // N-1 player

                //avoid self-fight
                if (nextFighterIndex == myIndex) {
                        nextFighterIndex = ((nextFighterIndex + 1) % immortalsPopulation.size());
                }

                im = immortalsPopulation.get(nextFighterIndex);

                this.fight(im);
                ```

            + **Each player permanently attacks some other immortal. The one who first attacks subtracts M life points from his opponent, and increases his own life points by the same amount.**

                > En la clase  **Inmortal** especificamente aca:
                
                ```java
                im = immortalsPopulation.get(nextFighterIndex);

                this.fight(im); //manda al inmortal a la funcion fight()

                public void fight(Immortal i2) {

                        if (i2.getHealth() > 0) {
                            // The one who first attacks subtracts M life points from h
                            i2.changeHealth(i2.getHealth() - defaultDamageValue);
                            // increases his own life points by the same amount
                            this.health += defaultDamageValue;
                            updateCallback.processReport("Fight: " + this + " vs " + i2+"\n");
                        } else {
                            updateCallback.processReport(this + " says:" + i2 + " is already dead!\n");
                        }
                }                
                ```

            + **The game could never have a single winner. Most likely, in the end there are only two left, fighting indefinitely by removing and adding life points.**

            <p align="center">
            <img src="https://drive.google.com/uc?export=view&id=1-ubOYF8a3ErWLS90eo-aQ-FkykvZaD2i" />
            </p>

            > Despues de varios minutos el programa nunca termina, osea los 3 inmortales nunca podran terminar y nunca habra un ganador.

        + **2. Given the intention of the game, an invariant should be that the sum of the life points of all players is always the same (of course, in an instant of time in which a time increase / reduction operation is not in process ). For this case, for N players, what should this value be?**

            > Deberia ser **(N x health)** de cada inmortal.

        + **3. Run the application and verify how the ‘pause and check’ option works. Is the invariant fulfilled?**

            > Como se ve en la imagen se verifico **"pause and check"** El invariante no se cumple. aunque la suma de la salud total si sea la de los N inmortales aun en juego, 
            ese valor va subiendo o bajando (osea variando) con el paso del tiempo. Como se muestra en las siguientes dos imagenes donde la suma total varia.

        <p align="center">
        <img src="https://drive.google.com/uc?export=view&id=1lKFdxKBxyjlMUBeP2lOEM1MFSR58Crxx" />
        <img src="https://drive.google.com/uc?export=view&id=1zQN3UgKF6FBxso-LmiBF67bN9n5pOJrQ" />
        </p>

        + **4. A first hypothesis that the race condition for this function (pause and check) is presented is that the program consults the list whose values ​​it will print, while other threads modify their values. To correct this, do whatever is necessary so that, before printing the current results, all other threads are paused.**

            + **Additionally, implement the ‘resume’ option.**