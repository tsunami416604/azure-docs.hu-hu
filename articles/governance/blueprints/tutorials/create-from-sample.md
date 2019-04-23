---
title: Tervrajz mintából környezet létrehozása
description: A tervezet-minta használatával hozza létre a tervezet-definíció egy két erőforráscsoport telepítése és konfigurálása a szerepkör-hozzárendelés minden.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4f400e45d8defc304cf58c4bd05fa19f16d0501b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785966"
---
# <a name="create-an-environment-from-a-blueprint-sample"></a>Tervrajz mintából környezet létrehozása

Minta tervezetek példákkal mi teheti meg Azure-tervek használatával. Minden egyes egy adott szándékot vagy céllal rendelkező mintát, de nem a teljes környezet létrehozása önmagukban. Minden egyes szolgál kiindulópontként Ismerkedés az Azure tervek használata a csomagban foglalt összetevők, minták és paraméterek különböző kombinációit.

A következő oktatóanyagban a **RBAC erőforráscsoportok** tervezet minta bemutatására a tervek szolgáltatás különböző aspektusait. A következő lépéseket tartalmazza:

> [!div class="checklist"]
> - A minta egy új tervezetdefiníció létrehozása
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés
> - A hozzárendelés üzembe helyezett erőforrások vizsgálata
> - A Zárolás eltávolítása a tervrajz hozzárendelésének megszüntetése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-definition-from-sample"></a>Tervezetdefiníció létrehozása mintából

Először a tervezet mintát valósítja meg. Egy új tervezet importálása hoz létre a környezetben, a minta alapján.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **RBAC erőforráscsoportok** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a tervezet-minta a példányának nevét. Ebben az oktatóanyagban a nevét használjuk _két-rgs-az-szerepkör-hozzárendelések_.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport vagy az előfizetés a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Ez a minta meghatároz két erőforráscsoport, a megjelenítendő nevének _ProdRG_ és _PreProdRG_. Tervezet-hozzárendelés során végső nevét és helyét az egyes erőforráscsoportokban vannak beállítva. A _ProdRG_ erőforráscsoport hozzá van rendelve a _közreműködői_ szerepkör és a _PreProdRG_ erőforráscsoport hozzá van rendelve a _tulajdonosa_ és  _Olvasói_ szerepköröket. A szerepkör a definícióban hozzárendelve a statikus, de a felhasználó, alkalmazás vagy csoport, amely hozzá van rendelve a szerepkör beállítása során a tervezet-hozzárendelést.

1. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

Ebben a lépésben a minta tervezetdefiníció másolatot hoz létre a kiválasztott felügyeleti csoportba vagy előfizetésbe. A mentett tervrajz-definíció bármilyen újonnan tervezet módon kezelhetők. A minta lehet, hogy menti a felügyeleti csoport vagy az előfizetéshez, ahányszor szükséges. Azonban minden példány egy egyedi nevet kell adni.

Miután a **mentése sikerült a tervezetdefiníció** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet-minta a másolat a környezetben, és igényeinek megfelelően testre szabható. Ebben az oktatóanyagban azt nem végezze el a módosításokat.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a _két-rgs-az-szerepkör-hozzárendelések_ tervezetet definícióját, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. Adja meg az új panelen a jobb oldalon, **verzió** , _1.0_ a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója közzétett RBAC tervezet mintát tartalmazó erőforráscsoportokat." Válassza ki **közzététel** az oldal alján.

Ez a lépés lehetővé teszi a tervezet hozzárendelése egy előfizetést. Közzététele után továbbra is módosíthatók. További módosításokat kell közzétenni egy új **verzió** értéket nyomon követéséhez az azonos tervezetdefiníció különböző verziói közötti különbségeket.

Egyszer a **közzététel sikerült definíció tervezetet** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="assign-the-sample-copy"></a>A minta másolási hozzárendelése

Miután a tervezet minta másolata sikeresen megtörtént **közzétett**, előfizetésre történő mentése felügyeleti csoporton belül is hozzárendelhető. Ez a lépés nem, melyekben a paraméterek vannak-e adva az, hogy az egyes telepítések a példány a tervezet minta egyedi.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a _két-rgs-az-szerepkör-hozzárendelések_ tervezetet definícióját, és válassza ki azt.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több az előfizetéseket, amelyek a felügyeleti csoportban, a tervezet minta másolatának mentése. Ha egynél több előfizetéssel, hozzárendelést a megadott paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: Az Ön a tervrajz-definíció neve alapján előre megadott név.
     - **Hely**: Válassza ki a régiót, a felügyelt identitás kell létrehozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../active-directory/managed-identities-azure-resources/overview.md).
       A jelen oktatóanyag esetében válassza ki a _USA keleti RÉGIÓJA 2_.
     - **Tervrajz-definíció verziója**: Válassza ki a **közzétett** verzió _1.0_ a minta tervezetdefiníció példány.

   - Hozzárendelés zárolása

     Válassza ki a _csak olvasható_ tervezet zárolási üzemmódban. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ lehetőséget. További információkért lásd: [felügyelt identitások](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Összetevő paraméterei

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. Minden egyes összetevő, állítsa a paraméter értéke határozzák meg a a **érték** oszlop. A `{Your ID}`, válassza ki az Azure felhasználói fiókot.

     |Elemnév|Összetevő típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |ProdRG resource group|Erőforráscsoport|Name (Név)|ProductionRG|Meghatározza az első erőforráscsoport nevét.|
     |ProdRG resource group|Erőforráscsoport|Földrajzi egység|USA nyugati régiója, 2.|Beállítja az első erőforráscsoport helyét.|
     |Közreműködő|Szerepkör-kijelölés|Felhasználó vagy csoport|{ID}|Határozza meg, mely felhasználó vagy csoport megadását a _közreműködői_ szerepkör-hozzárendelés az első erőforráscsoporton belül.|
     |PreProdRG resource group|Erőforráscsoport|Name (Név)|PreProductionRG|Meghatározza a második erőforráscsoport nevét.|
     |PreProdRG resource group|Erőforráscsoport|Földrajzi egység|USA nyugati régiója|A második erőforráscsoport helyét is beállítja.|
     |Tulajdonos|Szerepkör-kijelölés|Felhasználó vagy csoport|{ID}|Határozza meg, mely felhasználó vagy csoport megadását a _tulajdonosa_ szerepkör-hozzárendelés a második erőforráscsoporton belül.|
     |Olvasók|Szerepkör-kijelölés|Felhasználó vagy csoport|{ID}|Határozza meg, mely felhasználó vagy csoport megadását a _olvasók_ szerepkör-hozzárendelés a második erőforráscsoporton belül.|

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján.

Ebben a lépésben üzembe helyezi a definiált erőforrásokat, és konfigurálja a kijelölt **zárolási hozzárendelés**. Tervrajz zárolások alkalmazásához akár 30 percet is igénybe vehet.

Miután a **sikeres tervezetdefiníció hozzárendelése** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A tervezet-hozzárendelést hoz létre, és nyomon követi a tervezet-definícióban meghatározott összetevők. Láthatjuk, hogy az erőforrások a tervezet-hozzárendelés oldaláról, és közvetlenül megnézzük az erőforrások állapotát.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a _Assignment-two-rgs-with-role-assignments_ tervezetet hozzárendelést, és válassza ki azt.

   Ezen az oldalon láthatjuk a hozzárendelés sikerült, és a tervezet zárolási állapot együtt létrehozott erőforrások listájában. A hozzárendelés frissül, ha a **hozzárendelési művelet** legördülő minden definíció verzió az üzembe helyezéssel kapcsolatos részleteket jeleníti meg. Egyes listázott erőforrások létrehozásának, ha rájuk kattint, és megnyílik az adott erőforrás-tulajdonság lapon.

1. Válassza ki a **ProductionRG** erőforráscsoportot.

   Láthatjuk, hogy az erőforráscsoport neve nem **ProductionRG** , és nem az összetevő megjelenített neve _ProdRG_. Ez a neve megegyezik a tervezet-hozzárendelés során beállított értéket.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, majd a **szerepkör-hozzárendelések** fülre.

   Itt láthatjuk, hogy a fiók megkapta a _közreműködői_ szerepkör hatóköre az _ehhez az erőforráshoz_. A _Assignment-two-rgs-with-role-assignments_ tervezet-hozzárendelést rendelkezik a _tulajdonosa_ szerepkör, mert az erőforráscsoport létrehozásához használt. Ezeket az engedélyeket is használhatók az konfigurált tervezet zárolásokkal-erőforrások kezeléséhez.

1. Válassza ki az Azure portal webhely-navigációs, **Assignment-two-rgs-with-role-assignments** , lépjen vissza a egy oldallal, majd válassza ki a **PreProductionRG** erőforráscsoportot.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, majd a **szerepkör-hozzárendelések** fülre.

   Itt láthatjuk, hogy a fiók megkapta-e mind a _tulajdonosa_ és _olvasó_ szerepkörök, mindkét szolgáltatás hatókörének _ehhez az erőforráshoz_. A tervezet-hozzárendelést is rendelkezik a _tulajdonosa_ mint az első erőforráscsoportot.

1. Válassza ki a **hozzárendelések megtagadása** fülre.

   A tervezet-hozzárendelést, létrehozott egy [hozzárendelés megtagadása](../../../role-based-access-control/deny-assignments.md) kényszerítése a telepített erőforráscsoportot, amelybe a a _csak olvasható_ tervezet zárolási mód. A megtagadási hozzárendelés megakadályozza, hogy valaki megfelelő jogosultságokkal a a _szerepkör-hozzárendelések_ bizonyos műveleteket, fülre. A megtagadási hozzárendelés befolyásolja _összes rendszerbiztonsági tag_.

1. Válassza ki a Megtagadás hozzárendelést, majd válassza ki a **megtagadva engedélyek** a bal oldalon.

   A megtagadási hozzárendelés megakadályozza, hogy az összes műveletet a **\*** és **művelet** konfiguráció, de lehetővé teszi, hogy olvasási hozzáférés kizárásával  **\* /olvasási**keresztül **NotActions**.

1. Válassza ki az Azure portal webhely-navigációs, **PreProductionRG - hozzáférés-vezérlés (IAM)**. Válassza ki a **áttekintése** a bal oldalon, majd a **erőforráscsoport törlése** gombra. Adja meg a nevét _PreProductionRG_ a delete és select **törlése** a panel alján.

   A portál értesítései **PreProductionRG sikertelen erőforráscsoport törlése** jelenik meg. A hiba-állapotokat, amely a fiók rendelkezik engedéllyel az erőforráscsoport törlése közben megtagadja a hozzáférést a tervezet-hozzárendelést. Ne feledje, hogy kiválasztott a _csak olvasható_ tervezet zárolási mód tervezet-hozzárendelés során. A tervezet zárolása megakadályozza, hogy, engedéllyel rendelkező fiók még _tulajdonosa_, az erőforrás törlését. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések bemutatják, hogy az erőforrások létrejöttek meghatározottak szerint, és a tervezet zárolása ebben az esetben a nemkívánatos, amelytől a törlés, még akkor is engedéllyel rendelkező fiók.

## <a name="unassign-the-blueprint"></a>A tervrajz hozzárendelésének megszüntetése

Az utolsó lépés, hogy a tervezet és az erőforrások, központilag telepített a hozzárendelés eltávolítása.
A hozzárendelés eltávolítása nem távolítja el a telepített összetevők.

1. Válassza ki **minden szolgáltatás** a bal oldali panelen. Keresse meg és válassza **tervezetek**.

1. Válassza ki a **hozzárendelt tervezetek** a bal oldalon. A szűrők segítségével keresse meg a _Assignment-two-rgs-with-role-assignments_ tervezetet hozzárendelést, és válassza ki azt.

1. Válassza ki a **kijelölés megszüntetése tervezet** gombra a lap tetején. Olvassa el a figyelmeztetést a megerősítő párbeszédpanelen, majd válassza a **OK**.

   Az eltávolított tervezet-hozzárendelést a tervezet zárolása is el lesznek távolítva. A létrehozott erőforrásokat ismét engedélyekkel rendelkező fiók lehet törölni.

1. Válassza ki **erőforráscsoportok** az Azure menüjében, majd válassza ki **ProductionRG**.

1. Válassza ki a **hozzáférés-vezérlés (IAM)** a bal oldalon, majd a **szerepkör-hozzárendelések** fülre.

Az egyes erőforráscsoportokra vonatkozó biztonsági továbbra is rendelkezik a telepített szerepkör-hozzárendeléseket, de már nem rendelkezik a tervezet-hozzárendelést _tulajdonosa_ hozzáférést.

Miután a **eltávolítása tervezet-hozzárendelés sikerült** portál értesítés jelenik meg, helyezze át a következő lépéssel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag befejezésekor törölje az alábbi forrásanyagokat:

- Erőforráscsoport _ProductionRG_
- Erőforráscsoport _PreProductionRG_
- Definíció tervezetet _két-rgs-az-szerepkör-hozzárendelések_

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md)
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md)