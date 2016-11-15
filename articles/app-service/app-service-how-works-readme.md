---
title: "Az Azure App Service működése"
description: "Ismerje meg az App Service működését"
keywords: "app service, azure app service, méret, méretezhető, app service-csomag, app service ára"
services: app-service
documentationcenter: 
author: yochay
manager: wpickett
editor: 
ms.assetid: ae74fc32-969e-4580-8d61-02c922f1f184
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2016
ms.author: yochay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e5c425daf83962ca0b49035784c3804e0c17b2c


---
# <a name="how-app-service-works"></a>Az App Service működése
Az Azure App Service felhőszolgáltatás, amely a mérnökök számára napi kihívást jelentő gyakorlati problémákat hivatott megoldani.
Az App Service rendkívül hatékony fejlesztést tesz lehetővé anélkül, hogy ehhez kompromisszumokat kellene kötni az alkalmazások felhőhöz méretezett szolgáltatása terén.

Az App Service emellett biztosítja a vállalati szintű üzletági alkalmazások létrehozásához szükséges szolgáltatásokat és kereteket, miközben a legnépszerűbb fejlesztési nyelvekkel (például Microsoft, .NET, Java, PHP, Node.js és Python) támogatja a fejlesztőket.
Az App Service a következő lehetőségeket kínálja a fejlesztőknek:

* Kiválóan méretezhető webalkalmazások létrehozása.
* A Mobile Apps könnyen kezelhető mobileszközös lehetőségek (adatkezelési háttéralkalmazások, felhasználói hitelesítés és leküldéses értesítések) egész tárházát biztosítja.
* API-k végrehajtása, üzembe helyezése és közzététele az API Apps szolgáltatással.
* Üzleti alkalmazások összekapcsolása munkafolyamatokban és az adatok átalakítása a Logic Apps segítségével.

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

Mindegyik alkalmazástípus alapja a méretezhető és rugalmas Web Apps-platform, amely a fejlesztők számára a tervezésétől kezdve a karbantartásáig optimizált, teljes körű életciklus-kezelést biztosít. Az életciklus-kezelési funkciók a következőket teszik lehetővé:

* **Alkalmazások gyors létrehozása**. Kezdje nulláról, vagy válasszon egy operatív támogatási rendszer (OSS) csomagot az Azure Marketplace-ről.
* **Folyamatos üzembe helyezés**. Népszerű verziókövetési megoldásokból (például a TFS-ből, a GitHubról és a BitBucketből) származó új kód automatikus üzembe helyezése és online társzolgáltatásokban (például a OneDrive-ban vagy a DropBoxban) tárolt tartalom szinkronizálása.
* **Tesztelés élesben**. Zökkenőmentes éles üzem előtti környezetek létrehozása és a feléjük irányuló forgalom mennyiségének kezelése. Szükség szerint felhőbeli hibakeresést indíthat, és problémák észlelése esetén visszaállítást végezhet.
* **Aszinkron és kötegelt feladatok futtatása**. Kód futtatása háttérfolyamatként, vagy a kód aktiválása meghatározott események bekövetkezésekor (például az Azure Storage-üzenetsorokra érkező üzenetek esetén) és ütemezett időpontokban (CRON).
* **Az alkalmazás méretezése**. Számos lehetőség áll rendelkezésre a szolgáltatások adatforgalom és erőforrás-használat függvényében való, automatikus horizontális és vertikális méretezésére. Az alkalmazásokhoz igazított privát környezet kialakítása.   
* **Az alkalmazás karbantartása**. Aknázza ki a hibakeresési és diagnosztikai szolgáltatásokban rejlő számos lehetőséget a problémák megelőzésére és hatékony kezelésére – akár valós időben (például az automatikus javítási és éles hibakeresési funkcióval), akár utólag, a naplók és a memóriaképek elemzésével.

Az App Service tehát lehetővé teszi a fejlesztőknek, hogy a kódra összpontosítsanak, és gyorsan eljussanak a stabil, rugalmasan méretezhető fejlesztési állapotig. Az API Apps- és Logic Apps-szolgáltatások lehetővé teszik, hogy a fejlesztők tényleges vállalati alkalmazásokat tervezzenek, amelyekkel áthidalhatók az üzleti megoldások közötti akadályok, valamint a helyszíni környezet és a felhő integrálásának nehézségei.  

[!INCLUDE [app-service-blueprint-how-app-service-works](../../includes/app-service-blueprint-how-app-service-works.md)]




<!--HONumber=Nov16_HO2-->


