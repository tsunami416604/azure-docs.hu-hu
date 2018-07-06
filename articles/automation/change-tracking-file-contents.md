---
title: Fájltartalom-változások megjelenítése az Azure Automationnel
description: A Change tracking fájl tartalmának módosítása funkcióját használja, amelyek módosultak egy fájl tartalmának megtekintéséhez.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0582505d66bbef3064359fa4047676c4ba60b4e9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871884"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>A Change Tracking követett fájl tartalmának megjelenítése

Tartalom nyomon követésének lehetővé teszi, hogy egy fájl tartalmának megjelenítése előtt és után a Change Tracking követett változás. Ehhez írja a fájl tartalmát egy tárfiókba után minden változás következik be.

## <a name="requirements"></a>Követelmények

* Egy standard szintű tárfiókot a Resource Manager üzemi modell használatával szükség a fájl tartalmának tárolására. Prémium és a klasszikus üzemi modell tárfiókok nem használható. Storage-fiókokról további információért lásd: [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)

* A használt tárfiók legfeljebb 1 Automation-fiók csatlakoztatva.

* [A változáskövetés](automation-change-tracking.md) az Automation-fiókban engedélyezve van.

## <a name="enable-file-content-tracking"></a>Tartalom nyomon követésének engedélyezése

1. Az Azure Portalon nyissa meg az Automation-fiókját, és válassza **változáskövetés**.
2. A felső menüben válassza ki a **beállításainak szerkesztése**.
3. Válassza ki **fájltartalom** kattintson **hivatkozás**. Ekkor megnyílik a **tartalom helyének felvétele a Change Tracking megoldásba** ablaktáblán.

   ![engedélyezése](./media/change-tracking-file-contents/enable.png)

4. Válassza ki az előfizetést és a tárfiók tárolja a fájl tartalmát. Ha szeretné az összes meglévő követett fájl fájl tartalom nyomon követése, válassza ki a **a** a **töltse fel a fájl tartalmának az összes beállítás**. Ezt módosíthatja a minden fájl elérési útjának ezt követően.

   ![storage-fiók beállítása](./media/change-tracking-file-contents/storage-account.png)

5. Ha engedélyezve van, a storage-fiók és a SAS URI-k jelennek meg. A SAS URI-k 365 nap után lejár, és azok ismételten létrehozhatók kattintva a **újragenerálása** gombra.

   ![fiók kulcsainak listázása](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Adjon hozzá egy fájlt

A következő lépések végigvezetik a change tracking fájl bekapcsolása:

1. Az a **beállításainak szerkesztése** lapján **Change Tracking**, válassza **Windows fájlok** vagy **Linux-fájlok** lapra, majd kattintson a  **Hozzáadása**

1. Adja meg az adatokat a fájl elérési útja, és válassza ki **igaz** alatt **töltse fel az összes beállítás fájltartalom**. Ez a beállítás lehetővé teszi, hogy a fájl tartalmát, csak a fájl elérési útja a követési.

   ![egy linux-fájl hozzáadása](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>A nyomon követett fájl tartalmának megtekintése

1. Miután a változást észlelt a fájl vagy az elérési út egy fájlt, a portál jeleníti meg. Válassza ki a fájl módosítása változások a listából. A **változás részletei** ablaktáblán jelenik meg.

   ![Változások felsorolása](./media/change-tracking-file-contents/change-list.png)

1. Az a **változás részletei** lapon láthatja a standard előtt és után bal felső sarokban található információkat, fájlra, majd kattintson **fájltartalom-változások megjelenítése** a fájl tartalmának megtekintéséhez.

  ![változás részletei](./media/change-tracking-file-contents/change-details.png)

1. Az új lapon látható a fájl tartalmát egy egymás melletti nézet. Lehetőség kiválasztásával **beágyazott** beágyazott képet a módosítások megtekintéséhez.

  ![nézet fájlmódosítás](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>További lépések

Látogasson el az oktatóanyag a Change Tracking tudhat meg többet a megoldás használata:

> [!div class="nextstepaction"]
> [A környezet változásainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)

* Használat [Log Analytics naplóbeli kereséseivel](../log-analytics/log-analytics-log-searches.md) részletes változáskövetési adatok megtekintéséhez.
