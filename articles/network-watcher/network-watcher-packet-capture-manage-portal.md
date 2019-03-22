---
title: Csomagrögzítés kezelése az Azure Network Watcher – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a packet capture funkciójának a Network Watcher az Azure portal használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: jdial
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889981"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>A csomagrögzítés kezelése az Azure Network Watcher a portál használatával

Network Watcher csomagrögzítés nyomon követésére, és a virtuális gépről érkező forgalom rögzítése-munkamenetek létrehozását teszi lehetővé. Szűrők annak érdekében, hogy csak a kívánt forgalmat rögzíti a rögzítési munkamenet-okat. A csomagrögzítés segítségével diagnosztizálhatja a hálózat a rendellenességeket, mind reaktív, és proaktív módon. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresési ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez. Képes arra, hogy távolról aktiválása csomag rögzíti, megkönnyíti a csomagrögzítés manuálisan futtat egy használni kívánt virtuális gépet, amely értékes időt takarít meg terhe.

Ebből a cikkből elsajátíthatja az indítása, leállítása, töltse le és csomagrögzítés törlése. 

## <a name="before-you-begin"></a>Előkészületek

Csomagrögzítés a következő kapcsolatok szükségesek:
* Kimenő kapcsolatot a 443-as porton keresztül egy tárfiókba.
* Bejövő és kimenő kapcsolatot 169.254.169.254
* Bejövő és kimenő kapcsolatot 168.63.129.16

Ha egy hálózati biztonsági csoportot a hálózati adapter vagy az alhálózatot, amelyet a hálózati adapter társítva, győződjön meg arról, szabályok tartoznak, amelyek lehetővé teszik az előző portokat. 

## <a name="start-a-packet-capture"></a>Csomagrögzítés indítása

1. Navigáljon a böngészőben a [az Azure portal](https://portal.azure.com) , és válassza ki **minden szolgáltatás**, majd válassza ki **Network Watcher** a a **hálózatkezelés szakasz**.
2. Válassza ki **csomagrögzítés** alatt **hálózati diagnosztikai eszközök**. Bármely meglévő csomagrögzítés vannak felsorolva, függetlenül azok állapotát.
3. Válassza ki **Hozzáadás** csomagrögzítés létrehozásához. Választhat a következő tulajdonságok értékeit:
   - **Előfizetés**: Az előfizetést, amelyhez a virtuális gépet szeretne létrehozni a csomagot a capture van.
   - **Erőforráscsoport**: Az erőforráscsoport, a virtuális gép.
   - **Cél virtuális gép**: A csomagrögzítés a létrehozni kívánt virtuális gép.
   - **Csomagrögzítés neve**: A csomagrögzítés neve.
   - **Storage-fiók vagy a fájl**: Válassza ki **tárfiók**, **fájl**, vagy mindkettőt. Ha **fájl**, a rögzítés írt egy útvonalat a virtuális gépen.
   - **Helyi fájlelérési út**: A helyi elérési utat, ahol a csomagrögzítés menti a rendszer a virtuális gépen (csak akkor, ha érvényes *fájl* van kiválasztva). Az elérési út érvényes elérési útnak kell lennie. Ha Linux rendszerű virtuális gép használ, az elérési utat kell kezdődnie */var/rögzíti*.
   - **Storage-fiókok**: Válassza egy meglévő tárfiókot, ha a kiválasztott *tárfiók*. Ez a beállítás csak akkor érhető el, ha a kiválasztott **tárolási**.
   
     > [!NOTE]
     > Prémium szintű storage-fiókok jelenleg nem támogatottak a csomagok tárolására kell-e.

   - **Bájtok maximális száma csomagonként**: Az egyes csomagok rögzített bájtok száma. Ha üresen hagyja, az összes bájtot lesznek rögzítve.
   - **Bájtok maximális száma munkamenetenként**: Rögzített bájtok teljes száma. Ha az érték elérésekor a packet capture leáll.
   - **Időkorlát (másodperc)**: A határidő előtt a csomagrögzítés le van állítva. Az alapértelmezett érték 18,000 másodperc.
   - Szűrés (nem kötelező). Válassza ki **+ szűrő hozzáadása**
     - **Protokoll**: A csomagrögzítés szűrését protokoll. Az elérhető értékek a következők: TCP, UDP és bármilyen.
     - **Helyi IP-cím**: A csomagrögzítés csomagok szűri, ha a helyi IP-cím megegyezik-e ezt az értéket.
     - **Helyi port**: A csomagrögzítés csomagok szűri, ahol a helyi port egyezik-e ezt az értéket.
     - **Távoli IP-cím**: A csomagrögzítés csomagok szűri, ha a távoli IP-cím megegyezik-e ezt az értéket.
     - **Távoli port**: A csomagrögzítés csomagok szűri, ha a távoli port megegyezik-e ezt az értéket.
    
     > [!NOTE]
     > Port- és IP-cím értékeit egyetlen érték, -tartományt vagy egy tartományt, például a 80-as-1024-, port lehet. Tetszőleges számú szűrők szükség szerint határozhatja meg.

4. Kattintson az **OK** gombra.

Lejárt az időkorlát beállítása a csomagrögzítés, miután a csomagrögzítés le van állítva, és tekinthető meg. A csomag rögzítési munkamenet manuálisan is leállíthatja.

> [!NOTE]
> A portál automatikusan:
>  * Ha a régió még nem rendelkezik a network watchert hoz létre network watchert a régióban, a kiválasztott virtuális gép létezik, és ugyanabban a régióban.
>  * Hozzáadja a *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) vagy [Windows](../virtual-machines/windows/extensions-nwa.md) virtuálisgép-bővítményt a virtuális géphez, ha az még nem telepítette.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

1. A csomag rögzítési nézetben válassza ki a **...**  a jobb oldalon – a csomag rögzítése, vagy kattintson a jobb gombbal egy meglévő csomagrögzítés, és válassza ki **törlése**.
2. A rendszer felkéri erősítse meg a csomagrögzítés törli. Válassza ki **Igen**.

> [!NOTE]
> Csomagrögzítés törlése nem törli a rögzítési fájlt a tárfiókban, vagy a virtuális gépen.

## <a name="stop-a-packet-capture"></a>Csomagrögzítés leállítása

A csomag rögzítési nézetben válassza ki a **...**  a jobb oldalon – a csomag rögzítése, vagy kattintson a jobb gombbal egy meglévő csomagrögzítés, és válassza ki **leállítása**.

## <a name="download-a-packet-capture"></a>Töltse le a csomagrögzítés

A csomag rögzítési munkamenet befejezése után a rögzítési feltölti a blob storage-bA vagy egy helyi fájlba a virtuális gépen. A csomagrögzítés tárolási helyét a csomagrögzítés létrehozása során van meghatározva. Eszköz rögzítési-fájlokat a storage-fiók eléréséhez is a Microsoft Azure Storage Explorer [letöltése](https://storageexplorer.com/).

Ha egy storage-fiók van megadva, packet capture fájlok egy storage-fiókba, a következő helyen találhatóak meg:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Ha a kiválasztott **fájl** létrehozásakor a rögzítés a megtekintése, vagy töltse le a fájlt a virtuális gépen konfigurált elérési úton található.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan automatizálhatja a virtuális gép riasztások csomagrögzítés, lásd: [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md).
- Annak eldöntéséhez, hogy bizonyos forgalomra engedélyezve van-e, vagy ki a virtuális gép, lásd: [egy virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása](diagnose-vm-network-traffic-filtering-problem.md).
