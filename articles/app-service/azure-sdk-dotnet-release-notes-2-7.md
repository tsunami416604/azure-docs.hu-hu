---
title: Az Azure SDK for .NET 2.7 és .NET 2.7.1-es kibocsátási megjegyzések
description: Az Azure SDK for .NET 2.7 és .NET 2.7.1-es kibocsátási megjegyzések
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836720"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Az Azure SDK for .NET 2.7 és .NET 2.7.1-es kibocsátási megjegyzések
## <a name="overview"></a>Áttekintés
Ez a dokumentum az Azure SDK for .NET 2.7 kiadás tartalmazza a kibocsátási megjegyzéseket. 

A dokumentum vonatkozó .NET 2.7.1-es is tartalmaz a kibocsátási megjegyzések az Azure SDK-ban.

Az Azure SDK 2.7 csak akkor támogatott a Visual Studio 2015-öt és a Visual Studio 2013. [Az Azure SDK 2.6](https://azure.microsoft.com/downloads/) , mert a legutóbbi támogatott SDK a Visual Studio 2012.

Ebben a kiadásban kapcsolatos részletes információkért lásd: [Azure SDK 2.7 közlemény post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) és [Azure SDK 2.7.1-es közlemény post](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Jelentkezzen be a Visual Studio 2015 fejlesztései
A Visual Studio 2015 Azure SDK-2.7 az új identitáskezelési funkciókat támogatja a Visual Studio 2015-öt.  Ez magában foglalja a fiókokhoz való hozzáférés az Azure szerepköralapú hozzáférés-vezérlés, a felhőalapú megoldás szolgáltatókat, a DreamSpark és a fiókja és -előfizetése egyéb keresztül támogatása.

Az Azure SDK 2.7 mellékelt bejelentkezési fejlesztések a Visual Studio 2015 csak érhetők el. Azure SDK 2.7.1-es támogatja a Visual Studio 2013 része.

### <a name="mobile-sdk"></a>Mobil SDK
Frissített **Mobile Apps** megfelelően legújabb sablonok [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) és telepítési folyamata.

### <a name="service-bus"></a>Service Bus
Általános hibajavításokat és fejlesztéseket. A részletek a frissítésekkel és szolgáltatásokkal, tekintse meg a kibocsátási megjegyzések a legutóbbi [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>A HDInsight Tools
Ebben a kiadásban a következő frissítés megtörtént. Ezek a frissítések még csak előzetes verziójúak. További információkért lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=619108).

* Hive diagramok a Hive on Tez feladatokhoz
* A DML-IntelliSense Hive teljes körű támogatása
* A Pig sablon támogatása
* A Storm sablonok az Azure-szolgáltatások

#### <a name="breaking-changes"></a>Módosítások megszakítása
* Régi **Storm** projekt frissíteni kell az eszközök ezen verziója használatakor. További információkért lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=619108).
* A Visual Studio Web Express már nem támogatott. További információkért lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Az Azure App Service-eszközök
Ebben a kiadásban a következő frissítéseket Web eszközök Extensions történtek. További információ: [ez](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

* DreamSpark-fiókok hozzáadott támogatása
* Teljes módosítás Azure eszközök támogatására, az új Azure Resource felügyeleti API-k
* Az Azure App Service-támogatja az [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Ismert problémák
Webes alkalmazás központi telepítési tárhely csomópontot nem a Server Explorer üzembe helyezési ponti csomópontjában jelennek meg, és a webes alkalmazás központi telepítési tárolóhely gyermekcsomópontok nincs betöltés Cloud Explorer alatt. A probléma meg lett oldva és előkészített a következő SDK-verzióban. 

### <a name="cloud_explorer"></a>A Visual Studio 2015 cloud Explorer
Az Azure SDK 2.7 Cloud Explorer Visual Studio 2015, amely lehetővé teszi az Azure-erőforrások megtekintése, vizsgálja meg a tulajdonságaikról és műveleteket kulcs fejlesztők a Visual Studio magában foglalja. 

Cloud explorer támogatja a következőket:

* Az Azure-erőforrások erőforráscsoport és az erőforrástípus nézetei 
* Erőforrások keresése név (erőforrástípus nézetben érhető el) alapján
* Az előfizetések és -erőforrásokra, amelyekre a szerepköralapú hozzáférés vezérlés (RBAC) alkalmazott támogatása 
* A kijelölt erőforrások integrált műveletpulton megjelenítheti az adott műveletek fejlesztői kialakításával foglalkozik. Például: távoli hibakereső csatlakoztatása a létrehozott virtuális gépeket a Resource Manager-készletben használatával szeretné megtekinteni a diagnosztikai adatok virtuális gépek stb.
* Integrált Tulajdonságok panel fejlesztés/tesztelés során általában szükség fejlesztői tárgyalt tulajdonságai ablakban 
* Gyors váltás (használja a beállítások parancsot eszköztár) erőforrások számbavétele során használandó fiók 
* (Használja a beállítások parancsot eszköztár) erőforrások számbavétele során használandó előfizetések szűrése 
* Mélyhivatkozások az erőforrások és csoportok kezelését az Azure portálon 

### <a name="azure-resource-manager-tools"></a>Az Azure erőforrás-kezelő eszközei
Az Azure erőforrás-kezelő eszközei szerepköralapú hozzáférés vezérlés (RBAC) és az új előfizetés típusú frissítve lett.  Ezekkel a módosításokkal tartalmazza azt a képességet használata új tárfiókok mellett hagyományos tárolási összetevőinek tárolásához üzembe helyezése során.  

Egy Azure erőforráscsoport-projekt egy korábbi verziójáról, az SDK és az SDK 2.7 használata, új központi telepítési parancsfájl központi telepítése egy új tárfiókot helyett hagyományos tárolási van szükség.  Mielőtt módosításai a projekthez adja hozzá az új parancsfájl kéri.  A régi parancsfájl kap, és manuálisan bármilyen módosítást az új parancsfájl kell.

### <a name="storage-explorer-tools"></a>Tárolási Explorer Eszközök
* Támogatás a hozzáfűző BLOB megtekintése. További információ a [ebben a blogbejegyzésben](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Prémium szintű Storage-fiókok a Server Explorer megtekintése támogatása. Mivel ezek az egyetlen támogatott típus a prémium tárfiókokhoz Server Explorer csak jelenik meg prémium szintű storage-fiókok lapblobokat.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Az Azure Data Factory Tools for Visual Studio
Introducing **az Azure Data Factory eszközök** Visual Studio. Az alábbiakban megtalálja az engedélyezett szolgáltatásokat. Lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=617530) további információt.

* **Sablon alapú szerzői**: használata cased válassza sablonok, adatok mozgása sablonok vagy az adatfeldolgozás sablonok az adatok végpontok közötti integráció megoldás üzembe helyezéséhez és gyakorlati gyorsan az adat-előállító első lépések alapján. 
* **Integráció a Solution Explorer szerzői és központi telepítése a Data Factory entitások**: központi telepítése, folyamatok és a kapcsolódó entitásokból, mint a Visual Studio-projektek & létrehozása. 
* **Integráció a Diagram nézet visual kapcsolati tevékenység készítése során**: vizuálisan létre adatcsatornák és adatkészletek diagramnézetében támogatással. 
* **Integráció a Server explorer keresse meg és a már telepített entitások való együttműködéshez**: a Server Explorer már üzembe van helyezve Tallózás adat-előállítók és a kapcsolódó entitás használja. Egy telepített adat-előállító vagy egyetlen entitás (sorban, a társított szolgáltatás, adatkészleteket) importálnia kell a projekthez. 
* **JSON Szerkesztés a séma érvényesítése és a gazdag intellisense**: hatékonyan konfigurálása, és az adat-előállító entitások gazdag intellisense és a séma érvényesítése a JSON-dokumentumok szerkesztése 
* **Több környezet közzététel**: közzététele hozhatóak létre adatcsatornák fejlesztői, tesztelési vagy termék környezet minden környezet esetében külön konfigurációs fájlok létrehozásával.
* **A Pig, a Hive és a .net-alapú adatfeldolgozási támogatási**: támogatja a Pig és a Hive parancsfájlok Data Factory-projektben. C#-projektet hivatkozó .net kezelésére szolgáló támogatása tevékenység.

## <a name="azure-sdk-for-net-271"></a>Azure SDK a .NET 2.7.1-es verziójához
A következő szakasz tartalmazza az Azure SDK-val vonatkozó .NET 2.7.1-es bevezetett frissítések.

### <a name="hdinsight-tools"></a>A HDInsight Tools
Részletesebb tájékoztatás a HDInsight eszközök frissítések, lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=623831).

* Hive feladat operátor nézet (egy új szolgáltatás)
  
    Megismerheti a Hive-lekérdezést, a Hive operátor nézet szolgáltatás hozzá lett adva. Csúcspont összes operátort megtekintéséhez kattintson duplán arra a feladat ábra csúcspont. Megtekintheti annak további részleteit egy adott operátor, mozgassa az egérmutatót adott operátor fölé.
* Hive hiba jelölő (egy új szolgáltatás)
  
    Ahhoz, hogy meg a nyelvtani hibákat azonnal, a Hive hiba jelölő szolgáltatás hozzá lett adva. Ezenkívül hibaüzenetek volt fokozott, és azonnal részletes nyelvtani hibákat most már megtekintheti (ebben a kiadásban, amíg nem volt szükség elküldeni a Hive parancsfájl a fürthöz, és várjon egy kis ideig, a hibaüzenet részleteit elérése előtt).  
* Storm-topológia Graph (egy új szolgáltatás)
  
    Megjelenítése akkor nagyon fontos, ha meg szeretné tekinteni, ha a topológia a várt módon működik. Ebben a kiadásban hozzáadott képi megjelenítés a Storm diagramjait. A topológia jelenítheti meg a fontos metrikák (például egy színt azt jelzi, időjárási egy bizonyos Bolt "foglalt" vagy nem). Duplán kattintva is megtekintheti annak további részleteit Bolt/Spout.
* Az Azure portálon (hibajavítás) létrehozott HDInsight-fürtök támogatása
  
    Használhatja a Visual Studio megtekintéséhez és feladatok terjeszt a HDInsight-fürtök esetében nem számít, ahol a fürt lettek létrehozva.
* További támogatási IntelliSense & gyorsabban Hive metaadatok betöltése (javítása)
  
    Továbbfejlesztettük az IntelliSense további felhasználóbarát javaslatok hozzáadásával. Például tábla alias is most is a javasolt az IntelliSense így könnyebben írhat a lekérdezést. Emellett továbbfejlesztettük a Hive-metaadatok betöltése, így csak néhány másodpercig kilistázhatja az összes adatbázisok, táblák és a Hive metaadattárhoz oszlopait.

Részletesebb tájékoztatás a HDInsight eszközök frissítések, lásd: [ebben a blogban](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>A Visual Studio 2013 fejlesztései
* Az Azure SDK 2.7.1-es lehetővé teszi, hogy a Visual Studio 2013 Azure fiókjaihoz és előfizetéseihez szerepköralapú hozzáférés-vezérlés, a megoldás szolgáltatók és a Dreamspark eléréséhez.
* Az Azure SDK-val 2.7.1-es az új Cloud Explorer eszköz ablak már is elérhető a Visual Studio 2013.

### <a name="known-issues"></a>Ismert problémák
Az Azure SDK 2.6 vagy telepítése 2.7.1-es a Visual Studio Community 2013 a egy nem - angol nyelvű operációs rendszer, hogy a lehet, hogy egyeznek-e az angol és a nem angol nyelvű erőforrások a Visual Studio figyelmeztetés jelenik meg. Ez a figyelmeztetés lehet, hogy el biztonságosan. Akkor történik, ha a gép nem tartalmazta a számítógépen megtalálható a Visual Studio Community 2013, és telepíti az SDK-t a egy nem - angol nyelvű operációs rendszer. A figyelmeztetés jelenik meg a nyelvi csomag a RTM erőforrások vonatkozik a Visual Studio, de előtt Update 4 vonatkozik. A figyelmeztetés kikapcsolásakor lehetővé teszi a nyelvi csomag a folytatáshoz, majd végezze el a nyelvi csomag tartalmát verziójának Update 4 alkalmazása.

LightSwitch-projektek nincsenek compatibile ebben a kiadásban. A probléma fel lesz oldva, a következő SDK-verzióban.

## <a name="also-see"></a>Lásd még:
[Az Azure SDK 2.7.1-es közlemény post](http://go.microsoft.com/fwlink/?LinkId=623850)

[Az Azure SDK 2.7 közlemény post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Támogatás és használatból való kivonást információk az Azure SDK-t a .NET és API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

