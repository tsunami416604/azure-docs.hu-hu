---
title: "Intelligens észlelési - biztonsági észlelési csomagot az Azure Application insights szolgáltatással |} Microsoft Docs"
description: "A potenciális biztonsági problémákat az Azure Application insights szolgáltatással alkalmazás figyelésére."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3e4604a154c16b785db1ab903587ae4a35d93c05
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="application-security-detection-pack-preview"></a>Alkalmazás biztonsági észlelési pack (előzetes verzió)

Az Application Insights automatikusan elemzi a az alkalmazás által generált és észleli a potenciális biztonsági problémákat. Ez a funkció lehetővé teszi potenciális biztonsági problémákat azonosítása, és kezelni őket, azzal, hogy az alkalmazás vagy a szükséges biztonsági intézkedéseket.

Ehhez a szolgáltatáshoz nem speciális beállítási eltérő [konfigurálása az alkalmazás számára telemetriát](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha visszajelzést kap az ilyen típusú intelligens észlelési értesítést?
Háromféle típusú biztonsági problémákat észlelt hiba:
1. Nem biztonságos URL hozzáférés: az alkalmazás URL-címet a HTTP és HTTPS használatával is hozzáférnek. Általában egy URL-címet, amely fogadja a HTTPS-kéréseket kell fogadja el a HTTP-kérelmekre. Ez az alkalmazás egy hiba vagy biztonsági problémát jelezhet.
2. Nem biztonságos formában: űrlap (vagy más "POST" kérelem) az alkalmazásban a HTTPS kapcsolat helyett HTTP Protokollt használ. HTTP-n keresztül kedvezőtlenül befolyásolhatja a felhasználói adatokat az űrlap által küldött.
3. Gyanús felhasználói tevékenység: az alkalmazás is hozzáférnek több országban az azonos felhasználói körülbelül egy időben. Például ugyanazon felhasználó érhetők el az alkalmazást az Spanyolország és az Amerikai Egyesült Államokban az azonos órához. Az észlelés azt jelzi, hogy az alkalmazás a potenciálisan rosszindulatú hozzáférési kísérlet.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Saját alkalmazás mindenképpen rendelkezik egy biztonsági probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás egyértelműen a biztonsági hibát tartalmaz. A fenti esetekben kimutatása sok esetben azt jelzi, hogy a egy biztonsági probléma. Az észlelési azonban előfordulhat, hogy rendelkezik a természetes üzleti indoklásának, és figyelmen kívül lesz hagyva.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hogyan oldja meg a "Nem biztonságos URL hozzáférés" észlelési?
1. **Osztályozás.** Nem biztonságos URL-címeket, és az URL-címet, de a legtöbb hozzáférő felhasználók számát jeleníti meg az értesítés nem biztonságos hozzáférés érinti. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** Hány százaléka a felhasználók nem biztonságos URL hozzáférés? Hány URL-címeket is hatással volt? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési biztosít a lista nem biztonságos, és URL-címek és a felhasználók, amelyek segítséget nyújtanak a probléma további diagnosztizálása érintettek listáját.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hogyan oldja meg a "Nem biztonságos formában" észlelési?
1. **Osztályozás.** Az értesítés nem biztonságos űrlapok számától és a felhasználók, amelyek esetén az esetlegesen biztonsági szempontból sérült számát biztosítja. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** Nem biztonságos átvitelt legnagyobb számát megadó részt, és milyen nem biztonságos átvitelt időbeli megoszlása? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési itt nem biztonságos űrlapok listáján és a nem biztonságos átvitelt minden egyes űrlap segítséget nyújtanak a probléma további diagnosztizálása száma.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hogyan oldja meg a "gyanús felhasználói tevékenység" észlelési?
1. **Osztályozás.** Az értesítés, amely a gyanús viselkedését tett különböző felhasználók számát jeleníti meg. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** Mely országokból volt a gyanús kérések érkeznek? Felhasználói volt a legtöbb gyanús? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési gyanús felhasználót tartalmazó listát, és minden felhasználó esetében segítséget nyújtanak a probléma további diagnosztizálása országok listáját itt.
