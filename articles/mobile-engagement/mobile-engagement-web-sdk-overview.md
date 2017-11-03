---
title: "Az Azure Mobile Engagement webes SDK áttekintése |} Microsoft Docs"
description: "A legújabb frissítéseket és a webszolgáltatási SDK az Azure Mobile Engagement eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: 770a83131a3e661771db50b22ce7de25b2d541cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk"></a>Az Azure Mobile Engagement webes SDK-t
Minden a vonatkozó további információért integrálása az Azure Mobile Engagement egy web app alkalmazásban, kezdje itt. Ha azt szeretné, próbálja ki a saját webalkalmazás első lépések előtt, olvassa el a [15 perces oktatóanyag](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Integráció eljárások
1. Ismerje meg, [integrálása a Mobile Engagement a webalkalmazáson belüli](mobile-engagement-web-integrate-engagement.md).
2. Címke terv végrehajtásához további [hogyan használható a speciális Mobile Engagement API szerinti címkézését a webalkalmazás](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* Rögzített összeomlási a privát böngészés (Safari).
* Rögzített összeomlási a böngésző cookie-k le van tiltva.

Az összes verzió, lásd: a [végezze el a kibocsátási megjegyzések](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Frissítési eljárások
### <a name="upgrade-from-121-to-200"></a>2.0.0 1.2.1-es frissítésével
Az alábbi szakaszok ismertetik a Mobile Engagement webes SDK-integráció át a Capptain szolgáltatás, amelyet Capptain SAS-alkalmazásokhoz az Azure Mobile Engagement. Ha az áttelepítés verziójából származó rendszernél korábbi 1.2.1-es, tekintse át a Capptain webhely 1.2.1-es először át, és ezután alkalmazza az alábbi eljárásokat.

A Mobile Engagement webes SDK ezen verziója nem támogatja a Samsung intelligens TV, Opera TV, webOS vagy a Reach-szolgáltatás.

> [!IMPORTANT]
> Capptain és Azure Mobile Engagement nem ugyanazt a szolgáltatást, és a következő eljárások csak hogyan telepítheti át az ügyfélalkalmazás jelöljön ki. A Mobile Engagement webes SDK az alkalmazás áttelepítése rendszer nem telepíti át az adatok egy Capptain kiszolgálóról a Mobile Engagement-kiszolgálóhoz.
> 
> 

#### <a name="javascript-files"></a>JavaScript-fájlok
A fájl capptain sdk.js cserélje le az azure-engagement.js fájlt, és ennek megfelelően módosítsa a parancsfájl importálja.

#### <a name="remove-capptain-reach"></a>Capptain Reach eltávolítása
A Mobile Engagement webes SDK ezen verziója nem támogatja a Reach-szolgáltatás. Ha az alkalmazás rendelkezik Capptain Reach integrálva, el kell távolítania azt.

Távolítsa el a CSS elérni importálása a lapról, és törli a kapcsolódó .css-fájl (capptain-reach.css, alapértelmezés szerint).

A következő Reach erőforrások törlése: a Bezárás lemezkép (capptain-close.png, alapértelmezés szerint) és a márka ikon (capptain-értesítés-ikonra, alapértelmezés szerint).

Távolítsa el az alkalmazásbeli értesítésekben elérni felhasználói felülete. Az alapértelmezett elrendezési így néz ki:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Távolítsa el a szöveget és webes mutató hirdetmények és szavazások esetén elérni felhasználói felület. Az alapértelmezett elrendezési így néz ki:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Távolítsa el a `reach` a konfigurációját, ha van ilyen objektum. Néz ki:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Távolítsa el bármilyen egyéb Reach testreszabása: például kategóriák.

#### <a name="remove-deprecated-apis"></a>Távolítsa el az elavult API-k
Néhány API-kat Capptain a Mobile Engagement webes SDK elavult.

Távolítsa el a következő API-k bármely hívások: `agent.connect`, `agent.disconnect`, `agent.pause`, és `agent.sendMessageToDevice`.

Távolítsa el a következő visszahívások bármelyikét a Capptain konfigurációjából: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, és `onPushMessageReceived`.

#### <a name="configuration"></a>Konfiguráció
A Mobile Engagement SDK-azonosítókat, például az alkalmazásazonosító konfigurálása egy kapcsolati karakterláncot használ.

Az Alkalmazásazonosító cserélje le a kapcsolati karakterláncot. Vegye figyelembe, hogy az SDK-konfigurációhoz a globális objektum-ről változik `capptain` való `azureEngagement`.

Mielőtt áttelepítése:

    window.capptain = {
      appId: ...,
      [...]
    };

: Az áttelepítés után

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

A kapcsolati karakterlánc az alkalmazás az Azure-portálon jelenik meg.

#### <a name="javascript-apis"></a>JavaScript API-k
A globális JavaScript objektum `window.capptain` át lett nevezve `window.azureEngagement`, de használhatja a `window.engagement` alias az API-hívásokhoz. Az alias nem használható SDK konfigurációjának meghatározása.

Például `capptain.deviceId` válik `engagement.deviceId`, `capptain.agent.startActivity` válik `engagement.agent.startActivity`, és így tovább.

Ha az Azure Mobile Engagement webszolgáltatási SDK egy korábbi verzióját már a az alkalmazásba integrált része, kérjük, olvassa el kapcsolatos [eljárások frissítése](mobile-engagement-web-upgrade-procedure.md).

