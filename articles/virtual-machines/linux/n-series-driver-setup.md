---
title: "Linux Azure N-sorozat illesztőprogram beállítása |} Microsoft Docs"
description: "N sorozatú virtuális gépek Azure-ban futó Linux NVIDIA GPU illesztőprogramok beállítása"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/12/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de82062f605d060dc388022cdb8ee9d5c09b2b89
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>N-sorozat linuxos virtuális gépek NVIDIA GPU illesztőprogramok telepítéséhez

Linuxos Azure N sorozatú virtuális gépek GPU lehetőségeinek kihasználásához, támogatott NVIDIA video-illesztőprogramok telepítéséhez. Ez a cikk lépéseit illesztőprogram beállítása az N-sorozatú virtuális gép telepítése után. Telepítési információk illesztőprogram érhető el is [Windows virtuális gépek](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


N sorozatú virtuális gép specifikációk, tárolási kapacitás, és a lemez adatai: [GPU Linux Virtuálisgép-méretek](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-and-nd-vms"></a>A hálózati vezérlő által, NCv2 és ND virtuális gépek CUDA illesztőprogramok telepítése

Az alábbiakban a NVIDIA CUDA eszközkészlet a Linux NC gépeken NVIDIA illesztőprogramok telepítésének lépéseit. 

C és C++ fejlesztők is telepíthet a teljes eszközkészlet GPU-gyorsított alkalmazásokat hozhatnak létre. További információkért lásd: a [CUDA a telepítési útmutató](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> CUDA illesztőprogram letöltési hivatkozásokat itt megadott aktuális kiadvány időpontban. A legújabb CUDA illesztőprogramokat, látogasson el a [NVIDIA](https://developer.nvidia.com/cuda-zone) webhelyet.
>

CUDA eszközkészlet telepítése, ellenőrizze az SSH-kapcsolat az egyes virtuális. Ellenőrizze, hogy a rendszer egy CUDA-kompatibilis grafikus processzort tartalmaz, futtassa a következő parancsot:

```bash
lspci | grep -i NVIDIA
```
(Egy NVIDIA Tesla K80 kártya megjelenítő) az alábbi példához hasonló kimenetet fog látni:

![lspci parancs kimenete](./media/n-series-driver-setup/lspci.png)

Majd a terjesztéshez futtatási telepítési parancsokat.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Töltse le és telepítse a CUDA illesztőprogramokat.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  A telepítés több percet is igénybe vehet.

2. A teljes CUDA eszközkészlet telepíti, írja be:

  ```bash
  sudo apt-get install cuda
  ```

3. A virtuális gép újraindul, és ellenőrizheti a telepítést.

#### <a name="cuda-driver-updates"></a>Illesztőprogram-frissítést CUDA

Azt javasoljuk, hogy rendszeres időközönként frissíti CUDA illesztőprogramok a telepítés utáni.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>7.3 centOS-alapú vagy a Red Hat Enterprise Linux 7.3.

1. Telepítse a legújabb Hyper-v Linux integrációs szolgáltatások.

  > [!IMPORTANT]
  > Ha telepítette a CentOS-alapú HPC-lemezkép egy NC24r VM, folytassa a 3. lépés. Mivel az Azure RDMA-illesztőprogramjai és a Linux integrációs szolgáltatások a HPC-lemezképben előre telepített, LIS nem kell frissíteni, és a kernel frissítések alapértelmezés szerint le vannak tiltva.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-2.tar.gz
 
  tar xvzf lis-rpms-4.2.3-2.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Csatlakozzon újra a virtuális Gépet, és folytathatja a telepítést a következő parancsokat:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  A telepítés több percet is igénybe vehet. 

4. A teljes CUDA eszközkészlet telepíti, írja be:

  ```bash
  sudo yum install cuda
  ```

5. A virtuális gép újraindul, és ellenőrizheti a telepítést.


### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése


Lekérdezni a GPU eszközállapotba SSH a virtuális gép, és futtassa a [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram illesztőprogrammal telepítve. 

Ha az illesztőprogram telepítve van, látni fogja a következőhöz hasonló kimenetet. Vegye figyelembe, hogy **GPU-Util** 0 % mutatja, kivéve, ha meg van nyitva egy GPU munkaterhelés a virtuális Gépen. A illesztőprogramjának verziószámát és a GPU részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RDMA hálózati kapcsolatot az RDMA-kompatibilis N sorozatú virtuális gépeken futó engedélyezhető, mint például az azonos rendelkezésre állási készlet NC24r telepítve. Az RDMA hálózati Message Passing Interface (MPI) forgalmat támogatja az Intel MPI futó alkalmazások 5.x-es vagy újabb verziója. Kövesse a további követelmények:

### <a name="distributions"></a>Felosztások

RDMA-kompatibilisek-e N sorozatú virtuális gépeket, egy az Azure piactéren, amely támogatja az RDMA-kapcsolatot a következő lemezképek központi telepítése:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. RDMA-illesztőprogramok konfigurálja a virtuális Gépre, és regisztrálhatja az Intel Intel MPI letöltése:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-alapú HPC** -7.3 HPC CentOS-alapú. RDMA-illesztőprogramok és Intel MPI 5.1 telepítve vannak a virtuális Gépet. 

## <a name="install-grid-drivers-for-nv-vms"></a>Portok HV virtuális gépek rács illesztőprogramok telepítése

Portok HV virtuális gépeken NVIDIA rács illesztőprogramok telepítéséhez az SSH-kapcsolat ellenőrizze az egyes virtuális, és kövesse a lépéseket a Linux terjesztéshez. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Futtassa a `lspci` parancsot. Ellenőrizze, hogy a NVIDIA M60 kártya vagy kártyák PCI eszközként látható.

2. Telepítse a frissítéseket.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Tiltsa le a Nouveau kernel illesztőprogramot, amely nem kompatibilis a NVIDIA illesztőprogram. (Csak NVIDIA illesztőprogramot használja a portok HV virtuális gépeken.) Ehhez az szükséges, egy fájlt létrehozni a `/etc/modprobe.d `nevű `nouveau.conf` a következő tartalommal:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. A virtuális gép újraindul, és csatlakozzon újra. Kilépés X kiszolgáló:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Töltse le és telepítse a rács illesztőprogramját:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 

6. Ha megkérdezi, hogy kívánja-e frissíteni a X-konfigurációs fájlt, jelölje be a nvidia-xconfig segédprogram futtatásához **Igen**.

7. A telepítést követően /etc/nvidia/gridd.conf.template másolása egy új fájl gridd.conf a hely etc/nvidia /

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Adja hozzá a következőt a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. A virtuális gép újraindul, és ellenőrizheti a telepítést.


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>7.3 centOS-alapú vagy a Red Hat Enterprise Linux 7.3.

1. A kernel és DKMS frissítése.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Tiltsa le a Nouveau kernel illesztőprogramot, amely nem kompatibilis a NVIDIA illesztőprogram. (Csak NVIDIA illesztőprogramot használja a portok HV virtuális gépeken.) Ehhez az szükséges, egy fájlt létrehozni a `/etc/modprobe.d `nevű `nouveau.conf` a következő tartalommal:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Indítsa újra a virtuális gép, csatlakozzon újra, és telepítse a legújabb Linux integrációs szolgáltatásokat a Hyper-V:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-2.tar.gz

  tar xvzf lis-rpms-4.2.3-2.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. A virtuális gép, és futtassa újra a `lspci` parancsot. Ellenőrizze, hogy a NVIDIA M60 kártya vagy kártyák PCI eszközként látható.
 
5. Töltse le és telepítse a rács illesztőprogramját:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 
6. Ha megkérdezi, hogy kívánja-e frissíteni a X-konfigurációs fájlt, jelölje be a nvidia-xconfig segédprogram futtatásához **Igen**.

7. A telepítést követően /etc/nvidia/gridd.conf.template másolása egy új fájl gridd.conf a hely etc/nvidia /
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Adja hozzá a következőt a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. A virtuális gép újraindul, és ellenőrizheti a telepítést.

### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése


Lekérdezni a GPU eszközállapotba SSH a virtuális gép, és futtassa a [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram illesztőprogrammal telepítve. 

Ha az illesztőprogram telepítve van, látni fogja a következőhöz hasonló kimenetet. Vegye figyelembe, hogy **GPU-Util** 0 % mutatja, kivéve, ha meg van nyitva egy GPU munkaterhelés a virtuális Gépen. A illesztőprogramjának verziószámát és a GPU részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 kiszolgáló
Ha egy X11 van szüksége egy Permanens virtuális gépre, a távoli kapcsolatok kiszolgáló [x11vnc](http://www.karlrunge.com/x11vnc/) használata ajánlott, mivel lehetővé teszi a hardveres gyorsítás képek. A M60 eszköz BusID kell kézzel felvenni a xconfig fájl (`etc/X11/xorg.conf` Ubuntu 16.04 LTS, a `/etc/X11/XF86config` CentOS 7.3 vagy a Red Hat Enterprise Server 7.3). Adja hozzá a `"Device"` szakasz a következőhöz hasonló:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Emellett frissítse a `"Screen"` szakasz az eszköz használatához.
 
A BusID található futtatásával

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
A BusID módosíthatja, ha egy virtuális gép lekérdezi teljesítményigény vagy újraindítása után. Emiatt érdemes lehet egy parancsfájl segítségével frissítse a X11 a BusID konfigurációs, amikor egy virtuális gép újraindítása után. Példa:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Hozzon létre egy bejegyzést, az ezt a fájlt is elindítható a rendszerindító legfelső szintű `/etc/rc.d/rc3.d`.

## <a name="troubleshooting"></a>Hibaelhárítás

* Egy ismert probléma az Azure virtuális gépeken N-sorozat a 4.4.0-75 Linux kernel futó Ubuntu 16.04 LTS CUDA illesztőprogramok van. Ha egy korábbi kernel frissít, frissítsen legalább kernel verzió 4.4.0-77.

* Adatmegőrzési mód használatával állíthatja be `nvidia-smi` , a parancs kimenetében esetén gyorsabb lekérdezés kártyák kell. Adatmegőrzési üzemmód beállítása, hajtsa végre a `nvidia-smi -pm 1`. Vegye figyelembe, hogy a virtuális gép újraindul, ha a üzemmódját eltűnik. Lehet mindig parancsprogramot futtatni a üzemmódját indításkor végrehajtásához.


## <a name="next-steps"></a>További lépések

* A Linux virtuális gép lemezképének a telepített NVIDIA illesztőprogramokkal, lásd: [generalize és a Linux virtuális gép rögzítése](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
