---
title: Rövid útmutató – Az Azure-költségek feltérképezése költségelemzés használatával | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: b4302713188237b97ffbe8473f6a37edd6741b36
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793097"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Gyors útmutató: Vizsgálata és elemzése a költségeket a költségelemzés

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Emellett nem árt, ha tudja, mennyibe kerülnek az egyes szolgáltatások, és milyen környezeteket vagy rendszereket szolgálnak ki. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. A kiadásoknál fellépő mintázatok segítségével költségszabályozó mechanizmusok, például költségkeretek léptethetők érvénybe.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése


## <a name="prerequisites"></a>Előfeltételek

Költségelemzés támogatja a különböző típusú Azure-fiók típusa. A támogatott típusok teljes listáját, tekintse meg [megismerheti a Cost Management adataihoz](understand-cost-mgt-data.md). Költségadatok megtekintése, szükség van legalább olvasási hozzáféréssel az Azure-fiókjával.

A [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ügyfelek, rendelkeznie kell legalább olvasási hozzáférést egy vagy több, a költségadatok megtekintéséhez a következő hatókörök.

- Számlázási fiók
- Részleg
- Regisztrációs fiók
- Felügyeleti csoport
- Előfizetés
- Erőforráscsoport

További információ a Cost Management adataihoz való hozzáférés hozzárendelése: [adatokhoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

A költségek, a költségek elemzése, nyissa meg a hatókört az Azure Portalon, és válassza a **költségelemzés** menüjében. Például keresse meg **előfizetések**, és válasszon ki egy előfizetést a listából, majd válassza ki **költségelemzés** menüjében. Használja a **hatókör** megszámlálásához váltson át egy másik hatókört, a költségek elemzése. Hatókörök kapcsolatos további információkért lásd: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

Adja meg az adatok összevonása, és a költségadatok való hozzáférésének a hatókör választja Cost Management használja. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett válassza ki a nagyobb hatókör, amely mások akár állítja, és szűrjön rá kell a beágyazott hatókörök le. Ez a megközelítés akkor fontos tudni, mert vannak, akik előfordulhat, hogy rendelkezik hozzáféréssel egyetlen szülő hatókör, amely magában foglalja a több beágyazott hatókörök.

A kezdő költségelemzési nézet a következő területeket foglalja magába:

**Összesen** – Az aktuális hónap összes költségét jeleníti meg.

**Költségkeret** – A kijelölt hatókör tervezett költségkeretét jeleníti meg, amennyiben az elérhető.

**Összesített költségeit** – mutatja a teljes összesített napi összegű költségkeretet, a hónap kezdetétől fogva. Miután számlázási fiókjához vagy előfizetéséhez [létrehozta költségvetését](tutorial-acm-create-budgets.md), itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Kimutatásdiagramok (fánkdiagramok)** – Dinamikus kimutatásokat tesznek lehetővé a teljes költséget alapvető jellemzők alapján lebontva. Ezek bemutatják a legtöbbet a legkevésbé költséges, az aktuális hónaphoz. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. A költségek alapértelmezetten a következő kategóriákba vannak sorolva: szolgáltatás (fogyasztásmérő kategória), hely (régió), valamint gyermekhatókör. Például a regisztrációs fiókok a számlázási fiókok alatt, az erőforráscsoportok az előfizetések alatt, az erőforrások, pedig az erőforráscsoportok alatt jelennek meg.

![Az Azure Portalon költségelemzés kezdeti nézete](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

Költségelemzés négy beépített nézetek, a leggyakoribb célokat optimalizálva van:

Nézet | Válaszoljon a kérdésekre, mint...
--- | ---
Halmozott költség | IP-címek fenntartási töltötték eddig ebben a hónapban? Belül maradok-e a költségkeretemen?
Napi költség | Az elmúlt 30 napra vonatkozó napi költségek növekedését van voltak?
Díj szolgáltatás szerint | Hogyan tartozik a havi használat eltérőek lehetnek az elmúlt 3 számlák?
Díjak erőforrások szerint | Mely erőforrások költsége a legtöbb eddig ebben a hónapban?

![Adatnézet-választóhoz látható egy példa kiválasztása ebben a hónapban](./media/quick-acm-cost-analysis/view-selector.png)

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A dátum-választó használatával gyorsan válthat a közös dátumtartományokat. Néhány közé tartoznak az elmúlt hét nap, a múlt hónapban, aktuális év vagy egy egyéni dátumtartományt. Utólagos elszámolású előfizetések a számlázási időszak, amely nincs kötve a naptári hónap, például az aktuális elszámolási időszakban vagy a legutóbbi számla alapján dátumtartományokat is tartalmazhatnak. Használja a **< előző** és **Tovább >** Ugrás az előző vagy következő időszak jelölik, a menü tetején található hivatkozásokat. Tegyük fel **< előző** váltani fog a az elmúlt hét nap 8 – 14 nappal ezelőtt, majd 15-21 napja került sor.

![Dátum-választó megjelenítése egy példa kiválasztása ebben a hónapban](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. Összesített költségek összes költséget tartalmazzák, minden nap, valamint az elmúlt napokban, napi összesített költségeit egyre bővülő megjelenítéséhez. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha költségvetést is kiválasztott, a napi nézet egy becslést is megjelenít az egy napra vonatkozó költségkeretet illetően. Ha a napi költségei következetesen meghaladják a becsült napi költségkeretet, várhatóan havi költségkeretét is túl fogja lépni. A becsült napi költségkeret egy olyan eszköz, amellyel könnyebben megjelenítheti költségvetése alacsonyabb szintjeit. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

Általában a is látja a keresett adatok vagy a felhasznált erőforrások értesítések 8 – 12 órán belül.

![Az aktuális hónapra például napi költségek megjelenítő napi megtekintése](./media/quick-acm-cost-analysis/daily-view.png)

**Csoportosítás** bontja a költségek és az általános tulajdonságok leggyakoribb közreműködők. Erőforrás-címkék szerint kell csoportosítani, válassza ki például a címke kulcs szerint kell csoportosítani szeretné. Költségek szerint egyes címkeértékkel rendelkező erőforrások, amelyek nem rendelkeznek a alkalmazni a címkét egy extra szegmens bontásban.

A legtöbb [Azure-erőforrások támogatja a címkézést](../azure-resource-manager/tag-support.md), azonban az egyes címkék nem Költségkezelés szerepel a számlázási. Ezenkívül a csoport az erőforráscímkék nem támogatottak. A Cost Management csak a dátum, a címkék közvetlenül az erőforrás érvényesek az erőforráscímkék támogatja. Tekintse meg a [címkézési házirendek az Azure Cost Managementbe felülvizsgálata](https://www.youtube.com/watch?v=nHQYcYGKuyw) videó további információ a címke az Azure policyval költség adatok láthatóságának javítása érdekében.

Íme egy példa az elmúlt havi Azure-szolgáltatási költségeket bemutató nézetről.

![Csoportosított napi halmozott nézet-példa az Azure szolgáltatási költségei teszik a múlt hónapban](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Kimutatásdiagramok a fő diagram megjelenítése különböző csoportosítások, amelyek egy szélesebb körű képet az általános költségek a kijelölt időszak és a szűrők alapján. Válasszon egy tulajdonságot vagy címke minden dimenzió összesített költségeit megtekintésére.


![Összes adat megjelenítése az erőforrás csoportnevek aktuális nézet](./media/quick-acm-cost-analysis/full-data-set.png)

Az előző képen az erőforráscsoportok nevei olvashatóak. Címke megtekintéséhez a teljes költség / címke szerint csoportosíthatja, amíg nem érhető el cost analysis nézetek megtekintése az erőforrás vagy erőforráscsoport összes címkéjét.

Ha egy adott attribútum szerinti csoportosítás a költségeket, a felső 10 közreműködőkkel jelennek meg legmagasabbtól a legalacsonyabb. Ha több mint 10, a felső kilenc közreműködőkkel együtt jelennek meg egy **mások** csoport, amely magában foglalja az összes többi csoport együtt. Csoportosítás címkék szerint, amikor is megjelenhetnek egy **Untagged** csoport tagjai, amelyek nem rendelkeznek a alkalmazni címkekulcs költségeket. **Címkézetlen** mindig utolsó, akkor is, ha a címkézetlen költségek magasabbak címkézett költségeket. A címkézetlen költségek része lesz **mások**, ha létezik legalább 10 címke értéke.

*Klasszikus* virtuális gépekhez, hálózati és tárolási erőforrások nem adjuk ki a részletes számlázási adatok. Ezek van egyesítve **klasszikus szolgáltatások** Ha csoportosítási költségeket.

A teljes adatkészlet minden nézet megtekintéséhez. Bármelyik beállításokat vagy a alkalmazni szűrők hatással a megjelenített adatokhoz. A teljes adatkészlet megtekintéséhez kattintson a **diagramtípus** listából, majd kattintson **tábla** megtekintése.

![Aktuális nézet egy tábla adatainak megtekintése](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Költségelemzési adatok letöltése

A költségelemzés adatait **letöltheti** és az Azure Portalon jelenleg látható összes adatból egy CSV-fájlt generálhat. Az alkalmazott csoportosítások és szűrők a fájlban is szerepelni fognak. A fájlban az összes költség diagram olyan mögöttes adatai is szerepelni fognak, amelyek az aktív nézetben nem láthatóak.

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
