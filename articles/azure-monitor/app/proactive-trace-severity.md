---
title: Romlás a nyomkövetési súlyossági arányban – Azure Application Insights
description: Az Azure Application Insights segítségével figyelheti az alkalmazások nyomkövetéseit, hogy az intelligens észleléssel szokatlan mintákat keressenek a nyomkövetési telemetriai adatokban.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671681"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Lebomlás a nyomkövetési súlyossági arányban (előnézet)

A nyomokat széles körben használják az alkalmazásokban, mivel segítenek elmondani a történetet arról, hogy mi történik a színfalak mögött. Amikor a dolgok rosszra fordulnak, a nyomok döntő betekintést nyújtanak a nem kívánt állapothoz vezető események sorozatába. Míg a nyomok általában strukturálatlan, van egy dolog, hogy konkrétan lehet tanulni tőlük - a súlyossági szint. Egy alkalmazás állandósult állapotában a "jó" nyomkövetések *(Információ* és *részletes)* és a "rossz" nyomkövetések (*Figyelmeztetés*, *Hiba*és *Kritikus*) aránya stabil marad. A feltételezés az, hogy a "rossz" nyomok előfordulhatnak rendszeresen, bizonyos mértékig miatt számos okból (átmeneti hálózati problémák például). De amikor egy igazi probléma növekedni kezd, általában a "rossz" nyomok relatív arányának növekedéseként jelentkezik. Az Application Insights intelligens észlelése automatikusan elemzi az alkalmazás által naplózott nyomkövetéseket, és figyelmezteti a nyomkövetési telemetria súlyosságának szokatlan mintáira.

Ez a szolgáltatás nem igényel speciális telepítést, kivéve az alkalmazás nyomkövetési naplózásának konfigurálását (lásd: a nyomkövetési naplófigyelő konfigurálása [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) vagy [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)számára). Akkor aktív, ha az alkalmazás elegendő kivételtelemetriai adatokat hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Mikor kapom meg az ilyen típusú intelligens észlelési értesítést?
Előfordulhat, hogy ilyen típusú értesítést kap, ha a "jó" nyomkövetések *(az információ* vagy *részletes*szinttel naplózott nyomkövetések) és a "hibás" nyomkövetések *(a Figyelmeztetés,* *Hiba*vagy *Végzetes*szinttel naplózott nyomkövetések) aránya egy adott napon romlik az előző hét nap ban számított alaptervhez képest.

## <a name="does-my-app-definitely-have-a-problem"></a>Biztos, hogy az alkalmazásomnak van problémája?
Nem, az értesítés nem jelenti azt, hogy az alkalmazásnak feltétlenül problémája van. Bár a "jó" és a "rossz" nyomkövetés arányának romlása alkalmazásproblémát jelezhet, ez az arányváltozás jóindulatú lehet. A növekedés oka lehet például egy új folyamat az alkalmazásban, amely több "hibás" nyomkövetést bocsát ki, mint a meglévő folyamatok).

## <a name="how-do-i-fix-it"></a>Hogyan lehet kijavítani a hibát?
Az értesítések diagnosztikai információkat tartalmaznak a diagnosztikai folyamat támogatásához:
1. **Osztályozás.** Az értesítés megmutatja, hogy hány művelet érintett. Ez segíthet abban, hogy prioritást rendeljen a problémához.
2. **Hatókör.** A probléma minden forgalmat érint, vagy csak valamilyen műveletet? Ez az információ az értesítésből szerezhető be.
3. **Diagnosztizálni.** A kapcsolódó elemek és jelentések mellékelése i. alátámasztó információk segítségével tovább diagnosztizálhatja a problémát.


