---
title: Az Azure Application Insights Telemetria adatmodell - Telemetria környezet |} A Microsoft Docs
description: Application Insights telemetria környezet adatmodell
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3c1ef0a7a5a9c2ad416589118b61432210428e1c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720651"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetriai adatok környezeti: Application Insights adatmodell

Előfordulhat, hogy minden szereplő telemetriai elem egy szigorú típusmegadású környezet mezőket. Minden mező lehetővé teszi, hogy egy adott figyelési forgatókönyv. Az egyéni tulajdonságok gyűjteményén használatával egyéni vagy alkalmazásspecifikus környezetfüggő adatok tárolására.


## <a name="application-version"></a>Alkalmazás verziószáma

Az alkalmazás helyi mező adatait, mindig az alkalmazás, amely a telemetriai adatokat küldenek. Alkalmazás verziója szolgál az alkalmazás viselkedése, és a központi telepítéseket, a korrelációs trend változások elemzéséhez.

Maximális hossz: 1024


## <a name="client-ip-address"></a>Ügyfél IP-címe

Az ügyféleszköz IP-címét. IPv4 és IPv6 használata támogatott. Telemetriai adatokat a szolgáltatásból küldött, a hely környezet tárgya a felhasználó által kezdeményezett a műveletet a szolgáltatásban. Application Insights a földrajzi helymeghatározás információ kinyerését az ügyfél IP-Címét, és ezután csonkolja azt. Így önmagában ügyfél IP-címe nem használható végfelhasználói azonosításra alkalmas adatokat. 

Maximális hossz: 46


## <a name="device-type"></a>Eszköztípus

Ez a mező eredetileg használták a a végfelhasználó számára az alkalmazás által használt eszköz típusának jelöléséhez. Elsősorban az JavaScript telemetriai megkülönböztethetők az eszköztípus-ma használt kiszolgálóoldali telemetria az eszközzel a "böngésző" írja be a "PC".

Maximális hossz: 64


## <a name="operation-id"></a>Műveletazonosító

A legfelső szintű művelet egyedi azonosítója. Ez az azonosító több összetevőből csoport telemetriai lehetővé teszi. Lásd: [telemetriai korreláció](application-insights-correlation.md) részleteiről. A művelet azonosítója vagy egy kérelmet, vagy egy oldal nézet hozható létre. Minden egyéb telemetriai adat ebben a mezőben a tartalmazó kérelem vagy oldalmegtekintés nézet értékét állítja be. 

Maximális hossz: 128


## <a name="parent-operation-id"></a>A művelet Szülőazonosító

A telemetriai elem azonnali szülő egyedi azonosítója. Lásd: [telemetriai korreláció](application-insights-correlation.md) részleteiről.

Maximális hossz: 128


## <a name="operation-name"></a>Művelet neve

A (csoport) a művelet neve. A művelet nevét vagy egy kérelmet, vagy egy oldal nézet hozható létre. Minden egyéb telemetriai elem értékét az a kérelem vagy oldalmegtekintés tartalmazó nézetet adja meg a mezőben. Název operace műveletek (például "GET Home/Index") egy csoport összes telemetriai elem keresése szolgál. Ez a környezeti tulajdonság használható választ kérdéseire "Mik azok a jellemző kivételek, ezen az oldalon."

Maximális hossz: 1024


## <a name="synthetic-source-of-the-operation"></a>A művelet szintetikus forrás

Szintetikus forrás nevére. Az alkalmazás egyes telemetriáját tüntetheti szintetikus forgalom. A webhely, a webhely rendelkezésre állási tesztek vagy a diagnosztikai kódtárakat, mint maga az Application Insights SDK hívásláncait indexelő webbejáró lehet.

Maximális hossz: 1024


## <a name="session-id"></a>Munkamenet-azonosító

Munkamenet-azonosító – a felhasználó és az alkalmazás közötti interakció példányát. A munkamenet-környezet mező adatait, mindig a végfelhasználó kapcsolatban. Telemetriai adatokat a szolgáltatásból küldött, a munkamenet környezetében tárgya a felhasználó által kezdeményezett a műveletet a szolgáltatásban.

Maximális hossz: 64


## <a name="anonymous-user-id"></a>Névtelen felhasználó azonosítója

Névtelen felhasználó azonosítója. A végfelhasználó számára az alkalmazás jelöli. Telemetriai adatokat a szolgáltatásból küldött, a felhasználói környezet tárgya a felhasználó által kezdeményezett a műveletet a szolgáltatásban.

[Mintavételi](app-insights-sampling.md) minimalizálására gyűjtött telemetria módszerek egyike. Bármelyik mintát és leskálázása a korrelált telemetria megkísérli mintavételezési algoritmus használhatóvá válik. Névtelen felhasználói azonosítót használja a pontszám generációs mintavétel. Így a névtelen felhasználói azonosító elég véletlenszerű értéknek kell lennie. 

Névtelen felhasználó azonosítója használja a felhasználónév tárolására, a mező egy való visszaélés. Használja a hitelesített felhasználó azonosítója.

Maximális hossz: 128


## <a name="authenticated-user-id"></a>Hitelesített felhasználó azonosítója

Hitelesített felhasználó azonosítója. Névtelen felhasználó azonosítója, ellenkező esetben ez a mező képviseli a felhasználót egy rövid nevet. A PII adatok óta, nem lesznek begyűjtve alapértelmezés szerint a legtöbb SDK-t.

Maximális hossz: 1024


## <a name="account-id"></a>Fiókazonosító

Több-bérlős alkalmazásokban Ez az a fiók azonosítója vagy nevét, amely a felhasználó a jár el. Példák az Azure portal vagy a blogba neve blogplatform, amely az előfizetés-azonosító lehet.

Maximális hossz: 1024


## <a name="cloud-role"></a>Felhőalapú munkakör

A szerepkör az alkalmazás neve egy részét képezi. Leképezések közvetlenül a szerepkör nevét, az Azure-ban. Is használható különbséget tenni a mikroszolgáltatásokhoz, amely egyetlen alkalmazás részét képezik.

Maximális hossz: 256


## <a name="cloud-role-instance"></a>Felhőalapú szerepkörpéldány

Az alkalmazás hol fut.-példány nevét. Számítógép neve a helyszínen, az Azure-példány nevét.

Maximális hossz: 256


## <a name="internal-sdk-version"></a>Belső: SDK-verzió

SDK-verzió. Lásd: https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification információt.

Maximális hossz: 64


## <a name="internal-node-name"></a>Belső: Csomópont nevét a

Ez a mező képviseli a csomópont nevét a számlázási célokra. Akkor bírálja felül a standard szintű csomópontok felismerése.

Maximális hossz: 256


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [bővítése és szűrőtelemetria](app-insights-api-filtering-sampling.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Tekintse meg a standard szintű környezeti tulajdonságok gyűjteményén [konfigurációs](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
