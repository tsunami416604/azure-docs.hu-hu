---
title: "Ismerkedés az Azure Mobile Engagement webalkalmazásokkal való használatával | Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Mobile Engagement a webalkalmazásokhoz kapcsolódó elemzésekkel és leküldéses értesítésekkel."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: abcb04e4e0a3ae4fdba3a4ded20b3846ac3b21e6
ms.contentlocale: hu-hu
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Ismerkedés az Azure Mobile Engagement Web webalkalmazásokkal való használatával
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement a webalkalmazások használatának megértéséhez.

> [!NOTE]
> Az Azure Mobile Engagement szolgáltatást 2018 márciusától megszüntetjük, és jelenleg csak meglévő ügyfelek számára érhető el. További információkért lásd: [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Az oktatóanyaghoz az alábbiakra lesz szükség:

* Visual Studio 2015 vagy bármely másik választott szerkesztő
* [Web SDK](http://aka.ms/P7b453)

Ez a Web SDK egy előzetes verzió, és kizárólag az elemzést támogatja, böngészőn vagy alkalmazáson belüli leküldéses értesítésék küldését nem. 

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>A Mobile Engagement beállítása a webalkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez.

Létre fogunk hozni egy alapszintű alkalmazást a Visual Studio segítségével az integráció bemutatásához, a műveleti lépéseket azonban A Visual Studión kívül létrehozott webalkalmazásokon is követheti. 

### <a name="create-a-new-web-app"></a>Új webalkalmazás létrehozása
A következő lépések a Visual Studio 2015 használatát feltételezik, de a lépések a Visual Studio korábbi verziói esetén is hasonlóak. 

1. Indítsa el a Visual Studiót, és a **Kezdőképernyőn** válassza a **New Project** (Új projekt) lehetőséget.
2. Az előugró ablakban válassza a **Web** -> **ASP.Net Web Application** (ASP.NET-webalkalmazás) lehetőséget. Adja meg az alkalmazás **Name** (Név), **Location** (Hely) és **Solution name** (Megoldás neve) adatait, majd kattintson az **OK** gombra.
3. A **Select a template** (Sablon kiválasztása) előugró ablakban válassza az **Empty** (Üres) lehetőséget az **ASP.Net 4.5 Templates** (ASP.Net 4.5-sablonok) területen, majd kattintson az **OK** gombra. 

Most létrehozott egy új üres webalkalmazás-projektet, amelybe integrálni fogjuk az Azure Mobile Engagement Web SDK-t.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. A saját megoldásában hozzon létre egy **javascript** nevű új mappát, majd vegye fel abba az **azure-engagement.js** nevű Web SDK JS-fájlt. 
2. A javascript mappához a következő kóddal adjon hozzá egy **main.js** nevű új fájlt. Győződjön meg arról, hogy frissítette a kapcsolati karakterláncot. A rendszer ezt az `azureEngagement` objektumot használja a Web SDK-metódusokhoz történő hozzáféréshez. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![Visual Studio .js fájlokkal][1]

## <a name="enable-real-time-monitoring"></a>Valós idejű figyelés engedélyezése
Az adatok küldésének megkezdéséhez és annak biztosításához, hogy a felhasználók aktívak, legalább egy tevékenységet el kell küldenie a Mobile Engagement háttérrendszere számára. A webalkalmazás kontextusában a tevékenységek weblapok. 

1. A saját megoldásában hozzon létre egy **home.html** nevű új lapot, majd állítsa be azt a webalkalmazás kezdőlapjaként. 
2. A következők a törzscímkébe illesztésével foglalja bele a korábban ezen a lapon hozzáadott két javascriptet. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. Az EngagementAgent `startActivity` metódusának meghívásához frissítse a törzscímkét
   
        <body onload="engagement.agent.startActivity('Home')">
4. A **home.html** lapnak így kell kinéznie
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>Az elemzés kiterjesztése
Az alábbiakban látható az összes jelenleg elérhető, elemzési célra használható Web SDK-metódus:

1. Tevékenységek/weblapok:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. Események
   
        engagement.agent.sendEvent(name, extras);
3. Hibák
   
        engagement.agent.sendError(name, extras);
4. Feladatok
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png


