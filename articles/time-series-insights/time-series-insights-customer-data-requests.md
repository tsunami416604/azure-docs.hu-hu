---
title: Ügyféladatok-kérési funkciók – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az ügyféladatok-kérelmek funkcióit az Azure Time Series Insightsban.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 04/17/2020
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: 3578710bf066e7745215d8efacafd2cf6c005eac
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640494"
---
# <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-igénylési funkciók összefoglalása

Az Azure Time Series Insights egy felügyelt felhőszolgáltatás tárolási, elemzési és vizualizációs összetevőkkel, amelyek megkönnyítik az események milliárdjainak egyidejű betöltését, tárolását, feltárását és elemzését.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Az Azure Time Series Insights bérlői rendszergazdája az Azure Portalon vagy a REST API-kat használhatja az Azure Time Series Insights-rendszergazda az adatok megtekintéséhez, exportálásához és törléséhez. Az Azure Portal használatával az adattulajdonosi kérelmek kiszolgálása, egy kevésbé összetett módszert biztosít ezeket a műveleteket, hogy a legtöbb felhasználó inkább.

## <a name="identifying-customer-data"></a>Az ügyféladatok azonosítása

Az Azure Time Series Insights a személyes adatokat a Time Series Insights rendszergazdáihoz és felhasználóihoz társított adatoknak tekinti. A Time Series Insights tárolja a környezethez hozzáféréssel rendelkező felhasználók Azure Active Directory-objektumazonosítóját. Az Azure Portalon a felhasználói e-mail-címek, de ezek az e-mail-címek nem tárolja a Time Series Insights, azokat dinamikusan az Azure Active Directory objektum-azonosító az Azure Active Directoryban.

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A bérlői rendszergazda törölheti az ügyféladatokat az Azure Portalon keresztül.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Azonban mielőtt törölné az ügyféladatokat a portálon keresztül, távolítsa el a felhasználó hozzáférési szabályzatait a Time Series Insights környezetből az Azure Portalon belül. További információért olvassa el [az Adatok elérésének megadása egy Time Series Insights-környezethez az Azure Portal használatával](time-series-insights-data-access.md)című részt.

A REST API használatával törlési műveleteket is végrehajthat a hozzáférési házirendeken. További információért olvassa el [a Hozzáférési házirendek – Törlés című fájlcímű](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/delete)olvasni.

A Time Series Insights integrálva van az Azure Portalon a Szabályzat panellel. Mind a Time Series Insights, mind a Szabályzat panel lehetővé teszi a szolgáltatáson belül tárolt felhasználói adatok megtekintését, exportálását és törlését. Az Azure Portal Szabályzat paneljén belül végrehajtott törlési műveletek a felhasználói adatok idősorozat-elemzési adatokon belüli törlését eredményezik. Ha például egy felhasználó nak van egy mentett személyes lekérdezése, a lekérdezés véglegesen törlődik a Time Series Insights explorerből. Ha a felhasználó rendelkezik mentett megosztott lekérdezéssel, a lekérdezés megmarad, de a felhasználói adatok véglegesen törlődnek. A következő megjegyzés útmutatást tartalmaz a feladatok elvégzéséhez.

## <a name="exporting-customer-data"></a>Vevői adatok exportálása

Az adatok törléséhez hasonlóan a bérlői rendszergazda megtekintheti és exportálhatja a Time Series Insights ban tárolt adatokat az Azure Portal szabályzatpaneljéről.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Ha Ön bérlői rendszergazda, megtekintheti az adatelérési szabályzatokat a Time Series Insights környezetben az Azure Portalon. További információért olvassa el [az Adatok elérésének megadása egy Time Series Insights-környezethez az Azure Portal használatával](time-series-insights-data-access.md)című részt.

A megadott REST API "környezet szerinti lista" művelettel is végrehajthat exportálási műveleteket a hozzáférési házirendeken. További információért olvassa el [a Hozzáférési házirendek – Környezet szerint lista](https://docs.microsoft.com/rest/api/time-series-insights/management/accesspolicies/listbyenvironment)című részt.

## <a name="to-delete-data-stored-within-time-series-insights"></a>A Time Series Insights-ban tárolt adatok törlése

A személyes adatok előfordulhat, hogy az utat a Time Series Insights storage, egy másik forgatókönyv a felhasználói és rendszergazdai adatokat. Ha a Time Series Insightsban tárolt adatokat személyes adatnak tekinti, az alábbi lépésekkel exportálhatja és törölheti ezeket az adatokat:

**Adatok megtekintése és exportálása**

A Time Series Insights alkalmazásban tárolt adatok megtekintéséhez és exportálásához meg kell keresnie ezeket az adatokat. A Time Series Insights-kezelő vagy a Time Series Insights lekérdezési API-k segítségével megtekintheti és exportálhatja az adatokat. Az adatok megtekintéséhez és exportálásához a Time Series Insights intézővel először keresse meg a kérdéses felhasználói adatokat. Keresés után kattintson a jobb gombbal a diagramra, és válassza **az Események felfedezése parancsot.** Megjelenik az eseményrács, és az adatok CSV és JSON néven történő exportálási lehetőségeit mutatja be.

További információért olvassa el az [Azure Time Series Insights felfedezője című olvasnivalót.](time-series-insights-explorer.md)

**Adat törlése**

A Time Series Insights jelenleg nem támogatja az adatok részletes törlését. A Time Series Insights azonban lehetővé teszi a Time Series Insightsban tárolt ügyféladatok eltávolítását az adatmegőrzési szabályzatok konfigurálásával. A törlési követelmények támogatása érdekében a teljes Time Series Insights-környezet megőrzési idejét tetszőleges számú napra módosíthatja.

További információ: [Adatmegőrzés konfigurálása a Time Series Insights alkalmazásban.](time-series-insights-how-to-configure-retention.md)

## <a name="next-steps"></a>További lépések

* További információ az [Azure Time Series Insights-környezethez való adathozzáférés biztosításáról.](./time-series-insights-data-access.md)

* Tekintse meg az [Azure Time Series Insights explorert.](time-series-insights-explorer.md)

* További információ [a megőrzés konfigurálásáról a Time Series Insights ban.](time-series-insights-how-to-configure-retention.md)