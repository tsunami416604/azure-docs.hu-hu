---
title: Csomagrögzítés kezelése az Azure Network Watcher – Azure portal |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan kezelheti az Azure portal használatával a Network Watcher packet rögzítési funkciója
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 18e5f8eda51f8834f6346eef3d7ad31bc556290a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090197"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>A csomagrögzítés kezelése az Azure Network Watcher a portál használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Az Azure REST API-val](network-watcher-packet-capture-manage-rest.md)

Network Watcher csomagrögzítés nyomon követésére, és a virtuális gépről érkező forgalom rögzítése-munkamenetek létrehozását teszi lehetővé. Szűrők annak érdekében, hogy csak a kívánt forgalmat rögzíti a rögzítési munkamenet-okat. A csomagrögzítés segítségével diagnosztizálhatja a hálózat rendellenességeket, proaktív és reaktív is. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresése, ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez. Képes lesz távolról indításához csomagrögzítés, ez a funkció egyszerűsíti a csomagrögzítés fut, manuálisan, a kívánt számítógépre, amelyen értékes időt takarít meg terhe.

Ez a cikk végigvezeti a különböző felügyeleti feladatok csomagrögzítés jelenleg elérhető.

- [**Csomagrögzítés indítása**](#start-a-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-a-packet-capture)
- [**Csomagrögzítés törlése**](#delete-a-packet-capture)
- [**Töltse le a csomagrögzítés**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az alábbi forrásanyagokat:

- Csomagrögzítés létrehozni kívánt példányát, Network Watcher régióban
- A csomag rögzítése a bővítmény engedélyezve van a virtuális gép.

> [!IMPORTANT]
> A csomagrögzítés szükséges virtuálisgép-bővítmények `AzureNetworkWatcherExtension`. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Csomagok rögzítése agent bővítményt a portálon keresztül

Csomagok rögzítése Virtuálisgép-ügynök telepítése a portálon keresztül, keresse meg a virtuális gépet, kattintson a **bővítmények** > **Hozzáadás** és keressen rá a **hálózati figyelő ügynök a Windows**

![ügynök nézet][agent]

## <a name="packet-capture-overview"></a>A csomagrögzítés áttekintése

Keresse meg a [az Azure portal](https://portal.azure.com) kattintson **hálózatkezelés** > **Network Watcher** > **Csomagrögzítés**

Az Áttekintés lap látható az összes csomag listáját kell-e létező, függetlenül attól, hogy az állapot.

> [!NOTE]
> A csomagrögzítés conectivity a következő szükséges.
> * A tárfiók a 443-as porton keresztüli kimenő kapcsolódás.
> * Bejövő és kimenő kapcsolatot 169.254.169.254
> * Bejövő és kimenő kapcsolatot 168.63.129.16

![csomagok rögzítése áttekintés képernyő][1]

## <a name="start-a-packet-capture"></a>Csomagrögzítés indítása

Kattintson a **Hozzáadás** csomagrögzítés létrehozásához.

A csomagrögzítés adható meg a tulajdonságok a következők:

**Fő beállításai**

- **Előfizetés** -értéke a használt előfizetés, a network watcher-példány van szükség az egyes előfizetésekben.
- **Erőforráscsoport** – a virtuális gép, amely a megcélzott információtípustól az erőforráscsoportot.
- **Cél virtuális gép** – a virtuális gép, amelyen fut a csomagrögzítést
- **Csomagrögzítés neve** – Ez az érték pedig a csomagrögzítés neve.

**Konfigurációjának rögzítése**

- **Helyi fájlelérési út** – helyi elérési útját a virtuális gépen, csomagrögzítés mentési helye (csak akkor, ha érvényes **[-fájl]** van kiválasztva). Adjon meg érvényes elérési utat. Ha Linux rendszerű virtuális gép használ, az elérési utat kell kezdődnie / var / rögzíti.
- **Storage-fiók** – azt határozza meg, ha a csomagrögzítés menti a rendszer egy storage-fiókot.
- **Fájl** – azt határozza meg, ha a csomagrögzítés rendszer helyileg menti a virtuális gépen.
- **Storage-fiókok** – a kiválasztott tárhelyfiókba menti a csomagrögzítés a. Alapértelmezett hely a https://{storage name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription fiókazonosító} /resourcegroups/ {erőforráscsoport name}/providers/microsoft.compute/virtualmachines/{virtual gép neve} / {éé} / {MM} / {DD} / {HH} packetcapture__{MM}_{mm} _ {XXX} .cap. (Csak akkor engedélyezett, ha **tárolási** van kiválasztva)
- **Helyi fájlelérési út** – a helyi elérési utat a csomagrögzítés menteni a virtuális gépen. (Csak akkor engedélyezett, ha **fájl** van kiválasztva). Meg kell adni egy érvényes elérési utat. Linux virtuális gépek, az az elérési utat kell kezdődnie: */var/rögzíti*.
- **Bájtok maximális száma csomagonként** - száma (bájt) az egyes csomagok, amelyek rögzítve lesznek az összes bájt vannak rögzítve, ha üresen hagyja.
- **Bájtok maximális száma munkamenetenként** – teljes által rögzített, az érték elérésekor a packet capture leáll bájtok száma.
- **Időkorlát (másodperc)** -leállítani a csomagrögzítést határidőt állítja be. Alapértelmezett érték 18000 másodperc.

> [!NOTE]
> Prémium szintű storage-fiókok jelenleg nem támogatottak a csomagok tárolására kell-e.

**Szűrés (nem kötelező)**

- **Protokoll** – a csomagrögzítés szűrését a protokollt. Az elérhető értékek a következők: TCP, UDP és bármilyen.
- **Helyi IP-cím** – Ha a helyi IP-cím megegyezik-e a szűrő értéke szűri ezt az értéket a csomagrögzítés csomagok.
- **Helyi port** – Ha a helyi port megegyezik-e a szűrő értéke szűri ezt az értéket a csomagrögzítés csomagok.
- **Távoli IP-cím** – Ha a távoli IP-cím megegyezik-e a szűrő értéke szűri ezt az értéket a csomagrögzítés csomagok.
- **Távoli port** – Ha a távoli port megegyezik-e a szűrő értéke szűri ezt az értéket a csomagrögzítés csomagok.

> [!NOTE]
> Port- és IP-cím értékeit egyetlen érték, -tartományt, vagy egy lehet. (azaz a 80-as-1024 port) Megadhatja a kívánt számú szűrőket.

Ha az értékek ki van töltve, kattintson **OK** a csomagrögzítés létrehozásához.

![csomagrögzítés létrehozása][2]

Lejárt az időkorlát beállítása a csomagrögzítés, miután a csomagrögzítés le fog állni, és tekinthető meg. A csomag rögzíti munkamenetek manuálisan is leállíthatja.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

A csomag rögzítési nézetben kattintson a **helyi menü** (...), vagy kattintson a jobb gombbal, majd kattintson **törlése** leállítani a csomagrögzítést

![Csomagrögzítés törlése][3]

> [!NOTE]
> Csomagrögzítés törlése nem törli a storage-fiókban a fájlt.

Győződjön meg arról, hogy szeretné törölni a csomagrögzítést, kattintson a rendszer felkéri **Igen**

![Megerősítés][4]

## <a name="stop-a-packet-capture"></a>Csomagrögzítés leállítása

A csomag rögzítési nézetben kattintson a **helyi menü** (...), vagy kattintson a jobb gombbal, majd kattintson **leállítása** leállítani a csomagrögzítést

## <a name="download-a-packet-capture"></a>Töltse le a csomagrögzítés

A csomag rögzítési munkamenet befejezése után a rögzítési feltölti a blob storage-bA vagy egy helyi fájlba a virtuális gépen. A csomagrögzítés tárolási helyét a munkamenet létrehozásakor van meghatározva. Ezek eléréséhez eszköz rögzítési-fájlokat a storage-fiók itt tölthető le a Microsoft Azure Storage Explorerben:  http://storageexplorer.com/

Ha egy storage-fiók van megadva, packet capture fájlok egy storage-fiókba, a következő helyen találhatóak meg:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a virtuális gép riasztások csomagrögzítés megtekintésével [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md)

Keresse meg, ha bizonyos van engedélyezi a forgalmat a virtuális gép vagy a funkcionáló [ellenőrizze IP-folyamat ellenőrzése](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













