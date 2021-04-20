### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

* CARLOS ANDRES AMOROCHO AMOROCHO
* RUBIAN CAMILO SAENZ RODRIGUEZ

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

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%201.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%201%20-%20II.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%201%20-%20III.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%201%20-%20IV.PNG?raw=true">
</p>


2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM.

    `ssh scalability_lab@xxx.xxx.xxx.xxx`
    
<p align="center"> 
   <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%202.PNG?raw=true">
</p>


3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203%20-%20II.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203%20-%20III.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203%20-%20IV.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203%20-%20V.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%203%20-%20VI.PNG?raw=true">
</p>


4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`
    
<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%204.PNG?raw=true">
</p>


5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    `npm install forever -g`

    `forever start FibinacciApp.js`

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%206.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%206%20-%20II.PNG?raw=true">
</p>

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:

    * 1000000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207.PNG?raw=true">
</p>

    * 1010000
 
 <p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20II.PNG?raw=true">
</p>
    * 1020000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20III.PNG?raw=true">
</p>

    * 1030000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20IV.PNG?raw=true">
</p>

    * 1040000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20V.PNG?raw=true">
</p>

    * 1050000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20VI.PNG?raw=true">
</p>

    * 1060000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20VII.PNG?raw=true">
</p>

    * 1070000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20VIII.PNG?raw=true">
</p>

    * 1080000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20IX.PNG?raw=true">
</p>

    * 1090000    

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%207%20-%20X.PNG?raw=true">
</p>


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%208.PNG?raw=true">
</p>


9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```
    
<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%209.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%209%20-%20I.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%209%20-%20II.PNG?raw=true">
</p>

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2010.PNG?raw=true">
</p>

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.

    * 6

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011.PNG?raw=true">
</p>

Punto 7:

    * 1000000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20I.PNG?raw=true">
</p>

    * 1010000
 
 <p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20II.PNG?raw=true">
</p>
    * 1020000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20III.PNG?raw=true">
</p>

    * 1030000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20IV.PNG?raw=true">
</p>

    * 1040000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20V.PNG?raw=true">
</p>

    * 1050000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20VI.PNG?raw=true">
</p>

    * 1060000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20VII.PNG?raw=true">
</p>

    * 1070000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20VIII.PNG?raw=true">
</p>

    * 1080000

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20IX.PNG?raw=true">
</p>

    * 1090000    

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20X.PNG?raw=true">
</p>

Grafico Punto 8:

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20XI.PNG?raw=true">
</p>

newman Punto 9:

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%2011%20-%20XII.PNG?raw=true">
</p>

12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.


13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?

Se crean diferentes recursos al crear la maquina como:

* La llave de acceso a SSH.
* Recurso del Disco.
* Interfaz de Red.
* Grupo de Seguridad de Red.
* Ip Publica.
* Maquina Virtual.

2. ¿Brevemente describa para qué sirve cada recurso?

* La llave de acceso a SSH - Para conectarse mediante SSH por una maquina virtual externa.
* Recurso del Disco - Almacenamiento de datos.
* Interfaz de Red - Para la conexión de red entre un dispositivo y el sistema.
* Grupo de Seguridad de Red - Para controlar el trafico de Red.
* Ip Publica - Direccion IP publica con la que se tendra acceso a la maquina.
* Maquina Virtual - Funciona para la simulación y conexion de la maquina virtual en una externa.

3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

Porque se suspende la conexion con la maquina en azure.

4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

![image](https://user-images.githubusercontent.com/48153830/115461704-588fb200-a1ef-11eb-985f-daba8fa6849b.png)

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%208.PNG?raw=true">
</p>

6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:


    * Tiempos de ejecución de cada petición.
    * Si hubo fallos documentelos y explique.

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%201%20-%20Punto%209%20-%20II.PNG?raw=true">
</p>


7. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

* B2ms: Cuenta con 2 vCPUs, de memoria RAM 8 GB, 1 data disk y tiene un precio de $ 60.74 dólares por mes.
* B1ls: Cuenta con 1 vCPUs, de memoria RAM 0.5 GB, 1 data disk y tiene un precio de $ 3.80 dólares por mes.

8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

Simpemente ayuda a almacenar los datos mucho mas rapido, pero a la hora de ejecutar los tiempos siguen siendo muy similares.

9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

Que podría generar mas costos 

10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

Cambian un poco, pero no es un gran cambio.

11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201%20-%20I.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201%20-%20II.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201%20-%20III.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201%20-%20IV.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%201%20-%20V.PNG?raw=true">
</p>

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%202.PNG?raw=true">
</p>

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%203.PNG?raw=true">
</p>

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%204.PNG?raw=true">
</p>

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

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20I.PNG?raw=true">
</p>

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20II.PNG?raw=true">
</p>

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.


#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://20.72.142.228/
http://20.72.142.228/fibonacci/1000000
```

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20III.PNG?raw=true">
</p>

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

Se divide en dos tipos:

Publico: Realiza el proceso de traducir IPs privadas a IPs publicas.
Privadas: Para equilibrar el trafico dentro de la red virtual.

* ¿Cuál es el propósito del *Backend Pool*?

Define como evaluar los diferentes Back-Ends a través de sondas de estado, además de producir equilibrio de carga entre estos, en otras palabras, define el grupo de recursos que brindarán tráfico para una regla de equilibrio de carga determinada.

* ¿Cuál es el propósito del *Health Probe*?

Se deben realizar sondeos de estado para detectar el el endless point del Back-End, se determinan que instancias recibiran nuevos flujos, en otras palabras, determina si la instancia está en buen estado. Si la instancia falla su prueba de estado suficientes veces, dejará de recibir tráfico hasta que comience a pasar las pruebas de estado nuevamente.

* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.

El modo de distribución predeterminado para Azure Load Balancer es un hash de tupla de cinco elementos. La tupla contiene:

IP de origen.
Puerto de origen.
IP de destino.
Puerto de destino.
Tipo de protocolo.

* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?

Una Virtual Network o red virtual es una representación de la red propia en la nube, es un aislamiento lógico de la nube de Azure dedicada a su suscripción.

* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?

Es una oferta de alta disponibilidad que protege las aplicaciones y los datos de fallas que puedan ocurrir en el datacenter, las zonas de disponibilidad son ubicaciones físicas de una región determinada por Azure, cada zona cuenta con uno o varios centros de datos.

* ¿Cuál es el propósito del *Network Security Group*?

Se puede filtrar el trafico de red hacia los recursos de Azure y desde los recursos de Azure en una red virtual de Azure con un grupo de seguridd de red, además contienen reglas de seguridad que permiten o niegan el táfico de una red entrante.

* Informe de newman 1 (Punto 2)

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20III.PNG?raw=true">
</p>

* Presente el Diagrama de Despliegue de la solución.

* Horizontal:

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20HORIZONTAL.PNG?raw=true">
</p>

* VM1

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20VM1.PNG?raw=true">
</p>

* VM2

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20VM2.PNG?raw=true">
</p>

* VM3

<p align="center"> 
  <img src="https://github.com/camilosaenz/ARSW-LABORATORIO8/blob/master/images/img/Parte%202%20-%20Punto%205%20-%20VM3.PNG?raw=true">
</p>



