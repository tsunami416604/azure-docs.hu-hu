---
title: 2.6 .NET-hez készült Azure SDK kibocsátási megjegyzései
description: 2.6 .NET-hez készült Azure SDK kibocsátási megjegyzései
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: dfc1fe223dbff178c35a969e0273ed80fb4c8be9
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016426"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>2.6 .NET-hez készült Azure SDK kibocsátási megjegyzései
Ez a dokumentum az Azure SDK for .NET 2.6-os kiadás tartalmazza a kibocsátási megjegyzéseket. 

Az Azure SDK 2.6-os fejleszthet a .NET 4.5.2-es vagy a .NET 4.6 célzó, feltéve, hogy manuálisan telepíti a .NET-keretrendszer célja a Felhőszolgáltatási szerepkör a felhőbeli szolgáltatásalkalmazások (PaaS). Lásd: [a .NET telepítése egy Cloud Service szerepkör](https://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Service Bus-frissítések
* Az Event Hubs: 
  
  * Most már lehetővé teszi célzott hozzáférés-vezérlés, amikor események küldése az Event Hubs teszi elérhetővé a további közzétevő végpont.
  * Stabilitását és az Event Hubs szolgáltatás hozzá fokozása.
  * Vegye fel az Amqp protokoll támogatása websocketen-üzenetkezelés és az Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>HDInsight Tools for Visual Studio-frissítések
* **Az IntelliSense a fejlesztés**: metaadatok távoli javaslat
  
    HDInsight Tools for Visual Studio mostantól támogatja a metaadatok távoli első, a Hive parancsfájl szerkesztésekor. Beírhatja például **kiválasztása * FROM** és a táblanevek jelennek meg. Az oszlopok neveit is, megjelenik egy tábla megadása után.
* **HDInsight emulator támogatása**
  
    Most már HDInsight Tools for Visual Studio támogatja a HDInsight emulator csatlakozik, így a Hive-szkriptek helyi sikerült fejlesztés bármilyen költséggel, anélkül, majd hajtsa végre ezeket a szkripteket, szemben a HDInsight-fürtök. 
  
    További információkért tekintse meg [a jelen](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **HDInsight Tools for Visual Studio támogatja az általános Hadoop-fürtjeinek** (előzetes verzió)
  
    HDInsight Tools for Visual Studio támogassák az általános Hadoop-fürtök, így a HDInsight Tools for Visual Studio segítségével a következőket:
  
  * Csatlakozás a fürthöz 
  * Az IntelliSense/automatikus kiegészítését továbbfejlesztett támogatás, a Hive-lekérdezés írása 
  * egy intuitív felhasználói felületen keresztül a fürt összes feladat megtekintése. 
    
    További információkért tekintse meg [a jelen](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>A szerepköralapú gyorsítótár-frissítések
* **A szerepköralapú gyorsítótár** használandó frissült **a Microsoft Azure Storage SDK** 4.3-as verzióját. Eddig a **a szerepköralapú gyorsítótár** használta a szolgáltatást az Azure Storage SDK 1.7-es verziója.
  
    Ügyfelek használata az Azure SDK 2.5-ös vagy az alábbiakban kell frissíteni az Azure SDK 2.6-os és áthelyezése az Azure Storage SDK új verziójával. 
  
    Jelenleg az Azure Storage 2011-08-18-as verzió el kell távolítani a 2016. augusztus 1-én van ütemezve. A szerepköralapú gyorsítótár az Azure SDK 2.5-ös vagy az alatti bármely áttelepítések 2.6 kell lennie teljes az időpontig. A használatból való kivonást egyaránt az Azure Storage 2011-08-18-as verzió további információkért lásd: [a Microsoft Azure Storage szolgáltatás verziója eltávolítása frissítés: 2016-ra való kiterjesztés](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> A 2016. November 30 használatból való kivonást egyaránt az Azure Managed Cache Service és a szerepköralapú gyorsítótár adunk. Azt javasoljuk, hogy telepít át az Azure Cache redis a kivonás előkészítésekor. Dátum- és migrálási útmutató segítséget nyújt a további információkért lásd: [melyik Azure gyorsítótárat használjam a megfelelő a számomra?](../azure-cache-for-redis/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Az Azure App Service-eszközöket
A következő elemek frissítése az Azure SDK 2.6-os kiadásában.

* A továbbfejlesztett közé tartozik az Azure API Apps telepítési cél Azure-beli közzétételt.
* Az API Apps funkció kiépítés engedélyezése a felhasználók az API-alkalmazás létrehozása és üzembe helyezését funkciókkal.
* Server Explorer új App Service-csomópont, megfelelően az erőforráscsoport szerint csoportosított webes, mobilos és API apps módosítani.
* Adja hozzá a legtöbb hozzáadva az Azure API App Client kézmozdulat C# projektek, amely lehetővé teszi a Swagger-kompatibilis API-alkalmazásokat a felhasználó az Azure-előfizetés automatikus előállításához.
* Az API Apps azokat az eszközöket és az App Service-csomópontok a Server Explorerben érhető el a Visual Studio 2013 csak. 

## <a name="azure-resource-manager-tools-updates"></a>Azure Resource Manager Tools-frissítések
Az Azure resource manager tools frissítve lett-e, ha hozzá szeretné adni sablonok virtuális gépekhez, hálózati és tárolási. Sablonok és szerkesztheti a sablonok JSON-kódtöredékek használata lehetővé teszi új vázlat nézet tartalmazza a szerkesztési lehetőséget JSON frissítve lett. A Visual Studióból üzembe helyezett sablonokat, amelyekből a projekttel végzett módosítások a parancsfájlt a Visual Studio által használható egy PowerShell-parancsfájlt használja.

## <a name="diagnostics-improvements-for-cloud-services"></a>A Felhőszolgáltatások diagnosztikai fejlesztései
Az Azure SDK 2.6-os vonja vissza az Azure compute emulator a diagnosztikai naplók gyűjtésére, és mentse őket fejlesztési tárterülettel támogatása. Bármely diagnosztikai naplók (beleértve az alkalmazás nyomkövetési naplókat, esemény-nyomkövetés (ETW) Windows-naplók, teljesítményszámlálók, infrastruktúra naplók és a windows-eseménynaplók) jön létre az alkalmazás futtatásakor az emulátorban át lehet vinni fejlesztési tárterülettel Ellenőrizze, hogy működik-e a diagnosztikai naplózás a helyi gépen. 

A diagnosztikai tárfiók most már a szolgáltatás konfigurációs (.cscfg) fájl, megkönnyítve a különböző környezetek különböző diagnosztikai tárfiók használata az adható meg. Nincsenek hogyan a kapcsolati karakterlánc működött az Azure SDK 2.4 és Azure SDK 2.6-os közötti fontosabb különbségeket. További információ a diagnosztikai Tárfiók kapcsolati karakterláncának használata, és milyen hatással van a projektek: [diagnosztika beállítása az Azure Cloud Services](https://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Kompatibilitástörő változások
### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-eszközök
* A **felhőalapú üzembe helyezés projektek** projekt típusa érhető el az Azure SDK 2.5-ös kapott **Azure-erőforráscsoport**.
* **A felhő telepítési projekt** 2.6-projektek az Azure SDK 2.5-ös létrehozott típus használható, de a sablont a Visual Studio telepítése sikertelen lesz. Azonban a PowerShell-parancsprogram üzembe helyezésével továbbra is működnek, mint korábban.  Információk a használatát **felhőalapú üzembe helyezés projektek** 2.6, olvassa el ezt a [közzététele](https://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Ismert problémák
* Egy 64 bites operációs rendszer az emulátorban diagnosztikai naplók gyűjtésére van szükség. Ha 32 bites operációs rendszeren futtatja, a diagnosztikai naplók nem lesznek összegyűjtve. Ez nem befolyásolja a bármely más emulátor funkciója. 
* Az Azure SDK 2.6-os 4/29/2015-én kiadott kellett két problémák: 
  
  * Univerzális alkalmazás nem sikerült betölteni a Visual Studio 2015, Azure SDK 2.6-os lett telepítve a gépen.
  * A Felhőszolgáltatás-projekt hibakeresés a Visual Studio 2013 és a Visual Studio 2015, Visual Studio hosszabb ideig nem válaszol, és összeomlik, a következő üzenettel: "Configuring diagnosztikai Emulator" párbeszédpanel megjelenítése során sikertelen lesz.
    
    5/18/2015, Azure SDK 2.6-os frissítés jelent meg. A frissített verziója 2.6.30508.1601; a fent leírt két hibák javításait tartalmazza. Azonosíthatja a build, az SDK-t, a Vezérlőpult -> Programok és szolgáltatások -> Nástroje Microsoft Azure Pro Microsoft Visual Studio 2013 – v 2.6-os. A verzió oszlopban a buildszáma.
    
    Ha továbbra is a fenti problémák, telepítse a legújabb verziót az SDK 2.6-os Azure [VS 2012](https://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](https://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) vagy [VS 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Lásd még:
[Támogatási és kivezetési információkat az Azure SDK for .NET és az API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

