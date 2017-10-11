---
title: "Az Azure SDK for .NET 2.6 kibocsátási megjegyzések"
description: "Az Azure SDK for .NET 2.6 kibocsátási megjegyzések"
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Az Azure SDK for .NET 2.6 kibocsátási megjegyzések
Ez a dokumentum az Azure SDK for .NET 2.6 kiadás tartalmazza a kibocsátási megjegyzéseket. 

Az Azure SDK 2.6 fejleszthet felhőalapú szolgáltatás alkalmazások (PaaS) célcsoport-kezelési .NET 4.5.2 vagy .NET 4.6 feltéve, hogy a felhő szerepkör-szolgáltatás a .NET-keretrendszer célzott manuálisan telepítenie. Lásd: [.NET telepíthető egy felhőalapú szolgáltatás szerepkör](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>A Service Bus frissítések
* Az Event Hubs: 
  
  * Most már lehetővé teszi, hogy célzott hozzáférés-vezérlés jelentkezik, mintha a további publisher végpont az Event Hubs számára események küldésekor.
  * Stabilitását és fokozása Event Hubs szolgáltatás hozzá.
  * Üzenetküldési felvételekor WebSocket keresztül Amqp protokoll támogatása és az Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>A HDInsight Tools for Visual Studio frissítések
* **IntelliSense a fejlesztés**: távoli metaadatok javaslat
  
    A HDInsight Tools for Visual Studio támogatja távoli a metaadatokat a Hive parancsfájl szerkesztésekor. Beírhatja például **válasszon * FROM** és megjelenik-e a táblanevek. Is az oszlopnevek jelenik meg a tábla megadása után.
* **HDInsight emulátor támogatása**
  
    Most már a HDInsight Tools for Visual Studio támogatja a HDInsight emulátoron csatlakozik, így vezet be, semmilyen költség nélkül helyileg összeállíthatja a Hive parancsfájlokat, majd hajtható végre ezeket a HDInsight-fürtök parancsprogramok. 
  
    További információkért tekintse meg [a manuális](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **A HDInsight Tools for Visual Studio támogatja az általános Hadoop-fürtök** (előzetes verzió)
  
    A HDInsight Tools for Visual Studio támogassák az általános Hadoop-fürtök, hogy használhassa a HDInsight Tools for Visual Studio, a következőket teheti:
  
  * Csatlakozzon a fürthöz 
  * továbbfejlesztett IntelliSense/automatikus-végrehajtási-támogatással rendelkező Hive-lekérdezések írása 
  * a feladatok megtekintéséhez a fürt egy egyszerűen elsajátítható felhasználói felületén. 
    
    További információkért tekintse meg [a manuális](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Szerepköralapú gyorsítótár-frissítéseinek
* **Szerepköralapú gyorsítótár** használandó frissült **Microsoft Azure Storage szolgáltatás SDK** 4.3 verziója. Eddig a **szerepköralapú gyorsítótár** használta az Azure Storage szolgáltatás SDK 1.7-es verziójának.
  
    Az ügyfelek használata az Azure SDK 2.5-ös vagy az alábbiakban kell frissítése a Azure SDK 2.6 és áthelyezése az Azure Storage SDK új verziójával. 
  
    Jelenleg az Azure Storage 2011-08-18-as verzió 2016 augusztusától 1 eltávolításra van ütemezve. Bármely áttelepítések szerepköralapú gyorsítótár az Azure SDK 2.5-ös vagy az alatti 2.6 csak teljes időpontig. Az Azure Storage 2011-08-18-as verzió a használatból való kivonást további információkért lásd: [Microsoft Azure Storage szolgáltatás eltávolítása Verziófrissítés: 2016 bővítmény](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Az Azure Managed Cache Service és az Azure szerepköralapú gyorsítótár 30 2016 novemberétől kezdve, használatból való kivonást azt még bejelentése. Azt javasoljuk, hogy át Azure Redis Cache a használatból való kivonást előkészítésekor. A dátumok és áttelepítési útmutató további információkért lásd: [amely Azure Cache-ajánlatot megfelelő a számomra?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Az Azure App Service-eszközök
A következő elemek frissítve lett az Azure SDK 2.6 kiadásban.

* Az Azure közzétételi megnövekedett, hogy tartalmazzák az Azure API-alkalmazások központi telepítés céljaként.
* API-alkalmazások kiépítés funkció engedélyezése a felhasználók az API-alkalmazás létrehozása és üzembe helyezési funkciókkal.
* Server Explorer megfelelően új App Service-csomópont, a webes, mobil és API apps erőforráscsoport szerint csoportosítva módosítani.
* Adja hozzá az Azure API App ügyfél kézmozdulat hozzáadni a legtöbb C# projekt, amely lehetővé teszi a Swagger-kompatibilis API-alkalmazások futtatása a felhasználó Azure-előfizetés automatikus létrehozása.
* API-alkalmazások tooling és a Server Explorer App Service-csomópontok érhetők el a Visual Studio 2013 csak. 

## <a name="azure-resource-manager-tools-updates"></a>Frissíti az Azure erőforrás-kezelő eszközei
Az Azure erőforrás-kezelő eszközei közé tartoznak a sablonok a virtuális gépek, hálózati és tárolási frissített. A szerkesztési funkciót JSON sablonok és a sablonoknak JSON kódtöredékek szerkesztése új vázlat nézet tartalmazza frissítve lett. A Visual Studio telepített sablonok megadott a projekthez, milyen módosítások történtek a parancsfájlt a Visual Studio által használt PowerShell parancsfájl használata.

## <a name="diagnostics-improvements-for-cloud-services"></a>Cloud Services diagnosztika érintő fejlesztések
Az Azure SDK 2.6 megteremti vissza az Azure compute emulator a diagnosztikai naplók gyűjtésére, és mentse őket fejlesztési tárolási támogatása. A diagnosztikai naplók (beleértve az alkalmazás nyomkövetési naplókat, esemény-nyomkövetés (ETW) Windows-naplók, a teljesítményszámlálók, az infrastruktúra naplók és a windows eseménynaplóit) létre, amikor az alkalmazást az emulátorban fut-e át lehet vinni fejlesztési tárolás ellenőrizze, hogy a diagnosztikai naplózás működik-e a helyi számítógépen. 

A diagnosztikai tárfiók most már megadható a szolgáltatás konfigurációs (.cscfg) fájljában különböző diagnosztika storage-fiókok használatának különböző környezetek megkönnyítése. Bizonyos különbségek vannak a figyelmet a jelentősebb között hogyan Azure SDK 2.4 és az Azure SDK 2.6 működtek-e a kapcsolati karakterláncban. További információ a diagnosztika tárolási kapcsolati karakterlánc használatával, és milyen hatással van a projektek: [diagnosztika konfigurálása az Azure-szolgáltatásokhoz](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Módosítások megszakítása
### <a name="azure-resource-manager-tools"></a>Az Azure erőforrás-kezelő eszközei
* A **felhő telepítési projekt** érhető el az Azure SDK 2.5 projekttípus át lett nevezve a **Azure erőforráscsoport**.
* **A felhő telepítési projekt** projektek az Azure SDK 2.5 típusú 2.6 is használható, de a sablon a Visual Studio telepítése sikertelen lesz. Azonban a PowerShell parancsfájl központi telepítése továbbra is működni fog, ahogy azt korábban.  Használatáról információt **felhő telepítési projekt** 2.6, olvassa el ezt a [utáni](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Ismert problémák
* Az emulátorban diagnosztikai naplók gyűjtésére csak 64 bites operációs rendszer. Ha egy 32 bites operációs rendszeren futtatja, a diagnosztikai naplók nem lesznek összegyűjtve. Ez nem befolyásolja a más emulátor funkciók. 
* 4/29/2015-én kiadott Azure SDK 2.6 kellett két problémákat: 
  
  * Univerzális alkalmazást nem sikerült betölteni a Visual Studio 2015, amikor Azure SDK 2.6 lett telepítve a számítógépen.
  * Egy Felhőszolgáltatás-projekt hibakeresési sikertelen lesz a Visual Studio 2013 és a Visual Studio 2015, ahol a Visual Studio nem válaszol, és összeomlik "Configuring diagnosztika Emulator" üzenettel párbeszédpanel megjelenítése során.
    
    Azure SDK 2.6 frissítése 5/18/2015 lett szabadítva. A frissített verziója 2.6.30508.1601; a fent leírt két probléma javítását tartalmaz. Azonosíthatja, hogy a build a Vezérlőpultról az SDK -> Programok és szolgáltatások -> Microsoft Azure-eszközök a Microsoft Visual Studio 2013 – v 2.6. A verzió oszlopban jelenik meg a buildszáma.
    
    Ha a fenti problémák továbbra is küzdenek, telepítse a legújabb verziót 2.6 készült Azure SDK [Visual STUDIO 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) vagy [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Lásd még:
[Támogatás és használatból való kivonást információk az Azure SDK-t a .NET és API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

