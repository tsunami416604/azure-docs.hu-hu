---
title: "Azure Mobile Engagement belül Emoji hangulatjelek segítségével"
description: "A leküldéses értesítések belül Emoji Hangulatjelek használata"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Leküldéses értesítések belül Emoji hangulatjel használata
Megadhat Emoji hangulatjelek a leküldéses értesítési néhány egyszerű lépésben: 

1. Először meg kell találnia az Emoji is szeretne küldeni az üzenetben. Győződjön meg arról, hogy a Emoji kiválasztja a támogat a céleszközt, az eszköz megjelenítse némi időt újonnan jóváhagyott Emojis hozzáadása a kiválasztott platformokon. 
2. A **Windows** -lépjen a [hivatkozás](http://apps.timwhitlock.info/emoji/tables/unicode) , és másolja a "Natív" ikonra.
   
    ![][7] 
3. A **Mac** -szótár alkalmazásban szerkesztés alatt Emojis -> Emoji & szimbólumok található.
   
    ![][6] 
4. Most, lépjen a **elérni** lapján a az Azure Mobile Engagement portálon. Válassza ki a leküldéses értesítési (bejelentés, kérdezze le az stb) típusú. A példa egy bejelentési leküldéses választjuk.
5. Adja meg az értesítés a különböző mezők, amíg el nem éri az értesítés szövegét. Ez azért, ahol fel fog venni a Emoji hangulatjel. Ha szeretné, elhelyezi a cím, az üzenet vagy mindkettőt. Szüksége lesz húzza és dobja el, vagy másolja a Emoji talált a helyekről. 
   
    ![][1]
6. Töltse ki az értesítéshez a mezőket, és mentse. 
7. A teszt futtatásakor vagy a közlemény aktiválása megadott megjelenik egy értesítés a hangulatjel a.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

