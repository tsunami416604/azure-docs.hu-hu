---
title: "Csomag rögzíti az Azure hálózati figyelőt - Azure-portálon kezelése |} Microsoft Docs"
description: "Ezen a lapon ismerteti, hogyan kezelheti a csomag rögzítése funkció az Azure-portál használatával hálózati figyelőt"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 818f6513625a2677668dd6b6869ef969fe015bf7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Csomag rögzítésekre kezelése Azure hálózati figyelőt a portál használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Az Azure REST API-n](network-watcher-packet-capture-manage-rest.md)

Hálózati figyelő csomagrögzítéssel rögzítési munkamenetek nyomon követéséhez forgalma és a virtuális gép létrehozását teszi lehetővé. Annak érdekében, hogy csak a kívánt forgalom rögzíti a rögzítési munkamenet szűrők célokat szolgálnak. Csomagrögzítéssel segít diagnosztizálni hálózati rendellenességeket proaktív és reaktív is. Egyéb felhasználásra tartalmazzák a hálózati statisztikákat, hálózati behatolások, ügyfél-kiszolgáló közötti kommunikációt, és még sok más hibakeresési információkat való összegyűjtéséhez. Őket távolról elindítása csomag rögzíti, ez a funkció megkönnyíti a csomagrögzítéssel fut, manuálisan, a kívánt számítógépet, amely értékes időt takaríthat meg okozta terheket.

Ez a cikk végigvezeti Önt a különböző felügyeleti feladatok, amelyek jelenleg a csomagrögzítéssel.

- [**A csomagrögzítéssel indítása**](#start-a-packet-capture)
- [**A csomagrögzítéssel leállítása**](#stop-a-packet-capture)
- [**A csomagrögzítéssel törlése**](#delete-a-packet-capture)
- [**A csomagrögzítéssel letöltése**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik-e a következőket:

- A csomagrögzítéssel létrehozni kívánt hálózati figyelőt régióban példánya
- A csomag rögzítési engedélyezett bővítményekhez virtuális gépet.

> [!IMPORTANT]
> Csomagrögzítéssel van szükség a virtuálisgép-bővítmény `AzureNetworkWatcherExtension`. A bővítmény telepítése a Windows virtuális gép a Microsoft [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md) és a Linux virtuális gép helyezést [Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Csomag rögzítési ügynök bővítmény a portálon keresztül

A csomag rögzítési Virtuálisgép-ügynök a portálon keresztül telepítéséhez nyissa meg a virtuális gép, kattintson a **bővítmények** > **Hozzáadás** keresse meg a **hálózati figyelő ügynök a Windows**

![ügynök nézet][agent]

## <a name="packet-capture-overview"></a>Csomag rögzítési áttekintése

Keresse meg a [Azure-portálon](https://portal.azure.com) kattintson **hálózati** > **hálózati figyelőt** > **csomag rögzítése**

A – Áttekintés lapon látható összes csomag listája létező függetlenül állapotát rögzíti.

> [!NOTE]
> Csomagrögzítéssel a tárfiókkal igényel a 443-as porton keresztül.

![csomag rögzítési áttekintés képernyő][1]

## <a name="start-a-packet-capture"></a>A csomagrögzítéssel indítása

Kattintson a **Hozzáadás** egy csomagrögzítéssel létrehozásához.

A csomagrögzítéssel lehet definiálni tulajdonságok a következők:

**Fő beállítások**

- **Előfizetés** -ezt az értéket az előfizetés használt, a hálózati figyelőt példánya van szükség minden előfizetésben.
- **Erőforráscsoport** -az erőforráscsoport a célzott alatt álló virtuális gép.
- **A cél virtuális gép** – a virtuális gép, amelyen a csomagrögzítéssel fut.
- **Csomag rögzítési neve** -ezt az értéket a csomagrögzítéssel neve.

**Konfigurációjának rögzítése**

- **A Tárfiók** -határozza meg, ha a tárfiók csomagrögzítéssel menti.
- **Fájl** -határozza meg, ha egy csomagrögzítéssel rendszer helyileg menti a virtuális gépen.
- **Storage-fiókok** – a kiválasztott tárolási fiók segítségével menti a csomagrögzítéssel a. Alapértelmezett helye https://{storage name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription fiókazonosító} /resourcegroups/ {erőforráscsoport name}/providers/microsoft.compute/virtualmachines/{virtual gép neve} / {éé} / {MM} / {NN} / {HH} packetcapture__{MM}_{SS} {XXX} _ .cap. (Csak akkor engedélyezett, ha **tárolási** van kiválasztva)
- **Helyi fájl elérési útját** -a helyi elérési út mentése a csomagrögzítéssel virtuális gépen. (Csak akkor engedélyezett, ha **fájl** van kiválasztva). Érvényes elérési utat meg kell adni.
- **Csomag maximális bájtokat** - szám bájtok számát az egyes csomagok, amelyek rögzítve lesznek, az összes bájt rofilok rögzítésének, ha üresen hagyja.
- **Munkamenet maximális bájtokat** – teljes száma, amelyek rögzítve lesznek, az érték elérésekor a csomag rögzítési leáll bájt.
- **Időkorlát (másodperc)** – beállítja a csomagrögzítéssel leállításához időkorlátot. Alapértelmezett érték 18000 másodperc.

> [!NOTE]
> Prémium szintű storage-fiókok jelenleg nem támogatottak a csomag tárolásához rögzíti.

**Szűrés (nem kötelező)**

- **Protokoll** -a protokollt, hogy a csomagrögzítéssel szűréséhez. A lehetséges értékek a következők: TCP, UDP és bármilyen.
- **Helyi IP-cím** -Ha a helyi IP-cím megegyezik-e a szűrő szűrők ezt az értéket a csomagrögzítéssel csomagok.
- **Helyi port** -Ha a helyi port megegyezik-e a szűrő szűrők ezt az értéket a csomagrögzítéssel csomagok.
- **Távoli IP-cím** – Ha az távoli IP-cím megegyezik-e a szűrő szűrők ezt az értéket a csomagrögzítéssel csomagok.
- **Távoli port** -Ha a távoli port megegyezik-e a szűrő szűrők ezt az értéket a csomagrögzítéssel csomagok.

> [!NOTE]
> Port és az IP-cím értékek egyetlen értéket, az értéktartományon vagy egy lehet. (Ez azt jelenti, hogy a 80-1024 port) Megadhatja, hogy annyi szűrők, ahányat csak szeretne.

Ha az értékek ki van töltve, kattintson **OK** a csomagrögzítéssel létrehozásához.

![a csomagrögzítéssel létrehozása][2]

Lejárt az időkorlát a csomagrögzítéssel be, miután a csomagrögzítéssel leáll, és áttekintheti. A csomag rögzítésekre munkamenetek manuálisan is leállíthatja.

## <a name="delete-a-packet-capture"></a>A csomagrögzítéssel törlése

A csomag rögzítési nézetben kattintson a **helyi menü** (...), vagy kattintson a jobb gombbal, majd kattintson **törlése** a csomagrögzítéssel leállítása

![A csomagrögzítéssel törlése][3]

> [!NOTE]
> A csomagrögzítéssel törlése nem érinti a tárfiókban lévő fájlt.

A rendszer felkéri a jóváhagyásához a csomagrögzítéssel törléséhez kattintson **Igen**

![Jóváhagyás][4]

## <a name="stop-a-packet-capture"></a>A csomagrögzítéssel leállítása

A csomag rögzítési nézetben kattintson a **helyi menü** (...), vagy kattintson a jobb gombbal, majd kattintson **leállítása** leállítja a csomagrögzítéssel

## <a name="download-a-packet-capture"></a>A csomagrögzítéssel letöltése

A csomag rögzítési munkamenet befejezése után a rögzítési fájl feltöltése a blob storage vagy a virtuális gép helyi fájlba. A tárolási helye a csomagrögzítéssel definiálása a munkamenet létrehozását. Eszköz eléréséhez rögzítési-fájlokat egy tárfiókkal a Microsoft Azure Tártallózó, amely innen tölthető le: http://storageexplorer.com/

Ha egy tárfiókot meg van adva, csomag rögzítési fájlok kerülnek a storage-fiókok a következő helyen:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Következő lépések

Csomag rögzíti a virtuális gép a riasztások megtekintésével automatizálása [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

Keresése, ha bizonyos adatforgalom engedélyezett a virtuális gép kívül vagy belül ellátogatva [ellenőrizze IP folyamat ellenőrzése](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png













