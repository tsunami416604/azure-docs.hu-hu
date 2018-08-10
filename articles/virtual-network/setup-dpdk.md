---
title: Egy Azure Linux VM DPDK |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a Linux rendszerű virtuális gép DPDK.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: a03b72200f97c54bce188ec6a6ad8a06a43f26ae
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002579"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>A telepítő DPDK Linux rendszerű virtuális gépen

Adatok Adatsík Development Kit (DPDK) az Azure-ban egy gyorsabb felhasználói terület csomag feldolgozási keretrendszert biztosít a teljesítményt igénylő alkalmazások, amelyeket a virtuális gép kernel hálózati vermet.

Tipikus csomag feldolgozása a kernel hálózati verem használata megszakítás alapú. Minden alkalommal, amikor a hálózati adapter bejövő csomagok fogadása a kernel megszakítási feldolgozása a csomag- és környezeti Váltás a kernel területről felhasználói hely van. DPDK kiküszöböli a környezet közötti váltás és a megszakítási alapuló módszer egy felhasználói helyet megvalósítási gyors csomagfeldolgozáshoz a lekérdezési mód illesztőprogramok használata helyett.

Alacsonyabb szintű erőforrások, például a hardver, logikai magok, memória-kezelés, és lekérdezi a hálózati kártyák illesztőprogram való hozzáférés biztosítása a felhasználó terület kódtárak DPDK áll.

DPDK több operációs rendszer disztribúciók támogató Azure virtual machines szolgáltatásban futtathatja. DPDK biztosítja a környezetbarát hálózati funkciót egy fő differenciálás virtualizálási megvalósítását, hálózati virtuális berendezések (NVA) formájában egy virtuális útválasztó, tűzfal, VPN, terheléselosztót, fejlődött csomag core és -szolgáltatásmegtagadásos (például DDoS-) alkalmazások.

## <a name="benefit"></a>Előny

**Magasabb csomagok másodpercenként (PPS)**: megkerüli a kernel és a felhasználói térben csomagok véve irányítását csökkenti a ciklus száma elkerülve a környezet kapcsoló, és növeli a csomagok az Azure-beli Linuxos virtuális gépeken a másodpercenként feldolgozott.


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az Azure katalógusából a következő disztribúciók támogatottak:

| Linux operációs rendszer     | Kernel verziója        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Egyéni kernel támogatása**

Tekintse meg [javítások létrehozásához egy beállított Azure Linux-kernelt](https://github.com/microsoft/azure-linux-kernel) bármilyen Linux kernel verziója nem szerepel a listán, vagy további információkat, lépjen kapcsolatba [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Régió támogatása

Az összes Azure-régiók támogatják a DPDK.

## <a name="prerequisites"></a>Előfeltételek

Linux rendszerű virtuális gép gyorsított hálózatkezelés engedélyezni kell. A virtuális gépnek rendelkeznie kell legalább két hálózati adapterek, a felügyeleti felülethez. Ismerje meg, hogyan [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelés engedélyezett](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>DPDK függőségek telepítése

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev
```

### <a name="rhel75centos-75"></a>7.5 RHEL7.5/CentOS

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel
```

### <a name="sles-15"></a>SLES 15

**Az Azure kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Az alapértelmezett kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>(Ha) virtuális gépek környezetének beállítása

1. [Töltse le a legújabb DPDK](https://core.dpdk.org/download). 18.02 vagy újabb verziójú Azure szükség.
2. Először hozhat létre az alapértelmezett konfigurációt `make config T=x86_64-native-linuxapp-gcc`.
3. A generált konfigurációt Mellanox PMDs engedélyezése `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. A fordítási `make`.
5. Telepítés a `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Futtatókörnyezet konfigurálása

Futtassa az alábbi parancsokat egyszer, az újraindítást követően:

1. Hugepages

   * A következő parancsot, és egyszer az összes numanodes hugepage konfigurálása:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Hozzon létre egy könyvtárat a csatlakoztatáshoz szükséges `mkdir /mnt/huge`.
   *  A csatlakoztatási hugepages `mount -t hugetlbfs nodev /mnt/huge`.
   *  Ellenőrzés hugepages vannak fenntartva az `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Egy lehetséges, hogy módosítsa a grub-fájlt, hogy a nagyon nagy lapok vannak fenntartva az rendszerindításkor a következő a [utasításokat](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) DPDK számára. A lap alján található utasítás. Egy Azure-beli Linuxos virtuális gépen futó, amikor módosítani /etc/config/grub.d fájlokat ehelyett hugepages lefoglalása újraindítások között.

2. MAC és IP-címek: használata `ifconfig –a` megtekintéséhez a hálózati adapterek MAC- és IP-címét. A *VF* hálózati adapter és *NETVSC* hálózati adapter van az azonos MAC-címet, de csak a *NETVSC* hálózati illesztőnek egy IP-címet. VF felületek futtatja, az alárendelt felületek NETVSC felületek.

3. PCI-címek

   * Ismerje meg a használni kívánt PCI cím *VF* a `ethtool -i <vf interface name>`.
   * Győződjön meg arról, hogy testpmd véletlenül nem átveszi a VF pci eszközt *eth0*, ha *eth0* van a gyorsított hálózatkezelés engedélyezett. Ha DPDK alkalmazás véletlenül átvette a felügyeleti hálózati adaptert, és az SSH-kapcsolatot elvesztését eredményezi, használja a soros konzol kill DPDK alkalmazást, vagy a virtuális gép indítása vagy leállítása.

4. Betöltés *ibuverbs* minden egyes újraindításkor a `modprobe -a ib_uverbs`. Csak a SLES 15., is terheléselosztást *mlx4_ib* a `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>FailSafe PMD

DPDK alkalmazások kell futtatnia a failsafe PMD, amely az Azure-ban van közzétéve. Ha az alkalmazás közvetlenül a VF PMD keresztül futtat, akkor nem kap **összes** csomagok, a virtuális gép felé irányuló, mivel bizonyos csomagok jelennek meg a szintetikus felületen keresztül történik. Keresztül a failsafe PMD garantálja, hogy az alkalmazás megkapja, felé irányuló összes csomag-e, és emellett biztosítja az alkalmazás fut DPDK módban való futás esetén is folytatódik a VF vissza lett vonva, ha a gazdagép javítás alatt áll. Failsafe PMD további információkért tekintse meg a [hibamentes lekérdezési mód illesztőprogram könyvtárából](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Testpmd futtatása

Használat `sudo` előtt a *testpmd* parancsot a legfelső szintű módban történő futtatására.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Alapszintű: Megerősítést jelölőnégyzet, failsafe adapter inicializálása

1. Futtassa a következő parancsokat egy egyetlen port testpmd alkalmazás elindításához:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Futtassa a következő parancsokat egy kettős port testpmd alkalmazás elindításához:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Ha a 2-nél több hálózati adapterrel, futtatja a `--vdev` argumentum ezt a mintát követi: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Megkezdése után futtassa `show port info all` portadatokat ellenőrzéséhez. Megtekintheti az egy vagy két DPDK-portok net_failsafe (nem *net_mlx4*).
4.  Használat `start <port> /stop <port>` forgalom elindításához.

Az előző parancsok start *testpmd* interaktív módban, amely ajánlott, próbálja ki néhány testpmd parancsot.

### <a name="basic-single-sendersingle-receiver"></a>Alapszintű: Egyetlen feladó/egyetlen fogadó

A következő parancsok rendszeres időközönként nyomtatása a csomagok másodpercenkénti statisztika:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. A RX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Módosítsa a virtuális gépen az előző parancs futtatásakor *IP_SRC_ADDR* és *IP_DST_ADDR* a `app/test-pmd/txonly.c` a konkrét IP-címet a virtuális gépek megfelelően, mielőtt fordítása. Ellenkező esetben a csomagok megszakadnak a fogadó elérése előtt.

### <a name="advanced-single-sendersingle-forwarder"></a>Speciális: Egyetlen feladó/egyetlen továbbító
A következő parancsok rendszeres időközönként nyomtatása a csomagok másodpercenkénti statisztika:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. A FWD oldalon futtassa a következő parancsot:

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

Módosítsa a virtuális gépen az előző parancs futtatásakor *IP_SRC_ADDR* és *IP_DST_ADDR* a `app/test-pmd/txonly.c` a konkrét IP-címet a virtuális gépek megfelelően, mielőtt fordítása. Ellenkező esetben a csomagok megszakadnak a továbbító elérése előtt. Nem lehet szeretné, hogy egy harmadik gép továbbított forgalom fogadásához, mivel a *testpmd* továbbító nem módosítja a 3. rétegbeli címeket, kivéve, ha néhány kódot módosít.

## <a name="references"></a>Referencia

* [EAL beállításai](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd parancsok](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
