---
title: Oktatóanyag – az Azure költségeinek csökkentése javaslatokkal
description: Ez az oktatóanyag segítséget nyújt az Azure-költségek csökkentésében az optimalizálási javaslatok elvégzése során.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 37253bb4c6001afe436e22597e75e2bc869fbbc8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990293"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Oktatóanyag: költségek optimalizálása a javaslatokból

Az Azure Cost Management az Azure Advisor segítségével tesz költségoptimalizálási javaslatokat. Az Azure Advisor a tétlen és kihasználatlan erőforrások azonosításával segítséget nyújt a hatékonyság optimalizálásában és javításában. Ez az oktatóanyag végigvezeti a kihasználatlan Azure-erőforrások azonosítására szolgáló példán, és a költségek csökkentése érdekében hajt végre műveleteket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A lehetséges használati elégtelenségek megtekintéséhez a Cost Optimization javaslatainak megtekintése
> * A virtuális gépek költséghatékonyabb megoldásra való átméretezésére vonatkozó javaslat
> * Ellenőrizze a műveletet, hogy a virtuális gép átméretezése sikeres legyen

## <a name="prerequisites"></a>Előfeltételek
A javaslatok számos hatókörhöz és Azure-fiókhoz érhetők el. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez olvasási jogosultsággal kell rendelkeznie a következő hatókörök legalább egyikében: További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

- Előfizetés
- Erőforráscsoport

Legalább 14 napos tevékenységgel rendelkező aktív virtuális gépekkel kell rendelkeznie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="view-cost-optimization-recommendations"></a>A Cost Optimization javaslatainak megtekintése

Az előfizetéshez tartozó költségtakarékos javaslatok megtekintéséhez nyissa meg a kívánt hatókört a Azure Portalon, és válassza az **Advisor-javaslatok**elemet.

Egy felügyeleti csoportra vonatkozó javaslatok megtekintéséhez nyissa meg a kívánt hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. A **hatókör** -pirula használatával váltson át egy másik hatókörre, például egy felügyeleti csoportra. Válassza az **Advisor-javaslatok** lehetőséget a menüben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

![Cost Management Advisor-javaslatok a Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A javaslatok listája azonosítja a használati elégtelenségeket, vagy olyan vásárlási javaslatokat mutat be, amelyek segítségével további pénzt takaríthat meg. Az **évenként megtakarított teljes potenciális megtakarítás** azt a teljes mennyiséget mutatja, amelyet menthet, ha leállítja vagy felszabadítja az összes olyan virtuális gépet, amely megfelel az ajánlási szabályoknak. Ha nem szeretné leállítani őket, érdemes átméreteznie őket egy kevésbé költséges VM SKU-ra.

A **hatás** kategóriája, valamint az **éves megtakarítások lehetséges**célja, hogy könnyebben azonosíthatók legyenek azok a javaslatok, amelyekkel a lehető legnagyobb mértékben menthetők.

A nagy hatású javaslatok a következők:
- [Fenntartott virtuálisgép-példányok vásárlása az utólagos elszámolású költségek megtakarítása érdekében](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [A virtuális gépek a kihasználatlan példányok átméretezésével vagy leállításával optimalizálható](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Managed Disks-Pillanatképek tárolása a standard Storage használatával](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Közepes hatású javaslatok a következők:
- [Sikertelen Azure Data Factory folyamatok törlése](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Csökkentse a költségeket a nem kiépített ExpressRoute-áramkörök eltávolításával](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Csökkentse a költségeket a tétlen virtuális hálózati átjárók törlésével vagy újrakonfigurálásával](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Javaslat készítése

A Azure Advisor hét napig figyeli a virtuális gépek használatát, majd azonosítja a kihasználatlan virtuális gépeket. Azok a virtuális gépek, amelyek CPU-kihasználtsága öt százalék vagy kevesebb, és a hálózati használat hét MB vagy kevesebb, mint négy nap, alacsony kihasználtságú virtuális gépeknek számít.

Az alapértelmezett érték 5% vagy kevesebb CPU-kihasználtság beállítása, de a beállítások módosíthatók. A beállítás módosításával kapcsolatos további információkért tekintse meg az [átlagos CPU-kihasználtsági szabály konfigurálása vagy az alacsony kihasználtságú virtuális gép javaslata](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)című témakört.

Bár egyes forgatókönyvek alacsony kihasználtságot eredményezhetnek a tervezéssel, gyakran pénzt takaríthat meg a virtuális gépek méretének a kevésbé költséges méretekre való megváltoztatásával. Ha átméretezési műveletet választ, a tényleges megtakarítás változhat. Lássunk egy példát a virtuális gép átméretezésére.

A javaslatok listájában kattintson a **megfelelő méretű vagy leállítási kihasználatlan virtuális gépekre** vonatkozó javaslatra. A virtuálisgép-jelöltek listájában válassza ki az átméretezni kívánt virtuális gépet, majd kattintson a virtuális gépre. A virtuális gép adatai megjelennek, hogy ellenőrizni tudja a kihasználtsági metrikákat. A **lehetséges éves megtakarítási** érték a virtuális gép leállításakor vagy eltávolításakor menthető. A virtuális gépek átméretezése valószínűleg pénzt takarít meg, de nem fogja menteni a lehetséges éves megtakarítás teljes mennyiségét.

![Példa a javaslat részleteire](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

A virtuális gép részletei között ellenőrizze, hogy a virtuális gép kihasználtsága megfelelő átméretezési jelölt-e.

![Példa a virtuális gép korábbi kihasználtságát ábrázoló részletekre](./media/tutorial-acm-opt-recommendations/vm-details.png)

Jegyezze fel a virtuális gép aktuális méretét. Miután meggyőződött arról, hogy a virtuális gépet át kell méretezni, a virtuális gép részleteinek bezárásával tekintse meg a virtuális gépek listáját.

A leállítani vagy átméretezni kívánt jelöltek listájában válassza a * * *&lt;FromVirtualMachineSKU* átméretezése&gt;a *&lt;ToVirtualMachineSKU&gt;* * * elemet.
![példa a virtuális gép átméretezésére szolgáló lehetőségre](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ezután megjelenik az elérhető átméretezési lehetőségek listája. Válassza ki azt a lehetőséget, amely a legjobb teljesítményt és költséghatékonyságot biztosítja a forgatókönyvhöz. A következő példában a kiválasztott beállítás átméretezi **Standard_D8s_v3ról** **Standard_D2s_v3re**.

![Példa az elérhető virtuálisgép-méretek listájára, ahol kiválaszthatja a kívánt méretet](./media/tutorial-acm-opt-recommendations/choose-size.png)

Miután kiválasztotta a megfelelő méretet, kattintson az **átméretezés** gombra az átméretezés művelet elindításához.

Az átméretezéshez aktív futtatású virtuális gép szükséges az újraindításhoz. Ha a virtuális gép éles környezetben van, javasoljuk, hogy munkaidőn belül futtassa az átméretezési műveletet. Az újraindítás ütemezése csökkentheti a megszakítások okozta fennakadásokat.

## <a name="verify-the-action"></a>A művelet ellenőrzése

Ha a virtuális gép átméretezése sikeresen befejeződött, egy Azure-értesítés jelenik meg.

![A virtuális gép átméretezett értesítése sikeres volt](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A lehetséges használati elégtelenségek megtekintéséhez a Cost Optimization javaslatainak megtekintése
> * A virtuális gépek költséghatékonyabb megoldásra való átméretezésére vonatkozó javaslat
> * Ellenőrizze a műveletet, hogy a virtuális gép átméretezése sikeres legyen

Ha még nem olvasta el a Cost Management ajánlott eljárásokat ismertető cikket, magas szintű útmutatást és alapelveket biztosít a költségek kezeléséhez.

> [!div class="nextstepaction"]
> [Cost Management ajánlott eljárások](cost-mgt-best-practices.md)
