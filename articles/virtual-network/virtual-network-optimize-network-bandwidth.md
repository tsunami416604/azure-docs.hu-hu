---
title: Virtuálisgép-hálózat teljesítményének optimalizálása |} A Microsoft Docs
description: Ismerje meg, hogyan optimalizálható az Azure virtuális gépek hálózati átviteli sebessége.
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
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025548"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure-beli virtuális gépek hálózati teljesítmény optimalizálása

Az Azure virtual machines (VM) rendelkezik, amely a hálózati teljesítmény tovább optimalizálható az alapértelmezett hálózati beállításokat. Ez a cikk ismerteti a hálózati teljesítmény optimalizálása a Microsoft Azure Windows és Linux rendszerű virtuális gépekhez, beleértve a fő disztribúciók, például az Ubuntu, a CentOS és Red Hat.

## <a name="windows-vm"></a>Windows rendszerű virtuális gép

Ha a Windows virtuális gép támogatja [gyorsított hálózatkezelés](create-vm-accelerated-networking-powershell.md), az átviteli sebesség optimális konfigurációt, hogy a funkció engedélyezése lenne. A többi Windows virtuális fogadó oldali skálázás (RSS) használatával is elérheti magasabb maximális átviteli sebesség, mint egy virtuális gép RSS nélkül. Alapértelmezés szerint a Windows virtuális gép RSS letilthatja. Annak megállapításához, hogy az RSS engedélyezve van, és engedélyezze, ha jelenleg le van tiltva, a következő lépéseket:

1. Tekintse meg, hogy engedélyezve van-e az RSS a hálózati adapterhez a `Get-NetAdapterRss` PowerShell-parancsot. A következő példa kimenet által visszaadott a `Get-NetAdapterRss`, RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Ahhoz, hogy az RSS, adja meg a következő parancsot:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancs nincs kimenet. A parancs a hálózati adapter beállításait, átmeneti hálózati adatveszteség okozó körülbelül egy percig módosítani. Egy Reconnecting párbeszédpanel jelenik meg a kapcsolat megszakadását során. Kapcsolat általában helyreáll a harmadik kísérlet után.
3. Győződjön meg arról, hogy az RSS engedélyezve van a virtuális gépen írja be a `Get-NetAdapterRss` újra a parancsot. Ha sikeres, a rendszer visszaadja az alábbi példa kimenetében:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux rendszerű virtuális gép

Az RSS az Azure Linux VM alapértelmezés szerint mindig engedélyezve van. Kiadás dátuma: 2017. október óta Linux-kernelek vannak, amelyek lehetővé teszik a Linux virtuális gépek nagyobb hálózati átviteli sebesség eléréséhez, új hálózati optimalizálást beállításokat is tartalmazhat.

### <a name="ubuntu-for-new-deployments"></a>Az új központi telepítéseknél Ubuntu

Az Ubuntu Azure kernel a lehető legjobb hálózati teljesítményt biztosít az Azure-ban, és már évek óta az alapértelmezett kernel 2017. szeptember 21. Annak érdekében, hogy a kernel kap, először 16.04-LTS, legújabb támogatott verziót a következőképpen telepítheti:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

A létrehozás befejezése után adja meg a következő parancsok futtatásával beszerezni a legújabb frissítéseket. Ezeket a lépéseket az Ubuntu Azure kernel jelenleg futó virtuális gépek is működnek.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Az alábbi választható parancsot beállítása, amely már rendelkezik az Azure kernel, de további frissíti a hibák sikertelenül telepítéseit Ubuntu hasznosak lehetnek.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Meglévő virtuális gépek Ubuntu Azure kernel frissítése

Az Azure Linux-kernel való frissítéssel jelentős átviteli teljesítmény érhető el. Ellenőrizze, hogy a kernel rendelkezik, ellenőrizze a kernel verziója.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Ha a virtuális gép nem rendelkezik az Azure kernel, a verziószám általában kezdődik "4.4." Ha a virtuális gép nem rendelkezik az Azure kernel, futtassa a következő parancsokat rendszergazdaként:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Annak érdekében, hogy a legújabb optimalizálásokat lekéréséhez a legjobb, ha a virtuális gép létrehozása a legújabb támogatott verzióját a következő paraméterek megadásával:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Új és meglévő virtuális gépek is kihasználhatják a legújabb Linux Integration Services (LIS) telepítése. Az átviteli sebesség optimalizálása LIS, kezdve a 4.2.2-2, bár a további fejlesztéseket tartalmaz újabb verzió van. Adja meg a legújabb LIS telepítése a következő parancsokat:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Az optimalizálást, hogy a legjobb, ha a virtuális gép létrehozása a legújabb támogatott verzióját a következő paraméterek megadásával:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Új és meglévő virtuális gépek is kihasználhatják a legújabb Linux Integration Services (LIS) telepítése. Az átviteli sebesség optimalizálása LIS, kezdve a 4.2 szerepel. Adja meg, töltse le és LIS telepítése a következő parancsokat:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Tudjon meg többet a Linux Integration Services verzió 4.2 Hyper-V megtekintésével a [letöltési oldalát](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>További lépések
* Az optimalizált eredményeket [sávszélesség/átviteli sebesség tesztelése az Azure virtuális gép](virtual-network-bandwidth-testing.md) a forgatókönyvhöz.
* Megtudhatja, hogyan lehet [sávszélességet a virtuális gépek](virtual-machine-network-throughput.md)
* További tudnivalók a [Azure Virtual Network – gyakori kérdések (GYIK)](virtual-networks-faq.md)
