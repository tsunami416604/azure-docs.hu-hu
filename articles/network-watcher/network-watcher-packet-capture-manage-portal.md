---
title: Csomagrögzítések kezelése – Azure portal
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan kezelheti a Network Watcher csomagrögzítési funkcióját az Azure Portalon.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840827"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Csomagrögzítések kezelése az Azure Network Watcher segítségével a portálon

A Network Watcher csomagrögzítése lehetővé teszi rögzítési munkamenetek létrehozását a virtuális gépekre és a virtuális gépekről érkező és onnan érkező forgalom nyomon követésére. A rögzítési munkamenethez szűrők vannak megadva, amelyek biztosítják, hogy csak a kívánt forgalmat rögzítse. A csomagrögzítés segít diagnosztizálni a hálózati anomáliákat, mind reaktív, mind proaktív módon. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk gyűjtése, az ügyfél-kiszolgáló kommunikáció hibakeresése és még sok más. A csomagrögzítések távoli aktiválása megkönnyíti a csomagrögzítés manuális futtatásának terhét a kívánt virtuális gépen, ami értékes időt takarít meg.

Ebben a cikkben megtudhatja, hogyan kell elindítani, leállítani, letölteni és törölni egy csomagrögzítést. 

## <a name="before-you-begin"></a>Előkészületek

A csomagrögzítéshez a következő kapcsolat szükséges:
* Kimenő kapcsolat egy tárfiókkal a 443-as porton keresztül.
* Bejövő és kimenő kapcsolat a 169.254.169.254-hez
* Bejövő és kimenő kapcsolat a 168.63.129.16-hoz

Ha hálózati biztonsági csoport van társítva ahhoz a hálózati adapterhez vagy alhálózathoz, amelyben a hálózati csatoló található, győződjön meg arról, hogy léteznek olyan szabályok, amelyek engedélyezik az előző portokat. Hasonlóképpen, a felhasználó által definiált forgalmi útvonalak hozzáadása a hálózathoz megakadályozhatja a kapcsolatot a fent említett IP-kés és portok között. Kérjük, győződjön meg róla, hogy elérhető. 

## <a name="start-a-packet-capture"></a>Csomagrögzítés indítása

1. A böngészőben keresse meg az [Azure Portalt,](https://portal.azure.com) és válassza a **Minden szolgáltatás**lehetőséget, majd válassza a **Hálózatfigyelő** lehetőséget a **Hálózatcsoport csoportban.**
2. Válassza a **Csomagrögzítés lehetőséget** a **Hálózati diagnosztikai eszközök csoportban.** A meglévő csomagrögzítések az állapotuktól függetlenül jelennek meg.
3. Csomagrögzítés létrehozásához válassza a **Hozzáadás** lehetőséget. A következő tulajdonságokhoz választhat értékeket:
   - **Előfizetés**: Az az előfizetés, amelyhez a csomagrögzítést létrehozni kívánt virtuális gép létre kívánja hozni, megvan.
   - **Erőforráscsoport**: A virtuális gép erőforráscsoportja.
   - **Cél virtuális gép:** Az a virtuális gép, amelyhez a csomagrögzítést létre kívánja hozni.
   - **Csomagrögzítés neve**: A csomagrögzítés neve.
   - **Tárfiók vagy -fájl:** Válassza a **Tárfiók**, **A Fájl**vagy mindkettő lehetőséget. Ha a Fájl lehetőséget **választja,** a rögzítés a virtuális gépen belüli elérési útra kerül.
   - **Helyi fájl elérési útja**: Az a helyi elérési út a virtuális gépen, ahová a csomagrögzítés mentésre kerül (csak akkor érvényes, ha a *Fájl* ki van jelölve). Az elérési útnak érvényes elérési útnak kell lennie. Ha Linux os virtuális gépet használ, az elérési útnak *a /var/captures*kapcsolóval kell kezdődnie.
   - **Tárfiókok:** Válasszon ki egy meglévő tárfiókot, ha a *Tárfiókot*választotta. Ez a beállítás csak akkor érhető el, ha a **Tárolás**lehetőséget választotta.
   
     > [!NOTE]
     > Prémium szintű tárfiókok jelenleg nem támogatott a csomagrögzítések tárolására.

   - **Csomagonkénti maximális bájt:** A rögzített csomagok bájtjainak száma. Ha üresen hagyja, az összes bájt rögzítésre kerülnek.
   - **Munkamenetenkénti bájtok**maximális száma: A rögzített bájtok teljes száma. Az érték elérése után a csomagrögzítés leáll.
   - **Időkorlát (másodperc):** A csomagrögzítés leállítása előtti időkorlát. Az alapértelmezett érték 18 000 másodperc.
   - Szűrés (nem kötelező). + **Szűrő hozzáadása**
     - **Protokoll**: A csomagrögzítéshez szűrni való protokoll. A rendelkezésre álló értékek: TCP, UDP és Any.
     - **Helyi IP-cím:** Szűri a csomagrögzítést azokra a csomagokra, amelyekhelyi IP-címe megegyezik ezzel az értékkel.
     - **Helyi port**: Szűri a csomagrögzítést azokra a csomagokra, amelyeknek a helyi portja megegyezik ezzel az értékkel.
     - **Távoli IP-cím**: Szűri a csomagrögzítést azon csomagok esetében, amelyeknél a távoli IP-cím megegyezik ezzel az értékkel.
     - **Távoli port**: Szűri a csomagrögzítést azokra a csomagokra, amelyeknél a távoli port megfelel ennek az értéknek.
    
     > [!NOTE]
     > A port- és IP-címértékek lehetnek egyetlen értékek, értéktartomány vagy egy tartomány (például 80-1024) a porthoz. Annyi szűrőt határozhat meg, amennyire szüksége van.

4. Válassza **az OK gombot.**

Miután a csomagrögzítésre beállított időkorlát lejárt, a csomagrögzítés leáll, és ellenőrizhető. Manuálisan is leállíthatja a csomagrögzítési munkamenetet.

> [!NOTE]
> A portál automatikusan:
>  * Létrehoz egy hálózati figyelőt ugyanabban a régióban, ahol a kiválasztott virtuális gép létezik, ha a régióban még nincs hálózati figyelő.
>  * Hozzáadja az *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) vagy [Windows](../virtual-machines/windows/extensions-nwa.md) virtuálisgép-bővítményt a virtuális géphez, ha még nincs telepítve.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

1. A csomagrögzítési nézetben válassza a **...** lehetőséget a csomagrögzítés jobb oldalán, vagy kattintson a jobb gombbal egy meglévő csomagrögzítésre, és válassza a **Törlés parancsot.**
2. A rendszer megkéri, hogy erősítse meg a csomagrögzítés törlését. Válassza az **Igen** lehetőséget.

> [!NOTE]
> A csomagrögzítés törlése nem törli a rögzítési fájlt a tárfiókban vagy a virtuális gépen.

## <a name="stop-a-packet-capture"></a>Csomagrögzítés leállítása

A csomagrögzítési nézetben válassza a **...** lehetőséget a csomagrögzítés jobb oldalán, vagy kattintson a jobb gombbal egy meglévő csomagrögzítésre, és válassza a **Leállítás parancsot.**

## <a name="download-a-packet-capture"></a>Csomagrögzítés letöltése

Miután a csomagrögzítési munkamenet befejeződött, a rögzítési fájl feltöltésre kerül a blobstorage-ba vagy a virtuális gép helyi fájljába. A csomagrögzítés tárolási helye a csomagrögzítés létrehozása során van definiálva. A tárfiókba mentett rögzítési fájlok elérésének kényelmes eszköze a Microsoft Azure Storage Explorer, amely [letölthető](https://storageexplorer.com/).

Ha meg van adva tárfiók, a csomagrögzítő fájlok at a következő helyen menti a rendszer egy tárfiókba:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Ha a rögzítés létrehozásakor a **Fájl** lehetőséget választotta, megtekintheti vagy letöltheti a fájlt a virtuális gépen beállított elérési útról.

## <a name="next-steps"></a>További lépések

- A csomagrögzítések virtuálisgép-riasztásokkal történő automatizálásáról a [Riasztás aktivált csomagrögzítésének létrehozása című](network-watcher-alert-triggered-packet-capture.md)témakörben olvashat.
- Annak megállapításához, hogy egy adott forgalom engedélyezett-e a virtuális gépen vagy a virtuális gépen kívül, olvassa el [a Virtuálisgép hálózati forgalomszűrőproblémájának diagnosztizálása című témakört.](diagnose-vm-network-traffic-filtering-problem.md)
