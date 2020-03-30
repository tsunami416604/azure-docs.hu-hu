---
title: Azure Application Insights telemetriai adatmodell – telemetriai környezet | Microsoft dokumentumok
description: Az Application Insights telemetriai környezetének adatmodellje
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671863"
---
# <a name="telemetry-context-application-insights-data-model"></a>Telemetriai környezet: Application Insights-adatmodell

Minden telemetriai elem lehet egy erősen beírt környezet mezőket. Minden mező lehetővé tesz egy adott figyelési forgatókönyvet. Az egyéni tulajdonsággyűjtemény segítségével egyéni vagy alkalmazásspecifikus környezetfüggő információkat tároljon.


## <a name="application-version"></a>Alkalmazás verziója

Az alkalmazáskörnyezet-mezőkben szereplő információk mindig a telemetriai adatokat küldő alkalmazásról szólnak. Az alkalmazásverziója az alkalmazás viselkedésében és a központi telepítésekkel való korrelációjában bekövetkező trendváltozások elemzésére szolgál.

Maximális hossz: 1024


## <a name="client-ip-address"></a>Ügyfél IP-címe

Az ügyféleszköz IP-címe. Az IPv4 és az IPv6 protokoll támogatott. Amikor telemetriai adatokat küld egy szolgáltatásból, a helykörnyezet a felhasználóról szól, aki a műveletet a szolgáltatásban kezdeményezte. Az Application Insights kinyeri a földrajzi hely adatait az ügyfél IP-címéről, majd csonkolja azt. Így az ügyfél IP önmagában nem használható végfelhasználói azonosítható információként. 

Maximális hossz: 46


## <a name="device-type"></a>Eszköz típusa

Eredetileg ez a mező az alkalmazás végfelhasználója által használt eszköz típusát jelzi. Ma elsősorban a JavaScript-telemetria ii.

Maximális hossz: 64


## <a name="operation-id"></a>Műveleti azonosító

A gyökérművelet egyedi azonosítója. Ez az azonosító lehetővé teszi a telemetriai adatok csoportosítását több összetevő között. A részletekért lásd a [telemetriai korrelációt.](../../azure-monitor/app/correlation.md) A műveletazonosítót kérés vagy lapnézet hozta létre. Az összes többi telemetria ezt a mezőt a tartalmazó kérés vagy oldalnézet értékére állítja be. 

Maximális hossz: 128


## <a name="parent-operation-id"></a>Szülőművelet azonosítója

A telemetriai elem közvetlen szülőjének egyedi azonosítója. A részletekért lásd a [telemetriai korrelációt.](../../azure-monitor/app/correlation.md)

Maximális hossz: 128


## <a name="operation-name"></a>Művelet neve

A művelet neve (csoportja). A művelet nevét kérés vagy lapnézet alapján hozják létre. Az összes többi telemetriai elem ezt a mezőt a tartalmazó kérés vagy oldalnézet értékére állítja. A művelet neve egy műveletcsoport összes telemetriai elemének (például "Get Home/Index") keresésére szolgál. Ez a környezet tulajdonság olyan kérdések megválaszolására szolgál, mint például a "melyek az ezen az oldalon szereplő tipikus kivételek".

Maximális hossz: 1024


## <a name="synthetic-source-of-the-operation"></a>A művelet szintetikus forrása

A szintetikus forrás neve. Az alkalmazás egyes telemetriai adatai szintetikus forgalmat jelenthetnek. Lehet, hogy a webbejáró indexeli a webhelyet, a webhely rendelkezésre állási tesztjeit vagy a diagnosztikai kódtárakból , például az Application Insights SDK-ból származó nyomkövetéseket.

Maximális hossz: 1024


## <a name="session-id"></a>Munkamenet-azonosító

Munkamenet-azonosító – a felhasználó és az alkalmazás közötti interakció példánya. A munkamenet-környezet mezőiben szereplő információk mindig a végfelhasználóról szólnak. Amikor telemetriai adatokat küld egy szolgáltatásból, a munkamenet-környezet a szolgáltatásban a műveletet kezdeményező felhasználóról szól.

Maximális hossz: 64


## <a name="anonymous-user-id"></a>Névtelen felhasználói azonosító

Névtelen felhasználói azonosító. Az alkalmazás végfelhasználóját jelöli. Amikor telemetriai adatokat küld egy szolgáltatásból, a felhasználói környezet a felhasználóról szól, aki a műveletet a szolgáltatásban kezdeményezte.

[Mintavételezés](../../azure-monitor/app/sampling.md) az egyik olyan technikák, amelyek minimálisra csökkentik az összegyűjtött telemetriai adatok. Mintavételi algoritmus megpróbálja vagy mintát, vagy ki az összes korrelált telemetriai adatokat. Névtelen felhasználói azonosító mintavételi pontszám generálása használt. Tehát névtelen user id legyen egy véletlen elég érték. 

A névtelen felhasználói azonosító használata a felhasználónév tárolására a mezővel való visszaélés. Használja a hitelesített felhasználói azonosítót.

Maximális hossz: 128


## <a name="authenticated-user-id"></a>Hitelesített felhasználói azonosító

Hitelesített felhasználói azonosító. A névtelen felhasználói azonosító ellentéte, ez a mező a rövid névvel rendelkező felhasználót jelöli. Mivel a pii-adatokat alapértelmezés szerint nem gyűjti a legtöbb SDK.

Maximális hossz: 1024


## <a name="account-id"></a>Fiókazonosítója

A több-bérlős alkalmazásokban ez a fiókazonosító vagy név, amellyel a felhasználó jár el. Ilyen lehet például az Azure Portal előfizetési azonosítója vagy egy blog neve egy blogplatformhoz.

Maximális hossz: 1024


## <a name="cloud-role"></a>Felhőbeli szerepkör

Annak a szerepkörnek a neve, amelynek az alkalmazás része. Közvetlenül az azure-ban lévő szerepkörnévhez rendeli hozzá a leképezéseket. Az egyetlen alkalmazás részét képező mikroszolgáltatások megkülönböztetésére is használható.

Maximális hossz: 256


## <a name="cloud-role-instance"></a>Felhőalapú szerepkörpéldány

Annak a példánynak a neve, ahol az alkalmazás fut. A helyszíni számítógép neve, az Azure példányneve.

Maximális hossz: 256


## <a name="internal-sdk-version"></a>Belső: SDK verzió

SDK-verzió. Ebben a [cikkben](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) további információt talál.

Maximális hossz: 64


## <a name="internal-node-name"></a>Belső: Csomópont neve

Ez a mező a számlázási célokra használt csomópontnevet jelöli. Segítségével felülbírálhatja a csomópontok szabványos észlelését.

Maximális hossz: 256


## <a name="next-steps"></a>További lépések

- További információ a [telemetria meghosszabbításáról és szűréséről.](../../azure-monitor/app/api-filtering-sampling.md)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- Tekintse meg a szabványos környezeti tulajdonságok [gyűjteményének konfigurációját.](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)
