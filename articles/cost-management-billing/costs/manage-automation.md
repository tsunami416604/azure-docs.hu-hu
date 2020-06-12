---
title: Azure-költségek kezelése automatizálással
description: Ez a cikk az Azure-költségek automatizálással való kezelését ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449831"
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

Az Exports API megoldást kínál a költségadatok rendszeres kiírásának ütemezésére. Ez az ajánlott módszer a nem összesített költségadatok lekérésére azoknál a szervezeteknél, ahol a használati adatokat tartalmazó fájlok túl nagyok ahhoz, hogy megbízhatóan le lehessen hívni és tölteni az adatokat a Usage Details API-val. Az adatok a kiválasztott Azure Storage-fiókba kerülnek. Ezt követően betöltheti az adatokat a saját rendszereibe, és a csapatai szükség szerint elemezhetik őket. Az Exports az Azure Portalon való konfigurálásához lásd az [adatok exportálását](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) ismertető szakaszt.

## <a name="data-latency-and-rate-limits"></a>Adatkésés és sebességkorlátok

Azt javasoljuk, hogy naponta csak egy alkalommal hívja meg az API-kat. A Cost Management adatai négyóránként frissülnek. A rendszer ekkor kapja meg az új használati adatokat az Azure-beli erőforrás-szolgáltatóktól. Az ennél gyakoribb hívás nem szolgál további adatokkal. Csak a terhelést növeli meg. További információk az adatok frissítési gyakoriságáról és az adatkésés kezeléséről [a költségkezelési adatok értelmezését](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data) ismertető szakaszban találhatók.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>429-es hibakód – A hívások száma meghaladta a korlátokat

A Cost Management-előfizetők felhasználói élményének biztosítása érdekében a Cost Management API-k korlátozva vannak. A korlát elérésekor a `429: Too many requests` HTTP-állapotkódot kapja. Az API-jaink jelenlegi átvitelisebesség-korlátai:

- 30 hívás percenként – Hatókör, felhasználó vagy alkalmazás alapján.
- 200 hívás percenként – Bérlő, felhasználó vagy alkalmazás alapján.

## <a name="next-steps"></a>További lépések

- [Az Azure költségeinek elemzése a Power BI-sablonalkalmazással](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Exportált adatok létrehozása és kezelése](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) az Exports használatával.
- További információ a [Usage Details API-ról](https://docs.microsoft.com/rest/api/consumption/usageDetails).