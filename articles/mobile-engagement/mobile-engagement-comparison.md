---
title: "Azure Mobile Engagement összehasonlításával más hasonló Azure-szolgáltatásokkal"
description: "Azure Mobile Engagement összehasonlítva az egyéb hasonló Azure-szolgáltatások - Hockeyappra, az appinsights által biztosított, a Notification Hubs"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7df2eb9ecebe3313dad9c15171552a084787f6b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Azure Mobile Engagement összehasonlításával más hasonló Azure-szolgáltatásokkal
A Microsoft Azure által kínált szolgáltatások listája egyre növekszik, és esetenként Felvetődik hogyan eltér Azure Mobile Engagement a szolgáltatás, amely csak olvasható vagy szóló értesítésekre. Ez a cikk megpróbálja törölni a félreértések, és arra utasítja az Azure Mobile Engagement kiválaszthatja, ha a legmegfelelőbb a használatra. 

Az Azure Mobile Engagement egy olyan szolgáltatás, kifejezetten megcélzott **a digitális forgalmazók/CMOs** bármelyik használható, de **mobilalkalmazás tulajdonos vagy a közzétevő** ki szeretné javítja a használati, megőrzés jeleníthetnek azok mobilalkalmazásából. 

*A szoftver,--szolgáltatás (SaaS) felhasználóelérési platform, amely alkalmazások használatát, valós idejű felhasználói szegmentálást, adatvezérelt betekintést biztosít, és lehetővé teszi, hogy analitikát leküldéses értesítésekkel és alkalmazáson belüli üzenetek is.* 

Ez a definíció bontásához, a következő kulcs jellemzők, amely az egyedi értékajánlatához is van:

1. **Analitikát leküldéses értesítésekkel és alkalmazáson belüli üzenetek**
   
   Ez az alapvető célja azoknak a termék,-hajtsa végre a megcélzott és a testreszabott leküldéses értesítéseket. És, hogy ez a gazdag viselkedéselemzés adatokat gyűjtünk. 
2. **Alkalmazás használatának adatvezérelt betekintést**
   
   Azt adja meg a többplatformos SDK-k gyűjthet az alkalmazás felhasználói viselkedés elemzéséhez. Vegye figyelembe a kifejezés viselkedéselemzés (szemben teljesítmény analytics), mert azt összpontosít a felhasználók hogyan használják az alkalmazást. Alapvető analitikai adatok kapcsolatos hibák, a összeomlások stb azonban, amely nem a termék core fókusz gyűjtünk. 
3. **Valós idejű felhasználói szegmentálást**
   
   Miután összegyűjtötte alkalmazás felhasználói viselkedés elemzéséhez adatait, azt teszik lehetővé a célközönséget különböző paraméterek alapján, és az összegyűjtött adatok lehetővé teszik a megcélzott ügyfélleküldéses kampányok szegmenshez. 
4. **Szoftverek,--szolgáltatás (SaaS):**
   
   Az Azure felügyeleti portálján, amely interakciót és gazdag viselkedés elemzéséhez az alkalmazás felhasználók megtekintése és futtatása a leküldéses marketingkampányok optimalizált elkülönül a portál van. A termék kövesse az első címen nem található!   

Ez az aktuális megkülönböztetési vannak beállítva itt hogyan azt összehasonlításhoz más látszólag hasonló Azure-szolgáltatásokkal – vegye figyelembe, hogy a cikk egy szolgáltatás szintű összehasonlító nem, de egy további forgatókönyv tart alapján határozza meg, melyik termék működik a legjobban megközelítése:

1. [Hockeyappra](https://azure.microsoft.com/services/hockeyapp/) a Microsoft mobil DevOps megoldás. A segítségével eloszthatja a buildeket a bétatesztelők között, begyűjtheti az összeomlási adatokat, és lekérheti a felhasználói visszajelzéseket. Azt is integrálható a Visual Studio Team Services engedélyezésével könnyen build telepítések és a munkalem-integrációt. 
   
   Az itt elsősorban DevOps és gyűjtését analytics teljesítményadatainak a mobile apps szolgáltatásban. Akkor fordulhatnak elő mindkét HockeyApps integrálása és a Mobile Engagement az alkalmazás, amely nem lesz szokatlan, mivel annak ellenére, hogy az összegyűjtött adatokat az átfedés van, a termékek core elsősorban különböző, és elősegítik különböző célok eléréséhez meg.  
2. [Az Application Insights](../application-insights/app-insights-overview.md) , ha a mobilalkalmazás van a kiszolgálón található, akkor az Application Insights webes kiszolgálóoldali az alkalmazás figyelése használja, de az ügyfél oldali mobilalkalmazások, érdemes használni a Hockeyappra. 
3. [A Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) abban az értelemben, hogy nem kell az integrált a mobilalkalmazás SDK és teljes körű hozzáférést engedélyezzenek a mobilalkalmazás lehet, és lehetővé teszi a leküldéses értesítések küldése az alapszintű címkézési képességek egy egyszerűsített szolgáltatást biztosít. Ez a nagyszerű bármely alkalmazás tulajdonost, aki számára fontos kisebb célcsoport-kezelési és több Küldés/kommunikáció információt azonnal az alkalmazás felhasználók (a nagy a populáció elemeit reprezentáló szórás). 
   
   Vegye figyelembe a fókusz Itt a küldött gyors értesítések blazing alapvető Szegmentálás alkalmas. 

A következőkben néhány olyan forgatókönyvet:

1. TIM Adventure Works üzletben tesz közzé a felhasználók digitális marketing csapat része. TIM a célja, hogy töltse le a mobile apps szolgáltatásban a felhasználók továbbra is használhatja, és gyakran. Ez nem csak növeli a márka kapcsolódni az alkalmazásaik felhasználóit, de is növekszik jeleníthetnek amikor az alkalmazás felhasználók vásárlások a mobilalkalmazással. A Tim kell az alkalmazást, a felhasználóknak, amelyet akkor hasznosak, nyissa meg az alkalmazást, és térjen vissza az gyakran való célzott értesítések küldéséhez. Ez azért, ahol Tim megkeresi a Mobile Engagement hasznos. 
2. Joann az Adventure Works: a mobilalkalmazások a fejlesztői csapat része, és a termék jelentkezzen összeomlik, kivételek, adatait, és teljesítmény telemetriai adatokat az alkalmazásokból keres. Ez azért, ahol Joann megkeresi Hockeyappra hasznos. Joann integrálhat lekérni a legjobb is ugyanazt az alkalmazást a Hockeyappra saját fejlesztői arra irányul, hogy forgatókönyvek és Azure Mobile Engagement Tim is. 
3. Multiplexelés a fejlesztői csapat a Contoso hírek hálózati és az összes szeretné küldi friss hírek riasztások minden felhasználó nélkül sok szegmentálását, amint a hírek figyelmeztetés jelenik meg a mobilalkalmazások részét képezi. Ez azért, ahol multiplexelés megkeresi a Notification Hubs hasznos. 
   Az ebben az esetben is lehetséges azonban, hogy a mobilalkalmazás Miután kiforrottá válik, nincs sok gazdagabb Szegmentálás és részletes információkat szolgáltatva az alkalmazások felhasználói viselkedés követelmény. Ilyenkor multiplexelés kell kiértékelni az Azure Mobile Engagement. 

Recap, hogy a Mobile Engagement célja nem analytics összegyűjtése csak – nem "még egy másik Analytics termékhez a Microsoft". Célzott leküldéses értesítések küldése szól, és a célcsoport-kezelési, a viselkedéselemzés adatokat gyűjtünk, de a fókusz továbbra is megtalálható, amelyeket célszerű a legtöbb alkalmazás számára, hogy azt nem bővítményeként történő kezelését leküldéses értesítések küldése. 

További részletekért – tekintse meg a [gyors videó](mobile-engagement-overview.md) egy nutshell a Mobile Engagement kapcsolatban. 

