# 🧠 Proyecto: Entorno de Laboratorio con Rocky Linux, Kali Linux y Windows en QEMU/KVM

## 📌 Descripción General

Este proyecto tiene como objetivo crear un entorno de laboratorio virtualizado en **Ubuntu Linux**, utilizando **QEMU/KVM** y **Virt-Manager**, para instalar y conectar tres sistemas operativos:

- **Rocky Linux 9.6 (Blue Onyx)**  
- **Kali Linux (Debian 12)**  
- **Windows 10/11**  

El objetivo final fue permitir la **comunicación de red entre todas las máquinas virtuales** para realizar prácticas de administración, seguridad y redes.

---

## ⚙️ 1. Instalación de QEMU/KVM y Virt-Manager

Primero se instalaron las herramientas de virtualización:

```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager -y
```

Luego se verificó que KVM estuviera activo:

```bash
sudo systemctl status libvirtd
```

Y que el usuario tuviera permisos para usarlo:

```bash
sudo usermod -aG libvirt $(whoami)
```

> 🔁 Reiniciar sesión para aplicar los cambios.

---

## 💿 2. Instalación de Rocky Linux

1. En **Virt-Manager**, se creó una nueva máquina virtual.  
2. Se seleccionó el **ISO de Rocky Linux 9.6** como medio de instalación.  
3. Se configuraron los recursos (CPU, RAM y disco).  
4. Durante la instalación:
   - Se creó un usuario llamado `jonathan`
   - Se estableció una contraseña
   - Se instaló el sistema base

Tras finalizar, se reinició el sistema y se inició sesión con:

```bash
login: jonathan
password: ****
```

---

## 🐉 3. Instalación de Kali Linux

En este caso, se utilizó una **imagen QCOW2** descargada previamente (formato de disco de QEMU).  
Se agregó a Virt-Manager mediante:

```
Archivo → Nueva máquina virtual → Importar imagen de disco existente
```

Se seleccionó:
- Tipo de sistema operativo: **Debian / Kali Linux**
- Disco: el archivo `.qcow2`
- Configuración de red: **Red virtual (default): NAT**

> ⚠️ Si aparece el error `grub rescue>`, se debe montar correctamente el disco de arranque o usar una imagen con el sistema ya instalado.

---

## 🪟 4. Instalación de Windows

1. Se descargó la ISO oficial de **Windows 10/11** desde el sitio de Microsoft.  
2. En Virt-Manager:
   - Crear nueva máquina virtual.  
   - Seleccionar el archivo ISO de Windows.  
   - Asignar recursos (CPU, RAM, disco).  
   - Configurar la red con **Red virtual (default): NAT**.  
3. Durante la instalación, seguir los pasos del asistente de Windows hasta completar el proceso.

> 💡 Si Windows no detecta el disco, añadir el driver **VirtIO** montando el ISO de controladores (virtio-win.iso).

---

## 🌐 5. Configuración de red entre las máquinas

En Virt-Manager, se configuró cada VM para que compartiera la misma red:

- **Fuente de red:** Red virtual `default (NAT)`  
- **Modelo de dispositivo:** `virtio`  
- **Conexión:** activada ✅

Todas las máquinas obtuvieron direcciones IP dentro del mismo rango, por ejemplo:

| Máquina        | Dirección IP        |
|----------------|--------------------|
| Rocky Linux    | 192.168.100.150    |
| Kali Linux     | 192.168.100.181    |
| Windows        | 192.168.100.200    |

---

## 🔁 6. Verificación de conectividad

Desde **Rocky Linux**:

```bash
ping 192.168.100.181
```

Desde **Kali Linux**:

```bash
ping 192.168.100.150
```

Desde **Windows** (cmd):

```bash
ping 192.168.100.150
ping 192.168.100.181
```

Todos respondieron correctamente, confirmando la comunicación entre las máquinas.  
Ejemplo de salida:

```
64 bytes from 192.168.100.181: icmp_seq=1 ttl=64 time=1.2 ms
64 bytes from 192.168.100.150: icmp_seq=1 ttl=64 time=1.1 ms
```

✅ **Resultado:** las máquinas están correctamente interconectadas.

---

