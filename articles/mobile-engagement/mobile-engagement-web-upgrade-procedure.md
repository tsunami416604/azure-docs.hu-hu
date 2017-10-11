---
title: "Az Azure Mobile Engagement webes SDK frissítési eljárásait |} Microsoft Docs"
description: "A legújabb frissítéseket és a webszolgáltatási SDK az Azure Mobile Engagement eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Az Azure Mobile Engagement webes SDK frissítési eljárások
Ha az Azure Mobile Engagement webszolgáltatási SDK egy korábbi verzióját már a webes alkalmazás van integrálva, vegye figyelembe a következő szempontokat, amikor frissít, az SDK-t szeretné.

Ha kihagyja a Mobile Engagement webes SDK több verziója, szükség lehet a frissítési folyamat során számos műveletek végrehajtásához. Például ha áttelepít 1.4.0 1.6.0, először eljárások a 1.5.0 1.4.0 frissítésével. Ezután kövesse 1.6.0 1.5.0 frissítésével.

Bármelyik verzióra frissít, a fájl azure-engagement.js korábbi verzióját cserélje le a legújabb verzióját.

## <a name="upgrade-from-121-to-200"></a>2.0.0 1.2.1-es frissítésével
Ez a szakasz ismerteti, hogyan telepíthetők át a Mobile Engagement webes SDK-integráció a Capptain szolgáltatás, amelyet Capptain SAS-alkalmazásokhoz az Azure Mobile Engagement. Ha egy korábbi verziójáról telepít, tekintse át a Capptain webhely 1.2.1-es először át, és ezután alkalmazza az alábbi eljárásokat.

A Mobile Engagement webes SDK ezen verziója nem támogatja a Samsung intelligens TV, Opera TV, webOS vagy a Reach-szolgáltatás.

> [!IMPORTANT]
> Capptain és Azure Mobile Engagement nincsenek ugyanazt a szolgáltatást. Az alábbi eljárás csak hogyan telepítheti át az ügyfélalkalmazás mutatja be. A Mobile Engagement webes SDK az alkalmazás áttelepítése rendszer nem telepíti át az adatok egy Capptain kiszolgálóról a Mobile Engagement-kiszolgálóhoz.
> 
> 

### <a name="javascript-files"></a>JavaScript-fájlok
A fájl capptain sdk.js cserélje le az azure-engagement.js fájlt, és ennek megfelelően módosítsa a parancsfájl importálja.

### <a name="remove-capptain-reach"></a>Capptain Reach eltávolítása
A Mobile Engagement webes SDK ezen verziója nem támogatja a Reach-szolgáltatás. Ha az alkalmazás Capptain Reach integrálva, el kell távolítania azt.

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

### <a name="remove-deprecated-apis"></a>Távolítsa el az elavult API-k
Néhány API-kat Capptain a Mobile Engagement webes SDK elavult.

Távolítsa el a következő API-k bármely hívások: `agent.connect`, `agent.disconnect`, `agent.pause`, és `agent.sendMessageToDevice`.

Távolítsa el a következő visszahívások minden példányát a Capptain konfigurációjából: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, és `onPushMessageReceived`.

### <a name="configuration"></a>Konfiguráció
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

### <a name="javascript-apis"></a>JavaScript API-k
A globális JavaScript objektum `window.capptain` át lett nevezve `window.azureEngagement` is használhat, de a `window.engagement` alias az API-hívásokhoz. Az alias nem használható az SDK-konfiguráció.

Például `capptain.deviceId` válik `engagement.deviceId`, `capptain.agent.startActivity` válik `engagement.agent.startActivity`, és így tovább.

