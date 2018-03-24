---
title: Azure Application Insights Telemetria adatmodell - Telemetria környezetben |} Microsoft Docs
description: Application Insights telemetria környezetben adatmodell
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.openlocfilehash: 942a4bc09e632438e4a85280d723c72c4b09a015
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetria a környezetben: Application Insights adatmodell

Minden telemetriai elem lehet egy szigorú típusmegadású környezet mezőket. Minden mező lehetővé teszi, hogy egy adott figyelési forgatókönyvhöz. Az egyéni tulajdonságok gyűjteményén segítségével egyéni vagy az alkalmazás-specifikus környezetfüggő információk tárolására.


## <a name="application-version"></a>Alkalmazás verziószáma

Amely a telemetriai adatokat küld az alkalmazással kapcsolatos információkat az alkalmazás helyi mezőket mindig van. Alkalmazásverzió segítségével elemezheti az alkalmazás viselkedését, és a központi telepítésekre a korrelációs trend változásai.

Maximális hossz: 1024


## <a name="client-ip-address"></a>Ügyfél IP-címe

Az ügyféleszköz IP-címét. IPv4 és IPv6 használata támogatott. Telemetriai adatokat küldött a szolgáltatás, a hely környezetben tárgya a felhasználót, hogy a szolgáltatási műveletet kezdeményezett. Az Application Insights csomagolja ki a földrajzihely-információ az ügyfél IP-cím, és majd csonkolja. Ezért ügyfél IP-önmagában nem használható azonosításra alkalmas adatokat. 

Maximális hossz: 46


## <a name="device-type"></a>Eszköztípus

Ez a mező eredetileg jelzi a felhasználó az alkalmazás által használt eszköz típusának lett megadva. Ma használt elsősorban különböztetheti meg egymástól a JavaScript telemetriai eszköz típusú "Browser" kiszolgálóoldali a telemetriai adatok az eszköz nem írja be a "PC".

Maximális hossz: 64


## <a name="operation-id"></a>A művelet azonosítója

A legfelső szintű tevékenység egyedi azonosítója. Ez az azonosító lehetővé teszi, hogy a csoport telemetriai több összetevői között. Lásd: [telemetriai korrelációs](application-insights-correlation.md) részleteiről. A művelet azonosítója vagy egy kérelem, vagy egy nézet hozta létre. Minden egyéb telemetriai adat ebben a mezőben a tartalmazó kérelem vagy a lap nézet értéket állítja be. 

Maximális hossz: 128


## <a name="parent-operation-id"></a>Szülő művelet azonosítója

A telemetriai adatok elem azonnali szülő egyedi azonosítója. Lásd: [telemetriai korrelációs](application-insights-correlation.md) részleteiről.

Maximális hossz: 128


## <a name="operation-name"></a>Művelet neve

A (csoport) a művelet neve. A művelet neve vagy a kérelmet, vagy a lap nézet hozta létre. Minden egyéb telemetriai elem ebben a mezőben a tartalmazó kérelem vagy a lap nézet értékre állítva. Műveletek (például "GET otthoni/Index") egy csoportja számára a telemetriai adatok elemek kereséséhez használt művelet neve. A context tulajdonság segítségével fogadja a hívást kérdéseket, például "Mik a tipikus kivételek, ezen az oldalon."

Maximális hossz: 1024


## <a name="synthetic-source-of-the-operation"></a>A művelet szintetikus forrása

Szintetikus forrás nevére. Az alkalmazás egyes telemetriai szintetikus forgalom jelöl. A webhely, a webhely rendelkezésreállás figyelésére szolgáló tesztek vagy a nyomkövetések az Application Insights SDK maga például diagnosztikai szalagtárak indexelő webbejáró lehet.

Maximális hossz: 1024


## <a name="session-id"></a>munkamenet-azonosító

Munkamenet-azonosító – a felhasználó és az alkalmazás közötti interakció példányát. Információk a munkamenet-környezet mezőket a végfelhasználó mindig tárgya. Telemetriai adatokat küldött a szolgáltatás, a munkamenet környezetében tárgya a felhasználót, hogy a szolgáltatási műveletet kezdeményezett.

Maximális hossz: 64


## <a name="anonymous-user-id"></a>A névtelen felhasználói azonosító

A névtelen felhasználói azonosító. Az alkalmazás a végfelhasználó jelenti. Telemetriai adatokat küldött a szolgáltatás, a felhasználói környezet tárgya a felhasználót, hogy a szolgáltatási műveletet kezdeményezett.

[A mintavételi](app-insights-sampling.md) csökkentése érdekében az összegyűjtött telemetrikus módszerek egyike. Mintavételi algoritmus megkísérli a bejövő vagy kimenő a kapcsolódó telemetriai vagy minta. A névtelen felhasználói azonosítót használja a pontszám generációs mintavétel. Így a névtelen felhasználói azonosító elég véletlenszerű értéknek kell lennie. 

Névtelen felhasználói azonosítóját tárolja a felhasználónév használata a visszaélés miatt, a mező. Hitelesített felhasználói azonosítóját használnia.

Maximális hossz: 128


## <a name="authenticated-user-id"></a>Hitelesített felhasználó azonosítója

Hitelesített felhasználó azonosítója. A névtelen felhasználói azonosító ellentéte, ez a mező képviseli a felhasználót egy rövid nevet. Óta a PII adatok akkor van alapértelmezés szerint nem gyűjt a legtöbb SDK-ban.

Maximális hossz: 1024


## <a name="account-id"></a>Fiókazonosító

A több-bérlős alkalmazásokhoz Ez a futtatófiók-Azonosítóvá vagy nevét, amely a felhasználó működhet együtt. Példák az Azure portál vagy blog neve bloggolás platform előfizetés-azonosító lehet.

Maximális hossz: 1024


## <a name="cloud-role"></a>Felhő szerepkör

A szerepkör az alkalmazás nevének egy részét képezi. A szerepkör nevét, az azure-ban közvetlenül leképezve. Is segítségével különböztetheti meg egymástól micro szolgáltatásokat, amelyek egyetlen alkalmazás részét képezik.

Maximális hossz: 256


## <a name="cloud-role-instance"></a>Felhő szerepkör példánya

Az alkalmazást futtató-példány nevét. Számítógép neve a helyszíni, az Azure-példány nevét.

Maximális hossz: 256


## <a name="internal-sdk-version"></a>Belső: SDK-verzió

SDK-verzió. Lásd: https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification információt.

Maximális hossz: 64


## <a name="internal-node-name"></a>Belső: A csomópont neve

Ez a mező számlázási célokra csomópont nevét jelöli. Akkor bírálja felül a csomópontok szabványos észlelését.

Maximális hossz: 256


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [kiterjesztése és a telemetriai adatok szűrése](app-insights-api-filtering-sampling.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Tekintse meg a standard környezetben tulajdonsággyűjteményében [konfigurációs](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
