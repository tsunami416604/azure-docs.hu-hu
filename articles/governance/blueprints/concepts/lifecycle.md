---
title: A tervrajzok életciklusának megismerése
description: Ismerje meg, hogy a tervdefiníció életciklusa és az egyes fázisok részletei, beleértve a tervrajzok-hozzárendelések frissítését és eltávolítását.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 46da42ec97f16af91ef8e41362a085d466bbb887
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677371"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Az Azure Blueprint életciklusának megismerése

Az Azure-beli erőforrásokhoz hasonlóan az Azure Blueprints tervrajzai is rendelkeznek egy tipikus és természetes életciklussal. Ezek létrehozása, üzembe helyezése, és végül törlődik, ha már nincs szükség, vagy releváns. Az Azure Blueprints támogatja a szabványos életciklus-műveleteket. Ezután épít rájuk, hogy további szintű állapot, amely támogatja a közös folyamatos integráció és a folyamatos üzembe helyezési folyamatok szervezetek számára, amelyek kezelik az infrastruktúra kódként – kulcsfontosságú eleme a DevOps.

A tervrajzok és a szakaszok teljes megértéséhez egy szabványos életciklust fedjük le:

> [!div class="checklist"]
> - Tervrajz létrehozása és szerkesztése
> - A tervrajz közzététele
> - A tervrajz új verziójának létrehozása és szerkesztése
> - A tervrajz új verziójának közzététele
> - A tervrajz egy adott verziójának törlése
> - A tervrajz törlése

## <a name="creating-and-editing-a-blueprint"></a>Tervrajz létrehozása és szerkesztése

Tervrajz létrehozásakor adjon hozzá összetevőket, mentse egy felügyeleti csoportba vagy előfizetésbe, és adjon meg egy egyedi nevet és egy egyedi verziót. A tervrajz most **vázlat** módban van, és még nem rendelhető hozzá. A **Vázlat** módban továbbra is frissíthető és módosítható.

**A draft** módban soha nem közzétett tervezet ekkon konikál a **Blueprint Definitions** oldalon, mint a **közzétett.** A **legújabb verzió** **jelenik meg piszkozatként** ezekhez a soha nem közzétett tervrajzok.

Hozzon létre és szerkesztsen egy tervezetet az [Azure Portalon](../create-blueprint-portal.md#create-a-blueprint) vagy a [REST API-val.](../create-blueprint-rest-api.md#create-a-blueprint)

## <a name="publishing-a-blueprint"></a>Tervrajz közzététele

Miután az összes tervezett módosítás **elkészült** egy vázlat tervezetében Vázlat módban, **közzétehető** és hozzárendelésre elérhetővé tehető. A tervrajz **közzétett** verziója nem módosítható. A **közzététel után**a tervezet a Tervezet **tervezetétől** eltérő ikonnal jelenik meg, és a megadott verziószámot a Legújabb verzió oszlopban jeleníti **meg.**

Készítsen közzé egy tervezetet az [Azure Portalon](../create-blueprint-portal.md#publish-a-blueprint) vagy a [REST API-ban.](../create-blueprint-rest-api.md#publish-a-blueprint)

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>A tervrajz új verziójának létrehozása és szerkesztése

A tervrajz **közzétett** változata nem módosítható. A tervezet új verziója azonban hozzáadható a meglévő tervrajzhoz, és szükség szerint módosítható. Szerkesztéssel módosíthatja a meglévő tervrajzokat. Az új módosítások mentésekor a tervezet **közzé nem tett módosításokkal rendelkezik.** Ezek a módosítások **Draft** a tervezet új vázlatváltozatai.

A tervrajzok szerkesztése az [Azure Portalon.](../create-blueprint-portal.md#edit-a-blueprint)

## <a name="publishing-a-new-version-of-the-blueprint"></a>A tervrajz új verziójának közzététele

A tervezet minden szerkesztett változatát **közzé** kell tenni a hozzárendelés előtt. Ha **nem közzétett módosítások történtek** egy tervrajzon, de nem lettek **közzétéve,** a **Blueprint közzététele** gomb elérhető a szerkesztési tervlapon. Ha a gomb nem látható, a tervrajz már közzé lett **téve,** és nincs **közzé nem tett módosítás.**

> [!NOTE]
> Egyetlen tervezet több **közzétett** verzióval rendelkezhet, amelyek mindegyike előfizetésekhez rendelhető.

Ha közzé szeretne tenni egy tervezetet **a közzé nem tett módosításokkal,** kövesse ugyanazokat a lépéseket az új tervtervezet közzétételéhez.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>A tervrajz egy adott verziójának törlése

A tervezet minden verziója egyedi objektum, és külön-külön **közzétehető.** Így a tervezet minden verziója is törölhető. A tervrajz egy verziójának törlése nincs hatással a terv más verzióira.

> [!NOTE]
> Nem lehet törölni egy aktív hozzárendeléseket tartalmazó tervrajzot. Először törölje a hozzárendeléseket, majd törölje az eltávolítani kívánt verziót.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint-definíciókat** a bal oldali lapon, és a szűrőbeállításokkal keresse meg azt a tervrajzot, amelynek egy verzióját törölni szeretné. Jelölje ki a szerkesztési lap megnyitásához.

1. Válassza a **Közzétett verziók** lapot, és keresse meg a törölni kívánt verziót.

1. Kattintson a jobb gombbal a törölni kívánt verzióra, és válassza **a Verzió törlése parancsot.**

## <a name="deleting-the-blueprint"></a>A tervrajz törlése

A magterv is törölhető. Az alapterv törlése a tervtervezet tervrajzának minden verzióverzióját törli, beleértve a **Vázlat** és **a Közzétett** tervrajzokat is. A tervrajzok verziójának törléséhez is a fő tervrajz törlése nem távolítja el a tervezetverziók meglévő hozzárendeléseit.

> [!NOTE]
> Nem lehet törölni egy aktív hozzárendeléseket tartalmazó tervrajzot. Először törölje a hozzárendeléseket, majd törölje az eltávolítani kívánt verziót.

Tervezet törlése az [Azure Portalon](../create-blueprint-portal.md#delete-a-blueprint) vagy a [REST API-val.](../create-blueprint-rest-api.md#delete-a-blueprint)

## <a name="assignments"></a>Hozzárendelések

Az életciklus során több pont is van, amelyhez egy előfizetéshez hozzárendelhető egy tervrajz. Ha a tervezet verziójának módja **közzétéve**van, akkor ez a verzió hozzárendelhető egy előfizetéshez. Ez az életciklus lehetővé teszi a tervrajzok verzióinak használható és aktív hozzárendelését egy újabb verzió fejlesztése közben.

A tervrajzok verzióinak hozzárendelése ként fontos megérteni, hogy hol vannak hozzárendelve, és milyen paraméterekkel vannak hozzárendelve. A paraméterek lehetnek statikusak vagy dinamikusak. További információ: [statikus és dinamikus paraméterek.](parameters.md)

### <a name="updating-assignments"></a>Hozzárendelések frissítése

Tervrajz hozzárendelésekor a hozzárendelés frissíthető. Egy meglévő hozzárendelés frissítésének több oka is van, például:

- [Erőforrás-zárolás](resource-locking.md) hozzáadása vagy eltávolítása
- Dinamikus [paraméterek](parameters.md#dynamic-parameters) értékének módosítása
- A hozzárendelés frissítése a terv terv újabb **közzétett** verziójára

Ebből megtudhatja, hogyan, lásd: [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Hozzárendelések hozzárendelésének lemondott

Ha a tervezet már nincs szükség, akkor a felügyeleti csoport vagy az előfizetés nem rendelhető hozzá. A tervezet hozzárendelésének leírása során a következő a következő:

- A [tervezet erőforrászárolásának](resource-locking.md) eltávolítása
- A tervrajz-hozzárendelési objektum törlése
- (Feltételes) Ha **rendszeráltal hozzárendelt felügyelt identitást** használt, akkor azt is törli

> [!NOTE]
> A blueprint-hozzárendelés által üzembe helyezett összes erőforrás a helyén marad, de az Azure Blueprints már nem védi.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.