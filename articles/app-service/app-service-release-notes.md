---
title: Az Azure SDK for .NET 2.5.1-es verzióval készült kibocsátási megjegyzések
description: Az Azure SDK for .NET 2.5.1-es verzióval készült kibocsátási megjegyzések
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: ''
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
ms.locfileid: "23957415"
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Az Azure SDK for .NET 2.5.1-es verzióval készült kibocsátási megjegyzések
Ez a dokumentum a kibocsátási megjegyzések vonatkozó .NET 2.5.1-es verzióval készült Azure SDK tartalmazza. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Az Azure SDK for .NET 2.5.1-es verzióval készült kibocsátási megjegyzések
Új szolgáltatásokat és az Azure SDK for .NET 2.5.1-es verzióval készült frissítések a következők:

* Új features\scenarios kapcsolódó **Web eszközök Extensions**. 
  
  * Azure-webhelyek Azure App Service nevet viseli. 
  * Az Azure API-alkalmazások (előzetes verzió) már támogatja az, hogy az ügyfelek projekteket ASP.NET API-alkalmazások közzététele, és ezután használja az Add > Azure API App ügyfél-hitelesítési mód a C# projekt esetén is generálhat kódot a telepített API-alkalmazás szerkezete alapján. 
  * A Server Explorer a webhelyek csomópont helyett az Azure App Service, tartalmazó csomóponton Csoportalapú csoportosítása Azure API-alkalmazások, a Mobile Apps és a Web Apps erőforrás támogatása elavult.
  * Az Azure Mobile Apps (előzetes verzió) már támogatja az, hogy az ügyfelek hozhatnak létre új Mobile Apps-projektek, vegye fel a Mobile Apps-vezérlők, a projektek közzététele, és távolról az-alkalmazások hibakeresését.
  * Adja hozzá > Azure API App ügyfél-hitelesítési mód mostantól támogatja a helyi Swagger JSON-fájlokat, így a Web API a fejlesztők külső NuGets például a Swashbuckle a Swagger létrehozásához, vagy manuálisan létre kell azt. Ezzel a módszerrel ügyfél a fejlesztők a a kódgenerálás szolgáltatások felhasználásához, bármely Swagger-végpont C#-projektben. 
  * Új webalkalmazás és az API-alkalmazás üzembe helyezési párbeszédpanelen Web App és az API-alkalmazás közzétételi párbeszédpanelek erőforrás csoportosítása Azure portál fogalmát, és kiválasztása vagy létrehozása Azure-csoportok és az App Service-csomagok támogatása továbbfejlesztett jelöli. 
  * Az Azure API App Server Explorer csomópontok adja meg az API-alkalmazások mélyhivatkozása az Azure-portálon, valamint egyéb szolgáltatások, mint az adatfolyam-napló és a távoli hibakereséshez mutató hivatkozásokat tartalmaz.
    
    Ismert problémák és a jelenlegi korlátozások az Azure SDK .NET 2.5.1-es verzióval készült [ez](app-service-release-notes.md#known_issues_2_5_1) az alábbi szakasz.
* Új features\scenarios kapcsolódó **a HDInsight Tools** ebben a kiadásban engedélyezve vannak a Visual Studióban. 
  
  * Hive parancsfájlok helyi érvényesítése. Kattintson az érvényesítés parancsfájl gombra az eszköztáron megjelenítéséhez, ha hiba történik a parancsfájlban. 
  * Javult a Hive-feladatok a hibakeresés. Hive-feladatok a Yarn naplók a Visual Studio elérésével most hibakeresési. Ha az alkalmazás teljesítményproblémákat, vizsgáló YARN naplóit hasznos információt biztosító...
  * (Nyilvános előzetes verzió) Kulcsszó automatikus-befejezését és az IntelliSense támogatja a Hive. Súgó a Hive parancsfájlok készítésének módjától, a HDInsight Tools for Visual Studio hozzáadott kulcsszó automatikus-befejezését és az IntelliSense támogatja a Hive.
  * A Storm-támogatás. Most már használhatja a HDInsight Tools for Visual Studio topológiák/spoutokkal kapcsolatban/Boltokhoz Storm a C# fejlesztéséhez. Küldje el a fejlett topológia a Storm fürthöz, majd a topológia/bolt/spout állapotát tekintheti meg. Rendszer naplókat és az ügyfél-naplók segítségével hibaelhárítása a Storm topológiák/Boltokhoz/spoutokkal kapcsolatban. A HDInsight alatt futó Storm használhatja a meglévő JAVA-eszközök.
    
    További információkért lásd: [első lépései a HDInsight Hadoop Tools for Visual Studio használatával](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Az Azure SDK for .NET 2.5.1-es verzióval készült ismert problémák és korlátozások
* Az Azure API-alkalmazások esetén a Mobile Apps telepítési cél látható. Web Apps csak egy későbbi kiadásban kell lennie a Mobile Apps csak célját. 
* Az Azure API App kiépítés sikeres eredményez, de időnként nem lehet frissíteni a folyamatjelző az Azure App Service-tevékenység ablakában. Kerülő megoldás lehet az új Azure API-alkalmazás az Azure portálon állapotának ellenőrzéséhez. 
* Fájl > Új projekt > API-alkalmazás > F5 élmény HTTP hibát okoz, mivel nincs default/index.html. Kerülő megoldás lehet manuálisan navigáljon a /api/values URL-CÍMÉRE. 
* Időnként, Server Explorer ikonok jelennek meg egybesimított. VS újraindítás megszünteti ezt a problémát. 
* Ha a rendszer kivételt hoz létre Web App és az API-alkalmazások (például a kvóta túllépése hibák vagy ismétlődő Azure API App átjáró neve) kiépítése során, a hibák megjelenítése néhány nyers JSON-szöveg. 
* Időszakos projekt-létrehozásával kapcsolatos problémák, ha az Application Insights be van jelölve, a projekt létrehozás időpontjában.
* Alkalmanként az Azure API App generált Ügyfélkód hiányzik a névterek, a kód lefordításához manuálisan vizsgálni (és automatikusan importálja a Visual Studio kötegek keresztül) szükséges. 
* Mobilalkalmazás-projektek közzé kell tenni a webalkalmazások, de válasszon ki egy helyet, mivel a mobilalkalmazás-projektek igényel adatbázis az Azure-portálon mobilalkalmazás alkalmazásként létrehozott. 
* A Mobile Apps kezdőlapját "mobilszolgáltatás" helyett "mobilalkalmazások" kifejezést használja. 
* Mobilalkalmazás-projekt létrehozása igénybe vehet egy percre létrehozásához. 
* API-alkalmazás során (egyes esetekben) létesítési hiba ismét elérhető lesz a az Azure API, amely tükrözi, hogy az engedélyek nem állítható be megfelelően, amíg az API-alkalmazás megfelelően lettek kiosztva és használatra kész. Manuálisan beállíthatja az Azure portál használatával engedélyeit.
* Az Application Insights API-alkalmazás sablonok és a mobilalkalmazás-sablonok nem támogatott.
* API-alkalmazás projektek Cloud Service projektek együtt nem használható.
* A C# API-alkalmazás projekt sablonok csak érhetők el.
* Az "Azure API App ügyfél hozzáadása" helyi menü keresztül API-alkalmazás felhasználása csak akkor támogatott a C#.

