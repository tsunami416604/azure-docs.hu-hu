---
title: Az AWS-költségek és-használat kezelése Azure Cost Management
description: Ebből a cikkből megtudhatja, hogyan kezelheti az AWS-költségeket és-használatot Cost Management a Cost Analysis és a költségvetések használatát.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 772f6cdde575a9ac669c73ecca039914357ffe2f
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338895"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Az AWS-költségek és-használat kezelése az Azure-ban

Miután beállította és konfigurálta az AWS költségek és a használati jelentés integrációját a Azure Cost Management számára, készen áll az AWS-költségek és-használat kezelésének megkezdésére. Ebből a cikkből megtudhatja, hogyan kezelheti az AWS-költségeket és-használatot Cost Management a Cost Analysis és a költségvetések használatát.

Ha még nem konfigurálta az integrációt, olvassa el az [AWS használati jelentés integrációjának beállítása és konfigurálása](aws-integration-set-up-configure.md)című témakört.

_Kezdés előtt_: Ha nem ismeri a Cost Analysis-t, tekintse meg a [költségek elemzése és elemzése a Cost Analysis](quick-acm-cost-analysis.md) gyors üzembe helyezését ismertető témakört. Ha nem ismeri a költségvetést az Azure-ban, tekintse meg az [Azure-költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) című oktatóanyagot.

## <a name="view-aws-costs-in-cost-analysis"></a>Az AWS költségeinek megtekintése a Cost Analysis szolgáltatásban

Az AWS költsége a következő hatókörökben érhető el:

- AWS-hez csatolt fiókok felügyeleti csoportban
- AWS-fiókokkal kapcsolatos költségek
- AWS összevont fiók költségei

A következő szakaszok azt ismertetik, hogyan használhatók a hatókörök, hogy mindegyikhez a költségeket és a használati adatokat lássuk.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>AWS-hez csatolt fiókok megtekintése felügyeleti csoportban

A költségeket a felügyeleti csoport hatókörének használatával tekintheti meg, így a különböző előfizetések és a társított fiókok összesített költségei láthatók. A felügyeleti csoportok több felhőből álló nézetet is biztosítanak.

A Cost Analysis alkalmazásban nyissa meg a hatókör-választót, és válassza ki azt a felügyeleti csoportot, amely az AWS-hez társított fiókokat tárolja. Az alábbi ábrán egy példa látható a Azure Portalban:

![Példa a hatókör kiválasztása nézetre](./media/aws-integration-manage/select-scope01.png)



Íme egy példa a felügyeleti csoport költségeit a Cost Analysis, szolgáltató szerint csoportosítva (Azure és AWS).

![Példa az Azure-és AWS-költségekre a Cost Analysis negyedévhez](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Az AWS-hez kapcsolódó fiók költségeinek megtekintése

Az AWS-kapcsolatok fiókjának költségeinek megtekintéséhez nyissa meg a hatókör-választót, és válassza ki az AWS-hez kapcsolt fiókot. Vegye figyelembe, hogy a csatolt fiókok egy felügyeleti csoporthoz vannak társítva, az AWS-összekötőben meghatározottak szerint.

Az alábbi példa egy AWS-hez csatolt fiók hatókörének kiválasztását mutatja be.

![Példa a hatókör kiválasztása nézetre](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Az AWS összevont fiók költségeinek megtekintése

Az AWS-összevont fiókok költségeinek megtekintéséhez nyissa meg a hatókör-választót, és válassza ki az AWS konszolidált fiókot. Az alábbi példa egy AWS konszolidált fiók hatókörének kiválasztását mutatja be.

![Példa a hatókör kiválasztása nézetre](./media/aws-integration-manage/select-scope03.png)



Ez a hatókör összesített áttekintést nyújt az AWS-összevont fiókhoz társított összes AWS-hez kapcsolódó fiókról. Íme egy példa, amely egy AWS összevont fiókra vonatkozó költségeket mutat be szolgáltatásnév szerint csoportosítva.

![Példa az AWS konszolidált költségekre a Cost Analysis szolgáltatásban](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Szűréshez és csoportosításhoz elérhető dimenziók

A következő táblázat a csoportok számára elérhető dimenziókat ismerteti a Cost Analysis szolgáltatásban.

| Dimenzió | Amazon akt fejléc | Hatókörök | Megjegyzések |
| --- | --- | --- | --- |
| Rendelkezésre állási zóna | lineitem/AvailabilityZone | Összes |   |
| Location | termék/régió | Összes |   |
| Mérő |   | Összes |   |
| Fogyasztásmérő kategóriája | lineItem/ProductCode | Összes |   |
| Fogyasztásmérő alkategóriája | lineitem/Usagetype értékre | Összes |   |
| Művelet | lineItem/Operation | Összes |   |
| Resource | lineItem/ResourceId | Összes |   |
| Erőforrás típusa | termék/instanceType | Összes | Ha a Product/instanceType értéke null, a rendszer a lineItem/Usagetype értékre-et használja. |
| Erőforrás GUID azonosítója | – | Összes | Az Azure Meter GUID-azonosítója. |
| Szolgáltatás neve | termék/ProductName | Összes | Ha a Product/ProductName értéke null, a rendszer lineItem/ProductCode használ. |
| Szolgáltatási szint |   |   |   |
| Előfizetés azonosítója | lineItem/UsageAccountId | Konszolidált fiók és felügyeleti csoport |   |
| Előfizetés neve | – | Konszolidált fiók és felügyeleti csoport | A rendszer az AWS szervezeti API használatával gyűjti a fiók nevét. |
| Címke | resourceTags/@no__t – 0 | Összes | A _felhasználó:_ előtagot a rendszer eltávolítja a felhasználó által definiált címkékről a több felhőből származó címkék engedélyezéséhez. Az _AWS:_ előtag érintetlen marad. |
| Számlázási fiók azonosítója | bill/PayerAccountId | Felügyeleti csoport |   |
| Számlázási fiók neve | – | Felügyeleti csoport | A rendszer az AWS szervezeti API használatával gyűjti a fiók nevét. |
| Szolgáltató | – | Felügyeleti csoport | AWS vagy Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Költségvetések beállítása AWS-hatókörökre

A költségvetések segítségével proaktív módon kezelheti a költségeket, és elvégezheti az elszámoltathatóságot a szervezetben. A költségvetést az AWS konszolidált fiókja és az AWS-hez csatolt fiókok hatóköre határozza meg. Íme egy példa a költségvetésekre egy, a Cost Managementban látható AWS összevont fiókhoz:

![Példa egy AWS konszolidált fiók költségvetésére](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS adatgyűjtési folyamat

Az AWS-összekötő beállítása után elindul az adatgyűjtési és-felderítési folyamatok. Az összes használati adat összegyűjtése néhány órát is igénybe vehet. Az időtartam a következőktől függ:

- Az AWS S3 gyűjtőben található, az aktuális fájlok feldolgozásához szükséges idő.
- Az AWS konszolidált fiók és az AWS-hez csatolt fiókok hatókörének létrehozásához szükséges idő.
- Az AWS ideje és gyakorisága az S3 gyűjtőben lévő Cost és a használati jelentések fájljait írja le.

## <a name="aws-integration-pricing"></a>AWS-integráció díjszabása

Minden AWS-összekötő 90 ingyenes próbaidőszakot kap. A nyilvános előzetes verzióban díjmentesen használható.

A lista ára az AWS havi költségeinek 1%-a. Minden hónapban az előző hónap számlázott költségei alapján számítjuk fel a díjat.

Az AWS API-k elérése további költségeket eredményezhet.

## <a name="aws-integration-limitations"></a>AWS-integrációs korlátozások

- A Cost Management nem támogatja a több pénznemszimbólumot tartalmazó Cost-jelentéseket. Ha olyan hatókört választ ki, amely több pénznemet tartalmaz, hibaüzenet jelenik meg.
- A Felhőbeli összekötők nem támogatják az AWS GovCloud (US), az AWS gov vagy az AWS China platformot.
- Cost Management csak az AWS _használati költségeit_ jeleníti meg. Az adó, a támogatás, a visszatérítések, az RI, a kreditek vagy bármely egyéb díjköteles típus még nem támogatott.

## <a name="troubleshooting-aws-integration"></a>AWS-integráció hibaelhárítása

A gyakori problémák megoldásához használja az alábbi hibaelhárítási információkat.

### <a name="no-permission-to-aws-linked-accounts"></a>Nincs engedélye az AWS-hez csatolt fiókokhoz

**Hibakód:** _Jogosulatlan_

Az AWS-hez kapcsolódó fiókok költségeinek eléréséhez kétféleképpen kaphat engedélyeket:

- Szerezze be azt a felügyeleti csoportot, amelyhez az AWS-hez társított fiókok vannak társítva.
- Ha valaki engedélyt ad az AWS-hez kapcsolódó fiókra.

Alapértelmezés szerint az AWS-összekötő létrehozója az összekötő által létrehozott összes objektum tulajdonosa. Beleértve az AWS-összevont fiókot és az AWS-hez kapcsolódó fiókot is.

Az összekötő beállításainak ellenőrzéséhez legalább közreműködői szerepkörre lesz szüksége, az olvasó nem tudja ellenőrizni az összekötő beállításait

### <a name="collection-failed-with-assumerole"></a>A gyűjtemény nem sikerült a AssumeRole

**Hibakód:** _FailedToAssumeRole_

Ez a hiba azt jelenti, hogy a Cost Management nem tudja hívni az AWS AssumeRole API-t. Ez a probléma a szerepkör-definícióval kapcsolatos probléma miatt fordulhat elő. Ellenőrizze, hogy a következő feltételek teljesülnek-e:

- A külső azonosító ugyanaz, mint a szerepkör-definícióban és az összekötő definíciójában.
- A szerepkör típusa **egy másik AWS-fiókra van beállítva, amely az Ön vagy harmadik fél tulajdonában áll.**
- Az **MFA megkövetelése** beállítás nincs bejelölve.
- Az AWS szerepkörben a megbízható AWS-fiók _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>A gyűjtemény nem sikerült – a hozzáférés megtagadva – a jelentés definíciói

**Hibakód:** _AccessDeniedReportDefinitions_ 

Ez a hiba azt jelenti, hogy Cost Management nem tudja megtekinteni a használati jelentés definícióit. Ezzel a jogosultsággal ellenőrizheti, hogy a Azure Cost Management a várt módon van-e definiálva. Lásd: [Cost-és használati jelentés létrehozása az AWS-ben](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>A gyűjtemény nem sikerült – a hozzáférés megtagadva – jelentések listázása

**Hibakód:** _AccessDeniedListReports_ 

Ez a hiba azt jelenti, hogy a Cost Management nem tudja listázni az az S3 gyűjtőben lévő objektumot, ahol az a pénznem található. Az AWS IAM-szabályzatnak engedélyre van szüksége a gyűjtőn és a gyűjtőben lévő objektumokon. Lásd: [szerepkör és szabályzat létrehozása AWS-ben](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>A gyűjtemény nem sikerült – a hozzáférés megtagadva – jelentés letöltése 

**Hibakód:** _AccessDeniedDownloadReport_ 

Ez a hiba azt jelenti, hogy a Cost Management nem tud hozzáférni és letölteni az Amazon S3-gyűjtőben tárolt fájlokat. Győződjön meg arról, hogy a szerepkörhöz csatolt AWS JSON-szabályzat hasonlít a [szerepkör és a házirend létrehozása AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakaszban látható példához.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>A gyűjtemény nem sikerült, mert nem találta meg a költségeket és a használati jelentést

**Hibakód:** _FailedToFindReport_

Ez a hiba azt jelenti, hogy Cost Management nem találja az összekötőben definiált költségeket és használati jelentést. Győződjön meg arról, hogy nincs törölve, és hogy a szerepkörhöz csatolt AWS JSON-szabályzat hasonlít a [szerepkör és a házirend létrehozása AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakaszban látható példához.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nem lehet létrehozni vagy ellenőrizni az összekötőt, mert nem egyeznek a Cost és a használati jelentés definíciói

**Hibakód:** _ReportIsNotValid_

Ez a hiba az AWS-költségek és-használati jelentés definíciójában szerepel, ehhez a jelentéshez speciális beállítások szükségesek, lásd a [Cost and használati jelentés létrehozása az AWS-ben](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) című témakör követelményeit.

## <a name="next-steps"></a>További lépések

- Ha még nem konfigurálta az Azure-környezetet felügyeleti csoportokkal, tekintse meg a [felügyeleti csoportok kezdeti beállítását](../governance/management-groups/overview.md#initial-setup-of-management-groups)ismertető témakört.
