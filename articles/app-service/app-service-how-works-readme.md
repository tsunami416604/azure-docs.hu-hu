---
title: "Az Azure App Service működése"
description: "Ismerje meg az App Service működését"
keywords: "app service, azure app service, méret, méretezhető, app service-csomag, app service ára"
services: app-service
documentationcenter: 
author: yochay
manager: erikre
editor: 
ms.assetid: ae74fc32-969e-4580-8d61-02c922f1f184
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/23/2017
ms.author: yochayk
translationtype: Human Translation
ms.sourcegitcommit: edb3325414adf876548181243ddfa2d515aeb0b8
ms.openlocfilehash: 2d830963d3d2adba71a6ca99f79eac0fc8cbfb12
ms.lasthandoff: 02/24/2017


---
# <a name="how-app-service-works"></a>Az App Service működése
Az Azure App Service felhőszolgáltatás, amely a mérnökök számára napi kihívást jelentő gyakorlati problémákat hivatott megoldani.
Az App Service rendkívül hatékony fejlesztést tesz lehetővé anélkül, hogy ehhez kompromisszumokat kellene kötni az alkalmazások felhőhöz méretezett szolgáltatása terén. 

Az App Service emellett biztosítja a vállalati szintű üzletági alkalmazások létrehozásához szükséges szolgáltatásokat és kereteket. Az App Service-szel a legnépszerűbb fejlesztési nyelvekkel fejleszthet alkalmazásokat, beleértve a Java, PHP, Node.js, Python, és a Microsoft .NET nyelveket. Az App Service-szel a következőket teheti:

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
* **Tesztelés élesben**. Zökkenőmentes éles üzem előtti környezetek létrehozása és a feléjük irányuló forgalom mennyiségének kezelése. Szükség esetén felhőbeli hibakeresést indíthat, és problémák észlelése esetén visszaállítást végezhet.
* **Aszinkron és kötegelt feladatok futtatása**. Kód futtatása háttérfolyamatként, vagy a kód aktiválása meghatározott események bekövetkezésekor (például az Azure Storage-üzenetsorokra érkező üzenetek esetén) és ütemezett időpontokban (CRON).
* **Az alkalmazás méretezése**. Számos lehetőség áll rendelkezésre a szolgáltatások adatforgalom és erőforrás-használat függvényében való, automatikus horizontális és vertikális méretezésére. Az alkalmazásokhoz igazított privát környezet kialakítása.   
* **Az alkalmazás karbantartása**. Aknázza ki a hibakeresési és diagnosztikai szolgáltatásokban rejlő számos lehetőséget a problémák megelőzésére és hatékony kezelésére – akár valós időben (például az automatikus javítási és éles hibakeresési funkcióval), akár utólag, a naplók és a memóriaképek elemzésével.

Összességében az App Service tehát lehetővé teszi a fejlesztőknek, hogy a kódra összpontosítsanak, és gyorsan eljussanak a stabil és rugalmasan skálázható fejlesztési állapotig. Az API Apps- és Logic Apps-szolgáltatások lehetővé teszik, hogy a fejlesztők tényleges vállalati alkalmazásokat tervezzenek, amelyekkel áthidalhatók az üzleti megoldások közötti akadályok, valamint a helyszíni környezet és a felhő integrálásának nehézségei. 

## <a name="videos"></a>Videók
* [Azure App Service-architektúra](https://azure.microsoft.com/documentation/videos/why-azure-web-sites-plus-architecture/)

## <a name="next-steps"></a>Következő lépések

Az App Service-ről a következő témakörökben talál további információt:

* [Az Azure App Service-ről](app-service-value-prop-what-is.md)
  * [Webalkalmazás](../app-service-web/app-service-web-overview.md)
  * [Mobilalkalmazás](../app-service-mobile/app-service-mobile-value-prop.md)
  * [API-alkalmazás](../app-service-api/app-service-api-apps-why-best-platform.md)
* [Azure App Service-architektúra (bemutató)](http://www.slideshare.net/maartenba/windows-azure-web-sites-things-they-dont-teach-kids-in-school-comunity-day-2013)
* [Az Azure App Service, a Cloud Services és a Virtual Machines összehasonlítása](../app-service-web/choose-web-site-cloud-service-vm.md)
* [Az App Service-csomagok ismertetése](azure-web-sites-web-hosting-plans-in-depth-overview.md)
* [Az App Service Environment bemutatása](../app-service-web/app-service-app-service-environment-intro.md)
  * [Gyakorlat: App Service Environment létrehozása](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Azure App Service fejlesztői vermek támogatása](https://azure.microsoft.com/blog/windows-azure-websites-development-stacks-support/)




