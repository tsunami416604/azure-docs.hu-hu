---
title: AWS-költségek és az Azure Cost Management használata kezelheti
description: Ez a cikk segít megérteni a költségek elemzése és a költségvetés használata a Cost Management kezeli az AWS-költségek és a használat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 57e66d449b194662bfc03f7e130cf49c02a15793
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275711"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>AWS-költségek és az Azure-beli használati kezelése

Miután beállítása és az AWS-költségek és a használati jelentés integráció az Azure Cost Management konfigurált, készen áll az AWS-költségek és a használati kezelésének megkezdéséhez. Ez a cikk segít megérteni a költségek elemzése és a költségvetés használata a Cost Management kezeli az AWS-költségek és a használat.

Ha még nem konfigurált az integrációt, lásd: [beállítása beállítása és az AWS-használati jelentés-integráció konfigurálása](aws-integration-set-up-configure.md).

_Mielőtt elkezdené_: Ha még nem ismeri a költségek elemzése, tekintse meg a [vizsgálata és elemzése a költségeket a költségelemzés](quick-acm-cost-analysis.md) rövid. És, ha még nem ismeri a költségvetés az Azure-ban, tekintse meg a [létrehozása és kezelése az Azure költségvetése](tutorial-acm-create-budgets.md) oktatóanyag.

## <a name="view-aws-costs-in-cost-analysis"></a>AWS-költségek nézet a költségelemzés

A következő hatókörök költségelemzést AWS-költségek érhetők el:

- Egy felügyeleti csoportban társított AWS-fiókok
- Az AWS-beli társított fiók költségek
- Konszolidált AWS-fiók költségek

A következő szakaszokban a hatókörök használatával úgy, hogy minden egyes felhasználási és adatok.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Társított AWS-fiókok nézet a felügyeleti csoport szerint

Költségek megtekintése a felügyeleti csoport hatóköre segítségével az egyetlen módja más előfizetésekből származó összesített költségeit megtekintéséhez és a kapcsolódó fiókokat. A felügyeleti csoport használatával nyújt a több felhőre kiterjedő.

A cost analysis nyissa meg a Hatókörválasztó, és válassza ki a felügyeleti csoportot, amely a kapcsolódó AWS-fiókok tárol. Íme egy példa lemezképet az Azure Portalon:

![A példában válassza a hatókör-nézet](./media/aws-integration-manage/select-scope01.png)



Íme egy példa a felügyeleti csoport költség a költségelemzés szolgáltató (az Azure és AWS) szerint csoportosítva.

![Negyedév a költségek elemzése az Azure és AWS költségeinek bemutató példa](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Az AWS-beli társított fiók költségek megtekintése

AWS-hivatkozás fiók költségek megtekintéséhez nyissa meg a Hatókörválasztó és válassza ki az AWS társított fiókot. Vegye figyelembe, hogy az összekapcsolt fiókoknak társított felügyeleti csoporthoz, az AWS-összekötőben meghatározottak szerint.

Íme egy példa, hogy egy AWS kijelölése látható társított fiók hatókörében.

![A példában válassza a hatókör-nézet](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Nézet AWS összevont fiók költségek

Az AWS megtekintéséhez összevont fiók költségek, nyissa meg a Hatókörválasztó, és válassza ki az AWS konszolidált fiók. Íme egy példa, hogy mutat be egy AWS kijelölésével összevont fiók hatókörében.

![A példában válassza a hatókör-nézet](./media/aws-integration-manage/select-scope03.png)



Ebben a hatókörben biztosít az összes AWS összesített nézete az összevont AWS-fiókhoz társított fiók társított. Íme egy példa egy AWS költségeinek összesített fiók, a szolgáltatás neve szerint csoportosítva.

![Az AWS megjelenítő Példa összevont költségek a költségelemzés](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Szűrés és csoportosítási elérhető dimenziók

A következő táblázat ismerteti a rendelkezésre álló csoport és a költségek elemzése szűrés dimenziók.

| Dimenzió | Amazon CUR header | Hatókörök | Megjegyzések |
| --- | --- | --- | --- |
| Rendelkezésre állási zónában | lineitem/AvailabilityZone | Összes |   |
| Location egység | termék/régió | Összes |   |
| Forgalmi díj |   | Összes |   |
| Fogyasztásmérő kategóriája | lineItem/ProductCode | Összes |   |
| Fogyasztásmérő alkategóriája | lineitem/UsageType | Összes |   |
| Művelet | lineItem/Operation | Összes |   |
| Resource | lineItem/ResourceId | Összes |   |
| Erőforrás típusa | product/instanceType | Összes | Ha a termék/instanceType null értékű, lineItem/UsageType használatos. |
| ResourceGuid | – | Összes | Azure-mérőszámok GUID azonosítója. |
| Szolgáltatásnév | termék/ProductName | Összes | Ha a termék/ProductName null értékű, lineItem/ProductCode használatos. |
| Szolgáltatásszint |   |   |   |
| Előfizetés azonosítója | lineItem/UsageAccountId | Konszolidált fiók és a felügyeleti csoport |   |
| Előfizetés neve | – | Konszolidált fiók és a felügyeleti csoport | Az AWS szervezeti API-t a fiókneveket összegyűjtése. |
| Címke | resourceTags /\* | Összes | A _felhasználói:_ előtag törlődik a felhasználó által megadott címkéket, hogy a több felhőre kiterjedő címkék. A _aws:_ előtag érintetlen. |
| Számlázási fiók azonosítója | bill/PayerAccountId | Felügyeleti csoport |   |
| Számlázási fiók neve | – | Felügyeleti csoport | Az AWS szervezeti API-t a fiókneveket összegyűjtése. |
| Szolgáltató | – | Felügyeleti csoport | Az AWS vagy Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Az AWS-hatókörön set költségvetése

Költségvetés segítségével proaktív módon kezelheti a költségeket és a meghajtó accountability a szervezetben. Költségvetése vannak beállítva az összevont AWS-fiókon és AWS társított fiók hatókörök. Íme egy példa, költségvetések a Cost Management látható egy konszolidált AWS-fiók:

![Az AWS-költségvetése megjelenítő Példa összevont fiók](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Az AWS adatgyűjtési folyamat

Miután beállította az AWS-összekötőt, ha az adatgyűjtés és a felderítési folyamat indítása. Eltarthat néhány óra minden használati adatok gyűjtéséhez. Az időtartam függ:

- Az AWS S3 gyűjtőt a PÉNZNEM fájlok feldolgozásához szükséges időt.
- Az AWS összevont fiók és AWS társított fiókhatókörök létrehozásához szükséges időt.
- Az idő és gyakoriságát tartalmazza az AWS vannak a költségek és a használati jelentés fájlok írása az S3 gyűjtőt a

## <a name="aws-integration-pricing"></a>Az AWS-integráció díjszabása

Minden egyes AWS-összekötő lekérdezi az ingyenes próbaidőszak 90 napig. A nyilvános előzetes verzióban nem jár költségekkel.

A lista ár az 1 %-a havi AWS-költségek. Havonta számítjuk fel a számlázott költség az előző hónap alapján.

AWS – API-k elérése további költségekkel járhat.

## <a name="aws-integration-limitations"></a>Az AWS-integráció korlátozások

- Cost Management nem támogatja a több pénznem típusokat tartalmazó költségek jelentéseiben. Hibaüzenet akkor jelenik meg, ha kiválaszt egy hatókör, amely több pénznem rendelkezik.
- Felhő-összekötők nem támogatják a AWS GovCloud (amerikai), az AWS-beli államigazgatás – vagy az AWS-Kínát.
- A Cost Management jeleníti meg az AWS _adathasználati költségek_ csak. Adó, támogatás, visszatérítések, a fenntartott példányok, kreditek vagy bármely más díj még nem támogatottak.

## <a name="troubleshooting-aws-integration"></a>Az AWS-integráció hibaelhárítása

A következő hibaelhárítási információk használatával kapcsolatos gyakori problémák megoldásához.

### <a name="no-permission-to-aws-linked-accounts"></a>Nincs engedélye az AWS összekapcsolt fiókok

**Hibakód:** _Nem engedélyezett_

Megszerezni a csatolt AWS-fiókok költségek elérésére jogosult két módja van:

- A felügyeleti csoporthoz, amely rendelkezik az AWS az összekapcsolt fiókoknak hozzáférést kap.
- Valaki adjon engedélyt az AWS-beli társított fiók rendelkezik.

Alapértelmezés szerint az AWS-összekötő létrehozó a tulajdonosa az összekötő által létrehozott összes objektumot. Többek között, az AWS összevont fiók és az AWS társított fiókot.

Ahhoz, hogy az összekötő-beállítások ellenőrzése szüksége lesz egy legalább közreműködői szerepkör, olvasó nem ellenőrizheti az összekötő-beállítások

### <a name="collection-failed-with-assumerole"></a>Nem sikerült AssumeRole gyűjtemény

**Hibakód:** _FailedToAssumeRole_

Ez a hiba azt jelenti, hogy a Cost Management az AWS AssumeRole API meghívása nem sikerült. Ez a probléma akkor fordulhat elő, a szerepkör-definíció probléma miatt. Győződjön meg arról, hogy az alábbi feltételek teljesülése esetén:

- A külső azonosítója megegyezik egy, a szerepkör-definíció és az összekötő definíciójának.
- A szerepkör típusa **egy másik AWS tartozó fiókot, vagy a 3. fél.**
- A **többtényezős hitelesítés megkövetelése** választási lehetőség nincs bejelölve.
- A megbízható AWS-fiók az AWS-szerepkörre _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Nem sikerült a hozzáférés megtagadva a gyűjtemény

- **Hibakód:** _AccessDeniedReportDefinitions_ 
- **Hibakód:** _AccessDeniedListReports_ 
- **Hibakód:** _AccessDeniedDownloadReport_ 

Ez a hibaüzenet azt jelenti, hogy Cost Management nem fér hozzá az Amazon S3 gyűjtőt található aktuális fájlok. Győződjön meg arról, hogy a szerepkör csatolt AWS JSON házirend alján látható példához hasonlít-e a [hozzon létre egy szerepkör és a házirend az AWS-ben](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakaszban.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Sikertelen volt, mert nem található a költségek és a használati jelentés gyűjtemény

**Hibakód:** _FailedToFindReport_

Ez a hiba, az azt jelenti, hogy a Cost Management nem találja a felhasználási és jelentés, az összekötő a megadott. Ellenőrizze, hogy az nem törlődik, és, hogy a szerepkör csatolt AWS JSON házirend alján látható példához hasonlít-e a [hozzon létre egy szerepkör és a házirend az AWS-ben](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakaszban.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nem sikerült létrehozni, vagy ellenőrizze az összekötő költség- és használati jelentés definíciók eltérése miatt

**Hibakód:** _ReportIsNotValid_

Ez a hiba vonatkozik, az AWS-költségek és a használati jelentés, hogy szükséges specifikus beállításokat ehhez a jelentéshez, a követelményeket a [felhasználási és jelentés létrehozása az AWS-ben](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>További lépések

- Ha az Azure-környezet még nem konfigurált felügyeleti csoportokkal, lásd: [felügyeleti csoportok telepítése a kezdeti](../governance/management-groups/index.md#initial-setup-of-management-groups).
