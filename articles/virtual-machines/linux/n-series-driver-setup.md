---
title: Az Azure N sorozatú GPU-illesztőprogram beállítása Linuxhoz
description: Az NVIDIA GPU-illesztőprogramok beállítása Linuxot futtató N sorozatú virtuális gépekhez az Azure-ban
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944558"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Telepítse az NVIDIA GPU-illesztőprogramokat Linuxot futtató N sorozatú virtuális gépekre

A Linuxot futtató Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-linux.md) megfelelő NVIDIA CUDA vagy GRID illesztőprogramokat telepít egy N sorozatú Virtuális gépre. Telepítse vagy kezelje a bővítményt az Azure Portalon vagy az olyan eszközökkel, mint például az Azure CLI vagy az Azure Resource Manager-sablonok használatával. Tekintse meg az [NVIDIA GPU-illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúciókés telepítési lépésekről.

Ha úgy dönt, hogy manuálisan telepíti a GPU-illesztőprogramokat, ez a cikk támogatott disztribúciókat, illesztőprogramokat, valamint telepítési és ellenőrzési lépéseket tartalmaz. A Windows virtuális gépekhez manuális illesztőprogram-beállítási információk is rendelkezésre [állnak.](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Az N sorozatú virtuális gépek specifikációit, a tárolási kapacitásokat és a lemezadatokat lásd: [GPU Linux virtuális gép méretei.](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-illesztőprogramok telepítése N sorozatú virtuális gépekre

Az alábbiakban lépéseket olvashat a CUDA-illesztőprogramok telepítéséhez az NVIDIA CUDA toolkitből az N sorozatú virtuális gépekre. 


A C és C++ fejlesztők opcionálisan telepíthetik a teljes toolkit-et a GPU-gyorsított alkalmazások létrehozásához. További információt a [CUDA telepítési útmutatójában talál.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

CuDA-illesztőprogramok telepítéséhez készítsen SSH-kapcsolatot az egyes virtuális gépekhez. Annak ellenőrzéséhez, hogy a rendszer rendelkezik-e CUDA-kompatibilis GPU-val, futtassa a következő parancsot:

```bash
lspci | grep -i NVIDIA
```
A következő példához hasonló kimenetet fog látni (egy NVIDIA Tesla K80 kártyával):

![lspci parancs kimenete](./media/n-series-driver-setup/lspci.png)

Ezután futtassa a terjesztési telepítési parancsokat.

### <a name="ubuntu"></a>Ubuntu 

1. Töltse le és telepítse a CUDA illesztőprogramokat az NVIDIA webhelyéről. Például az Ubuntu 16.04 LTS esetében:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   A telepítés több percet is igénybe vehet.

2. A cuda teljes eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo apt-get install cuda
   ```

3. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzését.

#### <a name="cuda-driver-updates"></a>CUDA illesztőprogram-frissítések

Javasoljuk, hogy a telepítés után rendszeresen frissítse a CUDA-illesztőprogramokat.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux

1. Frissítse a rendszermagot (ajánlott). Ha úgy döntünk, hogy nem frissítjük `kernel-devel` `dkms` a rendszermagot, győződjön meg arról, hogy a rendszermag verziói megfelelnek a rendszermagnak, és azok megfelelnek-e a rendszermagnak.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Csatlakozzon újra a virtuális géphez, és folytassa a telepítést a következő parancsokkal:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A telepítés több percet is igénybe vehet. 

4. A cuda teljes eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo yum install cuda
   ```

5. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzését.

### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

A GPU-eszköz állapotának lekérdezéséhez az SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, a következőhöz hasonló kimenet jelenik meg. Vegye figyelembe, hogy **a GPU-Util** 0%-ot mutat, kivéve, ha jelenleg fut egy GPU-munkaterhelés a virtuális gépen. Az illesztőprogram verziója és a GPU adatai eltérhetnek a megjelenítettektől.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RDMA hálózati kapcsolat engedélyezhető rdma-képes N-sorozatú virtuális gépek, például NC24r telepített ugyanabban a rendelkezésre állási csoportban, vagy egy virtuális gép méretezési csoport egyetlen elhelyezési csoportban. Az RDMA-hálózat támogatja az MPI-forgalmat az Intel MPI 5.x vagy újabb verzióval futó alkalmazások esetében. További követelmények a következők:

### <a name="distributions"></a>Disztribúciók

RdMA-kompatibilis N-sorozatú virtuális gépek üzembe helyezése az Azure Piactéren az RDMA-kapcsolatot támogató, N sorozatú virtuális gépeken lévő egyik lemezképről:
  
* **Ubuntu 16.04 LTS** - RdMA-illesztőprogramok konfigurálása a virtuális gépen, és regisztráljon az Intel-nél az Intel MPI letöltéséhez:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-alapú 7.4 HPC** - RDMA-illesztőprogramok és Az Intel MPI 5.1 telepítve vannak a virtuális gépre.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-illesztőprogramok telepítése NV vagy NVv3 sorozatú virtuális gépekre

Az NVIDIA GRID-illesztőprogramok NV vagy NVv3 sorozatú virtuális gépekre történő telepítéséhez készítsen SSH-kapcsolatot minden virtuális géphez, és kövesse a Linux-disztribúció lépéseit. 

### <a name="ubuntu"></a>Ubuntu 

1. Futtassa a következő parancsot: `lspci`. Ellenőrizze, hogy az NVIDIA M60 kártya vagy -kártyák PCI-eszközként láthatók-e.

2. Telepítse a frissítéseket.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Tiltsa le a Nouveau kernel illesztőprogramot, amely nem kompatibilis az NVIDIA illesztőprogrammal. (Csak NV vagy NVv2 virtuális gépeken használja az NVIDIA illesztőprogramot.) Ehhez hozzon létre egy `/etc/modprobe.d` `nouveau.conf` megnevezett fájlt a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Indítsa újra a virtuális gépet, és csatlakozzon újra. Kilépés az X kiszolgálóból:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Töltse le és telepítse a GRID illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Amikor a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen**lehetőséget.

7. A telepítés befejezése után másolja az /etc/nvidia/gridd.conf.template fájlt egy új fájlba gridd.conf helyre /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. A következő `/etc/nvidia/gridd.conf`szövega:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ha van `/etc/nvidia/gridd.conf` ilyen, távolítsa el a következőket:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzését.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux 

1. Frissítse a kernelt és a DKMS-t (ajánlott). Ha úgy döntünk, hogy nem frissítjük `kernel-devel` `dkms` a rendszermagot, győződjön meg arról, hogy a rendszermag verziói megfelelnek a rendszermagnak, és azok megfelelnek-e a rendszermagnak.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Tiltsa le a Nouveau kernel illesztőprogramot, amely nem kompatibilis az NVIDIA illesztőprogrammal. (Csak NV vagy NV2 virtuális gépeken használja az NVIDIA illesztőprogramot.) Ehhez hozzon létre egy `/etc/modprobe.d` `nouveau.conf` megnevezett fájlt a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Indítsa újra a virtuális gépet, csatlakozzon újra, és telepítse a legújabb [Linux-integrációs szolgáltatásokat a Hyper-V és az Azure számára.](https://www.microsoft.com/download/details.aspx?id=55106)
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Csatlakozzon újra a virtuális `lspci` géphez, és futtassa a parancsot. Ellenőrizze, hogy az NVIDIA M60 kártya vagy -kártyák PCI-eszközként láthatók-e.
 
5. Töltse le és telepítse a GRID illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Amikor a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen**lehetőséget.

7. A telepítés befejezése után másolja az /etc/nvidia/gridd.conf.template fájlt egy új fájlba gridd.conf helyre /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. A következő `/etc/nvidia/gridd.conf`szövega:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ha van `/etc/nvidia/gridd.conf` ilyen, távolítsa el a következőket:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzését.


### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése


A GPU-eszköz állapotának lekérdezéséhez az SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, a következőhöz hasonló kimenet jelenik meg. Vegye figyelembe, hogy **a GPU-Util** 0%-ot mutat, kivéve, ha jelenleg fut egy GPU-munkaterhelés a virtuális gépen. Az illesztőprogram verziója és a GPU adatai eltérhetnek a megjelenítettektől.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 kiszolgáló
Ha egy NV vagy NVv2 virtuális gép távoli kapcsolatához X11 kiszolgálóra van szüksége, az [x11vnc](http://www.karlrunge.com/x11vnc/) használata ajánlott, mert lehetővé teszi a grafika hardveres gyorsítását. Az M60-as eszköz Buszazonosítóját manuálisan kell hozzáadni az X11 konfigurációs fájlhoz (általában) `etc/X11/xorg.conf`. Adjon `"Device"` hozzá egy, az alábbihoz hasonló szakaszt:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ezenkívül frissítse `"Screen"` a szakaszt az eszköz használatához.
 
A decimális buszazonosító a

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A BusID módosíthatja, ha egy virtuális gép újralefoglalt vagy újraindul. Ezért érdemes lehet létrehozni egy parancsfájlt a BusID frissítéséhez az X11 konfigurációban, amikor egy virtuális gép újraindul. Hozzon létre például `busidupdate.sh` egy (vagy ön által választott) parancsfájlt, amelynek tartalma hasonló a következőhöz:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Ezután hozzon létre egy `/etc/rc.d/rc3.d` bejegyzést a frissítési parancsfájl, így a parancsfájl meghívása gyökérként a rendszerindításkor.

## <a name="troubleshooting"></a>Hibaelhárítás

* Az adatmegőrzési módot `nvidia-smi` úgy állíthatja be, hogy a parancs kimenete gyorsabb, ha kártyákat kell lekérdeznie. Az adatmegőrzési mód `nvidia-smi -pm 1`beállításához hajtsa végre a parancsot. Vegye figyelembe, hogy ha a virtuális gép újraindul, a mód beállítás eltűnik. A módbeállítás indításkor bármikor végrehajtható.
* Ha frissítette az NVIDIA CUDA illesztőprogramokat a legújabb verzióra, és úgy találja, hogy az RDMA connectivcity már nem működik, [telepítse újra az RDMA illesztőprogramokat](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) a kapcsolat újragondolásához. 

## <a name="next-steps"></a>További lépések

* Ha linuxos Virtuálisgép-lemezképet szeretne rögzíteni a telepített NVIDIA illesztőprogramokkal, olvassa el [a Linux virtuális gépek általánosítása és rögzítése](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
