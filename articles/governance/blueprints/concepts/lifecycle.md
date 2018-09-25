---
title: Egy Azure tervezet életciklus megismerése
description: Ismerje meg az életciklus-tervrajz halad át, és minden egyes szakaszhoz részleteit.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991539"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Egy Azure tervezet életciklus megismerése

Számos Azure-erőforrások, például az Azure-tervek tervrajz rendelkezik egy tipikus és természetes eszközéletciklus. Ezek már létrehozott, telepítve, és véglegesen törlődnek, ha már nem szükséges és a vonatkozó.
Tervezetek támogatja a hagyományos CRUD (létrehozása/olvasása/frissítése/törlése) életciklus-műveleteket, de meg is alapozza a ezekre épül, további szintű állapot, amely támogatja a közös folyamatos integráció / folyamatos üzembe helyezés (CI/CD) folyamatok használatra azok őket kód – fejlesztési és üzemeltetési kulcsfontosságú eleme az infrastruktúra kezelése néven infrastruktúra-Code (IaC).

Teljes mértékben megérteni a tervezet és a szakaszok, egy szabványos életciklus-foglalkozik:

> [!div class="checklist"]
> - Létrehozása és tervrajz szerkesztése
> - A tervrajz közzététele
> - Létrehozása és egy új verziója a tervrajz szerkesztése
> - A tervezet új verziójának közzétételéhez
> - Egy adott verzióját a tervezet törlése
> - A tervezet törlése

## <a name="creating-and-editing-a-blueprint"></a>Létrehozása és tervrajz szerkesztése

Tervrajz létrehozásakor adjon hozzá összetevőket, a felügyeleti csoport mentse, és a megadott egyedi nevét és a egy egyedi verziót. Ezen a ponton a tervezet szerepel egy **Draft** mód és még nem lehet hozzárendelni. Azonban miközben a a **Draft** mód az továbbra is frissíthető és módosítható.

A tervrajz **Draft** jelenik meg egy másik ikonjára mód, amely soha nem lett közzétéve a **Tervezetdefiníciók** oldal, mint már **közzétett**. A **legújabb verzió** is vannak feltüntetve **Draft** a ezek soha nem közzétett tervrajzokat.

Hozzon létre, és a tervrajz szerkesztése a [az Azure portal](../create-blueprint-portal.md#create-a-blueprint) vagy [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Tervrajz közzététele

Miután az összes kívánt módosításokat, a tervrajz **Draft** lehet módban **közzétett** és a hozzárendelés elérhetővé. A **közzétett** a tervezet verziója nem módosítható.
Egyszer **közzétett**, a tervezet jeleníti meg, mint egy másik ikon **Draft** tervezetek, és megjeleníti a megadott verziószámot a **legújabb verzió** oszlop.

A tervrajz közzététele a [az Azure portal](../create-blueprint-portal.md#publish-a-blueprint) vagy [REST API-val](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Létrehozása és egy új verziója a tervrajz szerkesztése

Bár egy **közzétett** tervrajz verziója nem módosítható, egy új verziója a tervezet hozzáadható a meglévő tervezethez és igény szerint módosítani. Ehhez egy meglévő tervezet módosítása. Ha a tervezet már hozzá lett **közzétett**, ezek a módosítások mentésekor ezek állapotúként **közzé nem tett változások** tervezetdefiníciók listájában. A módosítások mentése mentése egy **Draft** a tervezet verzióját.

A tervrajz szerkesztése a [az Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>A tervezet új verziójának közzétételéhez

Ugyanúgy, mint a tervezet első verziója lett **közzétett** rendelje hozzá, minden ezt követő verziója, hogy ugyanazon tervezet kell **közzétett** előtt is hozzárendelhető. Amikor **közzé nem tett változások** vezettük be a tervezet még nem volt, de **közzétett**, a **tervrajz közzététele** gomb érhető el a tervezet lap. Ha a gomb nem látható, a tervezet már hozzá lett **közzétett**, de nem rendelkezik **közzé nem tett változások**.

> [!NOTE]
> Egyetlen tervezet rendelkezhet több **közzétett** verziókat, amelyek egymással előfizetésekhez rendelhető.

A tervrajz közzétételéhez **közzé nem tett változások** , egy új verziója egy meglévő tervezet ugyanazok, mint a lépéseket egy új tervrajz közzététele.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Egy adott verzióját a tervezet törlése

A tervezet egyes verzióihoz egy egyedi objektum, és külön-külön lehet **közzétett**. Ez azt is jelenti, hogy a tervezet egyes verzióihoz törölhetők. Egy verziója a tervezet törlése nincs hatással a tervezet a más verziói.

> [!NOTE]
> Nem alkalmas, amely rendelkezik aktív hozzárendelések tervrajz törlése. Először törölje a hozzárendeléseket, és törölje az eltávolítani kívánt verzió.

1. Indítsa el az Azure-tervek szolgáltatás az Azure Portalon kattintson a **minden szolgáltatás** keresése és kiválasztása **házirend** a bal oldali panelen. Az a **házirend** lapon **tervezetek**.

1. Válassza ki **Tervezetdefiníciók** az oldalról, a bal oldalon a szűrőbeállítások keresse meg a tervezet törli egy verziójának használatát. Kattintson rá a lap megnyitásához.

1. Kattintson a **közzétett verzió** lapra, és keresse meg a törölni kívánt verziót.

1. Kattintson a jobb gombbal a verzióra történő törléséhez, és válassza ki a **verzió törlése**.

## <a name="deleting-the-blueprint"></a>A tervezet törlése

A core tervezet is törölhetők. A core tervezet is törlése eltávolítja a tervezet tervezet verziói függetlenül attól, hogy **Draft** vagy **közzétett** állapotát. És a egy verziója a tervezet törlése a core tervezet törlése nem távolítja el a meglévő hozzárendelések a tervezet verzióinak.

> [!NOTE]
> Nem alkalmas, amely rendelkezik aktív hozzárendelések tervrajz törlése. Először törölje a hozzárendeléseket, és törölje az eltávolítani kívánt verzió.

A tervrajz törlése a [az Azure portal](../create-blueprint-portal.md#delete-a-blueprint) vagy [REST API-val](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Hozzárendelések

Nincsenek számos olyan pontja – életciklusa során egy tervezet előfizetéshez rendelhető.
Minden alkalommal, amikor az egy verziója a tervezet módja **közzétett**, akkor azt a verziót előfizetéshez rendelhető. Ha egy **Draft** a tervezet, ha egy vagy több tervezet verziók a verzióját egy **közzétett** módot, majd ezeket **közzétett** verziók van elérhető hozzárendelni. Ez lehetővé teszi a tervrajz kell felhasználni, és aktívan rendelve, amíg egy újabb verzióra még fejlesztés alatt verzióit.

Hozzárendelt tervezetek verzióit, fontos tudni, ha hozzá vannak rendelve, és a paramétereket, hogy hozzá van rendelve. A paraméterek lehet statikus vagy dinamikus. További tudnivalókért lásd: [statikus és dinamikus paraméterek](parameters.md).

### <a name="updating-assignments"></a>Hozzárendelések frissítése

A tervezet hozzárendelésekor a hozzárendelés lehet frissíteni. A meglévő hozzárendelések módosítása több okból is beleértve:

- Hozzáadása vagy eltávolítása [erőforrás zárolása](resource-locking.md)
- Módosítsa az értéket a [dinamikus paraméterek](parameters.md#dynamic-parameters)
- A hozzárendelés frissítsen egy újabb **közzétett** a tervezet verziója

További információ [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan használja [statikus és dinamikus paraméterek](parameters.md)
- Ismerje meg, szabhatja testre a [tervezetet alkalmazás-előkészítés sorrend](sequencing-order.md)
- Ismerje meg, győződjön meg arról, hogyan használhatja az [tervezetet erőforrás zárolása](resource-locking.md)
- Ismerje meg, hogyan [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md)
- A tervrajz hozzárendelésének során felmerülő problémák megoldása [általános hibaelhárítási](../troubleshoot/general.md)