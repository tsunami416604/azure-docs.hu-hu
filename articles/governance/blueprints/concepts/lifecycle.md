---
title: Tervrajz életciklusának ismertetése
description: Ismerje meg a életciklussal, amely végighalad a tervezet és az egyes szakaszok részleteit.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a57085fa37efd56a46b740d8cbc4278dc53cf39f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266613"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Egy Azure tervezet életciklusának ismertetése

Számos Azure-erőforrások, például egy Azure-tervek a tervezet egy természetes és a szokásos életciklussal. Ezek már létrehozott, telepítve, és véglegesen törlődnek, ha már nem szükséges és a vonatkozó.
Tervezetek támogatja a standard szintű életciklusához kapcsolódó műveletek. Majd épít állapota további szinteket, amelyek közös folyamatos integrációt és folyamatos üzembe helyezési folyamatok támogatják a szervezet számára, hogy az infrastruktúra kódként – fejlesztési és üzemeltetési kulcsfontosságú eleme felügyelő szerződtünk velük.

Teljes mértékben megérteni a tervezet és a szakaszok, egy szokásos életciklussal foglalkozik:

> [!div class="checklist"]
> - Létrehozása és tervrajz szerkesztése
> - A tervrajz közzététele
> - Létrehozása és egy új verziója a tervrajz szerkesztése
> - A tervezet új verziójának közzétételéhez
> - Egy adott verzióját a tervezet törlése
> - A tervezet törlése

## <a name="creating-and-editing-a-blueprint"></a>Létrehozása és tervrajz szerkesztése

Amikor hozzáadja a tervrajz létrehozása összetevők, a felügyeleti csoportba vagy előfizetésbe mentse, és egyedi nevét és a egy egyedi verziót a megadott. A tervezet már az egy **Draft** mód és még nem lehet hozzárendelni. A során a **Draft** módot, az továbbra is frissíthető és módosítható.

A soha nem közzétett a tervezet **Draft** mód a különböző ikont jelenít meg a **Tervezetdefiníciók** lap, amelyet voltak, mint **közzétett**. A **legújabb verzió** megjelent **Draft** a ezek soha nem közzétett tervrajzokat.

Hozzon létre, és a tervrajz szerkesztése a [az Azure portal](../create-blueprint-portal.md#create-a-blueprint) vagy [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Tervrajz közzététele

Után minden tervezett módosításokat, a tervrajz **Draft** lehet módban **közzétett** és a hozzárendelés elérhetővé. A **közzétett** a tervezet verziója nem módosítható.
Egyszer **közzétett**, a tervezet jeleníti meg, mint egy másik ikon **Draft** tervezetek, és megjeleníti a megadott verziószámot a **legújabb verzió** oszlop.

A tervrajz közzététele a [az Azure portal](../create-blueprint-portal.md#publish-a-blueprint) vagy [REST API-val](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Létrehozása és egy új verziója a tervrajz szerkesztése

A **közzétett** tervrajz verziója nem módosítható. Azonban a tervezet új verziójának a meglévő tervezet hozzá is, és igény szerint módosítani. Módosíthatja egy meglévő tervezet szerkesztéssel. Amikor új menti, most már rendelkezik-e a tervezet **közzé nem tett változások**. Ezeket a módosításokat a rendszer egy új **Draft** a tervezet verzióját.

A tervrajz szerkesztése a [az Azure portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>A tervezet új verziójának közzétételéhez

Minden egyes szerkesztett tervrajz kell lennie **közzétett** előtt is hozzárendelhető. Amikor **közzé nem tett változások** tervrajz felé, de nem **közzétett**, a **tervrajz közzététele** gomb érhető el a tervezet lap. Ha a gomb nem látható, a tervezet már hozzá lett **közzétett** és nem rendelkezik **közzé nem tett változások**.

> [!NOTE]
> Egyetlen tervezet rendelkezhet több **közzétett** verziókat, amelyek egymással előfizetésekhez rendelhető.

A tervrajz közzététele **közzé nem tett változások**, ugyanazokat a lépéseket egy új tervezet közzétételére használhatnak.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Egy adott verzióját a tervezet törlése

A tervezet egyes verzióihoz egy egyedi objektum, és külön-külön lehet **közzétett**. Emiatt a tervrajz minden verziója is törölhetők. Egy verziója a tervezet törlése nincs hatással a tervezet a más verziói.

> [!NOTE]
> Nem alkalmas, amely rendelkezik aktív hozzárendelések tervrajz törlése. Először törölje a hozzárendeléseket, és törölje az eltávolítani kívánt verzió.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki **definíciók tervezetet** az oldalról, a bal oldalon a szűrőbeállítások keresse meg a tervezet törli egy verziójának használatát. Kattintson rá a lap megnyitásához.

1. Kattintson a **közzétett verzió** lapra, és keresse meg a törölni kívánt verziót.

1. Kattintson a jobb gombbal a verzióra történő törléséhez, és válassza ki a **törli ezt a verziót**.

## <a name="deleting-the-blueprint"></a>A tervezet törlése

A core tervezet is törölhetők. A core tervezet törlése is törli a tervezet, mind az tervezet verziókat **Draft** és **közzétett** tervek. És a egy verziója a tervezet törlése a core tervezet törlése nem távolítja el a meglévő hozzárendelések a tervezet verzióinak.

> [!NOTE]
> Nem alkalmas, amely rendelkezik aktív hozzárendelések tervrajz törlése. Először törölje a hozzárendeléseket, és törölje az eltávolítani kívánt verzió.

A tervrajz törlése a [az Azure portal](../create-blueprint-portal.md#delete-a-blueprint) vagy [REST API-val](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Hozzárendelések

A tervrajz hozzárendelheti egy előfizetési életciklusa alatt számos olyan pontja van. Ha a módot a tervezet verziójához van **közzétett**, akkor azt a verziót előfizetéshez rendelhető. Ennek az életciklusnak lehetővé teszi, hogy a használt, és aktívan rendelve, amíg egy újabb verzióra még fejlesztés alatt tervrajz verzióit.

Hozzárendelt tervezetek verzióit, fontos tudni, ha hozzá vannak rendelve, és a paramétereket, hogy hozzá van rendelve. A paraméterek lehet statikus vagy dinamikus. További tudnivalókért lásd: [statikus és dinamikus paraméterek](parameters.md).

### <a name="updating-assignments"></a>Hozzárendelések frissítése

A tervezet hozzárendelésekor a hozzárendelés lehet frissíteni. A meglévő hozzárendelések módosítása több okból is beleértve:

- Hozzáadása vagy eltávolítása [erőforrás zárolása](resource-locking.md)
- Módosítsa az értéket a [dinamikus paraméterek](parameters.md#dynamic-parameters)
- A hozzárendelés frissítsen egy újabb **közzétett** a tervezet verziója

További információ [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>További lépések

- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).