# Redes 1 - Practica 4

Se le solicita configurar y administrar los dispositivos de una infraestructura de red para una compañía la cual está empezando a crecer y quieren ampliar su red. 

La compania cuenta con 2 departamentos: Contabilidad y Ventas. Se debe proveer comunicacion entre los usuarios del mismo departamento. Por ejemplo los usuarios del departamento de ventas no se podran comunicar con ningun otro departamente, solamente con host de su mismo departamento. 

## Topologia
<br>

<p align="center">
<img src="https://user-images.githubusercontent.com/30850990/96020656-98727880-0e0b-11eb-8ce7-5a131dc81038.JPG" height="450px"/>
</p>

<center>

| Host  |  VLAN  | Virtualizada |
|------:| ------:|-------------:|
| Linux |  10    |      SI      |
| PC2   |  20    |      NO      |
| PC3   |  10    |      NO      |
| PC4   |  20    |      NO      |

</center>

## Configuracion

1. Configurar los modos de acceso y/o troncal en ESW1, ESW2, ESW3 que correspondan para garantizar el tráfico de VLAN

    1.1 Configuracion en modo troncal para los puertos que se utilizan en el **ESW1**.

    ```
    conf t 
    int range f 1/0 - 1
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,1002-1005
    end

    conf t 
    int range f 1/4 - 6
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,1002-1005
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh int status
    ```

    ![image](https://user-images.githubusercontent.com/30850990/96022884-cb6a3b80-0e0e-11eb-9bd5-964587d52fa0.png)

    1.2 Configuracion en modo troncal para los puertos que se utilizan en el **ESW2**.

    ```
    conf t 
    int range f 1/0 - 5
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,1002-1005
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh int status
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96023593-e6897b00-0e0f-11eb-9dfd-358746e90173.png)

    1.3 Configuracion en modo troncal para los puertos que se utilizan en el **ESW3**.

    ```
    conf t 
    int range f 1/0 - 5
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,1002-1005
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh int status
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96023704-1042a200-0e10-11eb-82dd-0f568ac83848.png)

    1.4 Para los switches 1 y 2 el procedimiento es diferente ya que son mas simples. Se configura desde sus propiedades y poniendo las interfaces e modo *dot1q*
    <p>
        <img src="https://user-images.githubusercontent.com/30850990/96024554-1d13c580-0e11-11eb-9712-76a9eaeb611d.JPG" width="570">
    </p>

2. Configuración de VTP

    2.1 Configuracion VTP ESW1 modo Server

    ```
    conf t 
    vtp domain redes1_201612113
    vtp password redes1_201612113
    vtp mode server
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh vtp status
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96025449-61ec2c00-0e12-11eb-8031-cd6cc98a045d.png)

    2.2 Configuracion VTP ESW2 modo client
    ```
    conf t 
    vtp domain redes1_201612113
    vtp password redes1_201612113
    vtp mode client
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh vtp status
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96025628-9d86f600-0e12-11eb-8550-a310a26e92cb.png)

    2.3 Configuracion VTP ESW3 modo client
     ```
    conf t 
    vtp domain redes1_201612113
    vtp password redes1_201612113
    vtp mode client
    end

    # Verificar que la configuracion se haya hecho correctamente
    sh vtp status
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96025664-af689900-0e12-11eb-9d25-6020024404fa.png)

3. Crear VLAN 10, 20
    
    3.1 Las VLAN se crean en el servidor por lo tanto es en el ESW1

    ```
    conf t
    vlan 10
    name VENTAS
    end 

    vlan 20
    name CONTABILIDAD
    end

    # Verificar que las vlans hayan sido creadas
    sh vlan-sw
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96026172-66fdab00-0e13-11eb-9784-0c297c75d54d.png)

    3.2 Verificar que las vlans sean replicadas en ESW2 Y ESW3
    ```
    sh vlan-sw
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96026298-8dbbe180-0e13-11eb-9501-d463135e416f.png)

    ![image](https://user-images.githubusercontent.com/30850990/96026368-a2987500-0e13-11eb-9b3a-8a79a9bece79.png)

4. Configurar y crear los siguientes port-channel
    
    4.1 Configuracion Port-Channel Po1: ESW1-ESW2

    ```
    # Configuracion en el ESW1
    conf t 
    interface range  f 1/0 - 1
    channel-group 1 mode on
    end

    # Configuracion en el ESW2
    conf t
    interface range f 1/4 - 5
    channel-group 1 mode on

    # Verificar Port-Channel
    sh et summ
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96026878-56016980-0e14-11eb-8d17-cd0e7791fe99.png)

    4.2 Configuracion Port-Channel Po2: ESW1-ESW3

    ```
    # Configuracion en el ESW1
    conf t 
    interface range  f 1/4 - 5
    channel-group 2 mode on
    end

    # Configuracion en el ESW3
    conf t
    interface range f 1/4 - 5
    channel-group 2 mode on

    # Verificar Port-Channel
    sh et summ
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96027052-8e08ac80-0e14-11eb-8c5f-9254c96d398f.png)

    4.3 Configuracion Port-Channel Po3: ESW2-ESW3

     ```
    # Configuracion en el ESW2
    conf t 
    interface range  f 1/2 - 3
    channel-group 3 mode on
    end

    # Configuracion en el ESW3
    conf t
    interface range f 1/2 - 3
    channel-group 3 mode on

    # Verificar Port-Channel
    sh et summ
    ```
    ![image](https://user-images.githubusercontent.com/30850990/96027171-bd1f1e00-0e14-11eb-8ffc-4ba6e006544a.png)


5. Verificar que switch es el root bridge (STP) y que puertos están bloqueados por Spanning-tree.

6. Configurar las Subinterfaces del router con la dirección Gateway de los hosts de cada red proporcionada más adelante

    6.1 Activacion del puerto

    ```
        conf t
        int fa 0/0
        not shut
        exit
    ```

    6.2 Configuracion de las sub intefaces

    ```
    int fa 0/0.10
    encapsulation dot1q 10
    ip add 192.168.13.254 255.255.255.0
    exit

    int fa 0/0.20
    encapsulation dot1q
    ip add 192.168.23.254 255.255.255.0
    exit
    ```

    6.3 Creacion DHCP

    ```
    conf t
    int fa 0/0.10
    ip dhcp pool ventas
    network 192.168.13.0 255.255.255.0
    default-route 192.168.13.254
    exit

    int fa 0/0.20
    ip dhcp pool contabilidad
    network 192.168.23.0 255.255.255.0
    default-route 192.168.23.254
    exit
    ```


| VLAN | Direcciond de Red  | Primera Direccion Asignable | Ultima direccion Asignable  | Direccion de Broadcas  |
|-------------:|------:|------------:| -------------:| -------------:|
| 10           | 192.168.13.0/24 | 192.168.13.1 | 192.168.13.253 | 192.168.13.254
| 20           | 192.168.23.0/24 | 192.168.23.1 | 192.168.23.253 | 192.168.23.254

<br/>

## Captura de paquetes
![CapturePackages](https://user-images.githubusercontent.com/30850990/91699725-f71cb500-eb31-11ea-85cd-83523a4f416a.JPG)

# Herramientas utlizadas
* [GNS3](https://www.gns3.com/)
* [VMWare](https://www.vmware.com/latam/products/workstation-pro.html)
* [Tiny-Linux](http://tinycorelinux.net/)

# Autor
Raul Xiloj
