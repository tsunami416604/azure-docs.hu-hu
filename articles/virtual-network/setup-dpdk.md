---
title: DPDK egy Azure Linux virtuális gépben | Microsoft dokumentumok
description: Ismerje meg, hogyan kell beállítani a DPDK egy Linux virtuális gép.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c79c1fd687e329b97a854a3ff66a3cf95076b5d6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384228"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>A DPDK beállítása Linux os virtuális gépen

Data Plane Development Kit (DPDK) az Azure-ban kínál gyorsabb felhasználói tércsomag-feldolgozási keretrendszer teljesítményigényes alkalmazások hoz. Ez a keretrendszer megkerüli a virtuális gép kernelhálózati veremét.

A rendszermaghálózati vermet használó tipikus csomagfeldolgozásban a folyamat megszakításvezérelt. Amikor a hálózati illesztő bejövő csomagokat fogad, rendszermag-megszakítás sal feldolgozza a csomagot, és a környezet a kernelterületről a felhasználói területre vált. A DPDK kiküszöböli a környezetváltást és a megszakításvezérelt módszert a felhasználói tér implementációja érdekében, amely lekérdezési módú illesztőprogramokat használ a gyors csomagfeldolgozáshoz.

A DPDK felhasználói térkönyvtárak készleteiből áll, amelyek alacsonyabb szintű erőforrásokhoz biztosítanak hozzáférést. Ezek az erőforrások hardvert, logikai magokat, memóriakezelést és lekérdezési módú illesztőprogramokat tartalmazhatnak a hálózati csatolókártyákhoz.

A DPDK több operációsrendszer-disztribúciót támogató Azure virtuális gépeken is futtatható. A DPDK kulcsfontosságú teljesítménydifferenciálást biztosít a hálózati funkciók virtualizációs implementációinak ösztönzésében. Ezek a megvalósítások hálózati virtuális készülékek (NVA-k) formáját ölthetik, például virtuális útválasztók, tűzfalak, VPN-ek, terheléselosztók, fejlett csomagmagok és szolgáltatásmegtagadási (DDoS) alkalmazások formájában.

## <a name="benefit"></a>Előny

**Magasabb csomagok másodpercenként (PPS):** A rendszermag megkerülése és a csomagok vezérlésének átvétele a felhasználói térben csökkenti a ciklusok számát a környezeti kapcsolók kiküszöbölésével. Emellett javítja az Azure Linux virtuális gépekmásodpercenként feldolgozott csomagok arányát.


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az Azure Marketplace-ről a következő disztribúciók támogatottak:

| Linux operációs rendszer     | Kernel verziója               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Egyéni kernel támogatás**

Minden olyan Linux kernelverzió, amely nem szerepel a listában, lásd: [Javítások egy Azure-hangolt Linux kernel létrehozásához.](https://github.com/microsoft/azure-linux-kernel) További információkért vegye fel [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)a kapcsolatot. 

## <a name="region-support"></a>Régió támogatása

Minden Azure-régió támogatja a DPDK-t.

## <a name="prerequisites"></a>Előfeltételek

A gyorsított hálózatkezelést linuxos virtuális gépen engedélyezni kell. A virtuális gépnek legalább két hálózati adapterrel kell rendelkeznie, amelyek nek egy felügyeleti felülettel kell rendelkezniük. Ismerje meg, hogyan [hozhat létre linuxos virtuális gépet gyorsított hálózatkezeléssel.](create-vm-accelerated-networking-cli.md)

## <a name="install-dpdk-dependencies"></a>DPDK-függőségek telepítése

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CENTOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Alapértelmezett kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>A virtuálisgép-környezet beállítása (egyszer)

1. [Töltse le a legújabb DPDK](https://core.dpdk.org/download). Az Azure-hoz szükség van a 18.11 LTS vagy a 19.11 LTS verzióra.
2. Készítse el az `make config T=x86_64-native-linuxapp-gcc`alapértelmezett konfigurációt a segítségével.
3. A létrehozott konfigurációban engedélyezze a Mellanox PMD-ket. `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`
4. Fordítás `make`a segítségével.
5. Telepítés `make install DESTDIR=<output folder>`a programmal

## <a name="configure-the-runtime-environment"></a>A futásidejű környezet konfigurálása

Az újraindítás után futtassa a következő parancsokat egyszer:

1. Hatalmasoldalak

   * A hugepage konfigurálása a következő parancs futtatásával, egyszer minden numa csomóponthoz:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Hozzon létre egy `mkdir /mnt/huge`könyvtárat a csatlakoztatáshoz.
   * Mount hugepages `mount -t hugetlbfs nodev /mnt/huge`a .
   * Ellenőrizze, hogy a `grep Huge /proc/meminfo`hatalmas oldalak a segítségével vannak-e lefoglalva.

     > - Nem, nem, nem, nem, nem, Van egy módja annak, hogy módosítsa a grub fájlt úgy, hogy hugepages vannak fenntartva a csomagtartó [utasításait](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) követve a DPDK. Az utasítások az oldal alján találhatók. Ha egy Azure Linux virtuális gép használata, módosítsa a fájlokat **az /etc/config/grub.d** alatt, hogy hatalmasoldalakat foglaljon le az újraindítások között.

2. MAC & IP-címek: A hálózati adapterek MAC- és IP-címének megtekintéséhez használható. `ifconfig –a` A *VF* hálózati adapter és a *NETVSC* hálózati adapter mac-címe megegyezik, de csak a *NETVSC* hálózati adapter rendelkezik IP-címmel. *A* VF-csatolók a *NETVSC-összeköttetések* alárendelt összeköttetéseiként futnak.

3. PCI-címek

   * Itt `ethtool -i <vf interface name>` megtudhatja, hogy melyik PCI-címet használja a *VF-hez.*
   * Ha *eth0* gyorsított hálózatengedélyező, győződjön meg arról, hogy testpmd nem véletlenül átveszi a *VF* PCI eszköz *eth0*. Ha a DPDK alkalmazás véletlenül átveszi a felügyeleti hálózati interfészt, és az SSH-kapcsolat megszakad, a soros konzol segítségével állítsa le a DPDK alkalmazást. A soros konzol segítségével is leállíthatja vagy elindíthatja a virtuális gépet.

4. Töltsön be *ibuverbeket* minden újraindításkor a segítségével. `modprobe -a ib_uverbs` Csak az SLES 15 esetén `modprobe -a mlx4_ib`töltse be *mlx4_ib.*

## <a name="failsafe-pmd"></a>Hibabiztos PMD

A DPDK-alkalmazásoknak az Azure-ban elérhetővé tett hibabiztos PMD-n kell keresztülfutniuk. Ha az alkalmazás fut közvetlenül a *VF* PMD, nem fogadja el az **összes** csomagot, amely a virtuális gép, mivel egyes csomagok jelennek meg a szintetikus felületen keresztül. 

Ha egy DPDK-alkalmazást futtat a feladatbiztos PMD-n keresztül, garantálja, hogy az alkalmazás megkapja az összes hozzá szánt csomagot. Azt is gondoskodik arról, hogy az alkalmazás továbbra is Fut DPDK módban, akkor is, ha a VF visszavonásra kerül, amikor a gazdagép szervizelése folyamatban van. A hibabiztos PMD-ről a [Hibamentes szavazási mód illesztőprogram-könyvtára](https://doc.dpdk.org/guides/nics/fail_safe.html)című témakörben talál további információt.

## <a name="run-testpmd"></a>Tesztsorozat

A testpmd gyökér módban `sudo` történő futtatásához használja a *testpmd* parancs előtt.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Alapszintű: Józanság-ellenőrzés, hibabiztos adapterinicializálás

1. Egyetlen portos testpmd alkalmazás indításához futtassa a következő parancsokat:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. A következő parancsokkal indíthat el egy kétportos testpmd alkalmazást:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Ha kettőnél több hálózati adapterrel futtatja a `--vdev` testpmd-et, `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`az argumentum a következő mintát követi: .

3.  A kezdés után futtassa `show port info all` a portadatok ellenőrzéséhez. Egy vagy két dpdk-portnak net_failsafe (nem *net_mlx4)* kell látnia.
4.  A `start <port> /stop <port>` forgalom indítására használható.

Az előző parancsok interaktív módban indítják el a *tesztpmd* parancsot, amely a testpmd parancsok kipróbálásához ajánlott.

### <a name="basic-single-sendersingle-receiver"></a>Alap: Egy küldő/egycímzett

A következő parancsok rendszeresen kinyomtatják a csomagokat másodpercenkénti statisztikákból:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Az RX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Amikor az előző parancsokat egy virtuális gépen futtatja, `app/test-pmd/txonly.c` módosítsa *IP_SRC_ADDR* és *IP_DST_ADDR* a virtuális gépek tényleges IP-címének megfelelően fordítás előtt. Ellenkező esetben a csomagok eldobása a fogadó elérése előtt.

### <a name="advanced-single-sendersingle-forwarder"></a>Speciális: Egy-küldő/egytovábbító
A következő parancsok rendszeresen kinyomtatják a csomagokat másodpercenkénti statisztikákból:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Az FWD oldalán futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Amikor az előző parancsokat egy virtuális gépen futtatja, `app/test-pmd/txonly.c` módosítsa *IP_SRC_ADDR* és *IP_DST_ADDR* a virtuális gépek tényleges IP-címének megfelelően fordítás előtt. Ellenkező esetben a csomagok eldobása a továbbító elérése előtt eldobva lesz. Nem lesz képes, hogy egy harmadik gép fogadott továbbított forgalmat, mert a *testpmd* továbbító nem módosítja a réteg-3 címeket, kivéve, ha néhány kód módosításokat.

## <a name="references"></a>Referencia

* [EAL-beállítások](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd parancsok](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
