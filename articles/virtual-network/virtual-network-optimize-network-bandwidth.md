---
title: Virtuális gép hálózati átviteli csatornájának optimalizálása | Microsoft dokumentumok
description: Ismerje meg, hogyan optimalizálhatja az Azure virtuálisgép-hálózati átviteli csatornát.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690882"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Hálózati átviteli hang optimalizálása az Azure virtuális gépeiszámára

Az Azure virtuális gépek (VM) alapértelmezett hálózati beállításokkal rendelkeznek, amelyek tovább optimalizálhatók a hálózati átviteli értékre. Ez a cikk bemutatja, hogyan optimalizálhatja a hálózati átviteli hálózat microsoft Azure Windows és Linux virtuális gépek, beleértve a főbb disztribúciók, például az Ubuntu, CentOS és a Red Hat.

## <a name="windows-vm"></a>Windows rendszerű virtuális gép

Ha a Windows virtuális gép támogatja [az Accelerated Networking szolgáltatást,](create-vm-accelerated-networking-powershell.md)akkor az adott szolgáltatás engedélyezése lenne az átviteli teljesítmény optimális konfigurációja. Az összes többi Windows virtuális gépek használata fogadási oldali skálázás (RSS) elérheti a nagyobb maximális átviteli, mint a virtuális gép rss nélkül. Előfordulhat, hogy az RSS alapértelmezés szerint le van tiltva egy Windows virtuális gépben. Annak megállapításához, hogy az RSS engedélyezve van-e, és ha jelenleg le van tiltva, hajtsa végre az alábbi lépéseket:

1. Nézze meg, hogy az RSS `Get-NetAdapterRss` engedélyezve van-e egy hálózati adapterhez a PowerShell paranccsal. A következő példában a `Get-NetAdapterRss`visszaadott kimenet az RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Az RSS engedélyezéséhez írja be a következő parancsot:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancs nem rendelkezik kimenettel. A parancs módosította a hálózati adapter beállításait, ami körülbelül egy percig ideiglenes kapcsolatvesztést okozott. A kapcsolat elvesztése közben megjelenik egy Újracsatlakozás párbeszédpanel. A kapcsolat általában a harmadik kísérlet után áll helyre.
3. Ellenőrizze, hogy az RSS engedélyezve `Get-NetAdapterRss` van-e a virtuális gépben a parancs újbóli beírásával. Ha sikeres, a következő példakimenetet adja vissza:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux rendszerű virtuális gép

Az RSS alapértelmezés szerint engedélyezve van egy Azure Linux os virtuális gépben. A 2017 októbere óta kiadott Linux kernelek új hálózati optimalizálási lehetőségeket tartalmaznak, amelyek lehetővé teszik a Linux virtuális gép számára a nagyobb hálózati átviteli érték elérését.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu új telepítésekhez

Az Ubuntu Azure kernel biztosítja a legjobb hálózati teljesítményt az Azure-ban, és 2017. A rendszermag beszerezéséhez először telepítse a 16.04-LTS legújabb támogatott verzióját az alábbiak szerint:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

A létrehozás befejezése után adja meg a következő parancsokat a legújabb frissítések beszerezéséhez. Ezek a lépések az Ubuntu Azure kernelt jelenleg futtató virtuális gépeknél is működnek.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

A következő választható parancskészlet hasznos lehet a meglévő Ubuntu-központi telepítések, amelyek már rendelkeznek az Azure kernel, de nem sikerült a további frissítéseket a hibákat.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel frissítés meglévő virtuális gépekhez

Az Azure Linux kernelre való frissítéssel jelentős átviteli teljesítmény érhető el. Annak ellenőrzéséhez, hogy rendelkezik-e ezzel a kernelnel, ellenőrizze a kernel verzióját.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Ha a virtuális gép nem rendelkezik az Azure kernel, a verziószám általában kezdődik "4.4." Ha a virtuális gép nem rendelkezik az Azure kernel, futtassa a következő parancsokat gyökérként:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

A legújabb optimalizálások eléréséhez a legjobb, ha a következő paraméterek megadásával hoz létre virtuális gépet a legújabb támogatott verzióval:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Az új és a meglévő virtuális gépek számára előnyös lehet a legújabb Linux-integrációs szolgáltatások (LIS) telepítése. Az átviteli optimalizálás a LIS-ben van, 4.2.2-2-től kezdve, bár a későbbi verziók további fejlesztéseket tartalmaznak. Írja be a következő parancsokat a legújabb LIS telepítéséhez:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Az optimalizálások eléréséhez a legjobb, ha a következő paraméterek megadásával hoz létre virtuális gépet a legújabb támogatott verzióval:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Az új és a meglévő virtuális gépek számára előnyös lehet a legújabb Linux-integrációs szolgáltatások (LIS) telepítése. Az átviteli optimalizálás a LIS-ben van, 4.2-től kezdve. Írja be a következő parancsokat a LIS letöltéséhez és telepítéséhez:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Tudjon meg többet a Linux Integration Services Version 4.2 hyper-V megtekintésével a [letöltési oldalon](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>További lépések
* Tekintse meg az optimalizált eredményt [a sávszélesség-átviteli tesztelés azure-beli virtuális gép](virtual-network-bandwidth-testing.md) a forgatókönyvhöz.
* További információ [arról, hogy a virtuális gépek mennyi sávszélességet osztanak ki](virtual-machine-network-throughput.md)
* További információ az [Azure Virtual Network gyakori kérdéseivel (GYIK)](virtual-networks-faq.md)
