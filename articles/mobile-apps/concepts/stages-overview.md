---
title: A Mobile App Development különböző szakaszai a Visual Studio App Center és az Azure-szolgáltatásokkal
description: Ismerje meg a Mobile Application Development különböző szakaszait, valamint azt, hogy a Microsoft-szolgáltatások, például a Visual Studio App Center hogyan hozhatnak létre magas színvonalú mobil alkalmazást.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-1234-5678-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: cd76ff64777892397aeb6152445652fae6f4ba24
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485284"
---
# <a name="different-stages-in-mobile-application-development"></a>A mobil alkalmazások fejlesztésének különböző szakaszai
A mobil alkalmazások létrehozása több fázist is magában foglal. A Microsoft szolgáltatásait, eszközeit és technológiáit könnyedén kihasználhatja magas színvonalú mobil alkalmazások létrehozásához.

## <a name="app-development"></a>Alkalmazás-fejlesztés
Natív egyplatformos ügyfélalkalmazások olyan nyelvekkel is létrehozhatók, mint a Objective-C és a Java. Többplatformos alkalmazásokat hozhat létre a Xamarin használatával, a natív és az egységességi reagálással. A Cordova használatával hibrid alkalmazásokat is készíthet. Ügyfélalkalmazások létrehozásához használja az ide-ket és a kód-szerkesztőket, például a Visual studiót, a Visual Studio for Mac vagy a Visual Studio Code-ot a számítógépeken és a Mac gépeken.

## <a name="plan-and-design-with-back-end-services"></a>Tervezés és kialakítás háttér-szolgáltatásokkal
 A Microsoft számos szolgáltatást biztosít a mobil alkalmazások biztonságos, méretezhető és megbízható háttérrendszer beállításához. Kiszolgáló nélküli API-kat és eseményindítókat hozhat létre anélkül, hogy saját kiszolgálókat kellene fenntartania. A leküldéses értesítések, a kapcsolat nélküli adatszinkronizálás, a hitelesítés és az adatszolgáltatások segítségével átfogó és kiterjedt mobil háttérrendszer készíthető, amely számos alkalmazás-kialakítási forgatókönyvet támogat. 
 
 A háttér-szolgáltatás létrehozása számos követelményt is tartalmaz. A következőket kell tennie:
   - Korlátozza az adattárolást az eszközön.
   - Több eszközön szinkronizálhatja az adatokat.
   - Győződjön meg arról, hogy az alkalmazás hálózati kapcsolat nélkül fut.
   - Értesítések küldése a felhasználóknak.
   - Csökkentheti az akkumulátorok felhasználását.
   - Méretezhető, megbízható és biztonságos háttérrendszer létrehozása.
   - Automatizálja a kiszolgáló karbantartását.

## <a name="devops-and-continuous-monitoring"></a>DevOps és folyamatos figyelés
Mobil-és felhőalapú alkalmazások létrehozásához használja ki a folyamatos integráció (CI), a folyamatos teljesítés (CD) és a folyamatos monitorozási szolgáltatások előnyeit, hogy minden platformon natív buildeket készítsen. Több ezer fizikai eszközön is futtathat FELHASZNÁLÓIFELÜLET-automatizálási teszteket, automatizálhatja a kiadásokat a bétaverziós tesztelési csatornákon keresztül, vagy közvetlenül telepíthet az alkalmazás-áruházakba. Emellett a használati elemzések, a hibák és a kivételek adatai is begyűjthetők az alkalmazásaikban.

## <a name="additional-services"></a>További szolgáltatások
Számos Azure-szolgáltatás felhasználható a kiépített alkalmazás típusától és természetétől függően:
  - Az Azure Cognitive Search a gyors lekérdezéssel megkeresheti az összes típusú belső adattípust.
  - Az Azure Cognitive Services a mesterséges intelligencia használatát az alkalmazásban.
  - Azure-jelző, hogy valós idejű kommunikációt engedélyezzen az alkalmazásban.
