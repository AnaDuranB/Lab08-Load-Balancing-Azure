### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW
### Desarrollado por Laura Natalia Rojas y Ana Maria Duran
---

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

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

a. Creamos una llave SSH.

![image](https://github.com/user-attachments/assets/af258d27-d28d-4374-8fd5-4df19774dbd8)

b. Creamos la máquina virtual.

![image](https://github.com/user-attachments/assets/400baf30-ad4c-4fb4-a652-ec2d212177db)

![image](https://github.com/user-attachments/assets/b2e3e3a4-f021-4cfc-b8f6-8737cca05457)


c. Finalizamos la creación 

![image](https://github.com/user-attachments/assets/0cb46ddc-7ac7-4ac5-b259-2bff7697cb14)


2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

* Conexión:

   ![image](https://github.com/user-attachments/assets/5f284bac-2183-403a-8565-fffbf4aa43e2)


3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).

* Installing NVM (Node Version Manager) script

   ![image](https://github.com/user-attachments/assets/2ba771ce-5276-4790-a6e1-4c6003b6f47b)

* Installation of Curl Utility on Debian

   ![image](https://github.com/user-attachments/assets/4d03518d-5919-41de-92b4-c2e5b99a3d04)

   ![image](https://github.com/user-attachments/assets/30850626-ced2-42ff-bd8a-6590512b9ad1)

* Verificamos la instalación

   ![image](https://github.com/user-attachments/assets/fc04c29f-421a-4663-9849-a5aa94363263)


4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

   * Instalación de npm en la VM:
     ![image](https://github.com/user-attachments/assets/96fe6b0a-baba-4c37-add0-5370dcd3b62f)

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`

   * Instalamos forever para que el proceso se ejecute en segundo plano.
     
     ![image](https://github.com/user-attachments/assets/769f57fa-6c73-4216-b58b-995a6d56e37b)

     ![image](https://github.com/user-attachments/assets/a96a257b-0936-49a1-a88f-7407dd001fe8)



6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)


   * Probamos
     
     ![image](https://github.com/user-attachments/assets/9cbad44d-7b2f-485d-aa46-e73d8a7bd53f)

   * Corremos el ejecutable

     ![image](https://github.com/user-attachments/assets/a9166ddb-510e-4622-8424-4b393edb8921)


   * Verificamos el funcionamiento
     
     ![image](https://github.com/user-attachments/assets/3161f878-d76d-4dd7-89c3-c2b92deb1849)


7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
    * 1000000
  
      ![image](https://github.com/user-attachments/assets/6cccdc64-5e15-406c-bcb9-95a1bc616f4e)

    * 1010000

      ![image](https://github.com/user-attachments/assets/af18cc0d-5f6a-460e-938f-c0cdfd2a90c0)

    * 1020000

      ![image](https://github.com/user-attachments/assets/5261c49b-3fc2-4236-921d-6bd63aee68d9)

    * 1030000
  
      ![image](https://github.com/user-attachments/assets/7ab636fe-2446-4ca8-900f-18be6577c59d)

    * 1040000
  
      ![image](https://github.com/user-attachments/assets/b951b09d-6d8f-445d-8ff2-3ae9c296e388)

    * 1050000
  
      ![image](https://github.com/user-attachments/assets/cd093e51-a7bf-4b9d-a664-8919936bff26)

    * 1060000
  
      ![image](https://github.com/user-attachments/assets/78f08bb7-b405-4ac3-9861-9ca920abc118)

    * 1070000
  
      ![image](https://github.com/user-attachments/assets/2f7aa9f6-12f1-4fd3-bc1a-1db47cebe856)

    * 1080000
  
      ![image](https://github.com/user-attachments/assets/381997c2-b184-4fed-8ce3-cec783b64594)

    * 1090000
  
      ![image](https://github.com/user-attachments/assets/21d960bb-7057-4d56-a603-02d3c4a8ea7f)


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

* El consumo alcanza el 60%

   ![image](https://github.com/user-attachments/assets/d8cbc2f7-0cc9-49c5-a2ea-5ceaafad00f5)



9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).

      ![image](https://github.com/user-attachments/assets/fd98c333-44b1-411f-854e-08f9b3521c6b)

    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.
      ```
       newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
       newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
       ```
       
       *  Resultados:
         
          ![image](https://github.com/user-attachments/assets/5351f102-ed01-48fa-a052-b689d77075d9)

          ![image](https://github.com/user-attachments/assets/b3a0287d-d1ee-4d82-9e94-70041fe60b5b)



10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.

    * 1000000

      ![image](https://github.com/user-attachments/assets/d110ade8-795b-439c-8a9c-cb7e86ee9e77)

    * 1010000

      ![image](https://github.com/user-attachments/assets/bd013350-3293-4dcb-befc-b11305381eb1)


    * 1020000

      ![image](https://github.com/user-attachments/assets/6458c1ab-d2f1-4b59-a5f1-477a1df3c91f)


    * 1030000
  
      ![image](https://github.com/user-attachments/assets/675adbbc-e6de-402a-96ed-338bcf2cc2db)

    * 1040000
  
      ![image](https://github.com/user-attachments/assets/f615f4f2-96a9-4956-91e6-422be8d87124)


    * 1050000
  
      ![image](https://github.com/user-attachments/assets/7c9e9929-3af8-442f-8908-ba3b33125470)


    * 1060000
  
      ![image](https://github.com/user-attachments/assets/69fcedc0-aa75-4f1f-ab13-228c62e827a2)


    * 1070000
  
      ![image](https://github.com/user-attachments/assets/17aab080-c190-4f9d-a98a-62c4276bac32)


    * 1080000
  
      ![image](https://github.com/user-attachments/assets/353cabdf-db90-4cdd-9439-ce921fe31490)


    * 1090000
  
      ![image](https://github.com/user-attachments/assets/78b60f0c-aeb9-4c43-ae3f-22c387177703)

   * El consumo alcanzó el 43%
     
     ![image](https://github.com/user-attachments/assets/d24f89ab-fc59-401a-a30e-e30258cd0e75)

   * Primeras 10 iteraciones

     ![image](https://github.com/user-attachments/assets/abbe4799-7ecc-4be0-8b69-c3be6950e1a9)

   * Segundas 10 iteraciones

     ![image](https://github.com/user-attachments/assets/3f47ef33-2bb7-4d45-a106-22b3f51661b5)




12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.

   Respuesta: Se logró reducir el consumo a un 43%.
   
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?
   * La Máquina Virtual, Dirección IP Pública, Grupo de Seguridad de Red, Red virtual, Interfaz de red, Disco y la Clave SSH.
2. ¿Brevemente describa para qué sirve cada recurso?
   - **Máquina Virtual (VM)**:
       - Es la instancia de procesamiento en la que se ejecutan las aplicaciones, sistemas operativos y servicios. Proporciona el entorno de cómputo que necesita tu aplicación.
   - **Dirección IP Pública**:
       - Permite que tu VM sea accesible desde Internet. Es útil si deseas acceder a la VM desde fuera de la red de Azure, por ejemplo, para conectarte a través de SSH o acceder a servicios web.
   - **Grupo de Seguridad de Red (NSG)**:
       - Controla el tráfico entrante y saliente a la VM mediante reglas de firewall. Puedes definir qué puertos son accesibles desde Internet o desde otras redes en Azure, protegiendo tu VM de accesos no deseados.
   - **Red Virtual (VNet)**:
       - Es una red privada en Azure que conecta tus recursos (como VMs, bases de datos, etc.) de forma segura. Permite la comunicación entre recursos dentro de la misma red sin exponerlos a Internet.
   - **Interfaz de Red (NIC)**:
       - Es el componente que conecta la VM a la red, permitiéndole comunicarse con otros recursos y con Internet si tiene una IP pública. Cada VM tiene al menos una NIC para gestionar sus conexiones de red.
   - **Disco**:
       - Es el almacenamiento de la VM. Incluye el disco del sistema operativo (OS) que contiene el sistema operativo de la VM, y puede haber discos adicionales para almacenar datos o configuraciones de la aplicación.
   - **Clave SSH**:
       - Es un par de claves públicas y privadas utilizadas para la autenticación al conectarse a la VM de manera segura a través de SSH (Secure Shell). Permite acceder a la máquina sin necesidad de usar contraseñas, lo cual es más seguro.
      
3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?
   * Cuando nos conectamos usando SSH y ejecutamos la aplicación, se ejecuta en el contexto de la sesión. Esto significa que al cerrar la conexión SSH, el proceso dentro de la aplicación también se detiene porque está vinculada a la sesión. Creamos un Inbound port rule para permitir el tráfico de red entre la VM desde el exterior. Así configuramos que el servicio pueda ser accesible.
     
4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

   ![image](https://github.com/user-attachments/assets/26fd41c7-721c-40af-999f-eac46c51ebe0)

   En la función getNthNumberInSequence de FibonacciService, el cálculo de los números de Fibonacci puede ser extremadamente lento. La complejidad de tiempo es exponencial, específicamente O(2^n). Esto significa que con cada aumento en el valor de n, el tiempo de cálculo se duplica aproximadamente, lo que causa un incremento muy alto en los tiempos de respuesta. En la tabla "Antes de Escalabilidad", se observa que los tiempos crecen de manera considerable, alcanzando valores de más de 10 segundos para endpoints más altos.

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

   ![image](https://github.com/user-attachments/assets/d8cbc2f7-0cc9-49c5-a2ea-5ceaafad00f5)
   ![image](https://github.com/user-attachments/assets/d24f89ab-fc59-401a-a30e-e30258cd0e75)

   La función getNthNumberInSequence utiliza una gran cantidad de CPU debido a que ejecuta varias operaciones de suma sobre números de gran tamaño, lo que resulta ser muy demandante en términos de cálculos. A medida que los números aumentan, las operaciones implican manipular muchos dígitos y realizar cálculos a nivel de bits, lo que requiere un número significativo de ciclos de CPU.
   
6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:


   Con escabilidad.
   
    * Tiempos de ejecución de cada petición.
  
      
      ![image](https://github.com/user-attachments/assets/b3a0287d-d1ee-4d82-9e94-70041fe60b5b)
      

      Las 10 iteraciones tuvieron un tiempo de respuesta promedio de 9.2 segundos, el mínimo fue de 8.9s, el máximo de 9.5s, con una desviación estándar de 179ms. Esto indica que el sistema tiene un comportamiento bastante consistente en cuanto a tiempos de respuesta, con una ligera variabilidad, lo que podría ser un signo de estabilidad en el rendimiento bajo las condiciones actuales de carga.
      
    * Si hubo fallos documentelos y explique.

      Encontramos 1 error de conexión, ECONNRESET en la iteración 4. Este error se presenta cuando la conexión de red ocurre de forma inesperada. Se cerró la conexión antes de que se pudiera completar la solicitud, lo que puede indicar que el consumo de la CPU este siendo muy alto.

      
7. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

   - **B2ms**: Es una VM de gama media con 2 núcleos de CPU, 8GB de RAM y discos más rápidos. Es adecuada para aplicaciones de tamaño mediano que requieren mayor capacidad de procesamiento y memoria.
   - **B1ls**: Es una VM de nivel básico con solo 1 núcleo de CPU y 1GB de RAM. Es adecuada para aplicaciones pequeñas o para pruebas de desarrollo donde los recursos no son una gran preocupación.
     **Diferencia clave**: No solo es la infraestructura (CPU, RAM), sino que también el rendimiento general, como la capacidad para manejar múltiples conexiones y realizar cálculos más complejos. B2ms es más adecuado para aplicaciones de producción, mientras que B1ls es más económico y adecuado para pruebas.
     
8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

   Aumentar el tamaño de la VM puede ser una solución temporal si el problema es que la aplicación necesita más recursos (CPU, RAM). Sin embargo, esto no garantiza una mejora significativa si el problema está en el algoritmo de la función (por ejemplo, una implementación ineficiente de Fibonacci). También, aumentar el tamaño de la VM podría aumentar el costo.
   
9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?
   Cuando cambias el tamaño de una VM en Azure, la infraestructura se ajusta para darle más o menos recursos, lo cual requiere apagar y reiniciar la VM, causando una breve interrupción en el servicio.

      * Interrupción temporal: La VM se apaga y se reinicia, pausando cualquier servicio que esté en ejecución.
      * Rendimiento variable: Una VM más grande mejora el rendimiento, pero reducir el tamaño podría ralentizar la aplicación.
      * Costos adicionales: Aumentar el tamaño de la VM cuesta más dinero.
      * Ineficiencia en cargas variables: Si el tamaño es muy grande para ciertas cargas, se malgastan recursos.
      * Límite de escalabilidad: Solo puedes escalar verticalmente hasta el tamaño máximo que permite Azure, lo cual es limitado.
      * Dependencia de una sola VM: Toda la carga depende de una VM. Si falla, el servicio entero se ve afectado.

10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?
   Los resultados demuestran una notable optimización en el rendimiento general del sistema. Al migrar de una instancia B1ls a una B2ms, se evidencia una significativa mejora tanto en la eficiencia del procesamiento como en la velocidad de respuesta.
Esta mejora se atribuye principalmente a las capacidades superiores que ofrece la instancia B2ms, específicamente:

   * Mayor potencia de procesamiento gracias a sus núcleos adicionales
   * Incremento sustancial en la memoria RAM disponible
   * Capacidad de almacenamiento expandida

11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

      El uso de la GPU aumentó significativamente. Fallaron 9 solicitudes.

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
* ¿Cuál es el propósito del *Backend Pool*?
* ¿Cuál es el propósito del *Health Probe*?
* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?
* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
* ¿Cuál es el propósito del *Network Security Group*?
* Informe de newman 1 (Punto 2)
* Presente el Diagrama de Despliegue de la solución.




