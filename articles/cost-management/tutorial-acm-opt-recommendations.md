---
title: Oktatóanyag – Azure optimalizációs javaslatok a költségek csökkentése |} A Microsoft Docs
description: Ez az oktatóanyag segít az Azure-költségek csökkentése, optimalizálás javaslatok működjön, amikor.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229839"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Oktatóanyag: A javaslatok a költségek optimalizálása

Az Azure Cost Management költségek optimalizálása javaslatokat is ad az Azure Advisor együttműködik. Az Azure Advisor segít optimalizálása és a hatékonyság növelése az inaktív és kihasználatlan erőforrások azonosítása. Ez az oktatóanyag végigvezeti egy példa, ahol azonosíthatja a kihasználatlan fizetős Azure-erőforrások és a megfelelő, költségek csökkentése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Lehetséges a használat hatékonysági hiányosságainak megtekintéséhez optimalizálási javaslatok megtekintése
> * Reagálás a javaslatra kattintva további szolgáltatás költséghatékony lehetőséget a virtuális gép átméretezése
> * Ellenőrizze a műveletet, győződjön meg arról, hogy a virtuális gép sikeresen át lett méretezve

## <a name="prerequisites"></a>Előfeltételek
A javaslatok számos hatókörhöz és Azure-fiókhoz érhetők el. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A költségadatok megtekintéséhez olvasási jogosultsággal kell rendelkeznie a következő hatókörök legalább egyikében: További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

- Előfizetés
- Erőforráscsoport

Aktív virtuális gépeket, amelyek legalább 14 napos aktivitás kell rendelkeznie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="view-cost-optimization-recommendations"></a>Optimalizálás javaslatok megtekintése

Az előfizetéshez tartozó költségtakarékos javaslatok megtekintéséhez nyissa meg a kívánt hatókört a Azure Portalon, és válassza az **Advisor-javaslatok**elemet.

Egy felügyeleti csoportra vonatkozó javaslatok megtekintéséhez nyissa meg a kívánt hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. A **hatókör** -pirula használatával váltson át egy másik hatókörre, például egy felügyeleti csoportra. Válassza az **Advisor-javaslatok** lehetőséget a menüben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

![Az Azure Portalon látható a Cost Management Advisor-javaslatok](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A javaslatok listája a használat hatékonysági hiányosságainak azonosítja, vagy további pénzt takaríthat meg, amelyek segítségével vásárlási javaslatok megjelenítése. Az **évenként megtakarított teljes potenciális megtakarítás** azt a teljes mennyiséget mutatja, amelyet menthet, ha leállítja vagy felszabadítja az összes olyan virtuális gépet, amely megfelel az ajánlási szabályoknak. Ha nem szeretné leállíthatja őket, érdemes egy kevésbé költséges, Virtuálisgép-termékváltozatra méretezheti azokat.

A **hatás** kategóriája, valamint az **éves megtakarítások lehetséges**célja, hogy könnyebben azonosíthatók legyenek azok a javaslatok, amelyekkel a lehető legnagyobb mértékben menthetők.

A nagy hatású javaslatok a következők:
- [Fenntartott virtuálisgép-példányok vásárlása az utólagos elszámolású költségek megtakarítása érdekében](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [A virtuális gépek a kihasználatlan példányok átméretezésével vagy leállításával optimalizálható](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Managed Disks-Pillanatképek tárolása a standard Storage használatával](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Közepes hatású javaslatok a következők:
- [Sikertelen Azure Data Factory folyamatok törlése](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Csökkentse a költségeket a nem kiépített ExpressRoute-áramkörök eltávolításával](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Csökkentse a költségeket a tétlen virtuális hálózati átjárók törlésével vagy újrakonfigurálásával](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Reagálás a javaslat

A Azure Advisor hét napig figyeli a virtuális gépek használatát, majd azonosítja a kihasználatlan virtuális gépeket. Virtuális gépek, amelynek CPU-kihasználtság öt százalékban kifejezett vagy annál kisebb, és a hálózati használati hét MB vagy belül a négy vagy több napot számítanak a kis-kihasználtság virtuális gépeket.

A 5 % vagy kevesebb CPU-kihasználtsági beállítás az alapértelmezett érték, de módosíthatja a beállításokat. A beállítás módosításával kapcsolatos további információkért tekintse meg az [átlagos CPU-kihasználtsági szabály konfigurálása vagy az alacsony kihasználtságú virtuális gép javaslata](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)című témakört.

Bár bizonyos forgatókönyvek elvárt eredményezhetnek alacsony kihasználtságot, gyakran pénzt takaríthat kevésbé költséges, méret, a virtuális gépek méretének módosításával. A tényleges megtakarításai eltérőek lehetnek, ha úgy dönt, hogy egy átméretezési művelet. Nézzük meg a virtuális gép átméretezése egy példát.

A javaslatok listájában kattintson a **megfelelő méretű vagy leállítási kihasználatlan virtuális gépekre** vonatkozó javaslatra. A virtuális gép jelöltek listája válassza ki a virtuális gép átméretezése, és kattintson a virtuális gép. A virtuális gép részletei jelennek meg, hogy a kihasználtsági mérőszámokat ellenőrizheti. A **lehetséges éves megtakarítási** érték a virtuális gép leállításakor vagy eltávolításakor menthető. A virtuális gép átméretezése valószínűleg pénzt takaríthat meg, de nem menti a lehetséges éves megtakarítások teljes mennyisége.

![Javaslat részletei – példa](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

A virtuális gép adatait ellenőrizze a virtuális gép – győződjön meg arról, hogy egy megfelelő átméretezése jelölt kihasználását.

![Példa virtuális gép részletesen bemutató korábbi kihasználtsága](./media/tutorial-acm-opt-recommendations/vm-details.png)

Megjegyzés: az aktuális virtuális gép méretét. Miután meggyőződött arról, hogy a virtuális gépet át lehet méretezni, zárja be a virtuális gép adatait, hogy a virtuális gépek listájának megtekintéséhez.

A leállítani vagy átméretezni kívánt jelöltek listájában válassza a * * *&lt;FromVirtualMachineSKU* átméretezése&gt;a *&lt;ToVirtualMachineSKU&gt;* * * elemet.
![példa a virtuális gép átméretezésére szolgáló lehetőségre](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ezután megjelenik a rendelkezésre álló átméretezése lehetőségek listája. Válassza ki azt, amelyik a forgatókönyvnek a legjobb teljesítményt és költséghatékonyságot biztosít. A következő példában a kiválasztott beállítás átméretezi **Standard_D8s_v3ról** **Standard_D2s_v3re**.

![Adja meg a méret elérhető Virtuálisgép-méretek listájának példa](./media/tutorial-acm-opt-recommendations/choose-size.png)

Miután kiválasztotta a megfelelő méretet, kattintson az **átméretezés** gombra az átméretezés művelet elindításához.

Átméretezése egy aktívan futó virtuális gép újraindítására van szükség. A virtuális gép nem éles környezetben, azt javasoljuk, hogy az átméretezés munkaidő után futtatja. Az újraindítás ütemezés csökkentheti a fennakadások rövid ideig elérhetetlensége által okozott.

## <a name="verify-the-action"></a>A művelet ellenőrzéséhez.

Amikor a virtuális gép átméretezése sikeresen befejeződik, egy Azure-értesítés jelenik meg.

![Értesítés a sikeres a virtuális gép átméretezve](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Lehetséges a használat hatékonysági hiányosságainak megtekintéséhez optimalizálási javaslatok megtekintése
> * Reagálás a javaslatra kattintva további szolgáltatás költséghatékony lehetőséget a virtuális gép átméretezése
> * Ellenőrizze a műveletet, győződjön meg arról, hogy a virtuális gép sikeresen át lett méretezve

Ha még nem olvasta a Cost Management – gyakorlati tanácsok cikk, biztosít magas szintű útmutatást és alapelveket költségeinek kezelése érdekében érdemes figyelembe venni.

> [!div class="nextstepaction"]
> [Cost Management ajánlott eljárások](cost-mgt-best-practices.md)
