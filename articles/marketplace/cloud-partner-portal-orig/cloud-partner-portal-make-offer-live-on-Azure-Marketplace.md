---
title: Az ajánlat élő Azure Marketplace-en a virtuális gép
description: Az ajánlat élő Azure Marketplace-en a virtuális gép
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ff3ca665382efc9133a4c4ce3f3fde25c03c5e52
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810959"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Az ajánlat élő Azure Marketplace-en a virtuális gép
=========================================================

Miután feltöltötte az ajánlat részleteit, miután, ideje az ajánlat közzététele, és lehetővé teszi az élő Azure Marketplace-en. Az ajánlat halad át néhány szakaszból áll. Ellenőrizze, hogy a marketinganyagot, mind a Virtuálisgép-lemezkép a minőségi követelményeknek, annak érdekében, hogy az Azure Certified, és válassza a webhelyen.

![Nyissa meg az élő feladatütemezési 0 ajánlat](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Nézzük, ez a folyamat segít jobban megérteni, hogy mi történik minden egyes lépésnél részletesebben keresztül. Ennek a folyamatnak kell annak érdekében, hogy folyamatban van az ajánlat továbbra is működni.

<a name="publishing-process"></a>Közzétételi folyamat
------------------

Kattintson a "Közzététel" a szerkesztő lapján a közzétételi folyamat elindításához.

![Nyissa meg az élő feladatütemezési 1 - ajánlat közzététele](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Az állapot lapon jelenik meg a közzétételi lépéseket, és ahol az ajánlatot a folyamat során.

![Ajánlat Go élő feladatütemezési 2 – állapot](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

A közzétételi folyamat bármely mozzanata is is jelentkezzen be és a minden kínál fülre kattintva bármely az ajánlatok a legfrissebb állapotának megtekintéséhez. Az ajánlat közvetlenül az állapot kattintson, és a részletek megtekintéséhez, ahol az ajánlatot a közzétételi folyamat.

![Nyissa meg az élő feladatütemezési 3 - ajánlat az összes kínál állapota](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Vegyük végig a közzétételi lépéseket minden egyes, ismertetjük, hogy mi történik, ha az egyes lépések, és mennyi ideig kell becsülni az egyes lépések vesz igénybe.

**Előfeltételek ellenőrzése (\<1 nap)**

Ha "Közzététel" gombra kattint, egy automatikus ellenőrzést annak érdekében, hogy, miután feltöltötte az ajánlat az összes kötelező mezőt vesz igénybe. Ha mezőket nincsenek feltöltve adattal, egy figyelmeztetés fog megjelenni a mező mellett, és töltheti fel azt pontosan kell majd kattintson a "Közzététel" újra.

Miután végzett ezzel a lépéssel megfelelően, egy előugró ablak jelenik meg amely rákérdez, hogy e-mail-címet. Ez az az e-mailt, amelyhez a közzétételi folyamat fennmaradó közzétételi állapot értesítéseket fog kapni. Miután az e-mail-címét, ez a lépés befejeződött.

![Az ajánlat Go Live feladatütemezési 4 – az ajánlat közzététele](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Hitelesítésszolgáltató (\<5 nap)**

Ez a lépés nem, ahol futtatás, a Virtuálisgép-lemezkép megfelel-e a követelményeknek, az Azure Certified számos tesztet. Győződjön meg arról, át kell adnia a minősítési követelményeknek kell minden útmutatást is [Itt](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites).

Mivel ez a lépés több napig is eltarthat, a Cloud Partner portálra is kijelentkezik. Küldünk e-mailben értesítést ha bármely címre igénylő hibák. Ha minden rendben halad, sikeres, a folyamat automatikusan áthelyezi a kiépítési lépéssel.

**Kiépítés (\<1 nap)**

Ebben a szakaszban azt a képek az összes globális Azure-beli adatközpontok replikál, és akár is igénybe vehet egy nap végrehajtásához.

**Formátumokat támogató csomagolási és Érdeklődők generálása regisztrációs (\<1 óra)**

Ebben a szakaszban azt vannak összefűzhet a technikai és marketinganyagokat tartalom mi lesz a termék webhelyén.

Emellett ha konfigurálta az Érdeklődők generálása funkció, Microsoft ellenőrzi, hogy működik-e a CRM-integráció tesztelése az érdeklődő küld a CRM. Ez a lépés befejezése után töltse fel a CRM vagy az Azure Table hamis adatokat rekord jelenik meg. Érdeklődők generálása az összes dokumentáció megtalálható [Itt](./cloud-partner-portal-get-customer-leads.md).

**Az ajánlat előzetes verzióban érhető el**

Kap egy értesítő e-mailt, hogy az ajánlat sikeresen befejeződött az előzetes verzióban érhető el az ajánlatot eléréséhez szükséges lépéseket. Ezzel a lépéssel kell az ajánlat kipróbálásához, és ellenőrizze, hogy mindent úgy tűnik, kell lennie, és a virtuális gép megfelelően átmeneti környezetben üzembe helyezi.

**Csak az engedélyezett előfizetések teheti meg ezt az ellenőrzést.**\*

**Közzétevő jóváhagyás**

Miután ellenőrizte, hogy minden helyesnek tűnik, és jól működik-e előzetes verzióban érhető el, készen áll az élő esemény indításra. Ehhez kattintson a Go Live állapot lapra, és hogy lépéseket, hogy az ajánlat elkezdi élő, éles környezetben, és a webhelyen. Általában igénybe vesz néhány órát is várhatnak az élő gombra kattint, és élő a webhelyen az ajánlat esetén. Küldünk e-mailben értesítést után az ajánlat hivatalosan élő a webhelyen.

![Nyissa meg az élő feladatütemezési 5 ajánlat – az élő esemény indításra](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Élő**

Az ajánlat már élő Azure Marketplace-en és az Azure Portalon, és ügyfelek is megtekinthetik és a virtuális gépet az Azure-előfizetései üzembe helyezni. Bármikor minden ajánlatok lapján kattintson, és megnézheti az állapotukat minden az ajánlat, a jobb oldali oszlopban található. Kattintson az állapotadatokra kattintva részletesen ismertetjük az ajánlat közzétételi folyamat állapotát a.

<a name="error-handling"></a>Hibakezelés
--------------

A közzétételi folyamat során hiba merülhetnek fel. Ha hiba lép fel, amely tájékoztatja, hogy hiba történt a következő lépésekről utasításokat tartalmazó értesítő e-mailt kap. Hiba a folyamat során bármikor az állapot fülre kattintva is megjelenik. Amely mutasson, a folyamat a hiba történt a mellett megtudhatja, mit kell feloldani egy hibaüzenet jelenik meg.

![Nyissa meg az élő feladatütemezési 6 – hibaüzenet ajánlat](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Ha hibákat észlel a közzétételi folyamat során, akkor szükségesek, hárítsa el ezeket, majd kattintson a "Közzététel" indítsa újra a folyamatot. Ha bármilyen hiba javítása után újból közzéteszi el kell indítania az Előfeltételek ellenőrzése a közzétételi lépéseket kezdetén.

Ha a hiba elhárítása problémák merülnek fel, meg kell nyisson egy támogatási kérelmet, kérjen segítséget a támogatási.

![Ajánlat Go élő feladatütemezési 7 - támogatás](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>A közzétételi kérelem visszavonása
--------------------------------

Előfordulhat, hogy közzététel megkezdéséhez, és hogy a kérés visszavonása. A közzétételi kérelem csak vonhatja vissza a közzétételi kérelem eléri a közzétevő Sign-off lépés után. Megszakítja a műveletet, kattintson a "Mégse közzététele". A közzététel állapotának alaphelyzetbe állítja az 1. lépéshez, és újra közzé, meg kell kattintson a közzététel lehetőségre, majd kövesse az állapot.

![Ajánlat Go élő feladatütemezési 8 – állapot](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

