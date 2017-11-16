---
title: "Virtuális gép hálózati teljesítmény optimalizálása |} Microsoft Docs"
description: "Útmutató: Azure virtuális gép hálózati teljesítmény optimalizálása érdekében."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 2f7a65d32f662d7e265e58c5fe7d9dea81a4e63c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Az Azure virtuális gépek hálózati teljesítmény optimalizálása

Azure virtuális gépek (VM) rendelkezik, amely további optimalizálhatók a hálózat átviteli sebességét az alapértelmezett hálózati beállításokat. A cikkből megtudhatja, hogyan optimalizálható a hálózat átviteli sebességét a Microsoft Azure Windows és Linux virtuális gépeken, beleértve például Ubuntu, a CentOS, és a Red Hat fő terjesztéseket.

## <a name="windows-vm"></a>Windowsos VM

Ha a Windows virtuális gép használata támogatott [az elérését gyorsítja fel hálózati](virtual-network-create-vm-accelerated-networking.md), annak engedélyezése, hogy az optimális átviteli konfigurációt lenne. A többi Windows virtuális fogadó oldali skálázás (RSS) használatával érhető el újabb maximális átviteli sebesség, mint egy virtuális gép RSS nélkül. Alapértelmezés szerint a Windows virtuális gép RSS is tiltható le. Kövesse az alábbi lépéseket annak meghatározásához, hogy engedélyezve van-e az RSS és az engedélyezéshez, ha le van tiltva.

1. Adja meg a `Get-NetAdapterRss` engedélyezve van-e a RSS a hálózati adapterhez tartozó PowerShell-parancsot. A következő példa kimenet által visszaadott a `Get-NetAdapterRss`, RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Adja meg a következő parancs futtatásával engedélyezze az RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancs nincs kimenete. A parancs a hálózati kártya beállításait, ideiglenes kapcsolatok megszakadását okozva körülbelül egy percet megváltozott. A kapcsolat megszakadását alatt megjelenik egy Reconnecting párbeszédpanel. A harmadik kísérlet után a kapcsolat jellemzően helyre.
3. Győződjön meg arról, hogy engedélyezve van az RSS a virtuális gép írja be a `Get-NetAdapterRss` újra a parancsot. Ha sikeres, a következő egy példa a kimenetre adja vissza:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux virtuális gép

Az RSS az Azure Linux virtuális gép alapértelmezés szerint mindig engedélyezve van. Linux kernelek október 2017 óta megjelent új hálózati optimalizálás beállításokat, amelyek lehetővé teszik a Linux virtuális gépek nagyobb hálózati átviteli sebesség eléréséhez tartalmazhat.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu az új központi telepítéseknél

Az Ubuntu Azure kernel biztosítja a legjobb hálózati teljesítmény az Azure-on, és az alapértelmezett kernel mióta 2017. szeptember 21. A kernel eléréséhez először telepítse legújabb támogatott verzióját 16.04-es lts verzió, az alább ismertetett:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
A létrehozásának befejezése után adja meg a következő parancsok futtatásával beszerezni a legújabb frissítéseket. Ezek a lépések az Ubuntu Azure kernel jelenleg futó virtuális gépek is működnek.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

A következő opcionális parancskészlethez meglévő Ubuntu telepítésekhez, amely már rendelkezik az Azure kernel, de hibákkal további frissítések sikertelenül hasznos lehet.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel frissítés meglévő virtuális gépek

Frissítse az Azure Linux kernel jelentős átviteli teljesítményt érheti el. Ellenőrizze, hogy rendelkezik-e a kernel, ellenőrizze a kernel verzióját.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Ha a virtuális gép nem rendelkezik az Azure kernel, akkor a verziószám általában "4.4" kezdődik. Ezekben az esetekben futtassa az alábbi parancsokat rendszergazdaként.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

A legújabb optimalizálásokat eléréséhez a legcélszerűbb hozzon létre egy virtuális Gépet a legújabb támogatott verzióját a következő paraméterek megadásával:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Új és meglévő virtuális gépek kihasználhassa a legújabb Linux integrációs szolgáltatások (LIS) telepítését.
A teljesítmény optimalizálása LIS kiindulva 4.2.2-2, de további fejlesztéseket tartalmaz újabb verzió van. Adja meg a következő parancsok futtatásával telepítse a legújabb LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Az optimalizálás eléréséhez a legcélszerűbb hozzon létre egy virtuális Gépet a legújabb támogatott verzióját a következő paraméterek megadásával:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Új és meglévő virtuális gépek kihasználhassa a legújabb Linux integrációs szolgáltatások (LIS) telepítését.
A teljesítmény optimalizálása LIS 4.2-től kezdődő van. Adja meg a letölteni és telepíteni azokat a következő parancsokat:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Tudjon meg többet a Linux integrációs szolgáltatások verzió 4.2 a Hyper-V megtekintésével a [letöltési oldalát](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Következő lépések
* Most, hogy a virtuális gép megfelelően lett optimalizálva, tekintse meg a eredmény [sávszélesség/átviteli Azure virtuális gép tesztelési](virtual-network-bandwidth-testing.md) a forgatókönyvéhez.
* Ismerje meg [Azure Virtual Network gyakori kérdések (GYIK)](virtual-networks-faq.md)
