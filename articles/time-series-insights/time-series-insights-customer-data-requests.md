---
title: A vásárlói adatkérés funkciói a Azure Time Series Insightsban | Microsoft Docs
description: Azure Time Series Insights ügyfél-adatkérés funkcióinak összefoglalása.
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.date: 05/08/2019
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: a67e716300254b7ae73cf5c608e4ab9177638456
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677905"
---
# <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása

A Azure Time Series Insights egy felügyelt felhőalapú szolgáltatás tárolási, elemzési és vizualizációs összetevőkkel, amelyek megkönnyítik a több milliárd esemény egyidejű betöltését, tárolását, feltárását és elemzését.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Az adattulajdonosi kérelem alá tartozó személyes adatok megtekintéséhez, exportálásához és törléséhez egy Azure Time Series Insights bérlői rendszergazda használhatja a Azure Portal vagy a REST API-kat. A Azure Portal használata az adattulajdonosi kérelmek kiszolgálásához kevésbé összetett módszert biztosít a legtöbb felhasználó által előnyben részesített műveletek végrehajtásához.

## <a name="identifying-customer-data"></a>Ügyféladatok azonosítása

Azure Time Series Insights úgy gondolja, hogy a személyes adat a rendszergazdákkal és a Time Series Insights felhasználókkal kapcsolatos adat. A Time Series Insights a környezethez hozzáféréssel rendelkező felhasználók Azure Active Directory objektum-AZONOSÍTÓját tárolja. A Azure Portal megjeleníti a felhasználói e-mail-címeket, de ezek az e-mail-címek nem tárolódnak a Time Series Insightsban, a rendszer dinamikusan felkeresi a Azure Active Directory Object-ID-t a Azure Active Directory.

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése

A bérlői rendszergazdák a Azure Portal használatával törölhetik az ügyféladatokat.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

A portálon keresztüli ügyféladatok törlése előtt azonban el kell távolítania a felhasználó hozzáférési házirendjeit a Time Series Insights-környezetből a Azure Portalon belül. További információkért lásd: [adathozzáférés engedélyezése Time Series Insights környezethez Azure Portal használatával](time-series-insights-data-access.md).

A REST API használatával is elvégezheti a hozzáférési szabályzatok törlési műveleteit. További információ: [hozzáférési házirendek – törlés](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete).

Time Series Insights integrálva van a Azure Portal szabályzat paneljével. A Time Series Insights és a szabályzat panel is lehetővé teszi a szolgáltatáson belül tárolt felhasználói adatnézetek megtekintését, exportálását és törlését. A Azure Portal házirend paneljén végrehajtott törlési műveletek a felhasználói adatTime Series Insightson belüli törlését eredményezik. Ha például egy felhasználó mentett személyes lekérdezést tartalmaz, a lekérdezés véglegesen törlődik a Time Series Insights Explorerben. Ha a felhasználó rendelkezik mentett megosztott lekérdezéssel, a lekérdezés továbbra is fennáll, de a rendszer véglegesen törli a felhasználói adatokat. A következő Megjegyzés a feladatok végrehajtásával kapcsolatos utasításokat tartalmazza.

## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása

Az adatok törléséhez hasonlóan a bérlői rendszergazdák megtekinthetik és exportálhatjuk a Time Series Insights tárolt adatait a Azure Portal szabályzat paneljén.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Bérlői rendszergazdaként megtekintheti az adatelérési házirendeket a Azure Portal Time Series Insights környezetében. További információkért lásd: [adathozzáférés engedélyezése Time Series Insights környezethez Azure Portal használatával](time-series-insights-data-access.md).

A hozzáférési házirendekben az exportálási műveleteket is elvégezheti a megadott REST APIban a "környezetek listázása" művelet használatával. További információ: [hozzáférési házirendek – lista a környezet alapján](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Time Series Insights belül tárolt adattörlés

Előfordulhat, hogy a személyes adatok az Time Series Insights Storage-ban, a felhasználói és a rendszergazdai adatoktól eltérő forgatókönyvet alkotnak. Ha úgy gondolja, hogy a Time Series Insights tárolt adatai személyes adatként vannak tárolva, az alábbi lépések végrehajtásával exportálhatja és törölheti ezeket az adatfájlokat:

**Az adatmegjelenítés és-exportálás**

Time Series Insights belül tárolt adatmegjelenítéshez és-exportáláshoz meg kell keresnie az adott adathalmazt. A Time Series Insights Explorer vagy a Time Series Insights lekérdezési API-k segítségével megtekintheti és exportálhatja az adatfájlokat. Ha a Time Series Insights Explorerrel szeretné megtekinteni és exportálni az adatexportálást, először keresse meg a szóban forgó felhasználói adatelemet. A keresés után kattintson a jobb gombbal a diagramra, és válassza az **események feltárása**lehetőséget. Megjelenik az Events (események) rács, amely a CSV-ként és JSON-ként való exportálási beállításokat jeleníti meg.

További információ: [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

**Adattörlés**

A Time Series Insights jelenleg nem támogatja az adattárolás részletes törlését. A Time Series Insights azonban lehetővé teszi a Time Series Insights belül tárolt ügyféladatok eltávolítását az adatmegőrzési házirendek konfigurálásával. A törlési követelmények támogatásához módosíthatja a teljes Time Series Insights környezet megőrzési időtartamát tetszőleges számú napig.

További információ: [a megőrzés konfigurálása Time Series Insightsban](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>További lépések

* További információ [az ÁME-környezet](./time-series-insights-data-access.md)adatelérésének biztosításáról.

* Tekintse meg a [Azure Time Series Insights Explorert](time-series-insights-explorer.md).

* Tudnivalók az [adatmegőrzés konfigurálásáról Time Series Insightsban](time-series-insights-how-to-configure-retention.md).