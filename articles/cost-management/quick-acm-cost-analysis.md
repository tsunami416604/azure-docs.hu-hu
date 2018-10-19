---
title: Rövid útmutató – Az Azure-költségek feltérképezése költségelemzés használatával | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan vizsgálhatja meg és elemezheti vállalata Azure-költségeit a Költségelemzés szolgáltatás használatával.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/10/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 12b7a605350b07565660e9e4d1334b286aa5ac00
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079106"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rövid útmutató – Költségek feltérképezése és elemzése a Költségelemzés szolgáltatás használatával

Ahhoz, hogy érdemben szabályozhassa és optimalizálhassa Azure-költségeit, fontos, hogy tudja a vállalaton belül hol keletkeznek ezek a költségek. Emellett nem árt, ha tudja, mennyibe kerülnek az egyes szolgáltatások, és milyen környezeteket vagy rendszereket szolgálnak ki. A költségek teljes spektrumának ismerete elengedhetetlen a szervezet kiadásaiban fellépő mintázatok pontos megértéséhez. A kiadásoknál fellépő mintázatok segítségével költségszabályozó mechanizmusok, például költségkeretek léptethetők érvénybe.

Ebben a rövid útmutatóban költségelemzés használatával megvizsgálja és elemzi a szervezet költségeit. Az összesített költségeket szervezetekre lebontva jelenítheti meg, így jobban átláthatja, hogy az idő múlásával mely területeken keletkeznek költségei és felismerheti a kiadások trendjeit. Megtekintheti az egy adott időszak során felhalmozott költségeket is, így havi, negyedéves vagy akár éves költségtrendbecsléseket is készíthet a költségvetéshez viszonyítva. Költségvetés használatával könnyebb betartani a pénzügyi korlátozásokat. Ezen felül a költségvetés segít áttekinteni napi vagy havi szintű költségeit és elkülöníteni a rendhagyó kiadásokat. Az adatokat le is töltheti további elemzés vagy külső rendszerben való használat céljából.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

- Költségek áttekintése a Költségelemzés használatával
- Költségnézetek testreszabása
- Költségelemzési adatok letöltése


## <a name="prerequisites"></a>Előfeltételek

A Költségelemzés az összes [Nagyvállalati Szerződéssel](https://azure.microsoft.com/pricing/enterprise-agreement/) rendelkező ügyfél számára elérhető. A költségadatok megtekintéséhez olvasási jogosultsággal kell rendelkeznie a következő hatókörök legalább egyikében:

- A *Számlázási fiók* hatókör meghatározása a https://ea.azure.com webhelyen olvasható, használatához vállalati rendszergazdai hozzáférés szükséges. Nincs szükség vállalati rendszergazdai beállítások előzetes megadására. A költségelemzés számlázási adatai összesítve jelennek meg a nagyvállalati szerződés hatálya alá tartozó összes előfizetésre vonatkozóan. A számlázási fiókra gyakran *Nagyvállalati Szerződés* vagy *Igénylés* néven hivatkoznak.

- A *Részleg* hatókör meghatározása a https://ea.azure.com webhelyen olvasható, használatához részlegszintű rendszergazdai hozzáférés szükséges. A nagyvállalati szerződések portálján engedélyezni kell azt a beállítást, hogy **a részlegszintű rendszergazda láthassa a díjtételeket**. A költségelemzés számlázási adatai összesítve jelennek meg az adott részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetésre vonatkozóan.

- A *Regisztrációs fiók* hatókör meghatározása a https://ea.azure.com webhelyen olvasható, használatához fióktulajdonosi hozzáférés szükséges. A nagyvállalati szerződések portálján engedélyezni kell azt a beállítást, hogy **a fióktulajdonos láthassa a díjtételeket**. A költségelemzés számlázási adatai összesítve jelennek meg a regisztrációs fiókhoz tartozó összes előfizetésre vonatkozóan. A regisztrációs fiókra gyakran *fióktulajdonos* néven hivatkoznak.

- A *Felügyeleti csoport* hatókör meghatározása a https://portal.azure.com webhelyen olvasható, használatához Cost Management-olvasói (vagy olvasói) hozzáférés szükséges. A nagyvállalati szerződések portálján engedélyezni kell azt a beállítást, hogy **a fióktulajdonos láthassa a díjtételeket**. A költségelemzés számlázási adatai összesítve jelennek meg a felügyeleti csoport alá tartozó összes előfizetésre vonatkozóan.

- Az *Előfizetés* hatókör meghatározása a https://portal.azure.com webhelyen olvasható, használatához Cost Management-olvasói (vagy olvasói) hozzáférés szükséges. A nagyvállalati szerződések portálján engedélyezni kell azt a beállítást, hogy **a fióktulajdonos láthassa a díjtételeket**. A költségelemzés számlázási adatai összesítve jelennek meg az előfizetéshez tartozó összes erőforrásra és erőforráscsoportra vonatkozóan.

- Az *Erőforráscsoport* hatókör meghatározása a https://portal.azure.com webhelyen olvasható, használatához Cost Management-olvasói (vagy olvasói) hozzáférés szükséges. A nagyvállalati szerződések portálján engedélyezni kell azt a beállítást, hogy **a fióktulajdonos láthassa a díjtételeket**. A költségelemzés számlázási adatai összesítve jelennek meg az erőforráscsoporthoz tartozó összes erőforrásra vonatkozóan.



A díjtételeket a **részlegszintű rendszergazda** és a **fióktulajdonos** számára láthatóvá tevő beállítások konfigurálásával kapcsolatos további információkért lásd [a díjtételekhez való hozzáféréssel foglalkozó részt](../billing/billing-enterprise-mgmt-grp-troubleshoot-cost-view.md#enabling-access-to-costs).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="review-costs-in-cost-analysis"></a>Költségek áttekintése a Költségelemzés használatával

A költségek költségelemzés használatával való megtekintéséhez az Azure Portalon lépjen a **Költségkezelés + Számlázás**&gt;**Költségkezelés**&gt;**Hatókör megváltoztatása** területre, majd válassza ki a hatókört, és kattintson a **Kiválasztás** lehetőségre.

A választott hatókör az adatkonszolidációt és a költségadatokhoz való hozzáférés szabályozását határozza meg a Költségkezelés során. A hatókörök használata során nem lehet egyszerre több hatókört is kiválasztani. Ehelyett egy magasabb szintű hatókört választ ki, amely alá több alacsonyabb szintű hatókör is tartozik, és szűrő alkalmazásával keresi meg a kívánt adatokat. Ez azért fontos, mert nem minden személy rendelkezhet hozzáféréssel egy szülőhatókörhöz, amely több gyermekhatókört is magába foglal.

Kattintson a **Költségelemzés megnyitása** lehetőségre.

A kezdő költségelemzési nézet a következő területeket foglalja magába:

**Összesen** – Az aktuális hónap összes költségét jeleníti meg.

**Költségkeret** – A kijelölt hatókör tervezett költségkeretét jeleníti meg, amennyiben az elérhető.

**Halmozott költség** – Megjeleníti az összes összegyűlt napi kiadást a hónap kezdetétől számítva. Miután számlázási fiókjához vagy előfizetéséhez [létrehozta költségvetését](tutorial-acm-create-budgets.md), itt gyorsan, költségkeretéhez viszonyítottan áttekintheti kiadási trendjeit. A kurzort egy adott dátum felé helyezve megtekintheti az adott nap halmozott költségeit.

**Kimutatásdiagramok (fánkdiagramok)** – Dinamikus kimutatásokat tesznek lehetővé a teljes költséget alapvető jellemzők alapján lebontva. Az adott hónap költségeit jelenítik meg a legmagasabb halmozott költségtől a legalacsonyabb felé. A kimutatásdiagramokat bármikor módosíthatja másik kimutatás kiválasztásával. A költségek alapértelmezetten a következő kategóriákba vannak sorolva: szolgáltatás (fogyasztásmérő kategória), hely (régió), valamint gyermekhatókör. Például a regisztrációs fiókok a számlázási fiókok alatt, az erőforráscsoportok az előfizetések alatt, az erőforrások, pedig az erőforráscsoportok alatt jelennek meg.

![A költségelemzés kezdőnézete](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Költségnézetek testreszabása

Az alapértelmezett nézet gyors választ adhat az olyan gyakori kérdésekre, mint:

- Mennyit költöttem összesen?
- Belül maradok-e a költségkeretemen?

Azonban sok esetben ennél mélyebb elemzésre is szükség lehet. A testreszabást az oldal tetején a dátum kiválasztásánál kezdhetjük.

Alapértelmezetten a költségelemzés az aktuális hónap adatait jeleníti meg. A választómező segítségével gyorsan átválthat az előző hónap, az aktuális hónap, az aktuális negyedév, az aktuális év, vagy egy egyénileg választott dátumtartomány adatainak megjelenítésére. A előző hónap lehetőség választásával könnyedén kielemezheti legfrissebb Azure-számláját és egyeztetheti kiadásait. Az aktuális negyedév és aktuális év lehetőségek segítenek költségeit nyomon követni a hosszabb távú költségvetések viszonyában is. Egyedi dátumtartományt is megadhat. Kiválaszthat például egyetlen napot, az elmúlt hét napot, vagy bármilyen időtartamot az aktuális hónaptól számítva egy évre visszamenőleg.

![Dátumválasztó](./media/quick-acm-cost-analysis/date-selector.png)

A költségelemzés alapértelmezetten a **Halmozott** költségeket jeleníti meg. A halmozott költségek az egyes napok költségét, valamint a megelőző napok költségeit foglalják magukba, tehát a napi költségek folyamatos halmozódásának nyomon követésére szolgálnak. A nézetet arra optimalizáltuk, hogy megvizsgálhassa, hogyan alakulnak költségei az adott időszakban a tervezett költségvetéshez képest.

Emellett a **napi** nézet használatával az egyes napok költségeit is megtekintheti. A napi nézet nem jeleníti meg a növekedési trendeket. A nézet célja a szabálytalanságok, vagyis a kiugróan magas vagy alacsony értékek megjelenítése a napról napra jelentkező költségekben. Ha költségvetést is kiválasztott, a napi nézet egy becslést is megjelenít az egy napra vonatkozó költségkeretet illetően. Ha a napi költségei következetesen meghaladják a becsült napi költségkeretet, várhatóan havi költségkeretét is túl fogja lépni. A becsült napi költségkeret egy olyan eszköz, amellyel könnyebben megjelenítheti költségvetése alacsonyabb szintjeit. Ha napi költségei ingadozóak, a becsült napi költségkeret viszonya a havi költségkeretével kevésbé lesz pontos.

![Napi nézet](./media/quick-acm-cost-analysis/daily-view.png)

A **Csoportosítás** használatával csoportkategóriák közül választhat, és módosítja, mely adatok jelenjenek meg a felső összes költség terület diagramjában. A csoportosítás használatával gyorsan áttekintheti költségei milyen erőforrástípusok szerinti kategóriákra bonthatók. Íme egy példa az elmúlt havi Azure-szolgáltatási költségeket bemutató nézetről.

![Csoportosított napi halmozott költségek megtekintése](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

A felső összes költség nézet alatt található kimutatásdiagramok különböző csoportosítási és szűrési kategóriák nézeteit jelenítik meg. Ha kiválasztja valamelyik csoportosítási kategóriát, az összes költség teljes adatkészlete a nézet alsó részére kerül. Íme egy példa az erőforráscsoportokhoz.

![A jelenlegi nézet összes adata](./media/quick-acm-cost-analysis/full-data-set.png)

Az előző képen az erőforráscsoportok nevei olvashatóak. Az egyes erőforrások címkéi nem érhetőek el egyik költségelemzési nézet, szűrési feltétel vagy csoportosítás használatával sem.

Ha a költségeket egy adott attribútum szerint csoportosítja, csökkenő sorrendben megjelenik a tíz legfontosabb költségelem. Ha tíznél több ilyen csoport van, akkor a kilenc legfontosabb költségelem mellett megjelenik egy **Egyéb** csoport is, amelyben az összes többi csoport együttesen szerepel.

A *Klasszikus* típusba sorolt (Azure Service Management vagy ASM rendszerű) virtuális gépek, hálózatkezelési és tárolási erőforrások megosztások nem osztanak meg részletes számlázási adatokat. Ezek a költségek csoportosításakor a **Klasszikus szolgáltatások** csoportba kerülnek.


## <a name="download-cost-analysis-data"></a>Költségelemzési adatok letöltése

A költségelemzés adatait **letöltheti** és az Azure Portalon jelenleg látható összes adatból egy CSV-fájlt generálhat. Az alkalmazott csoportosítások és szűrők a fájlban is szerepelni fognak. A fájlban az összes költség diagram olyan mögöttes adatai is szerepelni fognak, amelyek az aktív nézetben nem láthatóak.

## <a name="next-steps"></a>További lépések

Lépjen tovább az első oktatóanyaghoz, amely a költségvetés létrehozását és kezelését mutatja be.

> [!div class="nextstepaction"]
> [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md)
