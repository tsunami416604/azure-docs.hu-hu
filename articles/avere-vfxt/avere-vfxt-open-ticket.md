---
title: Az Avere vFXT for Azure támogatásának beszerezni
description: Az Avere vFXT for Azure támogatási jegyeinek megnyitásának magyarázata
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252557"
---
# <a name="get-help-with-your-system"></a>Segítség a rendszerrel kapcsolatban

Ha segítségre van szüksége az Avere vFXT for Azure rendszerrel kapcsolatban, az alábbiakban segítséget kaphat:

* **Avere vFXT-probléma** – Az Azure Portal on-ból megnyithat egy támogatási jegyet az Avere vFXT-hez az [alábbiak szerint.](#open-a-support-ticket-for-your-avere-vfxt)
* **Kvóta** – Ha kvótafüggő problémája van, [kérjen kvótanövelést](#request-a-quota-increase)
* **Dokumentáció és példák** – Ha problémákat talál ezzel a dokumentációval vagy példákkal kapcsolatban, görgessen a problémát tartalmazó oldal aljára, és a **Visszajelzés** szakaszban keresse meg a meglévő problémákat, és szükség esetén nyújtson be egy újat.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Nyisson meg egy támogatási jegyet az Avere vFXT-hez

Ha problémákat tapasztal az Avere vFXT telepítése vagy használata során, kérjen segítséget az Azure Portalon keresztül.

Az alábbi lépésekkel győződjön meg arról, hogy a támogatási jegy a fürtből származó erőforrással van címkézve. A jegy címkézése segít a megfelelő támogatási erőforráshoz irányítani.

1. A [https://portal.azure.com](https://portal.azure.com)forrás ból válassza **az Erőforráscsoportok lehetőséget.** Tallózással keresse meg azt az erőforráscsoportot, amely a problémát okozó vFXT-fürtöt tartalmazza, és kattintson az Avere fürt egyik virtuális gépére.

    ![képernyőkép az Azure Portal erőforráscsoport "áttekintése" panelről egy adott virtuális gép bekarikázva](media/avere-vfxt-ticket-vm.png)

1. A Virtuálisgép lapon görgessen le a bal oldali panel aljára, és kattintson az **Új támogatási kérelem gombra.**

    ![Képernyőkép az Azure Portal virtuális gép lapjáról a virtuális gép az előző képernyőképből. A bal oldali menü az aljára görgetve van, és az "Új támogatási kérelem" be van karikázva.](media/avere-vfxt-ticket-request.png)

1. A támogatási kérelem első oldalán válassza ki a probléma típusát, és győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva.

   A **Szolgáltatás**csoportban kattintson a **Minden szolgáltatás** elemre, és az **Avere vFXT**kiválasztásához a **Tárolás** csoportban válassza a lehetőséget.

   Adjon hozzá egy rövid összefoglalót, és válassza ki a probléma típusát.

    ![képernyőképet az Azure Portalon egy új támogatási kérelem képernyőről. Az Alapok lap ki van jelölve. A képernyőelemek közé tartozik a probléma típusa, az Előfizetés, a Szolgáltatás, az Összegzés és a Probléma típusa.](media/ticket-basics.png)

   A folytatáshoz kattintson a **Tovább** gombra.

1. A támogatási űrlap második oldala javaslatokat tartalmaz a probléma megoldására az összefoglaló leírás alapján. Kattintson a **Tovább** gombra az alján, ha továbbra is létre kell hoznia egy támogatási jegyet.

   ![képernyőképet az új támogatási kérelem képernyőről, amelyen a Megoldások lap van kiválasztva. A középső szövegmező címe "Ajánlott megoldás", és ismerteti a lehetséges megoldásokat.](media/ticket-solutions.png)

1. A harmadik oldalon adja meg a részleteket – ez magában foglalja a fürtre, a probléma előfordulásának időpontjára, a súlyosságára és az Önnel való kapcsolatfelvétel módjára vonatkozó információkat. Töltse ki az adatokat, és kattintson a **Tovább** gombra az alján.

   ![képernyőképet az új támogatási kérelem képernyőről, amelyen a Részletek lap van kiválasztva. Az információs mezők a "Probléma részletei", a "Támogatási módszer" és a "Elérhetőségi adatok" kategóriákba vannak rendezve.](media/ticket-details.png)

1. Tekintse át az utolsó lapon található információkat, és kattintson a **Létrehozás gombra.** A visszaigazolást és a jegyszámot elküldjük az e-mail címére, és a támogatási személyzet tagja felveszi Önnel a kapcsolatot.

## <a name="request-a-quota-increase"></a>Kvótanövelés kérése

Olvassa [el a vFXT-fürt kvótáját,](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) és ismerje meg, hogy milyen összetevőkszükségesek az Avere vFXT Azure-hoz való üzembe helyezéséhez. [Kvótanövelést kérhet](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) az Azure Portalról.
