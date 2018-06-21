---
title: Felhasználói adatok kérése Azure idő adatsorozat Insights szolgáltatásai
description: Felhasználói adatok kérelem szolgáltatások összefoglalása.
author: ashannon7
ms.author: dobett
manager: timlt
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.openlocfilehash: 5714782d4fe44ce4521dd604055e925937a7328d
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295278"
---
# <a name="summary-of-customer-data-request-features"></a>Felhasználói adatok kérelem szolgáltatásainak összefoglalása

Az Azure idő adatsorozat Insights egy felügyelt felhőszolgáltatás, amellyel könnyen betöltési, tárolását, vizsgálatát, és elemzését események milliárd egyidejűleg tárolási, elemzés és a képi megjelenítés összetevőkkel.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Megtekintéséhez, exportálni, és törölheti a személyes adatokat a tulajdonos kérelem vonatkozhatnak, Azure idő adatsorozat Insights Bérlői rendszergazda az Azure-portálon vagy a REST API-k is használhatja. A szolgáltatási adatok tulajdonos kérelmekre az Azure portál használatával, hajtsa végre ezeket a műveleteket, amelyek a legtöbb felhasználónak inkább kevésbé összetett módszert biztosít.

## <a name="identifying-customer-data"></a>Felhasználói adatok azonosítása

Azure idő adatsorozat Insights figyelembe veszi a személyes adatok a rendszergazdák és felhasználók az idő adatsorozat Insights adatait. Idő adatsorozat Insights tárolja az Azure Active Directory objektum-Azonosítóját, a környezet hozzáféréssel rendelkező felhasználók. Az Azure-portálon jeleníti meg a felhasználó e-mail címét, de a következő e-mail címek nem tárolja el idő adatsorozat Insights, azok dinamikusan kereshető használata az Azure Active Directory-objektum-Azonosítóját az Azure Active Directoryban.

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése

A bérlői rendszergazda törölheti a felhasználói adatokat az Azure portál használatával.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Azonban a portálon keresztül ügyféladatok törlése előtt távolítsa el a felhasználó hozzáférési házirendek a a idő adatsorozat Insights környezet az Azure portálon. További információkért lásd: [adatokat az Azure-portál használatával idő adatsorozat Insights környezetben való hozzáférést](time-series-insights-data-access.md).

Delete művelet a hozzáférési házirendek a REST API használatával is elvégezheti. További információkért lásd: [hozzáférési házirendek - törlése](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Idő adatsorozat Insights integrálva van a szabályzat paneljén az Azure portálon. Idő adatsorozat Insights, mind a szabályzat paneljén engedélyezi, hogy a nézetben exportálni, és a szolgáltatáson belül tárolt felhasználói adatok törlése. Bármely törlése az Azure portál eredmények az az idő adatsorozat Insights belül felhasználói adatok törlését a szabályzat paneljén belül végrehajtott műveletet. Például ha egy felhasználó személyes mentett lekérdezés tartozik, adott lekérdezés véglegesen törlődik a idő adatsorozat Insights explorer. Ha a felhasználó egy korábban mentett megosztott lekérdezés, a lekérdezés továbbra is fennáll, de a felhasználói adatok véglegesen törlődik. A következő megjegyzés útmutatást elvégezni ezeket a feladatokat tartalmazza.

## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása

Hasonlóképpen adatok törlésével, egy Bérlői rendszergazda megtekintheti, és exportálja a szabályzat paneljén az Azure portálon idő adatsorozat Insights tárolt adatokat.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Ha a bérlői rendszergazda, adat-hozzáférési házirendjeit a idő adatsorozat Insights környezeten belül megtekintheti az Azure portálon. További információkért lásd: [adatokat az Azure-portál használatával idő adatsorozat Insights környezetben való hozzáférést](time-series-insights-data-access.md).

Akkor is a hozzáférési házirendekkel a "list környezet által" művelet a megadott REST API exportálási műveletek végrehajtásához. További információkért lásd: [hozzáférési házirendek - lista által környezet](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Idő adatsorozat Insights tárolt adatok törlése

Személyes adatok megérkezik tehetik a idő adatsorozat Insights tárolóba, felhasználó és rendszergazda adatokból másikat. Ha a idő adatsorozat Insights személyes adatokat tárolt adatokat, exportálhatja és az alábbi lépéseket követve adatok törlése:

**Megtekintheti és adatok exportálása**

Megtekintéséhez, és az idő adatsorozat Insights tárolt adatok exportálása, szeretné, hogy az adatok kereséséhez. A idő adatsorozat Insights explorer vagy idő adatsorozat Insights lekérdezés API-k segítségével megtekintheti és exportálhatja az adatokat. Megtekintheti, és az idő adatsorozat Insights Explorerrel adatok exportálása, először keresse a kérdéses a felhasználói adatok kereséséhez. Keresés, kattintson a jobb gombbal a diagramra, majd a **események tallózása**. Az események rács jelenik meg, és kapcsolódhatnak az adatok exportálása CSV és a JSON-NÁ.

További információkért lásd: [Azure idő adatsorozat Insights explorer](time-series-insights-explorer.md).

**Adatok törlése**

Idő adatsorozat Insights jelenleg nem támogatja az adatok részletes törlését. Idő adatsorozat Insights azonban lehetővé teszi a úgy konfigurálja az adatmegőrzési idő adatsorozat Insights belül tárolt ügyféladatok eltávolítása. Módosíthatja a megőrzési időtartam, a teljes idő adatsorozat Insights-környezet tetszőleges számú napon törlés igényei támogatásához.

További információkért lásd: [konfigurálása a megőrzési idő adatsorozat insightsban](time-series-insights-how-to-configure-retention.md).