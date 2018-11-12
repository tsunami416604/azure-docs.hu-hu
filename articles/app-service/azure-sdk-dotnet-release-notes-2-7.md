---
title: Azure SDK for .NET 2.7-es és a .NET 2.7.1-es verziójához kibocsátási megjegyzései
description: Azure SDK for .NET 2.7-es és a .NET 2.7.1-es verziójához kibocsátási megjegyzései
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
ms.openlocfilehash: 01a02296354ebe3d60f0e1fda6a6da8554a265fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239066"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK for .NET 2.7-es és a .NET 2.7.1-es verziójához kibocsátási megjegyzései
## <a name="overview"></a>Áttekintés
Ez a dokumentum az Azure SDK for .NET 2.7 kibocsátási a kibocsátási megjegyzéseket tartalmaz. 

A dokumentum is tartalmaznak a kibocsátási megjegyzések az Azure SDK for .NET 2.7.1-es verziójához kiadási.

Az Azure SDK 2.7 csak akkor támogatott a Visual Studio 2015-öt és a Visual Studio 2013-hoz. [Az Azure SDK 2.6-os](https://azure.microsoft.com/downloads/) , a legutóbbi támogatott SDK-t a Visual Studio 2012.

Ebben a kiadásban kapcsolatos részletes információkért lásd: [Azure SDK 2.7-es közlemény post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) és [Azure SDK 2.7.1-es verziójának közleményt post](https://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK for .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Jelentkezzen be a Visual Studio 2015 fejlesztései
A Visual Studio 2015 Azure SDK 2.7 az új identitáskezelési funkciókat támogatja a Visual Studio 2015-ben.  Ez magában foglalja a elérése az Azure szerepköralapú hozzáférés-vezérlés, a Cloud Solution Providers, a DreamSpark és a fiók és -előfizetés egyéb fiókok támogatása.

Jelentkezzen be fejlesztésekre az Azure SDK 2.7-es csak Visual Studio 2015-ben érhető el. Támogatás a Visual Studio 2013-hoz készült Azure SDK 2.7.1-es verziójához tartalmazza.

### <a name="mobile-sdk"></a>Mobile SDK
Frissített **Mobile Apps** , hogy a legújabb sablonokat [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) és telepítési folyamata.

### <a name="service-bus"></a>Service Bus
Általános hibajavításokat és fejlesztéseket. Frissítések és funkciók a részletekért tekintse meg a kibocsátási megjegyzéseket, a legutóbbi [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>HDInsight-eszközök
Ebben a kiadásban a következő frissítések történtek. Ezek a frissítések vannak előzetes verzióban érhető el. További információkért lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=619108).

* Hive-diagramok a Hive on Tez feladatokhoz
* Teljes körű Hive DML IntelliSense-támogatás
* A Pig sablon támogatása
* A Storm-sablonok az Azure-szolgáltatások

#### <a name="breaking-changes"></a>Kompatibilitástörő változások
* Régi **Storm** projekt frissíteni kell az eszközöket ezen verziója használatakor. További információkért lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=619108).
* A Visual Studio Web Express már nem támogatott. További információkért lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Az Azure App Service-eszközöket
Ebben a kiadásban a következő frissítések webes eszközök bővítmények történtek. További információ: [ez](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

* Támogatás hozzáadva DreamSpark-fiókok
* Az Azure-eszközök támogatására, az új Azure Resource Management API-k teljes módosítása
* Az Azure App Service támogatja az [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Ismert problémák
Webes alkalmazás üzembe helyezési tárhely csomópontok nem jelennek meg a Server Explorerben a tárolóhelyek csomópont alatt, és a webes alkalmazás üzembe helyezési tárhely gyermek csomópont nem betölteni a Cloud Explorer alatt. A probléma feloldva, és a készíteni a következő SDK-verzióban. 

### <a name="cloud_explorer"></a>A Visual Studio 2015 cloud Explorer
Az Azure SDK 2.7-es Cloud Explorer tartalmaz a Visual Studio 2015, amely lehetővé teszi, hogy az Azure-erőforrások megtekintése, azok tulajdonságait megvizsgálhatja és műveleteket kulcs Fejlesztőeszközök a Visual Studión belül. 

Cloud explorer a következőket támogatja:

* Az Azure-erőforrások nézeteinek erőforráscsoportot és az erőforrás típusa 
* Erőforrások keresése (erőforrástípus nézetben érhető el) neve szerint
* Előfizetések és a szerepkör alapú hozzáférés-vezérlés (RBAC) alkalmazott rendelkező erőforrások támogatása 
* A kijelölt erőforrások integrált műveletpulton, amely jellemző fejlesztői irányultságú műveleteket jeleníti meg. Például: csatlakoztatása a távoli hibakeresőt a létrehozott virtuális gépek használata a Resource Manager-készletben, diagnosztikai adatok megtekintése a virtuális gépek stb.
* Integrált Tulajdonságok panel, amely fejlesztési-tesztelési során gyakran szükséges fejlesztőközpontú tulajdonságokat jeleníti meg 
* (Használja a beállítások parancsot eszköztár) erőforrások számbavétele során használandó fiók gyors váltás 
* (Használja a beállítások parancsot eszköztár) erőforrások számbavétele során használandó előfizetések szűrése 
* Az erőforrások és -erőforráscsoportok kezelése az Azure Portal mutató mélyhivatkozások 

### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-eszközök
Az Azure Resource Manager-eszközök a szerepkör alapú hozzáférés-vezérlés (RBAC) és az új előfizetés-típusok frissítése megtörtént.  Ezek a változások mellékelt rendszer azon képessége, új storage-fiókok mellett a hagyományos tárfiók használatával tárolja az összetevők üzembe helyezése során.  

Ha egy Azure erőforráscsoport-projektet az SDK-t egy korábbi verziójáról az SDK 2.7-t használ, egy új üzembe helyezési parancsfájl szükséges üzembe helyezés hagyományos tároló helyett egy új tárfiókot.  Mielőtt a módosítások a projekthez hozzáadni az új parancsfájl kéri.  A régi parancsfájl neve lesz, és manuálisan módosíthatja az új parancsfájl kell.

### <a name="storage-explorer-tools"></a>Storage Explorer Eszközök
* Támogatás a hozzáfűző Blobok megtekintése. További információ a [ebben a blogbejegyzésben](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Megtekintés a Server Explorer eszközével Premium Storage-fiókok támogatása. Server Explorer csak lapblobokat a prémium szintű tárfiókok esetén megjeleníti azok prémium szintű tárfiókok esetén az egyetlen támogatott típus.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Az Azure Data Factory Tools for Visual Studio
Introducing **az Azure Data Factory-eszközök** Visual Studióhoz készült. Az alábbiakban az engedélyezett szolgáltatásokat. Lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=617530) további információt.

* **Sablonalapú szerzői**: használható Case alakú válassza alapuló, adatok mozgását sablonok vagy az adatok teljes körű integrációs megoldás üzembe helyezése, és gyorsan a Data Factory gyakorlati bevezetés adatfeldolgozási sablonokat. 
* **Integráció a Megoldáskezelőben készítése és üzembe helyezése a Data Factory-entitások**: létrehozása és telepítése a folyamatok és a Visual Studio-projektek, a kapcsolódó entitások. 
* **Integráció a Vizualizációk közti interakció készítése során a Diagram nézet**: vizuálisan hozhat létre a folyamatokról és adatkészletekről diagramnézetében támogatással. 
* **Integráció a Server explorer tallózása és a már üzembe helyezett entitások való interakció**: kihasználhatja a Server Explorerben keresse meg a már üzembe helyezte az adat-előállítók és a megfelelő entitásokat. Üzembe helyezett adat-előállító vagy minden entitás (folyamatban, társított szolgáltatást, adatkészleteket) a projektbe importálja. 
* **JSON-adatok szerkesztése a séma érvényesítése és teljes funkcionalitású intellisense**: hatékony konfigurálása és szerkesztése a gazdag intellisense és a séma érvényesítése a Data Factory-entitások JSON-dokumentumok 
* **Több környezet közzététel**: közzététel folyamatok fejlesztési, tesztelési vagy éles környezetben létrehozott minden környezethez külön konfigurációs fájlok létrehozásával.
* **A Pig, Hive és a .net-alapú adatfeldolgozási támogatási**: támogatja a Pig és a Hive-parancsprogramok a Data Factory-projektben. Hivatkozó támogatása C# projekt kezeléséhez .net tevékenység.

## <a name="azure-sdk-for-net-271"></a>Azure SDK a .NET 2.7.1-es verziójához
A következő szakaszban lettek bevezetve, az Azure SDK for .NET 2.7.1-es verziójához kiadás frissítéseket tartalmaz.

### <a name="hdinsight-tools"></a>HDInsight-eszközök
Részletesebb tájékoztatás a HDInsight tools frissítések, lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=623831).

* Hive-feladat operátor nézet (új funkció)
  
    Segítenek megérteni a Hive-lekérdezést hatékonyabban, a Hive-operátor megtekintése funkció hozzá lett adva. Csúcs belül az operátorok megtekintéséhez kattintson duplán a feladatábra csúcsaira lévő. Egy adott operátor további részleteket az adott operátor fölé.
* Hive-hiba jelölő (új funkció)
  
    Ahhoz, hogy a nyelvtani hibák megtekintéséhez azonnal, a Hive-hiba jelölő szolgáltatás hozzá lett adva. Ezenkívül hibaüzenetek egészültek ki, és azonnal részletes nyelvtani hibát most már megtekintheti (ebben a kiadásban, amíg nem kellett küldje el a fürt egy Hive-szkriptet, és a hibaüzenet részleteinek megismeréséhez elérése előtt kis idő múlva).  
* A Storm-topológia Graph (új funkció)
  
    Jelenítenek meg, akkor nagyon fontos, ha meg szeretné tekinteni, hogy a várt módon működik-e a topológia. Ebben a verzióban hozzáadtunk képi megjelenítés, a Storm-diagramok esetében. A topológia jelenítheti meg a fontos metrikákat (például egy szín azt jelzi, időjárási egy bizonyos Bolt "forgalmas" vagy nem). Akkor is is kattintson duplán a Bolt/Spout további részletek megtekintéséhez.
* Az Azure Portalon (a hibajavítás) létrehozott HDInsight-fürtök támogatása
  
    Mostantól használhatja a Visual Studio megtekintéséhez és feladatok elküldéséhez minden a HDInsight fürtökhöz függetlenül attól, ahol a fürt létrejöttek.
* További IntelliSense-támogatás és gyorsabb Hive-metaadatok betöltése (javulását)
  
    Továbbfejlesztettük az IntelliSense további felhasználóbarát javaslatok hozzáadásával. Például tábla hivatkozási is most is a javasolt az IntelliSense, így könnyebben írhat a lekérdezést. Azt is, javult a Hive-metaadatok betöltése, így csak eltarthat néhány másodpercig, az összes adatbázis, táblák és oszlopok, a Hive-metaadattár listázásához.

Részletesebb tájékoztatás a HDInsight tools frissítések, lásd: [ebben a blogbejegyzésben](https://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>A Visual Studio 2013 fejlesztései
* Azure SDK 2.7.1-es verziójának lehetővé teszi, hogy a Visual Studio 2013 Azure-fiókok és a szerepköralapú hozzáférés-vezérlés, a Cloud Solution Providers és a Dreamspark-előfizetések eléréséhez.
* Az Azure SDK 2.7.1-es verziójához az új Cloud Explorer eszköz ablak már is elérhető a Visual Studio 2013.

### <a name="known-issues"></a>Ismert problémák
Az Azure SDK 2.6-os vagy telepítése 2.7.1-es verziójához a Visual Studio Community 2013 a egy nem angol nyelvű operációsrendszer-, hogy az angol és a nem angol nyelvű erőforrásokat a Visual Studio nem egyeznek a előfordulhat, hogy figyelmeztetés jelenik meg. Előfordulhat, hogy ez a figyelmeztetés biztonságosan eltűnik. Ez akkor történik, ha a gép nem tartalmaz egy Visual Studio Community 2013 előzetes telepítése, és telepítette az SDK-t az olyan nem angol nyelvű operációsrendszer. A figyelmeztetés akkor jelenik meg, a nyelvi csomag a Visual Studio vonatkozik az RTM-erőforrásokat, de előtt Update 4 vonatkozik. A figyelmeztetés bezárása lehetővé teszi a nyelvi csomag a folytatáshoz, majd fejezze be az alkalmazása a nyelvi csomag tartalmát a Update 4 verzióját.

Ebben a kiadásban compatibile LightSwitch projektek nem tartoznak. A következő SDK-verzióban. a probléma megoldódik.

## <a name="also-see"></a>Lásd még:
[Azure SDK 2.7.1-es verziójának közleményt post](https://go.microsoft.com/fwlink/?LinkId=623850)

[Az Azure SDK 2.7-es közlemény post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Támogatási és kivezetési információkat az Azure SDK for .NET és az API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

