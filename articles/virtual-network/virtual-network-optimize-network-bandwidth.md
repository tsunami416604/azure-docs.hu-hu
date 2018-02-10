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
ms.openlocfilehash: b604782f917584d1ecec432c20de75f427176ed1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Az Azure virtuális gépek hálózati teljesítmény optimalizálása

Azure virtuális gépek (VM) rendelkezik, amely további optimalizálhatók a hálózat átviteli sebességét az alapértelmezett hálózati beállításokat. A cikkből megtudhatja, hogyan optimalizálható a hálózat átviteli sebességét a Microsoft Azure Windows és Linux virtuális gépeken, beleértve például Ubuntu, a CentOS, és a Red Hat fő terjesztéseket.

## <a name="windows-vm"></a>Windowsos VM

Ha a Windows virtuális gép támogatja [az elérését gyorsítja fel hálózati](create-vm-accelerated-networking-powershell.md), annak engedélyezése, hogy az optimális átviteli konfigurációt lenne. A többi Windows virtuális fogadó oldali skálázás (RSS) használatával érhető el újabb maximális átviteli sebesség, mint egy virtuális gép RSS nélkül. Alapértelmezés szerint a Windows virtuális gép RSS is tiltható le. Annak megállapításához, hogy az RSS engedélyezve van-e, és engedélyezze, ha jelenleg le van tiltva, kövesse az alábbi lépéseket:

1. Engedélyezve van-e a RSS a hálózati adapterhez a `Get-NetAdapterRss` PowerShell-parancsot. A következő példa kimenet által visszaadott a `Get-NetAdapterRss`, RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. RSS engedélyezéséhez adja meg a következő parancsot:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancs nincs kimenete. A parancs a hálózati kártya beállításait, ideiglenes kapcsolatok megszakadását okozva körülbelül egy percet megváltozott. A kapcsolat megszakadását alatt megjelenik egy Reconnecting párbeszédpanel. A harmadik kísérlet után a kapcsolat jellemzően helyre.
3. Győződjön meg arról, hogy engedélyezve van az RSS a virtuális gép írja be a `Get-NetAdapterRss` újra a parancsot. Ha sikeres, a következő egy példa a kimenetre adja vissza:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux virtuális gép

Az RSS az Azure Linux virtuális gép alapértelmezés szerint mindig engedélyezve van. Linux kernelek október 2017 óta megjelent új hálózati optimalizálás beállításokat, amelyek lehetővé teszik a Linux virtuális gépek nagyobb hálózati átviteli sebesség eléréséhez tartalmazhat.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu az új központi telepítéseknél

Az Ubuntu Azure kernel biztosítja a legjobb hálózati teljesítmény az Azure-on, és az alapértelmezett kernel mióta 2017. szeptember 21. A kernel eléréséhez először telepítse a legújabb támogatott verzióját 16.04-es lts verzió, az alábbiak szerint:

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
#4.13.0-1007-azure
```

Ha a virtuális gép nem rendelkezik az Azure kernel, a verziószám általában kezdődik "4.4." Ha a virtuális gép nem rendelkezik az Azure kernel, futtassa az alábbi parancsokat rendszergazdaként:

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

Új és meglévő virtuális gépek kihasználhassa a legújabb Linux integrációs szolgáltatások (LIS) telepítését. A teljesítmény optimalizálása LIS kiindulva 4.2.2-2, de további fejlesztéseket tartalmaz újabb verzió van. Adja meg a következő parancsok futtatásával telepítse a legújabb LIS:

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

Új és meglévő virtuális gépek kihasználhassa a legújabb Linux integrációs szolgáltatások (LIS) telepítését. A teljesítmény optimalizálása LIS 4.2-től kezdődő van. Adja meg a letölteni és telepíteni azokat a következő parancsokat:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Tudjon meg többet a Linux integrációs szolgáltatások verzió 4.2 a Hyper-V megtekintésével a [letöltési oldalát](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>További lépések
* Az optimalizált eredményt a [sávszélesség/átviteli Azure virtuális gép tesztelési](virtual-network-bandwidth-testing.md) a forgatókönyvéhez.
* Megtudhatja, hogyan [sávszélességet a virtuális gépek] (virtuális-machine-hálózati-throughput.md)
* Ismerje meg [Azure Virtual Network gyakori kérdések (GYIK)](virtual-networks-faq.md)
