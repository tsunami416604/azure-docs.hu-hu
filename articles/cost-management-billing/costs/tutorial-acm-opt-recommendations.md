---
title: Oktatóanyag – Az Azure-költségek csökkentése javaslatokkal
description: Ezen oktatóanyag segítségével csökkentheti Azure-költségeit, ha követi az optimalizálással kapcsolatos javaslatokat.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: de558ddad37e1621c633d26fff51d1506c237577
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80155919"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Oktatóanyag: Javaslatok alapján történő költségoptimalizálás

Az Azure Cost Management az Azure Advisor segítségével tesz költségoptimalizálási javaslatokat. Az Azure Advisor a tétlen és kihasználatlan erőforrások azonosításával segítséget nyújt a hatékonyság optimalizálásában és javításában. Ez az oktatóanyag bemutat egy példát, ahol azonosíthatja az alacsony kihasználtságú Azure-erőforrásokat, majd intézkedéseket tehet a költségek csökkentésére.

Ha többet szeretne megtudni arról, hogyan optimalizálhatja a költségeit az Advisorral, tekintse meg a videót, amely [a felhővel kapcsolatos befektetések Azure Cost Managementben történő optimalizálásával](https://www.youtube.com/watch?v=cSNPoAb-TNc) foglalkozik.

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségoptimalizálási javaslatok megtekintése a lehetséges használati hatékonysági hiányosságok áttekintéséhez
> * Javaslat követése a virtuális gép költséghatékonyabb változatra való átméretezéséhez
> * A művelet ellenőrzése, hogy a virtuális gép átméretezése sikeres volt-e

## <a name="prerequisites"></a>Előfeltételek
Különböző hatókörökhöz és Azure-fióktípusokhoz kapcsolódó javaslatok érhetők el. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez olvasási jogosultsággal kell rendelkeznie a következő hatókörök legalább egyikében: További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Előfizetés
- Erőforráscsoport

Új előfizetés esetén nem használhatja azonnal a Cost Management szolgáltatásait. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa. Emellett legalább 14 napja aktív virtuális gépekkel kell rendelkeznie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="view-cost-optimization-recommendations"></a>Költségoptimalizálási javaslatok megtekintése

Egy adott előfizetéshez kapcsolódó költségoptimalizálási javaslatok megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza az **Advisor-ajánlások** lehetőséget.

Egy adott felügyeleti csoportra vonatkozó javaslatok megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza a **Költségelemzés** lehetőséget a menüben. A **Hatókör** elemmel másik hatókörre, például felügyeleti csoportra válthat. Válassza az **Advisor-ajánlások** lehetőséget a menüben. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

![Az Azure Portalon megjelenő Cost Management Advisor-ajánlások](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A javaslatok listája azonosítja a használat hatékonysági hiányosságait, illetve olyan vásárlási javaslatokat jelenít meg, amelyekkel további pénzt takaríthat meg. Az összegzett **Lehetséges éves megtakarítás** mezőben az a teljes összeg látható, amelyet megtakaríthat, ha a javaslati szabályoknak eleget tevő összes virtuális gépét leállítja vagy felszabadítja. Ha nem szeretné leállítani ezeket a virtuális gépeket, fontolja meg átméretezésüket egy olcsóbb termékváltozatra.

A **Hatás** kategória (a **Lehetséges éves megtakarítás mellett**) létrehozásának célja, hogy segítse azonosítani azokat a javaslatokat, amelyekkel a lehető legnagyobb megtakarítás érhető el.

Nagy hatású javaslatok:
- [Vásároljon fenntartott virtuálisgép-példányokat a használatalapú fizetéshez képest elért pénzmegtakarítás érdekében](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimalizálja virtuálisgép-költségeit az alacsony kihasználtságú példányok átméretezésével vagy leállításával](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Standard szintű Storage használata Managed Disks-pillanatfelvételek tárolására](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Közepes hatású javaslatok:
- [Meghiúsult Azure Data Factory-folyamatok törlése](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Költségek csökkentése a kiépítetlen ExpressRoute-kapcsolatcsoportok kiiktatásával](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Költségek csökkentése az inaktív virtuális hálózati átjárók törlésével vagy újrakonfigurálásával](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Javaslat végrehajtása

Az Azure Advisor hét napig figyeli a virtuálisgép-használatot, majd azonosítja az alacsony kihasználtságú virtuális gépeket. Azok a virtuális gépek számítanak alacsony kihasználtságúnak, amelyeknek négy vagy több napon keresztül öt százalékos vagy annál kisebb a processzorkihasználtsága, valamint hét MB vagy annál kisebb az adatforgalma.

Az alapértelmezett érték az 5%-os vagy annál kisebb processzorkihasználtság, de a beállítások módosíthatók. A beállítások módosításával kapcsolatos további információért lásd az [átlagos processzorkihasználtsághoz kapcsolódó szabály vagy az alacsony kihasználtságú virtuális gépre vonatkozó javaslat konfigurálásával foglalkozó részt](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Bár vannak olyan forgatókönyvek, amelyeknél az alacsony kihasználtság az elvárt működés, a virtuális gépek olcsóbb termékváltozatra való átméretezésével gyakran pénzt takaríthat meg. Átméretezés esetén a tényleges megtakarításai változhatnak. Nézzünk egy adott virtuális gép átméretezésére vonatkozó példát.

A javaslatok listájában kattintson **A kihasználatlan virtuális gépek megfelelő méretezése vagy leállítása** elemre. Az alkalmas virtuális gépek listájában válasszon ki egy virtuális gépet az átméretezéshez, majd kattintson a virtuális gépre. Ekkor megjelennek a virtuális gép részletes adatai, így a kihasználtsági metrikák ellenőrizhetők. A **lehetséges éves megtakarítás** értékének megfelelő pénzt takaríthat meg a virtuális gép leállítása vagy eltávolítása esetén. A virtuális gép átméretezése valószínűleg megtakarítással jár, de ezzel még nem éri el a lehetséges éves megtakarítás teljes összegét.

![Példa a javaslat részleteire](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

A virtuális gép részletes adatai között ellenőrizze a virtuális gép kihasználtságát, hogy az adott megfelelő jelölt-e az átméretezésre.

![Példa a virtuális gép részletes adataira a kihasználtsági előzmények megjelenítésével](./media/tutorial-acm-opt-recommendations/vm-details.png)

Vegye figyelembe az aktuális virtuális gép méretét. Miután ellenőrizte, hogy a virtuális gépet át kell-e méretezni, zárja be a virtuális gép részletes adatait, hogy megtekinthesse a virtuális gépek listáját.

A leállítandó vagy átméretezendő jelöltek listájában válassza a következőt: **_&lt;FromVirtualMachineSKU&gt;_ átméretezése a következőre: _&lt;ToVirtualMachineSKU&gt;_** .
![Példa javaslatra a virtuális gép átméretezésére vonatkozó lehetőséggel](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ezt követően megjelenik a rendelkezésre álló átméretezési beállítások listája. Válassza ki azt, amelyik a legjobb teljesítményt és költséghatékonyságot biztosítja a forgatókönyve esetében. Az alábbi példában a kiválasztott átméretezési beállítás: **Standard_D8s_v3** – **Standard_D2s_v3**.

![Példa a rendelkezésre álló virtuálisgép-méretek listájára, ahol a méret kiválasztható](./media/tutorial-acm-opt-recommendations/choose-size.png)

Miután kiválasztotta a megfelelő méretet, kattintson az **Átméretezés** elemre az átméretezési művelet indításához.

Az átméretezéshez egy aktív virtuális gépet újra kell indítani. Ha a virtuális gép éles környezetben található, javasoljuk, hogy az átméretezést munkaidő után futtassa. Az újraindítás ütemezésével csökkenthető a pillanatnyi elérhetetlenség okozta kimaradások előfordulása.

## <a name="verify-the-action"></a>A művelet ellenőrzése

Ha a virtuális gép átméretezése sikeresen befejeződött, egy Azure-értesítés jelenik meg.

![Sikeresen átméretezett virtuális gépről szóló értesítés](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségoptimalizálási javaslatok megtekintése a lehetséges használati hatékonysági hiányosságok áttekintéséhez
> * Javaslat követése a virtuális gép költséghatékonyabb változatra való átméretezéséhez
> * A művelet ellenőrzése, hogy a virtuális gép átméretezése sikeres volt-e

A Cost Management ajánlott eljárásait ismertető cikk magas szintű útmutatást és alapelveket tartalmaz, amelyeket érdemes figyelembe venni a költségek kezelése során.

> [!div class="nextstepaction"]
> [A Cost Management ajánlott eljárásai](cost-mgt-best-practices.md)
