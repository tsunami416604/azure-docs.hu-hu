---
title: "Az Azure Mobile Engagement webes SDK-integráció |} Microsoft Docs"
description: "A legújabb frissítéseket és az Azure Mobile Engagement webes SDK eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrálása az Azure Mobile Engagement egy webalkalmazásban
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Az ebben a cikkben szereplő eljárásokat a legegyszerűbben úgy, hogy aktiválja a elemzés és a webalkalmazás az Azure Mobile Engagement figyelési funkciók.

Kövesse a jelentések, amelyek szükségesek ahhoz, hogy a felhasználók, munkamenetek, tevékenységekhez, összeomlik és technicals összes statisztikája számítási aktiválásához. Az alkalmazás-függő statisztikákat, pl.: eseményeket, hibákat és feladatokat aktiválnia kell a jelentések manuálisan az Azure Mobile Engagement API használatával. További információt a további [használata a speciális API webalkalmazás szerinti címkézését a Mobile Engagement](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Bevezetés
[Töltse le az Azure Mobile Engagement SDK webes](http://aka.ms/P7b453).
A Mobile Engagement webes SDK egyetlen JavaScript fájlként van kiadva, hogy a hely vagy a webes alkalmazás egyes lapján szerepeljenek azure-engagement.js.

> [!IMPORTANT]
> Ez a parancsfájl futtatása előtt futtatnia kell egy parancsfájl vagy a kód részlet, hogy írni a Mobile Engagement beállítása az alkalmazáshoz.
> 
> 

## <a name="browser-compatibility"></a>Böngészőkompatibilitás
A Mobile Engagement webes SDK-t használ a natív JSON kódolási és dekódolási, tartományokon átívelő AJAX-kérelmek (a W3C CORS megadását hagyatkoznia) mellett. Az alábbi böngészők kompatibilis:

* A Microsoft Edge 12 +
* Az Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Mobilmarketing konfigurálása
Írni egy parancsfájlt, amely létrehoz egy globális `azureEngagement` JavaScript object, az alábbi példában látható módon. A hely rendelkezhet olyan Többszörösök lapokat, mert ez a példa feltételezi, hogy ez a parancsfájl minden oldalon szerepel-e. Ebben a példában a JavaScript-objektum neve `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

A `connectionString` érték, az alkalmazás az Azure-portálon jelenik meg.

> [!NOTE]
> `appVersionName`és `appVersionCode` megadása nem kötelező. Azt javasoljuk azonban, hogy konfigurálja azokat, hogy analytics képes a fájlverzió-információkat.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>A Mobile Engagement parancsfájlok szerepeljenek a lapokon
A Mobile Engagement parancsfájlok hozzáadása a lap a következő módszerek valamelyikével:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Vagy ezt:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Miután a Mobile Engagement webes SDK-parancsprogramot be van töltve, létrehozza a **engagement** alias az SDK API-k elérésére. Az alias nem használható SDK konfigurációjának meghatározása. Ez az alias lesz a jelen dokumentációban lévő hivatkozás.

Vegye figyelembe, hogy ha az alapértelmezett alias ütközik egy másik globális változó a lapról, akkor újra meg lehet adni azt a konfigurációban az alábbiak szerint ahhoz, hogy betöltve a Mobile Engagement webes SDK:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Alapvető jelentéskészítési
A Mobile Engagement alapvető jelentéskészítési munkamenet szintű statisztika, például a felhasználók, a munkamenetek, a tevékenységek és az összeomlások statisztikája ismerteti.

### <a name="session-tracking"></a>Munkamenet-követés
A Mobile Engagement munkamenet tevékenységek sorrendje oszlik, minden egyes névvel azonosíthatók.

Klasszikus webhelyen azt javasoljuk, hogy a hely minden oldalon egy másik tevékenység deklarálja. Egy webhelyre vagy webalkalmazásra alkalmazást, amelyben az aktuális oldalon soha nem változik érdemes nyomon követheti a tevékenységek kisebb méretű, például a lapon belül.

Mindkét esetben indítsa el, vagy módosítsa az aktuális felhasználói tevékenység, hívja meg a `engagement.agent.startActivity` függvény. Példa:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

A Mobile Engagement-kiszolgáló automatikusan az alkalmazás lap bezárása után három percen belül munkamenetekkel karakterlánccal végződik-e.

Azt is megteheti, hogy is munkamenet befejezése manuálisan meghívásával `engagement.agent.endActivity`. Ez beállítja, hogy az aktuális felhasználói tevékenységet "Inaktív".  A munkamenet véget ér 10 másodperc múlva, kivéve, ha új hívása `engagement.agent.startActivity` folytatja a munkamenet.

A globális bevonási objektumot, az alábbiak szerint konfigurálhatja a 10 másodperces késleltetési:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Nem használhat `engagement.agent.endActivity` a a `onunload` visszahívási mert AJAX-hívások nem hajtható végre ezen a ponton.
> 
> 

## <a name="advanced-reporting"></a>Speciális jelentéskészítés
Szükség esetén szeretne jelentést készíteni, az alkalmazás-specifikus eseményeket, hibákat és feladatok, ha szeretné a Mobile Engagement API-t használja. A Mobile Engagement API keresztül hozzáférhet a `engagement.agent` objektum.

A speciális funkciók a Mobile Engagement a Mobile Engagement API érheti el. Az API-cikkben található részletes [használata a speciális API webalkalmazás szerinti címkézését a Mobile Engagement](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Az AJAX-hívások használt URL-címek testreszabása
Testre szabhatja a Mobile Engagement webes SDK-t használó URL-címeket. Például a napló URL-címe (az SDK végpont naplózás) újra, konfigurációt lehet felülbírálni a ehhez hasonló:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Ha az URL-címet adja vissza, amely kezdődik `/`, `//`, `http://`, vagy `https://`, az alapértelmezett séma nem használatos. Alapértelmezés szerint a `https://` séma az URL-címeket szolgál. Ha szeretné testre szabni az alapértelmezett séma, bírálja felül a konfigurációt, ehhez hasonló:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
