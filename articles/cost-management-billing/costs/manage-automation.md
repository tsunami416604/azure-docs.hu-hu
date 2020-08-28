---
title: Azure-költségek kezelése automatizálással
description: Ez a cikk az Azure-költségek automatizálással való kezelését ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 08/19/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a5ab84794884cc0c87bd766be7a0fa2fe4c52aa9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684405"
---
# <a name="manage-costs-with-automation"></a>Költségek kezelése automatizálással

A Cost Management automatizálásával egy egyéni megoldáskészletet hozhat létre a költségadatok lekérdezéséhez és kezeléséhez. Ez a cikk a Cost Management automatizálásának gyakori forgatókönyveit, illetve a helyzete alapján rendelkezésre álló lehetőségeket ismerteti. Ha API-kat szeretne használni a fejlesztéshez, a gyakori API-kérések példáival felgyorsíthatja a fejlesztési folyamatot.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Költségadatok lekérésének automatizálása offline elemzéshez

Előfordulhat, hogy le kell töltenie az Azure-költségadatokat ahhoz, hogy egyesíteni tudja azokat más adathalmazokkal. Az is lehet, hogy integrálnia kell a költségadatokat a saját rendszereibe. Az adatok mennyiségétől függően többféle lehetőség áll rendelkezésre. Az API-k és az eszközök használatához minden esetben rendelkeznie kell Cost Management-engedélyekkel a megfelelő hatókörben. További információt az [adatokhoz való hozzáférés hozzárendelését](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data) ismertető szakaszban talál.

## <a name="suggestions-for-handling-large-datasets"></a>Javaslatok a nagy méretű adathalmazok kezelésére

Ha a szervezet sok erőforrással vagy előfizetéssel van jelen az Azure-ban, rengeteg használati adattal fog rendelkezni. Az Excel gyakran nem tudja betölteni az ilyen nagy méretű fájlokat. Ebben az esetben az alábbi lehetőségeket javasoljuk:

**Power BI**

A Power BI-t nagyobb adatmennyiségek betöltéséhez és kezeléséhez használják. Ha Ön Nagyvállalati Szerződéssel rendelkező ügyfél, akkor a Power BI-sablonalkalmazással elemezheti a számlázási fiókja költségeit. A jelentés az ügyfelek által használt legfontosabb nézeteket tartalmazza. További információt [az Azure költségeinek a Power BI-sablonalkalmazással történő elemzését](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app) ismertető szakaszban talál.

**Power BI-adatösszekötő**

Ha naponta szeretné elemezni az adatokat, a [Power BI-adatösszekötő](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) használatát javasoljuk a részletes elemzéshez szükséges adatok lekéréséhez. Az összekötő nyomon követi a költségek alakulását, és naprakészen tartja a létrehozott jelentéseket.

**Cost Management – Exports**

Az is előfordulhat, hogy nem kell minden nap elemeznie az adatokat. Ebben az esetben érdemes lehet a Cost Management [Exports](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) API-ját használni az adatok Azure Storage-fiókba való exportálásának ütemezéséhez. Az adatokat ezután szükség szerint betöltheti a Power BI-ba, vagy ha elég kicsi a fájl, az Excelben is elemezheti. Az exportálások elérhetők az Azure Portalon, vagy az [Exports API-val](https://docs.microsoft.com/rest/api/cost-management/exports) is konfigurálhatja őket.

**Usage Details API**

Akkor érdemes megfontolni a [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) használatát, ha kis méretű költségadathalmazzal rendelkezik. Ha nagy mennyiségű költségadattal rendelkezik, akkor a lehető legkisebb mennyiségű használati adatot kell lekérnie az adott időszakból. Ehhez adjon meg egy rövid időtartományt, vagy használjon szűrőt a kérelemben. Például ha három év költségadataira van szüksége, az API jobban teljesít, amikor egyetlen hívás helyett több, különböző időtartományokat megcélzó hívást indít. Ezután betöltheti az adatokat az Excelbe a további elemzéshez.

## <a name="automate-retrieval-with-usage-details-api"></a>Lekérés automatizálása a Usage Details API-val

A [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usageDetails) egyszerű módot kínál az Azure-számlájának megfelelő, nyers, nem összesített költségadatok lekérésére. Az API akkor hasznos, ha a szervezetnek programozott adatlekérési megoldásra van szüksége. Akkor érdemes megfontolni az API használatát, ha kisebb költségadathalmazokat szeretne elemezni. Ha nagyobb adathalmazokkal rendelkezik, használja a korábban említett megoldások egyikét. A Usage Details mérőeszközök alapján adja meg az adatokat, napi bontásban. A havi számla kiszámításához használják. Az API-k általánosan elérhető (GA) verziója a `2019-10-01`. Az API-kkal végzett foglalások és Azure Marketplace-vásárlások előzetes verziójának eléréséhez használja a `2019-04-01-preview` verziót.

### <a name="usage-details-api-suggestions"></a>Usage Details API-ra vonatkozó javaslatok

**Kérelmek ütemezése**

Azt javasoljuk, hogy naponta _ne küldjön egynél több kérelmet_ a Usage Details API-nak. További információk a költségadatok frissítési gyakoriságáról és a kerekítésről [a költségkezelési adatok értelmezését](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule) ismertető szakaszban találhatók.

**Legfelső szintű hatókörök megcélzása szűrés nélkül**

Az API-val lekérheti az összes szükséges adatot az Ön számára elérhető legmagasabb szintű hatókörben. A szűrés, a csoportosítás és az összesített elemzés elvégzése előtt várja meg, amíg a rendszer betölti a szükséges adatokat. Az API-t kifejezetten a nagy mennyiségű, nem összesített, nyers költségadatok biztosítására optimalizálták. További információk a Cost Management hatóköreiről [a hatókörök ismertetését és használatát](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes) bemutató szakaszban találhatók. Miután letöltötte a hatókör szükséges adatait, Excelben a szűrőkkel és a kimutatásokkal lehetőség nyílik a további elemzésükre.

## <a name="example-usage-details-api-requests"></a>Példák a Usage Details API kéréseire

A következő példakérelmeket a Microsoft ügyfelei használják a gyakori forgatókönyvekben.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>A Usage Details lekérése egy hatókör adott dátumtartományához

A kérelem által visszaadott adatok megfelelnek annak a dátumnak, amikor a számlázási rendszer megkapta a használati adatokat. Több számla költségeit is tartalmazhatja.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Amortizált költségadatok lekérése

Ha azt szeretné, hogy a tényleges költségekben a vásárlások keletkezésük szerinti jelenjenek meg, módosítsa az alábbi kérelem *metrikáját* a következőre: `ActualCost`. Az amortizált és a tényleges költségek használatához a `2019-04-01-preview` verziót kell használni. A jelenlegi API-verzió ugyanúgy működik, mint a `2019-10-01` verzió, az új típus/metrika attribútum és a módosított tulajdonságnevek kivételével. Ha Microsoft Ügyfélszerződéssel rendelkezik, a példa szűrői a `startDate` és az `endDate`.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Nagy méretű költségadathalmazok ismétlődő lekérése az Exports használatával

Rendszeresen exportálhat nagy mennyiségű adatot a Cost Management exportálási funkciójával. Az exportálás az ajánlott módszer a nem összesített költségadatok lekérésére. Különösen akkor, amikor a használati adatokat tartalmazó fájlok túl nagyok ahhoz, hogy megbízhatóan meg lehessen őket hívni és letölteni a Usage Details API-val. Az exportált adatok a kiválasztott Azure Storage-fiókba lesznek helyezve. Ezt követően betöltheti az adatokat a saját rendszereibe, és igény szerint elemezheti őket. Az Exports az Azure Portalon való konfigurálásához lásd az [adatok exportálását](tutorial-export-acm-data.md) ismertető szakaszt.

Ha az exportálást különböző hatókörökben szeretné automatizálni, a következő szakaszban található minta API-kérelem jó kiindulási pont lehet. Az Exports API-val az általános környezeti konfiguráció részeként hozhat létre automatikus exportálást. Az automatikus exportálással biztosíthatja, hogy a szükséges adatok rendelkezésére álljanak. Az Azure használatának bővítésével párhuzamosan ezt a saját szervezeti rendszerében is használhatja.

### <a name="common-export-configurations"></a>Az exportálások gyakori konfigurációi

Az első exportálás létrehozása előtt állapítsa meg, milyen az adott forgatókönyv, és milyen konfigurációs beállításokkal valósulhat meg. Fontolja meg a következő exportálási beállítások alkalmazását:

- **Ismétlődés** – Meghatározza az exportálási feladat futtatásának gyakoriságát és a fájlok Azure Storage-fiókba való helyezésének idejét. Napi, heti és havi ütemezés választható. Próbálja meg úgy konfigurálni az ismétlődést, hogy az megfelelő legyen a szervezet belső rendszere által használt adatimportálási feladatokhoz.
- **Ismétlődési időtartam** – Meghatározza az exportálás érvényességének időtartamát. A fájlokat a rendszer csak az ismétlődési időtartamban exportálja.
- **Időkeret** – Meghatározza az egy adott futtatás során az exportálás által előállított adatok mennyiségét. Gyakran használt lehetőség az Ebben a hónapban és az Ez a hét.
- **Kezdő dátum** – Konfigurálja az exportálás ütemezésének kezdetét. Az exportálás létrehozása a Kezdő dátum értékeként megadott napon történik, majd ezt követően az Ismétlődés alapján.
- **Típus** – Az exportálásnak három típusa van:
  - Tényleges költség – Egy meghatározott időszak alatt keletkező és a számlán megjelenő összes használatot és költséget mutatja.
  - Amortizált költség – Egy meghatározott időszak alatt keletkező és a számlán megjelenő összes használatot és költséget mutatja a vonatkozó foglalásvásárlási költségekre alkalmazott amortizációval együtt.
  - Használat – A 2020. július 20. előtt létrehozott valamennyi exportálás Használat típusú. Frissítse az összes ütemezett exportálást Tényleges költség vagy Amortizált költség típusúra.
- **Oszlopok** – Meghatározza, hogy milyen adatmezőket tartalmazzon az exportált fájl. Ezek megegyeznek a Usage Details API-ban elérhető mezőkkel. További információkért lásd: [Usage Details API](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Napi tárgyhavi exportálás létrehozása egy előfizetéshez

Kérés URL-címe: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Költségvetéssel kapcsolatos riasztások és műveletek automatizálása

Két kritikus tényező szükséges ahhoz, hogy a felhővel kapcsolatos befektetéséből a lehető legtöbbet tudja kihozni. Az egyik az automatikus költségvetés-létrehozás. A másik a költségalapú vezénylés konfigurálása a költségvetési riasztásokra válaszul. Az Azure-beli költségvetés-létrehozás automatizálásának három különböző módja van. A különböző riasztási válaszok a beállított riasztási küszöbérték túllépése esetén mennek végbe.

A következő szakaszok bemutatják az elérhető lehetőségeket, és minta API-kérelmeket ismertetnek a költségvetések automatizálásának megkezdéséhez.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>A költségek kiértékelése a költségvetési küszöbértékhez képest

A költségek a költségvetési küszöbértékhez képesti kiértékelése naponta egyszer történik meg. Amikor új költségvetést hoz létre vagy eléri a költségvetés alaphelyzetbe állításának napját, a költségek mértéke a küszöbértékhez képest nulla lesz, mert előfordulhat, hogy nem történt meg az értékelés.

Ha az Azure azt észleli, hogy a költségek túllépték a küszöbértéket, a rendszer az észlelést követő egy órán belül értesítést küld.

#### <a name="view-your-current-cost"></a>Aktuális költségek megtekintése

A jelenlegi költségek megtekintéséhez GET-hívást kell végrehajtania a [Query API](/rest/api/cost-management/query)-val.

A Budgets API GET-hívása nem adja vissza a költségelemzésben szereplő aktuális költségeket. A hívás ehelyett a legutóbbi kiértékelt költségeket adja vissza.

### <a name="automate-budget-creation"></a>Költségvetések létrehozásának automatizálása

A költségvetések létrehozását a [Budgets API](/rest/api/consumption/budgets)-val automatizálhatja. Emellett [költségvetési sablonnal](quick-create-budget-template.md) is létrehozhat költségvetést. A sablonokkal könnyedén egységesítheti az Azure-beli üzemelő példányokat, ráadásul a költségszabályozás megfelelően konfigurálva és érvényesítve lesz.

#### <a name="common-budgets-api-configurations"></a>Gyakori Budgets API-konfigurációk

A költségvetéseket számos különféle módon konfigurálhatja az Azure-környezetben. Gondolja végig az adott forgatókönyvet, majd állapítsa meg, mely konfigurációs beállításokkal valósulhat meg. Tekintse át a következő beállításokat:

- **Időfelbontási szint** – Az az ismétlődő időszak, amely alapján a költségvetés kiszámítja és kiértékeli a költségeket. A leggyakrabban használt lehetőségek a havi, negyedéves és éves időszakok.
- **Időtartam** – A költségvetés érvényességének hossza. A költségvetés csak addig végez aktív monitorozást és küld riasztásokat, amíg érvényben van.
- **Értesítések**
  - Kapcsolattartói e-mail-címek – Azok az e-mail-címek, amelyekre a rendszer riasztásokat küld a költségvetésben felmerülő költségekről és a küszöbértékek túllépéséről.
  - Kapcsolattartói szerepkörök – A beállítás kiválasztásakor minden felhasználó e-mailes riasztást kap, aki megfelelő Azure RBAC-szerepkörrel rendelkezik az adott hatókörben. Az előfizetés-tulajdonosok például riasztásokat kaphatnak az előfizetés hatókörében létrehozott költségvetésekről.
  - Kapcsolattartói csoportok – A riasztási küszöbérték túllépésekor a költségvetés meghívja a beállított műveletcsoportokat.
- **Költségdimenzió-szűrők** – A költségelemzésben vagy a Query API-ban alkalmazott szűrések a költségvetésen is alkalmazhatók. A szűrővel csökkentheti a költségvetésben monitorozott költségek tartományát.

Miután azonosította az igényeinek megfelelő költségvetés-létrehozási lehetőségeket, hozza létre a költségvetést az API-val. Az alábbi példa segít a gyakori költségvetés-konfigurációk első lépéseiben.

**Több erőforrásra és címkére szűrt költségvetés létrehozása**

Kérés URL-címe: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Költségalapú vezénylés konfigurálása költségvetési riasztásokhoz

Az Azure-műveletcsoportokkal konfigurálhatja, hogy a költségvetések automatizált műveleteket indítsanak el. A költségvetésekkel végzett automatizált műveletekről további információkat az [Azure-költségvetésekkel való automatizálásról](../manage/cost-management-budget-scenario.md) szóló témakörben találhat.

## <a name="data-latency-and-rate-limits"></a>Adatkésés és sebességkorlátok

Azt javasoljuk, hogy naponta csak egy alkalommal hívja meg az API-kat. A Cost Management adatai négyóránként frissülnek. A rendszer ekkor kapja meg az új használati adatokat az Azure-beli erőforrás-szolgáltatóktól. Az ennél gyakoribb hívás nem szolgál további adatokkal. Csak a terhelést növeli meg. További információk az adatok frissítési gyakoriságáról és az adatkésés kezeléséről [a költségkezelési adatok értelmezését](understand-cost-mgt-data.md) ismertető szakaszban találhatók.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>429-es hibakód – A hívások száma meghaladta a korlátokat

A Cost Management-előfizetők felhasználói élményének biztosítása érdekében a Cost Management API-k korlátozva vannak. A korlát elérésekor a `429: Too many requests` HTTP-állapotkódot kapja. Az API-jaink jelenlegi átvitelisebesség-korlátai:

- 30 hívás percenként – Hatókör, felhasználó vagy alkalmazás alapján.
- 200 hívás percenként – Bérlő, felhasználó vagy alkalmazás alapján.

## <a name="next-steps"></a>További lépések

- [Az Azure költségeinek elemzése a Power BI-sablonalkalmazással](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Exportált adatok létrehozása és kezelése](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) az Exports használatával.
- További információ a [Usage Details API-ról](https://docs.microsoft.com/rest/api/consumption/usageDetails).