---
title: "Észlelési - nyomkövetési súlyossági aránya, Azure Application Insights akár teljesítménycsökkenés intelligens |} Microsoft Docs"
description: "Alkalmazások nyomkövetéseit rendelkező Azure Application Insights – nyomkövetési telemetria szokatlan minták a figyelheti."
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
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 29ed195dadb7230df425d6d981a0a482e09ee79f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Akár teljesítménycsökkenés nyomkövetési súlyossági arány (előzetes verzió)

Nyomok széles körben használt alkalmazásokban, azok segít mondja el, mi történik a háttérben története. Problémák esetén, a nyomkövetések kritikus fontosságú láthatósága idézte események sorozatát adja meg. Míg nyomkövetési adatokat általában strukturálatlan, nincs egyik dolog, ami konkrétan is adatnézetből származó őket – a súlyossági szintet. Egy alkalmazás stabil állapotban "jó" nyomkövetési aránya volna várhatóan (*Info* és *részletes*) és a "hibás" nyomkövetési (*figyelmeztetés*, *hiba* és *kritikus*) az állandó marad. A feltételezi, hogy "hibás" nyomkövetési fordulhat elő, tetszőleges számú okok miatt bizonyos mértékben rendszeres időközönként (például átmeneti hálózati problémák esetén). De valós probléma pedig kezdődő, növekvő, akkor általában akkor jelentkezik, "hibás" nyomkövetési vs "jó" nyomkövetési viszonyított aránya növekedése. Application Insights intelligens észlelési automatikusan elemzi az alkalmazás által naplózott nyomkövetés és is figyelmeztessen a szokatlan minták a nyomkövetési telemetria súlyossága.

Ehhez a szolgáltatáshoz nem speciális beállítása eltérő nyomkövetési adatainak naplózása az alkalmazás konfigurálása (lásd: a nyomkövetési napló figyelő konfigurálása [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) vagy [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Azt akkor aktív, ha az alkalmazás elég kivételtelemetria állít elő.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha visszajelzést kap az ilyen típusú intelligens észlelési értesítést?
Az ilyen típusú értesítést kaphat, ha "jó" nyomkövetési aránya (szintű naplózott nyomkövetési *Info* vagy *részletes*) és a "hibás" nyomkövetési (szintű naplózott nyomkövetési *Figyelmeztetés*, *, Hiba vagy *végzetes*) egy adott napon, az előző hét napban számított alapterv képest van terhelése.

## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképpen rendelkezik probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás véglegesen rendelkezik-e probléma. Egy "jó" és "hibás" nyomkövetési hányadosa akár teljesítménycsökkenés alkalmazásproblémák jelezhetik, bár lehet, hogy ez a változtatás arány jóindulatú. Például a növelését lehet egy új módszer az alkalmazás további "hibás" nyomkövetési adatokat, mint a meglévő forgalom kibocsátó miatt).

## <a name="how-do-i-fix-it"></a>Hogyan tegye megjavítani?
Az értesítések tárgya lehet a diagnosztikai adatokat küld a diagnosztika folyamat támogatja:
1. **Osztályozás.** Az értesítés elsajátíthatja, hogy hány művelet vannak hatással. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** A hiba érinti az összes forgalom, vagy csak egy művelet? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** A kapcsolódó elemek is használhat, és jelentések csatolása támogató adatokat, további segítséget a probléma diagnosztizálásához.


