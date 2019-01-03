---
title: Vásárlói adatok kérése funkciók az Azure Time Series Insightsban |} A Microsoft Docs
description: Az Azure Time Series Insights vásárlói adatok funkciók összefoglalása.
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 30f6b1fd953f89170a18d56bf0353c643853074e
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558549"
---
# <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása

Az Azure Time Series Insights rendkívül egy felügyelt felhőszolgáltatás tárolási, elemzési és megjelenítési összetevőkkel, amelyek megkönnyítik a betöltési, tárolása, Fedezze fel és elemezhető akár több milliárd esemény egyszerre.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Megtekintheti, exportálása és törölje a személyes adatokat, amely vonatkozhat egy tulajdonos kérelem, az Azure Time Series Insights-Bérlői rendszergazda segítségével vagy az Azure Portalon, vagy a REST API-k. A szolgáltatás adattulajdonosi kérelmek az Azure portal használatával, hajtsa végre ezeket a műveleteket, amelyek a legtöbb felhasználó inkább kevésbé összetett módszert biztosít.

## <a name="identifying-customer-data"></a>Vásárlói adatok azonosítása

Az Azure Time Series Insights úgy véli, hogy a rendszergazdák és felhasználók részére a Time Series Insights társított személyes adatok. A Time Series Insights tárolja a környezethez hozzáféréssel rendelkező felhasználók Azure Active Directory-Objektumazonosító. Az Azure portal megjeleníti a felhasználó e-mail-címeket, de ezekre az e-mail címekre nem tárolja el a Time Series Insights, azok dinamikusan kulcskeresési az Azure Active Directory-object-ID használatával az Azure Active Directoryban.

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

Bérlői rendszergazda törölheti a vásárlói adatokat az Azure portal használatával.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Azonban mielőtt törli a portálon keresztül a vásárlói adatokat, el kell távolítania a felhasználó hozzáférési szabályzatok a Time Series Insights-környezetből az Azure Portalon. További információkért lásd: [adathozzáférés biztosítása egy Time Series Insights-környezet az Azure portal használatával](time-series-insights-data-access.md).

Törlési műveletek a hozzáférési szabályzatok a REST API használatával is elvégezheti. További információkért lásd: [hozzáférési házirendek – Törlés](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

A Time Series Insights integrálva van a szabályzat paneljén, az Azure Portalon. A Time Series Insights és a szabályzat paneljén is lehetővé teszi megtekintése, exportálása és a szolgáltatásban tárolt felhasználói adatok törlése. Bármely törölje a szabályzat panelen a Time Series Insights felhasználói adatok törlése az Azure portál eredmények végrehajtott műveletet. Például ha egy felhasználó egy korábban mentett személyes lekérdezés, ez a lekérdezés véglegesen törlődik a Time Series Insights explorer. Ha a felhasználó egy korábban mentett megosztott lekérdezés, a lekérdezés továbbra is fennáll, de az véglegesen törlődni fog a felhasználói adatokat. Az alábbi megjegyzésben való elvégezni ezeket a feladatokat tartalmazza.

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

Ugyanígy az adatok törlése egy Bérlői rendszergazda megtekintheti és az Azure Portalon a házirend panelén a Time Series Insights-ban tárolt adatok exportálása.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Ha a bérlő rendszergazdája, megtekintheti az adathozzáférési házirendek a Time Series Insights-környezetből az Azure Portalon. További információkért lásd: [adathozzáférés biztosítása egy Time Series Insights-környezet az Azure portal használatával](time-series-insights-data-access.md).

Akkor is exportálási műveletek végrehajtása a hozzáférési szabályzatokkal a "környezet által list" művelet a megadott REST API-ban. További információkért lásd: [hozzáférési szabályzatok - lista alapján környezet](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>A Time Series Insights tárolt adatok törlése

Személyes adatok korongot tehetik a Time Series Insights-tároló, a felhasználói és rendszergazdai más-más forgatókönyvet. Ha a Time Series Insights, a személyes adatok tárolt adatokat, exportálhatja, és törli az adatokat az alábbi lépéseket követve:

**Adatok megtekintése és exportálása**

Megtekintheti, és tárolja a Time Series Insights adatainak exportálása, szüksége, hogy az adatok kereséséhez. A Time Series Insights explorer vagy a Time Series Insights lekérdezési API-k segítségével megtekintheti és exportálhat adatokat. Megtekintheti, és a Time Series Insights explorer használata az adatok exportálása, először keresse a kérdéses a felhasználói adatok kereséséhez. A keresés után kattintson a jobb gombbal a diagramra, majd válassza a **események tallózása**. Az események rács jelenik meg, és megadja az adatok exportálása CSV és a JSON beállításai.

További információkért lásd: [Azure Time Series Insights explorer](time-series-insights-explorer.md).

**Adatok törlése**

A Time Series Insights jelenleg nem támogatja az adatok részletes törlését. Azonban a Time Series Insights távolítsa el a vásárlói adatokat tárolja a Time Series Insights adatmegőrzési szabályzatok konfigurálásával lehetővé teszi lehetővé. Módosíthatja a megőrzési idő, a teljes Time Series Insights környezetet tetszőleges számú napig törlés igényeinek támogatásához.

További információkért lásd: [megőrzés konfigurálása a Time Series Insights](time-series-insights-how-to-configure-retention.md).