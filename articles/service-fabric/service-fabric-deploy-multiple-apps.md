---
title: A Node.js-alkalmazás által használt Azure Service Fabric a MongoDB telepítése |} Microsoft Docs
description: Általános bemutató csomag több Vendég végrehajtható fájlokat az Azure Service Fabric-fürt központi telepítése
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 9a7ab3881cd1058a60ff7d5f6e50c296f042e76e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-multiple-guest-executables"></a>Több futtatható vendégalkalmazás üzembe helyezése
Ez a cikk bemutatja, hogyan csomagot, és több Vendég végrehajtható fájlok telepítése Azure Service Fabric. Kialakításához, és egyetlen Service Fabric-csomag telepítése olvasási hogyan való [központi telepítése egy Vendég végrehajtható Service Fabric](service-fabric-deploy-existing-app.md).

Amíg ez a forgatókönyv bemutatja, hogyan telepítsen egy Node.js előtér MongoDB, a tárolót használó alkalmazást, a lépéseket alkalmazhatja a bármely alkalmazás, amely egy másik alkalmazás függ.   

Visual Studio segítségével létrehozhat több Vendég végrehajtható fájlokat tartalmazó alkalmazáscsomagot. Lásd: [csomag egy meglévő alkalmazást a Visual Studio használatával](service-fabric-deploy-existing-app.md). Miután hozzáadta az első Vendég végrehajtható fájlt, kattintson jobb gombbal a projekt a, és válassza ki a **Hozzáadás -> új Service Fabric-szolgáltatás** a második Vendég végrehajtható projekt hozzáadása a megoldás. Megjegyzés: Ha a forrás, a Visual Studio-projekt hivatkozásra, a Visual Studio megoldás létrehozása fog ellenőrizze, hogy az alkalmazáscsomag naprakészen a forrás változásairól. 

## <a name="samples"></a>Példák
* [Minta csomagolás és központi telepítése egy Vendég végrehajtható fájl](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) kapcsolaton keresztül kommunikáljon a Naming szolgáltatás REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>A több Vendég futtatható alkalmazás manuálisan csomag
Másik lehetőségként a Vendég végrehajtható manuálisan csomagot. A manuális csomagolása, az ebben a cikkben az érhető el a Service Fabric csomagolás eszköz [ http://aka.ms/servicefabricpacktool ](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>A Node.js-alkalmazás csomagolás
Ez a cikk feltételezi, hogy a Node.js nincs telepítve a Service Fabric-fürt csomópontjain. Következésképpen kell Node.exe hozzáadása előtt csomagolása a csomópont alkalmazás gyökérkönyvtárában. A Node.js-alkalmazás (Express webes keretrendszer és a sablon Jade motorral használatával) könyvtárszerkezete az alábbihoz hasonlóan kell kinéznie:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Következő lépésként hozzon létre egy alkalmazáscsomagot, a Node.js-alkalmazás. Az alábbi kódot a Service Fabric alkalmazáscsomagot, amely tartalmazza a Node.js-alkalmazást hoz létre.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Az alábbiakban a használt paraméterek leírását a következő:

* **/ source** arra a könyvtárra kell csomagolva alkalmazás mutat.
* **/ target** határozza meg a könyvtárban, amely a csomag kell létrehozni. Ez a könyvtár nem lehet azonos a forráskönyvtár.
* **típustárnevek** határozza meg az alkalmazás nevét, a meglévő alkalmazás. Fontos megérteni, hogy a következőkből fordítja le a szolgáltatás nevét a jegyzékfájlban, nem pedig a Service Fabric-alkalmazás neve.
* **/exe** határozza meg a végrehajtható fájl, amely a Service Fabric kellene indítása, ebben az esetben `node.exe`.
* **/Ma** elindíthatja a végrehajtható fájl éppen használatban lévő argumentum határozza meg. Node.js nincs telepítve, a Service Fabric kell indítsa el a Node.js web server végrehajtásával `node.exe bin/www`.  `/ma:'bin/www'` közli a csomagolás eszköz `bin/www` node.exe argumentumaként.
* **/ Alkalmazástípus** határozza meg a Service Fabric-alkalmazás típusnév.

Ha tallózással a/TARGET paraméterben megadott könyvtárat, láthatja, hogy az eszköz hozott létre a teljes mértékben működő Service Fabric-csomag alább látható módon:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
A generált ServiceManifest.xml most már egy szakaszt, amely leírja, hogyan kell indítani a Node.js webalkalmazás-kiszolgáló, az alábbi kódrészletben látható módon:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Ez a példa a Node.js web server figyeli a port 3000, ezért szüksége lesz a végpont frissítése a lent látható módon a ServiceManifest.xml fájlban.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>A MongoDB alkalmazás csomagolás
Most, hogy a Node.js-alkalmazás van csomagolva, lépjen tovább, és MongoDB csomag. Ahogy korábban említettük, a lépéseket, amelyek most halad át nincsenek adott Node.js és a mongodb-Protokolltámogatással. Valójában vonatkoznak, amelyek jelzik, hogy egy Service Fabric-alkalmazás együtt csomagolt alkalmazások.  

A csomag a MongoDB, kívánt győződjön meg arról, hogy Mongod.exe és Mongo.exe csomag. Mindkét bináris fájljai találhatók a `bin` mappában található a MongoDB telepítési könyvtára. A könyvtárstruktúra az alábbihoz hasonlít.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
A Service Fabric elindításához szükséges MongoDB egy hasonló parancs az alábbi, így kell használnia a `/ma` paramétert, ha a MongoDB-csomagban.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Az adatok nem alatt megmarad, ha egy csomópont meghibásodik, ha a csomópont helyi könyvtárban MongoDB adatkönyvtára. Kell vagy tartós tárhelyet használja, vagy adatvesztés elkerülése érdekében a MongoDB replikakészlet megvalósításához.  
>
>

PowerShell vagy a parancs-rendszerhéj azt futtassa a csomagolás eszköz a következő paraméterekkel:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

A Service Fabric alkalmazáscsomagra a MongoDB hozzáadásához, győződjön meg arról, hogy a/TARGET paraméter mutat, amely már tartalmazza az alkalmazás könyvtárába együtt a Node.js-alkalmazás jegyzékfájlja kell. Szükség győződjön meg arról, hogy a ApplicationType néven használunk.

Most nyissa meg a mappát, és vizsgálja meg, mire az eszköz hozott létre.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Ahogy látja, az eszköz hozzáadni egy új mappát, a MongoDB, a MongoDB bináris fájlokat tartalmazó könyvtárat. Ha megnyitja a `ApplicationManifest.xml` fájl, láthatja, hogy a csomag most már tartalmazza a Node.js-alkalmazás és a mongodb-Protokolltámogatással. Az alábbi kód megjeleníti az alkalmazás jegyzékében tartalmát.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Az alkalmazás közzététele
Az utolsó lépése, hogy az alkalmazás a helyi Service Fabric-fürt közzététele az alábbi PowerShell-parancsfájlok használatával:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Miután az alkalmazás sikeresen közzétette a helyi fürthöz, a Node.js-alkalmazás, amely azt adta-e a szolgáltatás jegyzékben a Node.js-alkalmazás – például a porton hozzáférhet http://localhost:3000.

Ebben az oktatóanyagban egyszerűen egy Service Fabric-alkalmazás, a két meglévő alkalmazások csomagolása láthatta. Is megtanulhatta, hogyan lehet telepíteni a Service Fabric, hogy kihasználja a Service Fabric szolgáltatás, például magas rendelkezésre állás és a rendszerállapot rendszerintegráció némelyike.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>További Vendég végrehajtható fájlok hozzáadása egy meglévő alkalmazást Yeoman Linux rendszeren

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket: 
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtatás `yo azuresfguest:AddService` , és adja meg a szükséges adatokat.

## <a name="next-steps"></a>További lépések
* További tudnivalók a tárolók telepítése [Service Fabric és a tárolók – áttekintés](service-fabric-containers-overview.md)
* [Minta csomagolás és központi telepítése egy Vendég végrehajtható fájl](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) kapcsolaton keresztül kommunikáljon a Naming szolgáltatás REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
