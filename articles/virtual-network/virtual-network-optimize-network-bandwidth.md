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
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: 914747983d4d974810836be66d6c6af343f58b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Az Azure virtuális gépek hálózati teljesítmény optimalizálása

Azure virtuális gépek (VM) rendelkezik, amely további optimalizálhatók a hálózat átviteli sebességét az alapértelmezett hálózati beállításokat. A cikkből megtudhatja, hogyan optimalizálható a hálózat átviteli sebességét a Microsoft Azure Windows és Linux virtuális gépeken, beleértve például Ubuntu, a CentOS és a Red Hat fő terjesztéseket.

## <a name="windows-vm"></a>Windowsos VM

Ha a Windows virtuális gép használata támogatott [az elérését gyorsítja fel hálózati](virtual-network-create-vm-accelerated-networking.md), annak engedélyezése, hogy az optimális átviteli konfigurációt lenne. A többi Windows virtuális fogadó oldali skálázás (RSS) használatával érhető el újabb maximális átviteli sebesség, mint egy virtuális gép RSS nélkül. Alapértelmezés szerint a Windows virtuális gép RSS is tiltható le. Kövesse az alábbi lépéseket annak meghatározásához, hogy engedélyezve van-e az RSS és az engedélyezéshez, ha le van tiltva.

1. Adja meg a `Get-NetAdapterRss` engedélyezve van-e a RSS a hálózati adapterhez tartozó PowerShell-parancsot. A következő példa kimenet által visszaadott a `Get-NetAdapterRss`, RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Adja meg a következő parancs futtatásával engedélyezze az RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancs nincs kimenete. A parancs a hálózati kártya beállításait, ideiglenes kapcsolatok megszakadását okozva körülbelül egy percet megváltozott. A kapcsolat megszakadását alatt megjelenik egy Reconnecting párbeszédpanel. A harmadik kísérlet után a kapcsolat jellemzően helyre.
3. Győződjön meg arról, hogy engedélyezve van az RSS a virtuális gép írja be a `Get-NetAdapterRss` újra a parancsot. Ha sikeres, a következő egy példa a kimenetre adja vissza:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux virtuális gép

Az RSS az Azure Linux virtuális gép alapértelmezés szerint mindig engedélyezve van. Linux kernelek január, 2017 óta megjelent új hálózati optimalizálási beállításokat, amelyek lehetővé teszik a Linux virtuális gépek nagyobb hálózati átviteli sebesség eléréséhez tartalmazhat.

### <a name="ubuntu"></a>Ubuntu

Az optimalizálás eléréséhez először frissítse frissítésétől. június 2017, amely a legfrissebb támogatott verziót:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
A frissítés befejeződése után adja meg a legújabb kernel eléréséhez a következő parancsokat:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Nem kötelező parancs:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-preview-kernel"></a>Ubuntu Azure betekintő kernel
> [!WARNING]
> Az Azure Linux Preview kernel nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, mint a piactéren elérhető rendszerkép és kernelek, amelyek általában a rendelkezésre állási kiadással. A funkció nem támogatott, van, korlátozott képességeit, és nem lehet a megbízható, mint az alapértelmezett kernel. A kernel ne használja termelési számítási feladatokhoz.

A javasolt Azure Linux kernel telepítésével jelentős átviteli teljesítményt érheti el. Próbálja meg a kernel, vegye fel ezt a sort /etc/apt/sources.list

```bash
#add this to the end of /etc/apt/sources.list (requires elevation)
deb http://archive.ubuntu.com/ubuntu/ xenial-proposed restricted main multiverse universe
```

Ezután futtassa az alábbi parancsokat rendszergazdaként.
```bash
apt-get update
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Az optimalizálás eléréséhez először frissítse frissítésétől július 2017, amely a legfrissebb támogatott verziót:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
A frissítés befejezése után telepítse a legújabb Linux integrációs szolgáltatások (LIS).
A teljesítmény optimalizálása LIS 4.2.2-2-től kezdődő van. Adja meg a következő parancsok futtatásával telepíteni azokat:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Az optimalizálás eléréséhez először frissítse frissítésétől július 2017, amely a legfrissebb támogatott verziót:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017071923"
```
A frissítés befejezése után telepítse a legújabb Linux integrációs szolgáltatások (LIS).
A teljesítmény optimalizálása LIS 4.2-től kezdődő van. Adja meg a letölteni és telepíteni azokat a következő parancsokat:

```bash
mkdir lis4.2.2-2
cd lis4.2.2-2
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
tar xvzf lis-rpms-4.2.2-2.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Tudjon meg többet a Linux integrációs szolgáltatások verzió 4.2 a Hyper-V megtekintésével a [letöltési oldalát](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Következő lépések
* Most, hogy a virtuális gép megfelelően lett optimalizálva, tekintse meg a eredmény [sávszélesség/átviteli Azure virtuális gép tesztelési](virtual-network-bandwidth-testing.md) a forgatókönyvéhez.
* Ismerje meg [Azure Virtual Network gyakori kérdések (GYIK)](virtual-networks-faq.md)
