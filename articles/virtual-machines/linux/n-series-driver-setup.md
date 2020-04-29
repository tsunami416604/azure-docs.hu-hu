---
title: Azure N-Series GPU illesztőprogram-telepítő Linux rendszerhez
description: NVIDIA GPU-illesztőprogramok beállítása az Azure-ban Linux rendszerű N sorozatú virtuális gépekhez
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944558"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>NVIDIA GPU-illesztőprogramok telepítése a Linuxon futó N sorozatú virtuális gépeken

Az Azure N sorozatú, Linux rendszerű virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-linux.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. Telepítse vagy kezelje a bővítményt a Azure Portal vagy az eszközök, például az Azure CLI vagy a Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúciók és telepítés lépéseihez.

Ha a GPU-illesztőprogramokat manuálisan telepíti, ez a cikk a támogatott disztribúciókat, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza. A manuális illesztőprogram-telepítési információk a Windows rendszerű [virtuális gépek](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)esetében is elérhetők.

Az N sorozatú virtuális gépekhez tartozó specifikációk, a tárolókapacitások és a lemezek részleteiért lásd: [GPU LINUXOS VM-méretek](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>A CUDA-illesztőprogramok telepítése N sorozatú virtuális gépeken

Az alábbi lépésekkel telepítheti a CUDA-illesztőprogramokat az NVIDIA CUDA Toolkit-ből az N sorozatú virtuális gépeken. 


A C és C++ fejlesztők igény szerint telepíthetik a teljes eszközkészletet a GPU-gyorsított alkalmazások létrehozásához. További információ: a [CUDA telepítési útmutatója](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

A CUDA-illesztőprogramok telepítéséhez létesítsen SSH-kapcsolatokat az egyes virtuális gépekhez. A következő parancs futtatásával ellenőrizheti, hogy a rendszer rendelkezik-e CUDA-kompatibilis GPU-val:

```bash
lspci | grep -i NVIDIA
```
A következő példához hasonló kimenet jelenik meg (NVIDIA Tesla K80 kártya):

![lspci parancs kimenete](./media/n-series-driver-setup/lspci.png)

Ezután futtassa az adott disztribúcióhoz tartozó telepítési parancsokat.

### <a name="ubuntu"></a>Ubuntu 

1. Töltse le és telepítse a CUDA-illesztőprogramokat az NVIDIA webhelyről. Például Ubuntu 16,04 LTS esetén:
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

2. A teljes CUDA-eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo apt-get install cuda
   ```

3. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.

#### <a name="cuda-driver-updates"></a>A CUDA-illesztőprogram frissítései

Javasoljuk, hogy a telepítés után rendszeresen frissítse a CUDA-illesztőprogramokat.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux

1. Frissítse a kernelt (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról `kernel-devel` , `dkms` hogy a és a rendszermagja megfelelő-e.

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
 
3. Kapcsolódjon újra a virtuális géphez, és folytassa a telepítést a következő parancsokkal:

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

4. A teljes CUDA-eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo yum install cuda
   ```

5. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.

### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Ha le szeretné kérdezni a GPU-eszköz állapotát, SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. Vegye figyelembe, hogy a **GPU-util** 0%-ot mutat, ha jelenleg nem fut GPU-s munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

A RDMA hálózati kapcsolat engedélyezhető a RDMA-kompatibilis N sorozatú virtuális gépeken, például az azonos rendelkezésre állási csoporton vagy a virtuálisgép-méretezési csoportok egyetlen elhelyezési csoportjában telepített NC24r. A RDMA-hálózat támogatja a Message Passing Interface (MPI) forgalmat az Intel MPI 5. x vagy újabb verzióját futtató alkalmazások esetében. További követelmények:

### <a name="distributions"></a>Disztribúciók

Telepítsen RDMA-kompatibilis N sorozatú virtuális gépeket az Azure Marketplace egyik rendszerképéből, amely támogatja az N sorozatú virtuális gépek RDMA-kapcsolatait:
  
* **Ubuntu 16,04 LTS** – RDMA-illesztőprogramok konfigurálása a virtuális gépen, és az Intel MPI letöltése az Intel használatával:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* A **CentOS-alapú 7,4 HPC** -RDMA illesztőprogramok és az Intel MPI 5,1 telepítve vannak a virtuális gépen.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-illesztőprogramok telepítése NV-vagy NVv3-sorozatú virtuális gépeken

Az NVIDIA GRID-illesztőprogramok NV-vagy NVv3-sorozatú virtuális gépekre való telepítéséhez létesítsen SSH-kapcsolatokat minden virtuális géphez, és kövesse a Linux-disztribúció lépéseit. 

### <a name="ubuntu"></a>Ubuntu 

1. Futtassa a következő parancsot: `lspci`. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártya PCI-eszközként jelenik meg.

2. Telepítse a frissítéseket.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Tiltsa le a Nouveau kernel illesztőprogramját, amely nem kompatibilis az NVIDIA-illesztőprogrammal. (Csak az NVIDIA-illesztőprogramot használja az NV-vagy NVv2-alapú virtuális gépeken.) Ehhez hozzon létre egy `/etc/modprobe.d` nevű `nouveau.conf` fájlt a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Indítsa újra a virtuális gépet, és kapcsolódjon újra. Kilépési X kiszolgáló:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Töltse le és telepítse a GRID-illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Ha a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen**lehetőséget.

7. A telepítés befejezése után másolja a/etc/NVIDIA/GRIDD.conf.Template egy új fájlba. conf fájlt a következő helyen:/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adja hozzá a következőket `/etc/nvidia/gridd.conf`a következőhöz:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. `/etc/nvidia/gridd.conf` Ha megtalálható, távolítsa el a következőket:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux 

1. Frissítse a kernelt és a DKMS (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról `kernel-devel` , `dkms` hogy a és a rendszermagja megfelelő-e.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Tiltsa le a Nouveau kernel illesztőprogramját, amely nem kompatibilis az NVIDIA-illesztőprogrammal. (Csak az NVIDIA-illesztőprogramot használja az NV-vagy NV2-alapú virtuális gépeken.) Ehhez hozzon létre egy `/etc/modprobe.d` nevű `nouveau.conf` fájlt a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Indítsa újra a virtuális gépet, kapcsolódjon újra, és telepítse a [Hyper-V és az Azure legújabb linuxos integrációs szolgáltatásait](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Kapcsolódjon újra a virtuális géphez, és `lspci` futtassa a parancsot. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártya PCI-eszközként jelenik meg.
 
5. Töltse le és telepítse a GRID-illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Ha a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen**lehetőséget.

7. A telepítés befejezése után másolja a/etc/NVIDIA/GRIDD.conf.Template egy új fájlba. conf fájlt a következő helyen:/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adja hozzá a következőket `/etc/nvidia/gridd.conf`a következőhöz:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. `/etc/nvidia/gridd.conf` Ha megtalálható, távolítsa el a következőket:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése


Ha le szeretné kérdezni a GPU-eszköz állapotát, SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. Vegye figyelembe, hogy a **GPU-util** 0%-ot mutat, ha jelenleg nem fut GPU-s munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-kiszolgáló
Ha egy NV-vagy NVv2-alapú virtuális géphez távoli kapcsolatokhoz X11-kiszolgálóra van szüksége, akkor a [x11vnc](http://www.karlrunge.com/x11vnc/) használata javasolt, mivel lehetővé teszi a grafikus processzorok hardveres gyorsítását. A M60 eszközt manuálisan kell hozzáadni az X11 konfigurációs fájljához (általában: `etc/X11/xorg.conf`). Vegyen `"Device"` fel egy, az alábbihoz hasonló szakaszt:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Emellett frissítse a `"Screen"` szakaszt az eszköz használatához.
 
A megtalált tizedes tört a futtatásával

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A teendő megváltozhat, ha egy virtuális gép újra le lesz foglalva vagy újraindul. Ezért előfordulhat, hogy létre kell hoznia egy parancsfájlt az X11 konfigurációjának frissítéséhez, amikor egy virtuális gép újraindul. Hozzon létre például egy nevű `busidupdate.sh` szkriptet (vagy egy másik nevet) a következőhöz hasonló tartalommal:

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

Ezután hozzon létre egy bejegyzést a frissítési parancsfájlhoz `/etc/rc.d/rc3.d` , hogy a parancsfájl gyökérként legyen meghívva a rendszerindításhoz.

## <a name="troubleshooting"></a>Hibaelhárítás

* Az adatmegőrzési módot `nvidia-smi` úgy állíthatja be, hogy a parancs kimenete gyorsabb legyen, ha kártyákat kell lekérdezni. Az adatmegőrzési mód beállításához futtassa `nvidia-smi -pm 1`a következőt:. Vegye figyelembe, hogy ha a virtuális gép újraindul, a Mode (mód) beállítás eltűnik. A mód beállítását bármikor végrehajthatja indításkor.
* Ha a legújabb verzióra frissítette az NVIDIA CUDA-illesztőprogramokat, és megkeresi a RDMA connectivcity, akkor [telepítse újra a RDMA-illesztőprogramokat](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) a kapcsolat reistablish. 

## <a name="next-steps"></a>További lépések

* Linux rendszerű virtuálisgép-lemezképnek a telepített NVIDIA-illesztőprogramokkal való rögzítéséről lásd: [Linux rendszerű virtuális gép általánosítása és rögzítése](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
