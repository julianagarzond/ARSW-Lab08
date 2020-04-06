### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/en-us/free/search/?&ef_id=Cj0KCQiA2ITuBRDkARIsAMK9Q7MuvuTqIfK15LWfaM7bLL_QsBbC5XhJJezUbcfx-qAnfPjH568chTMaAkAsEALw_wcB:G:s&OCID=AID2000068_SEM_alOkB9ZE&MarinID=alOkB9ZE_368060503322_%2Bazure_b_c__79187603991_kwd-23159435208&lnkd=Google_Azure_Brand&dclid=CjgKEAiA2ITuBRDchty8lqPlzS4SJAC3x4k1mAxU7XNhWdOSESfffUnMNjLWcAIuikQnj3C4U8xRG_D_BwE). Al hacerlo usted contará con $200 USD para gastar durante 1 mes.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM.

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).
4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    `npm install forever -g`

    `forever start FibinacciApp.js`

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:

### A1

   * 1000000
    ![image](https://user-images.githubusercontent.com/53972469/78586264-ce052c00-7800-11ea-9676-7e30a9ccc529.png)
   * 1010000
    ![image](https://user-images.githubusercontent.com/53972469/78581279-49fb7600-77f9-11ea-96da-bfcf6fbafd31.png)
   * 1020000
    ![image](https://user-images.githubusercontent.com/53972469/78574345-7f9b6180-77ef-11ea-9711-ddab880cf39f.png)
   * 1030000
    ![image](https://user-images.githubusercontent.com/53972469/78581654-c68e5480-77f9-11ea-89c6-e10f075ae18c.png)
   * 1040000
    ![image](https://user-images.githubusercontent.com/53972469/78582383-cf335a80-77fa-11ea-84ad-b1e2da544c74.png)
   * 1050000
    ![image](https://user-images.githubusercontent.com/53972469/78582539-0ace2480-77fb-11ea-9d58-94c721a370f6.png)
   * 1060000
    ![image](https://user-images.githubusercontent.com/53972469/78582692-4cf76600-77fb-11ea-903b-37a622ceadf6.png)
   * 1070000
    ![image](https://user-images.githubusercontent.com/53972469/78583014-d60e9d00-77fb-11ea-91c2-6e21badbf015.png)
   * 1080000
    ![image](https://user-images.githubusercontent.com/53972469/78586461-16244e80-7801-11ea-9b51-4665d1982b84.png)
   * 1090000
    ![image](https://user-images.githubusercontent.com/53972469/78586669-600d3480-7801-11ea-8980-50257f66a31e.png)
    
### A6

   * 1000000
    ![image](https://user-images.githubusercontent.com/53972469/78571661-0fd7a780-77ec-11ea-91b5-dd4ef7e80e5f.png)
   * 1010000
    ![image](https://user-images.githubusercontent.com/53972469/78574121-2d5a4080-77ef-11ea-937b-03d9c330a91a.png)
   * 1020000
    ![image](https://user-images.githubusercontent.com/53972469/78574345-7f9b6180-77ef-11ea-9711-ddab880cf39f.png)
   * 1030000
    ![image](https://user-images.githubusercontent.com/53972469/78576441-4ca69d00-77f2-11ea-9ed6-7067bdcdd16b.png)
   * 1040000
    ![image](https://user-images.githubusercontent.com/53972469/78575039-6e9f2000-77f0-11ea-8025-c3f35e7f92a8.png)
   * 1050000
    ![image](https://user-images.githubusercontent.com/53972469/78575277-c0e04100-77f0-11ea-8a98-bf59a04ed733.png)
   * 1060000
    ![image](https://user-images.githubusercontent.com/53972469/78575523-187eac80-77f1-11ea-8156-4cc46f643056.png)
   * 1070000
    ![image](https://user-images.githubusercontent.com/53972469/78576780-c6d72180-77f2-11ea-822c-ee486120dd65.png)
   * 1080000
    ![image](https://user-images.githubusercontent.com/53972469/78577254-6bf1fa00-77f3-11ea-9e43-8b85e31605a7.png)
   * 1090000
    ![image](https://user-images.githubusercontent.com/53972469/78577482-bf644800-77f3-11ea-931d-e538ec593c84.png)


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

- Cpu
![image](https://user-images.githubusercontent.com/43153078/78571480-d99a2800-77eb-11ea-8d24-f305717d77f2.png)


9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.
12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?
2. ¿Brevemente describa para qué sirve cada recurso?
3. 
- ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? 

Sí se utiliza el comando npm FibonacciApp.js y la maquina se suspende por inactividad la aplicación dejaría de correr, al igual que si existe un error en la maquina virtual. Por eso se utiliza forever start FibonacciApp.js 

- ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

El inbound port rule sirve para permitir la entrada al servicio que se está levantando. En este caso la aplicación corre por el puerto 3000, así que este se debe abrir.

4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

![image](https://user-images.githubusercontent.com/53972469/78600424-90140200-7818-11ea-8640-787cd373dddf.png)
Se puede deber a la capacidad de procesamiento de la máquina virtual.

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

El consumo de CPU se debe a que la función tiene una complejidad lineal, debido a el for que se utiliza para calcular el resultado, esto hace que entre mayor el número, mayor el tiempo que se necesita.

6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
    * Si hubo fallos documentelos y explique.
7. ¿Cuál es la diferencia entre los tamaños `A0` y `A6`?

- A0 : Tiene 1 vCPUs, 0.75GB de RAM, 1 data disk y cuesta 14.60 dólares mensuales.
- A6 : Tiene 4 vCPUs, 28GB de RAM, 8 data disk y cuesta 365.00 dólares mensuales. 

8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

Si bien aumentar la capacidad de procesamiento a la máquina ayuda a bajar el tiempo de ejecución de la aplicación, no es la mejor manera de agilizar el cálculo del número, ya que se podría optimizar el código para tener un menor tiempo de ejecución.

9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

En el caso de un servicio como éste, podría llevar a sobrecostos en el caso de que se escoja una maquina con exceso de capacidad, o un crecimiento ilimitado.

10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

Si bien se ve una leve mejora en el tiempo en los primeros, hay una variación atípica en los últimos datos haciendo que no se pueda concluir si el aumento del tamaño de la maquina permitió un mejor funcionamiento.

11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

**Preguntas**

* ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?
1. Los dos tipos de balanceadores de carga que utiliza Azure son:
   - Publico: Proporciona conexiones salientes para máquinas virtuales dentro de su misma red virtual, mediante la traducción de ip privadas a ip publicas.
   - Privadas: Se utilizan para equilibrar el trafico dentro de la red virtual.

2. SKU : Es un adquirible bajo un producto
![image](https://user-images.githubusercontent.com/43153078/78604807-f7818000-781f-11ea-929c-7fd05fd5bd99.png)


   
* ¿Cuál es el propósito del *Backend Pool*?

* ¿Cuál es el propósito del *Health Probe*?
* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?
* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
* ¿Cuál es el propósito del *Network Security Group*?
* Informe de newman 1 (Punto 2)
* Presente el Diagrama de Despliegue de la solución.




