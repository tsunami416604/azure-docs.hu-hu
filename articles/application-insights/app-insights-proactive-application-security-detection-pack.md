---
title: Intelligens detektálás – biztonsági kimutatási csomagot az Azure Application Insights |} A Microsoft Docs
description: Monitorozhatók az alkalmazások az Azure Application Insights a potenciális biztonsági problémákat.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 16dd381301bdc650022ba5580f96a1733aeb32b0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722717"
---
# <a name="application-security-detection-pack-preview"></a>Alkalmazás biztonsági észlelési csomag (előzetes verzió)

Az Application Insights automatikusan elemzi a telemetria az alkalmazás által generált, és észleli a potenciális biztonsági problémákat. Ezzel a képességgel azonosíthatja a potenciális biztonsági problémákat, és kezelni őket, azzal, hogy az alkalmazás és a szükséges biztonsági intézkedéseket.

Ez a funkció nem speciális beállítás nem szükséges [telemetriát küldjön az alkalmazás konfigurálásához](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha szeretné be intelligens detektálás értesítést az ilyen típusú?
Észlelt biztonsági problémák három típusa van:
1. Nem biztonságos URL-hozzáférés: az alkalmazás URL-hozzáférnek a HTTP és HTTPS-n keresztül. Általában egy URL-címet, amely HTTPS-kéréseket fogad el kell nem fogadja el a HTTP-kérések. Ez egy programhiba vagy biztonsági probléma az alkalmazásban utalhat.
2. Nem biztonságos űrlap: egy űrlapot (vagy más "POST" kérelem), az alkalmazás használja a HTTP helyett HTTPS. HTTP-n keresztül kedvezőtlenül befolyásolhatja a felhasználói adatokat az űrlap által küldött.
3. Gyanús felhasználói tevékenység: az alkalmazás hozzáférnek több országok szerint ugyanaz a felhasználó körülbelül egy időben. Például ugyanaz a felhasználó elérhető az alkalmazás Spanyolország és az Egyesült Államokban adott órán belül. Az észlelés azt jelzi, hogy az alkalmazás egy potenciálisan kártékony hozzáférési kísérlet.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Saját alkalmazás mindenképp rendelkezik egy biztonsági probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás mindenképp rendelkezik egy biztonsági probléma. Sok esetben egy észlelését, az a fenti esetekben biztonsági jelezheti. Az észlelés azonban előfordulhat, hogy természetes üzleti indoklásának, és figyelmen kívül hagyható.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hogyan oldja meg a "Nem biztonságos URL-cím hozzáférés" észlelését?
1. **Osztályozási.** Az értesítés biztosít a felhasználók száma, akik nem biztonságos URL-címeket, és az URL-cím, amely a legtöbb volt elérhető, biztonságos hozzáférés által érintett. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** A felhasználók milyen százalékos érhető el a nem biztonságos URL-címek? Hány URL-címek érintettek? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelés nem biztonságos kérések listáját és az URL-címek és, amelyek segítséget nyújtanak a probléma további diagnosztizálása érintett felhasználók listáját biztosít.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hogyan oldja meg a "Biztonságos űrlap" észlelését?
1. **Osztályozási.** Az értesítés nem biztonságos űrlapok számától és a felhasználók, amelynek adatait potenciálisan sérült biztonságú biztosít. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** Melyik képernyő legnagyobb mennyisége nem biztonságos átvitelt részt, és mi nem biztonságos átvitelek időbeli megoszlása? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelés nem biztonságos űrlapok listáján, és a egy bontása minden egyes űrlap segítséget a probléma további diagnosztizálása nem biztonságos átvitelek száma biztosít.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hogyan oldja meg a "gyanús felhasználói tevékenység" észlelését?
1. **Osztályozási.** Az értesítés biztosít, amely a gyanús viselkedés sokfélék különböző felhasználók száma. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** Mely országokban az volt a gyanús ügyfélkérések? Melyik felhasználó volt a legnagyobb gyanús? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelés a gyanús felhasználók listáját, és minden felhasználó segítséget a probléma további diagnosztizálása országok listáját itt.
