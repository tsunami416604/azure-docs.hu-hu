---
title: 'Oktatóanyag: Blueprint-minta az új környezetbe'
description: Ebben az oktatóanyagban egy tervezetminta használatával hozzon létre egy tervezetdefiníciót, amely két erőforráscsoportot állít be, és mindegyikhez beállít egy szerepkör-hozzárendelést.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: c4230282223b0a64f6254448fe069bf8f7ab9a15
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80282018"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Oktatóanyag: Környezet létrehozása tervrajzmintából

Minta tervrajzok példákat, hogy mit lehet tenni az Azure Blueprints használatával. Mindegyik egy adott szándékkal vagy céllal rendelkező minta, de nem hoz létre teljes környezetet önmagukban. Mindegyik kiindulópontként szolgál az Azure Blueprints különböző összetevők, tervek és paraméterek kombinációjával történő felfedezéséhez.

A következő oktatóanyag az **Erőforráscsoportok RBAC-tervezetminta** segítségével mutatja be a Blueprints szolgáltatás különböző aspektusait. A következő lépések relevankadnak:

> [!div class="checklist"]
> - Új tervezetdefiníció létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez
> - Az üzembe helyezett erőforrások vizsgálata a hozzárendeléshez
> - A zárolások eltávolításához rendelje vissza a tervrajzot

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-blueprint-definition-from-sample"></a>Tervrajz-definíció létrehozása mintából

Először hajtsa végre a tervmintát. Importálása létrehoz egy új tervrajzot a környezetben a minta alapján.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg az **Erőforrás-csoportok RBAC** tervezet minta _alatt Egyéb minták,_ és válassza ki.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve:** Adja meg a tervezet minta példányának nevét. Ebben az oktatóanyagban a _két rgs nevet használjuk a szerepkör-hozzárendelésekkel._
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot vagy előfizetést a minta példányának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Ez a minta két erőforráscsoportot határoz meg, _a ProdRG_ és a _PreProdRG_megjelenítendő neveivel. Az egyes erőforráscsoportok végső neve és helye a tervezethozzárendelés során van beállítva. A _ProdRG_ erőforráscsoport a _közreműködői_ szerepkörhöz, a _PreProdRG_ erőforráscsoport pedig a _Tulajdonos_ és _az Olvasók_ szerepkörhöz van rendelve. A definícióban rendelt szerepkörök statikusak, de a szerepkörhöz rendelt felhasználó, alkalmazás vagy csoport a tervhozzárendelés során van beállítva.

1. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

Ez a lépés létrehoz egy másolatot a minta tervezet definíciója a kiválasztott felügyeleti csoportban vagy előfizetésben. A mentett tervezet definíciója úgy van kezelve, mint bármely teljesen új, mindentől létrehozott tervrajz. A mintát annyiszor mentheti a felügyeleti csoportba vagy előfizetésbe, ahányszor csak szükséges. Minden példánynak azonban egyedi nevet kell adni.

Miután a **mentési tervezet definíciója sikeres** portál értesítés jelenik meg, lépjen a következő lépésre.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezet minta példánya testreszabható a környezet és az igények igényeinek megfelelően. Ebben az oktatóanyagban nem fogunk módosításokat végrehajtani.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a _két rgs-with-role-hozzárendelések tervdefinícióját,_ majd válassza ki azt.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új ablaktáblában adja meg a verziót _1.0-s_ **verzióként** a tervezetminta példányához. Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **meg módosítási megjegyzéseket,** például "Az erőforráscsoportokból közzétett első verzió RBAC-tervezetmintával." Ezután válassza a **Közzététel** lehetőséget a lap alján.

Ez a lépés lehetővé teszi, hogy a tervezet egy előfizetéshez. A közzétételt követően a módosítások továbbra is elláthatók. További módosítások at közzétételre van szükség egy új **verzióértékkel** az ugyanazon tervezetdefiníció különböző verziói közötti különbségek nyomon követéséhez.

Miután a **közzétételi tervezet definíciója sikeresportálértesítés** jelenik meg, lépjen a következő lépésre.

## <a name="assign-the-sample-copy"></a>A mintapéldány hozzárendelése

Miután a tervezetminta példánya sikeresen **közzélett,,** hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve mentették. Ez a lépés az, ahol paraméterek et biztosítanak, hogy a tervezet minta másolatának minden egyes központi telepítése egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a _két rgs-with-role-hozzárendelések tervdefinícióját,_ majd válassza ki azt.

1. Válassza **a Blueprint hozzárendelése** lehetőséget a tervezetdefiníciós lap tetején.

1. Adja meg a tervezet hozzárendelésének paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések:** Válasszon ki egy vagy több előfizetést, amely abban a felügyeleti csoportban van, amelybe a tervezetminta másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez egy-egy hozzárendelés jön létre a megadott paraméterek használatával.
     - **Hozzárendelés neve**: A név előre ki van töltve a tervezet definíciója alapján.
     - **Hely**: Válassza ki azt a régiót, amelyben a felügyelt identitást létre szeretné hozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Ehhez az oktatóanyaghoz válassza _az USA keleti része 2_lehetőséget.
     - **Blueprint-definíciós verzió:** Válassza ki a **közzétett** _1.0-s_ verziója a mintatervezet definíciója.

   - Hozzárendelés zárolása

     Válassza az _Írásvédett_ tervzárolási módot. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _Rendszer hozzárendelt_ beállítást. További információt a felügyelt identitások című [témakörben talál.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. Minden egyes műtermékhez állítsa a paraméter értékét az **Érték** oszlopban megadott értékre. A `{Your ID}`területen válassza ki az Azure-felhasználói fiókját.

     |Műtermék neve|Műtermék típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |ProdRG erőforráscsoport|Erőforráscsoport|Név|TermelésRG|Az első erőforráscsoport nevét határozza meg.|
     |ProdRG erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója, 2.|Beállítja az első erőforráscsoport helyét.|
     |Közreműködő|Szerepkör-kijelölés|Felhasználó vagy csoport|{Azonosítója}|Azt határozza meg, hogy melyik felhasználó vagy csoport adja meg a _közreműködői_ szerepkör-hozzárendelést az első erőforráscsoporton belül.|
     |PreProdRG erőforráscsoport|Erőforráscsoport|Név|Gyártás előttiRG|A második erőforráscsoport nevét határozza meg.|
     |PreProdRG erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója|Beállítja a második erőforráscsoport helyét.|
     |Tulajdonos|Szerepkör-kijelölés|Felhasználó vagy csoport|{Azonosítója}|Azt határozza meg, hogy a _tulajdonos_ szerepkör-hozzárendelést melyik felhasználónak vagy csoportnak adja meg a második erőforráscsoporton belül.|
     |Olvasók|Szerepkör-kijelölés|Felhasználó vagy csoport|{Azonosítója}|Azt határozza meg, hogy a második erőforráscsoporton belül melyik felhasználónak vagy csoportnak adja meg az _Olvasók_ szerepkör-hozzárendelést.|

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján.

Ez a lépés telepíti a definiált erőforrásokat, és konfigurálja a kijelölt **Zárolási hozzárendelést.** A tervrajzok zárolása akár 30 percet is igénybe vehet.

Miután a **hozzárendelési tervezet definíciója sikerült** portál értesítés jelenik meg, lépjen a következő lépésre.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A tervrajz-hozzárendelés létrehozza és nyomon követi a tervezet definíciójában definiált összetevőket. Láthatjuk az erőforrások állapotát a tervezet hozzárendelési oldalon, és az erőforrások közvetlen megtekintésével.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Hozzárendelt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-két rgs-with-role-hozzárendelések_ terv hozzárendelés, majd jelölje ki azt.

   Ezen a lapon láthatjuk, hogy a hozzárendelés sikeres volt, és a létrehozott erőforrások listája a tervezet zárolási állapotával együtt. Ha a hozzárendelés frissül, a **Hozzárendelési művelet** legördülő menü az egyes definíciós verziók központi telepítésével kapcsolatos részleteket jeleníti meg. Minden létrehozott felsorolt erőforrásra kattinthat, és megnyitja az erőforrás tulajdonságlapot.

1. Válassza ki a **ProductionRG** erőforráscsoportot.

   Látjuk, hogy az erőforráscsoport neve **ProductionRG,** és nem a műtermék megjelenítendő _neve ProdRG_. Ez a név megegyezik a tervezet hozzárendelése során beállított értékkel.

1. Válassza a **hozzáférés-vezérlés (IAM)** lapot a bal oldalon, majd a **Szerepkör-hozzárendelések** lapot.

   Itt azt látjuk, hogy a fiók megkapta a _közreműködői_ szerepkört a hatálya alá _tartozó Ez_az erőforrás . A _hozzárendelés-két rgs-with-role-hozzárendelések_ terv hozzárendelés a _tulajdonos_ szerepkör, ahogy az erőforráscsoport létrehozásához használták. Ezek az engedélyek is használható erőforrások kezelésére konfigurált tervezet zárolások.

1. Az Azure Portalon a zsemlemorzsa, válassza **a hozzárendelés-két rgs-with-role-hozzárendelések** egy oldallal visszalép, majd válassza ki a **PreProductionRG** erőforráscsoport.

1. Válassza a **hozzáférés-vezérlés (IAM)** lapot a bal oldalon, majd a **Szerepkör-hozzárendelések** lapot.

   Itt azt látjuk, hogy a fiók megkapta mind a _tulajdonos_ és _az olvasó_ szerepkörök, mind a hatálya alá ez az _erőforrás_. A tervezet hozzárendelés is rendelkezik a _tulajdonos_ szerepkör, mint az első erőforráscsoport.

1. Válassza a **Hozzárendelések megtagadása** lapot.

   A tervezet-hozzárendelés létrehozott egy [megtagadási hozzárendelést](../../../role-based-access-control/deny-assignments.md) az üzembe helyezett erőforráscsoporton az _írásvédett_ tervezet zárolási mód kényszerítéséhez. A megtagadási hozzárendelés megakadályozza, hogy a _szerepkör-hozzárendelések_ lapon megfelelő jogosultságokkal rendelkező személy meghatározott műveleteket hajtson végre. A megtagadási hozzárendelés _az összes megbízóra_hatással van.

1. Jelölje ki a megtagadási **hozzárendelést,** majd a bal oldalon a Megtagadott engedélyek lapot.

   A megtagadási hozzárendelés megakadályozza az **\*** összes műveletet a és a **művelet** konfigurációval, de lehetővé teszi az olvasási hozzáférést a ** \*/read** via **NotActions**kizárásával.

1. Az Azure Portalon a zsemlemorzsa válassza **preProductionRG - hozzáférés-vezérlés (IAM)** lehetőséget. Ezután válassza az **Áttekintés** lapot a bal oldalon, majd az **Erőforráscsoport törlése** gombot. Írja be a _PreProductionRG_ nevet a törlés megerősítéséhez, és válassza a **Törlés** lehetőséget az ablaktábla alján.

   Megjelenik a portálértesítés **A PreProductionRG erőforráscsoport törlése sikertelen.** A hiba azt állítja, hogy bár a fiók rendelkezik az erőforráscsoport törlésére, a hozzáférést a tervezet hozzárendelése megtagadja. Ne feledje, hogy az _írásvédett_ tervrögzítési módot választottuk a tervleési hozzárendelés során. A tervezet zárolása megakadályozza, hogy egy engedéllyel rendelkező fiók , még _a Tulajdonos_is , de az erőforrás törlése. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések azt mutatják, hogy az erőforrások at meghatározott, és a tervezet zárolások megakadályozta a nem kívánt törlés, még egy fiókot engedéllyel rendelkező.

## <a name="unassign-the-blueprint"></a>A tervrajz hozzárendelésének visszavonása

Az utolsó lépés a terv és az általa üzembe helyezett erőforrások hozzárendelésének eltávolítása.
A hozzárendelés eltávolítása nem távolítja el az üzembe helyezett összetevőket.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **Hozzárendelt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-két rgs-with-role-hozzárendelések_ terv hozzárendelés, majd jelölje ki azt.

1. A lap tetején válassza a **Tervezet hozzárendelésének visszavonása** gombot. Olvassa el a figyelmeztetést a megerősítést kérő párbeszédpanelen, majd kattintson az **OK gombra.**

   A tervrajz-hozzárendelés eltávolításával a tervrajz zárolásait is eltávolítja. A létrehozott erőforrásokat ismét törölheti egy engedéllyel rendelkező fiók.

1. Válassza **az Erőforráscsoportok lehetőséget** az Azure menüből, majd válassza a **ProductionRG parancsot.**

1. Válassza a **hozzáférés-vezérlés (IAM)** lapot a bal oldalon, majd a **Szerepkör-hozzárendelések** lapot.

Az egyes erőforráscsoportok biztonsága továbbra is rendelkezik az üzembe helyezett szerepkör-hozzárendelésekkel, de a tervezet-hozzárendelés már nem rendelkezik _tulajdonosi_ hozzáféréssel.

Miután a **tervhozzárendelés eltávolítása sikeres** portálértesítés jelenik meg, lépjen a következő lépésre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az oktatóanyagot, törölje a következő erőforrásokat:

- Termelési _RG_ erőforráscsoport
- _Erőforráscsoport PreProductionRG_
- Tervrajz _definíciója két rgs-with-szerep-hozzárendelések_

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre új tervrajzot egy mintadefinícióból. Ha többet szeretne megtudni az Azure Blueprints, folytassa a blueprint életciklus-cikk.

> [!div class="nextstepaction"]
> [További információ a tervterv életciklusáról](../concepts/lifecycle.md)