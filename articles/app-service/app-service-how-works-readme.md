---
title: Az Azure App Service működése
description: Az App Service működésének áttekintése
keywords: app service, azure app service, méret, méretezhető, app service-csomag, app service ára
services: app-service
documentationcenter: ''
author: yochay
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2016
ms.author: yochay

---
# <a name="how-app-service-works"></a>Az App Service működése
Az Azure App Service egy felhőszolgáltatás, amely a mérnökök számára napi kihívást jelentő gyakorlati problémákat hivatott megoldani. Az App Service rendkívül hatékony fejlesztést tesz lehetővé anélkül, hogy ehhez kompromisszumokat kellene kötni az alkalmazások felhőhöz méretezett szolgáltatásában. Az App Service emellett biztosítja a vállalati szintű üzletági alkalmazások létrehozásához szükséges szolgáltatásokat és kereteket, miközben a legnépszerűbb fejlesztési nyelvekkel (.NET, Java, PHP, Node.JS és Python) támogatja a fejlesztőket.
Az App Service a következő lehetőségeket kínálja a fejlesztőknek:

* Kiválóan méretezhető webalkalmazások létrehozása
* A Mobile Apps könnyen kezelhető mobileszközös lehetőségek egész tárházát biztosítja, amelyekkel adatkezelési háttéralkalmazások, felhasználói hitelesítés és leküldéses értesítések segítségével készítheti el mobilalkalmazásainak háttéralkalmazásait. 
* API-k létrehozása, telepítése és közzététele az API Apps szolgáltatással.
* Üzleti alkalmazások összekapcsolása munkafolyamatokban és az adatok átalakítása a Logic Apps segítségével.

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

Mindegyik alkalmazástípus alapja a méretezhető és rugalmas webalkalmazásplatform, amely a fejlesztők számára a tervezésétől kezdve a karbantartásáig optimizált, teljes körű életciklus-kezelést biztosít. Az életciklus-képességek az alábbiakat teszik lehetővé:

* Alkalmazások gyors létrehozása – kezdje teljesen az elejéről, vagy válasszon OSS-csomagot az Azure Piactérről. 
* Folyamatos üzembe helyezés – népszerű verziókövetési megoldásokból (például a TFS-ből, a GitHubról és a BitBucketből) származó új kód automatikus üzembe helyezése és online társzolgáltatásokban (például a OneDrive-ban vagy a DropBoxban) tárolt tartalom szinkronizálása.
* Éles környezetben végzett tesztelés – üzem előtti környezetek egyszerű létrehozása és az azok felé irányuló adatforgalom felügyelete. Szükség esetén felhőbeli hibakeresést indíthat, és problémák észlelése esetén visszaállítást végezhet.
* Aszinkron és kötegelt feladatok futtatása – kód végrehajtása háttérfolyamatként, vagy a kód aktiválása meghatározott események bekövetkezésekor (például az Azure Storage-üzenetsorokra érkező üzenetek esetén) és ütemezett időpontokban (CRON).
* Alkalmazások skálázása – számos lehetőség áll rendelkezésre a szolgáltatások adatforgalom és erőforrás-használat függvényében való horizontális és vertikális skálázására. Az alkalmazásokhoz igazított privát környezet kialakítása   
* Alkalmazások karbantartása – aknázza ki a hibakeresési és diagnosztikai szolgáltatásokban rejlő számos lehetőséget a problémák megelőzésére és hatékony kezelésére, akár valós időben (például az automatikus javítási és éles hibakeresési funkcióval), akár utólag, a naplók és a memóriaképek elemzésével.

Az App Service tehát lehetővé teszi a fejlesztőknek, hogy a kódra összpontosítsanak, és gyorsan eljussanak a stabil, rugalmasan skálázható fejlesztési állapotig. Az API- és logikaialkalmazás-szolgáltatások lehetővé teszik, hogy a fejlesztők tényleges vállalati alkalmazásokat tervezzenek, amelyekkel áthidalhatók az üzleti megoldások közötti akadályok, valamint a helyszíni környezet és a felhő integrálásának nehézségei.  

[!INCLUDE [app-service-blueprint-how-app-service-works](../../includes/app-service-blueprint-how-app-service-works.md)]

<!--HONumber=Oct16_HO3-->


