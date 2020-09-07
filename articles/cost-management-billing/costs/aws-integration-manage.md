---
title: Az AWS-költségek és -használat kezelése az Azure Cost Managementben
description: Ez a cikk segít megérteni, hogyan használhatja a költségelemzést és a költségvetéseket a Cost Managementben saját AWS-költségei és -használata kezeléséhez.
author: bandersmsft
ms.author: banders
ms.date: 08/28/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 7df27a6ed288555d0f4815223fd0bb6dddff6f44
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266196"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Az AWS-költségek és -használat kezelése az Azure-ban

Miután beállította és konfigurálta az AWS költség- és használati jelentés integrálását az Azure Cost Management esetében, készen áll az AWS-költségek és -használat kezelésének megkezdésére. Ez a cikk segít megérteni, hogyan használhatja a költségelemzést és a költségvetéseket a Cost Managementben saját AWS-költségei és -használata kezeléséhez.

Ha még nem konfigurálta az integrációt, lásd az [AWS használati jelentés integrálásának beállítását és konfigurálását ismertető részt](aws-integration-set-up-configure.md).

_Előkészületek_: Ha korábban még nem használta a költségelemzést, tekintse meg a [Költségek feltérképezése és elemzése költségelemzés használatával](quick-acm-cost-analysis.md) gyorsútmutatót. Ha pedig korábban még nem használta az Azure-költségvetéseket, tekintse meg az [Azure-költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) oktatóanyagot.

## <a name="view-aws-costs-in-cost-analysis"></a>AWS-költségek megtekintése a költségelemzésben

Az AWS-költségek a következő hatókörökre vonatkozóan állnak rendelkezésre a költségelemzésben:

- Csatolt AWS-fiókok egy felügyeleti csoport esetében
- Csatolt AWS-fiók költségei
- Összesített AWS-fiók költségei

A következő szakaszok azt ismertetik, hogyan történik a hatókörök használata a költség- és használati adatok megtekintéséhez az egyes hatókörök esetében.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Csatolt AWS-fiókok megtekintése egy felügyeleti csoport esetében

A költségek felügyeleticsoport-hatókör használatával történő megtekintése az egyetlen módja a különböző Azure-előfizetésekből és összesített AWS-fiókokból származó összesített költségek láthatóvá tételének. A felügyeleti csoport használata több felhőre kiterjedő nézetet biztosít az Azure és az AWS költségeinek együttes megtekintéséhez.

A költségelemzésben nyissa meg a hatókörválasztót, és válassza ki azt a felügyeleti csoportot, amelyben saját csatolt AWS-fiókjai találhatók. Alább egy szemléltető kép látható az Azure Portalon:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Példa a hatókör-kijelölési nézetre felügyeleti csoporthoz tartozó összekapcsolt fiókokkal" :::

Az alábbi példa bemutatja a felügyeleti csoport költségeit a költségelemzésben, szolgáltató (Azure és AWS) szerint csoportosítva.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Negyedéves Azure- és AWS-költségeket bemutató példa a költségelemzésben" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> A felügyeleti csoportok a Microsoft Ügyfélszerződéssel (MCA) rendelkező ügyfelek esetében jelenleg nem támogatottak. Az MCA-ügyfelek létrehozhatják az összekötőt és megtekinthetik AWS-adataikat. Az MCA-ügyfelek azonban nem tekinthetik meg egyszerre az Azure- és az AWS-költségeiket egy felügyeleti csoport esetében.

### <a name="view-aws-linked-account-costs"></a>Csatolt AWS-fiók költségeinek megtekintése

A csatolt AWS-fiók költségeinek megtekintéséhez nyissa meg a hatókörválasztót, és válassza ki a csatolt AWS-fiókot. Vegye figyelembe, hogy a csatolt fiókok felügyeleti csoporthoz vannak rendelve, az AWS-összekötőben meghatározott módon.

Az alábbi példa egy csatolt AWS-fiók hatókörének kiválasztását mutatja be.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Példa hatókör-kijelölési nézetre, amely összekapcsolt AWS-fiókokat mutat" :::

### <a name="view-aws-consolidated-account-costs"></a>Összesített AWS-fiók költségeinek megtekintése

Az összesített AWS-fiók költségeinek megtekintéséhez nyissa meg a hatókörválasztót, és válassza ki az összesített AWS-fiókot. Az alábbi példa egy összesített AWS-fiók hatókörének kiválasztását mutatja be.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Példa a hatókör-kijelölési nézetre összesített fiókokkal" :::

Ez a hatókör összesített nézetet biztosít az összesített AWS-fiókhoz rendelt összes csatolt AWS-fiók esetében. Az alábbi példa egy összesített AWS-fiók költségeit mutatja be, szolgáltatásnév szerint csoportosítva.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Példa az összesített AWS-fiók költségeire a költségelemzésben" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>A szűréshez és a csoportosításhoz rendelkezésre álló dimenziók

Az alábbi táblázat ismerteti a csoportosításhoz és szűréshez rendelkezésre álló dimenziókat a költségelemzésben.

| Dimenzió | Amazon CUR-fejléc | Hatókörök | Megjegyzések |
| --- | --- | --- | --- |
| A rendelkezésre állási zóna | lineitem/AvailabilityZone (sortétel/RendelkezésreállásiZóna) | Összes |   |
| Hely | product/Region (termék/Régió) | Mind |   |
| Mérő |   | Összes |   |
| Meter Category (Mérési kategória) | lineItem/ProductCode (sortétel/Termékkód) | Összes |   |
| Meter Subcategory (Mérési alkategória) | lineitem/UsageType (sortétel/HasználatTípusa) | Összes |   |
| Művelet | lineItem/Operation (sortétel/Művelet) | Összes |   |
| Erőforrás | lineItem/ResourceId (sortétel/ErőforrásAzonosítója) | Összes |   |
| Erőforrás típusa | product/instanceType (termék/PéldányTípusa) | Mind | Ha a product/instanceType (termék/PéldányTípusa) értéke null, a rendszer a lineitem/UsageType (sortétel/HasználatTípusa) értékét használja. |
| ResourceGuid (Erőforrás GUID azonosítója) | N/A | Mind | Azure-beli mérőeszköz GUID azonosítója. |
| Szolgáltatásnév | product/ProductName (termék/Terméknév) | Mind | Ha a product/ProductName (termék/Terméknév) értéke null, a rendszer a lineItem/ProductCode (sortétel/Termékkód) értékét használja. |
| Szolgáltatási szint |   |   |   |
| Előfizetés azonosítója | lineItem/UsageAccountId (sortétel/HasználatiFiókAzonosítója) | Összesített fiók és felügyeleti csoport |   |
| Előfizetés neve | N/A | Összesített fiók és felügyeleti csoport | A fióknevek gyűjtése az AWS Organization API használatával történik. |
| Címke | resourceTags | Mind | A _user:_ előtag el lett távolítva a felhasználó által definiált címkékből a több felhőre kiterjedő címkék engedélyezéséhez. Az _aws:_ előtag nem módosult. |
| Számlázási fiók azonosítója | bill/PayerAccountId (számla/BefizetőiFiókAzonosítója) | Felügyeleti csoport |   |
| Számlázási fiók neve | N/A | Felügyeleti csoport | A fióknevek gyűjtése az AWS Organization API használatával történik. |
| Szolgáltató | N/A | Felügyeleti csoport | AWS vagy Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Költségvetések beállítása AWS-hatókörökhöz

A költségvetések segítségével proaktív módon kezelhetők a költségek, illetve hatékonyabb szervezeti elszámoltathatóságot tesznek lehetővé. A költségvetések beállítása az összesített és a csatolt AWS-fiókok hatóköreire történik. Az alábbi példa bemutatja egy összesített AWS-fiók költségvetéseit a Cost Managementben:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Példa egy összesített AWS-fiók költségvetéseiről" :::

## <a name="aws-data-collection-process"></a>AWS-adatgyűjtési folyamat

Az AWS-összekötő beállítása után elindulnak az adatgyűjtési és -felderítési folyamatok. Az összes használati adat összegyűjtése néhány órát vehet igénybe. A folyamat időtartama a következőktől függ:

- Az AWS S3-gyűjtőben lévő CUR-fájlok feldolgozásához szükséges idő.
- Az összesített és a csatolt AWS-fiók hatóköreinek létrehozásához szükséges idő.
- Az AWS-időpont és -gyakoriság írása a költség- és használati jelentés fájljaiba történik az S3-gyűjtőben

## <a name="aws-integration-pricing"></a>Az AWS-integráció díjszabása

Minden AWS-összekötőhöz 90 napos ingyenes próbaidőszak tartozik.

A listaár a havi AWS-költségeinek 1%-a. Minden hónapban az előző hónap számlázott költségei alapján számítják fel Önnek a díjat.

Az AWS API-khoz való hozzáférés további költségvonzattal járhat az AWS esetében.

## <a name="aws-integration-limitations"></a>Az AWS-integráció korlátozásai

- A Cost Management költségvetései nem támogatják a több pénznemmel rendelkező felügyeleti csoportokat. A több pénznemmel rendelkező felügyeleti csoportoknál nem jelenik meg költségvetés-kiértékelés. Ha több pénznemmel rendelkező felügyeleti csoportot választ ki a költségvetés létrehozásakor, hibaüzenet jelenik meg.
- A felhőbeli összekötők nem támogatják az AWS GovCloud (USA), az AWS gov és az AWS China (Kína) platformot.
- A Cost Management csak az AWS _használati költségeit_ jeleníti meg. Az adó, a támogatás, a visszatérítések, a fenntartott példányok, a jóváírások vagy az egyéb díjtételtípusok egyelőre nem támogatottak.

## <a name="troubleshooting-aws-integration"></a>Az AWS-integráció hibaelhárítása

Az alábbi hibaelhárítási megoldásokkal elháríthatók a gyakori problémák.

### <a name="no-permission-to-aws-linked-accounts"></a>Nincs engedélye a csatolt AWS-fiókokhoz

**Hibakód:** _Nem engedélyezett_

Kétféleképpen kaphat engedélyt a csatolt AWS-fiókok költségeinek eléréséhez:

- Szerezzen hozzáférést ahhoz a felügyeleti csoporthoz, ahol a csatolt AWS-fiókok találhatók.
- Szerezzen valakitől engedélyt a csatolt AWS-fiókhoz.

Alapértelmezés szerint az AWS-összekötő létrehozója az összekötő által létrehozott összes objektum tulajdonosa. Ide értendő az összesített és a csatolt AWS-fiók is.

Az összekötő beállításainak ellenőrzéséhez legalább a közreműködői szerepkörhöz kell tartoznia, olvasói szerepkörrel nem ellenőrizheti az összekötő beállításait

### <a name="collection-failed-with-assumerole"></a>A gyűjtés meghiúsult az AssumeRole szerepkör használatával

**Hibakód:** _FailedToAssumeRole_

Ez a hiba azt jelenti, hogy a Cost Management nem tudja meghívni az AWS AssumeRole API-t. Ez a probléma a szerepkör-definícióval kapcsolatos hiba miatt fordulhat elő. Ellenőrizze, hogy a következő feltételek teljesülnek-e:

- A külső azonosító megegyezik a szerepkör és az összekötő definíciójában szereplővel.
- A szerepkörtípusa beállítása **másik, Önhöz vagy harmadik félhez tartozó AWS-fiók**.
- Az **MFA megkövetelése** kiválasztás törlődik.
- Az AWS-szerepkörben található megbízható AWS-fiók: _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>A gyűjtés meghiúsult a hozzáférés megtagadása miatt – CUR-jelentésdefiníciók

**Hibakód:** _AccessDeniedReportDefinitions_

Ez a hiba azt jelenti, hogy a Cost Management nem látja a költség- és használati jelentés definícióit. Ezzel az engedéllyel ellenőrizhető, hogy a CUR definíciója megfelel-e az Azure Cost Management elvárásainak. Lásd a [költség- és használati jelentés AWS-beli létrehozásával foglalkozó részt](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>A gyűjtés meghiúsult a hozzáférés megtagadása miatt – Jelentések listázása

**Hibakód:** _AccessDeniedListReports_

Ez a hiba azt jelenti, hogy a Cost Management nem tudja listázni az objektumot az S3-gyűjtőben, ahol a CUR található. Az AWS IAM-szabályzat szerint engedélyre van szükség a gyűjtőre és a gyűjtőben lévő objektumokra vonatkozóan. Lásd a [szerepkör és szabályzat AWS-beli létrehozásával foglalkozó részt](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>A gyűjtés meghiúsult a hozzáférés megtagadása miatt – Jelentés letöltése

**Hibakód:** _AccessDeniedDownloadReport_

Ez a hiba azt jelenti, hogy a Cost Management nem tudja elérni és letölteni az Amazon S3-gyűjtőben lévő CUR-fájlokat. Győződjön meg arról, hogy a szerepkörhöz csatolt AWS JSON-szabályzat hasonlít a [szerepkör és szabályzat AWS-beli létrehozásával foglalkozó](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakasz végén található példára.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>A gyűjtés meghiúsult, mivel a költség- és használati jelentés nem található

**Hibakód:** _FailedToFindReport_

Ez a hiba azt jelenti, hogy a Cost Management nem találja az összekötőben definiált költség- és használati jelentést. Győződjön meg arról, hogy a jelentés nem lett törölve, továbbá a szerepkörhöz csatolt AWS JSON-szabályzat hasonlít a [szerepkör és szabályzat AWS-beli létrehozásával foglalkozó](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) szakasz végén található példára.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nem lehet létrehozni vagy ellenőrizni az összekötőt a költség- és használati jelentés definícióinak eltérései miatt

**Hibakód:** _ReportIsNotValid_

Ez a hiba az AWS költség- és használati jelentés definíciójával kapcsolatos, jelentésspecifikus beállításokra van szükség. Lásd a [költség- és használati jelentés AWS-beli létrehozásával foglalkozó részt](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Következő lépések

- Ha még nem konfigurálta az Azure-környezetét a felügyeleti csoportokkal, lásd: [A felügyeleti csoportok kezdeti beállítása](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
