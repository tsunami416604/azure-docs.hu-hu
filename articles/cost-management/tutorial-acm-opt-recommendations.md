---
title: Oktatóanyag – Azure optimalizációs javaslatok a költségek csökkentése |} A Microsoft Docs
description: Ez az oktatóanyag segít az Azure-költségek csökkentése, optimalizálás javaslatok működjön, amikor.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 6a26b4c08915b44d24290fde2c74d71b9362c11c
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338569"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Oktatóanyag: A javaslatok a költségek optimalizálása

Az Azure Cost Management költségek optimalizálása javaslatokat is ad az Azure Advisor együttműködik. Az Azure Advisor segít optimalizálása és a hatékonyság növelése az inaktív és kihasználatlan erőforrások azonosítása. Ez az oktatóanyag végigvezeti egy példa, ahol azonosíthatja a kihasználatlan fizetős Azure-erőforrások és a megfelelő, költségek csökkentése érdekében.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Lehetséges a használat hatékonysági hiányosságainak megtekintéséhez optimalizálási javaslatok megtekintése
> * Reagálás a javaslatra kattintva további szolgáltatás költséghatékony lehetőséget a virtuális gép átméretezése
> * Ellenőrizze a műveletet, győződjön meg arról, hogy a virtuális gép sikeresen át lett méretezve

## <a name="prerequisites"></a>Előfeltételek
Javaslatok érhetők el a hatóköröket és az Azure-fiók típusa, beleértve a különböző [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ügyfelek. A támogatott típusok teljes listáját, tekintse meg [megismerheti a Cost Management adataihoz](understand-cost-mgt-data.md). A költségadatok megtekintéséhez olvasási jogosultsággal kell rendelkeznie a következő hatókörök legalább egyikében: Hatókörök kapcsolatos további információkért lásd: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

- Előfizetés
- Erőforráscsoport

Aktív virtuális gépeket, amelyek legalább 14 napos aktivitás kell rendelkeznie.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="view-cost-optimization-recommendations"></a>Optimalizálás javaslatok megtekintése

Az Azure Portalon kattintson a **Költségkezelés + Számlázás** elemre a szolgáltatások listáján. Ezt a listában **Cost Management**válassza **Advisor-javaslatok**. Az Advisor díjakkal kapcsolatos ajánlások jelennek meg.

![Az Azure Portalon látható a Cost Management Advisor-javaslatok](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A javaslatok listája a használat hatékonysági hiányosságainak azonosítja, vagy további pénzt takaríthat meg, amelyek segítségével vásárlási javaslatok megjelenítése. Az összegzett **lehetséges éves megtakarítások** takaríthat meg, állítsa le vagy szabadítsa fel a virtuális gépeket, amelyek megfelelnek a javaslat szabályokat az összes összegét mutatja. Ha nem szeretné leállíthatja őket, érdemes egy kevésbé költséges, Virtuálisgép-termékváltozatra méretezheti azokat.

A **hatás** kategória, és a **lehetséges éves megtakarítások**, úgy tervezték, hogy könnyebben azonosítsák a javaslatok, amelyek a lehető legnagyobb mértékben mentése lehetséges. A nagy jelentőségű javaslatok láthatók, [vásárlás megtakarítást érhet el a használatalapú fizetéses költségekhez képest virtuálisgép-példányok lefoglalva](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) és [optimalizálása a virtuális gép átméretezése, vagy azokat az alacsony kihasználtságú példányokleállításafelhőköltéseiket](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances). Közepes hatású javaslatok [nem kiosztott ExpressRoute-Kapcsolatcsoportok kiküszöbölése révén csökkentheti a költségeket](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits) és [csökkentheti a költségeket törlése vagy újrakonfigurálása inaktív virtuális hálózati átjárók](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways).

## <a name="act-on-a-recommendation"></a>Reagálás a javaslat

Az Azure Advisor 14 nap a virtuális gép használatát figyeli, és azonosítja azokat az alacsony kihasználtságú virtuális gépeket. Virtuális gépek, amelynek CPU-kihasználtság öt százalékban kifejezett vagy annál kisebb, és a hálózati használati hét MB vagy belül a négy vagy több napot számítanak a kis-kihasználtság virtuális gépeket.

A 5 % vagy kevesebb CPU-kihasználtsági beállítás az alapértelmezett érték, de módosíthatja a beállításokat. A beállítás módosításával kapcsolatos további információkért lásd: a [konfigurálja a átlagos CPU-kihasználtság szabály vagy az alacsony kihasználtságú virtuális gépre vonatkozó javaslatok](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Bár bizonyos forgatókönyvek elvárt eredményezhetnek alacsony kihasználtságot, gyakran pénzt takaríthat kevésbé költséges, méret, a virtuális gépek méretének módosításával. A tényleges megtakarításai eltérőek lehetnek, ha úgy dönt, hogy egy átméretezési művelet. Nézzük meg a virtuális gép átméretezése egy példát.

A javaslatok listája, kattintson a **a kihasználatlan virtuális gépek megfelelő méretezése vagy leállítása** javaslat. A virtuális gép jelöltek listája válassza ki a virtuális gép átméretezése, és kattintson a virtuális gép. A virtuális gép részletei jelennek meg, hogy a kihasználtsági mérőszámokat ellenőrizheti. A **lehetséges éves megtakarítások** értéke mi takaríthat meg, állítsa le vagy távolítsa el a virtuális Gépet. A virtuális gép átméretezése valószínűleg pénzt takaríthat meg, de nem menti a lehetséges éves megtakarítások teljes mennyisége.

![Javaslat részletei – példa](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

A virtuális gép adatait ellenőrizze a virtuális gép – győződjön meg arról, hogy egy megfelelő átméretezése jelölt kihasználását.

![Példa virtuális gép részletesen bemutató korábbi kihasználtsága](./media/tutorial-acm-opt-recommendations/vm-details.png)

Megjegyzés: az aktuális virtuális gép méretét. Miután meggyőződött arról, hogy a virtuális gépet át lehet méretezni, zárja be a virtuális gép adatait, hogy a virtuális gépek listájának megtekintéséhez.

Leállítása vagy átméretezése a jelöltek listájából válassza ki **méretezze át a virtuális gép**.
![Példa javaslat az a virtuális gép átméretezése](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Ezután megjelenik a rendelkezésre álló átméretezése lehetőségek listája. Válassza ki azt, amelyik a forgatókönyvnek a legjobb teljesítményt és költséghatékonyságot biztosít. A következő példában a beállítást választott a átméretezi a **DS14\_V2** , egy **DS13\_V2**. A javaslat a következő menti a $551.30 havonta vagy $6,615.60/ év.

![Adja meg a méret elérhető Virtuálisgép-méretek listájának példa](./media/tutorial-acm-opt-recommendations/choose-size.png)

Miután kiválasztotta a megfelelő méretű, kattintson a **kiválasztása** az átméretezési művelet elindításához.

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
> [A Cost Management ajánlott eljárások](cost-mgt-best-practices.md)
