---
title: Az Azure Service fabric MongoDB használó Node.js-alkalmazás üzembe helyezése |} A Microsoft Docs
description: Több futtatható vendégalkalmazás csomagolása a forgatókönyv az Azure Service Fabric-fürt üzembe helyezése
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 69df9eff85d96c9cc6ca7fa1d3aabd2c54fae416
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884720"
---
# <a name="deploy-multiple-guest-executables"></a>Több futtatható vendégalkalmazás üzembe helyezése
Ez a cikk bemutatja, hogyan csomagolását és több futtatható vendégalkalmazás üzembe helyezése az Azure Service fabric. Fejlesztésére és üzembe helyezése a Service Fabric egyetlen csomagban további ismertetés a [egy futtatható vendégalkalmazás üzembe helyezése Service Fabric](service-fabric-deploy-existing-app.md).

Ez az útmutató bemutatja, hogyan helyezhet üzembe egy egy Node.js kezelőfelületes szavazóalkalmazással, amely a MongoDB-t az adattárat használja, amíg a lépéseket minden olyan alkalmazás, amely rendelkezik egy másik alkalmazás függőségeit is alkalmazhat.   

A Visual Studio segítségével a több futtatható vendégalkalmazás tartalmazó alkalmazáscsomagot előállításához. Lásd: [meglévő alkalmazás becsomagolása a Visual Studio használatával](service-fabric-deploy-existing-app.md). Miután hozzáadta az első Vendég végrehajtható fájlt, kattintson jobb gombbal az alkalmazásprojektre, és válassza ki a **Hozzáadás -> új Service Fabric-szolgáltatás** hozzáadása a második Vendég végrehajtható projektet a megoldáshoz. Megjegyzés: Ha a forrás a Visual Studio-projekt mutató hivatkozást választja, a Visual Studio-megoldás létrehozása fog ellenőrizze, hogy az alkalmazáscsomag naprakészen a forrás változásainak. 

## <a name="samples"></a>Példák
* [Minta csomagolás és a egy futtatható vendégalkalmazás üzembe helyezése](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) keresztül kommunikáljon az elnevezési szolgáltatásban REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>A több Vendég futtatható alkalmazás manuális csomag
Másik lehetőségként kézzel csomagot készíthet a Vendég végrehajtható. A manuális csomagolást, ebben a cikkben a Service Fabric csomagolási eszközt, amely mindig elérhető legyen az [ https://aka.ms/servicefabricpacktool ](https://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>A Node.js-alkalmazás csomagolása
Ez a cikk azt feltételezi, hogy Node.js nincs telepítve, a Service Fabric-fürt csomópontjain. Ennek következményeképpen kell Node.exe hozzáadása előtt csomagolási a node-alkalmazás gyökérkönyvtárában. A könyvtárstruktúra, a Node.js-alkalmazás (Expressz webes keretrendszert és Jade sablonmotor használatával), az alábbihoz hasonlóan kell kinéznie:

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

Következő lépésként hozzon létre egy alkalmazáscsomagot, a Node.js-alkalmazás. Az alábbi kód egy Service Fabric-alkalmazáscsomagot, amely tartalmazza a Node.js-alkalmazást hoz létre.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Az alábbi, a használt paraméterek leírását:

* **/ source** a könyvtárban kell lennie csomagolt alkalmazás mutat.
* **cél/** határozza meg a könyvtárat, amely a csomag kell létrehozni. Ez a könyvtár nem lehet eltérő a forráskönyvtár.
* **típustárnevek** határozza meg az alkalmazás neve, a meglévő alkalmazás. Fontos megérteni, hogy ez a rendszer lefordítja a szolgáltatás nevét, a jegyzékfájlban, és nem a Service Fabric-alkalmazás neve.
* **/exe** határozza meg a végrehajtható fájl, amely a Service Fabric kellene indítása, ebben az esetben `node.exe`.
* **/Ma** az argumentum, amely használatban van, indítsa el a végrehajtható fájl határozza meg. Node.js nincs telepítve, mint a Service Fabric kell úgy, hogy végrehajtja a Node.js-webkiszolgáló indításához `node.exe bin/www`.  `/ma:'bin/www'` arra utasítja a csomagolás eszköz `bin/www` node.exe argumentumaként.
* **/ Alk. típusa** határozza meg a Service Fabric-alkalmazás típusát.

Ha a címtárnak, amelyhez a/TARGET paraméterben megadott tallózással, láthatja, hogy az eszköz egy teljesen működőképes a Service Fabric-csomag hozott létre, ahogy az alábbi:

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
A létrehozott ServiceManifest.xml most már rendelkezik egy szakaszt, amely leírja, hogyan kell indítani a Node.js web server, az alábbi kódrészletben látható módon:

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
Ebben a példában a Node.js webalkalmazás-kiszolgáló figyel 3000, port, akkor frissítenie kell a végpont adatait a ServiceManifest.xml fájlban, ahogy az alábbi.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>A MongoDB-alkalmazások csomagolása
Most, hogy a Node.js-alkalmazás van csomagolva, lépjen tovább, és a MongoDB csomag. Ahogy korábban említettük, a lépéseket, hogy Ön már nem konkrétan a Node.js és mongodb-hez. Valójában a vonatkoznak minden olyan alkalmazások, amelyek jelzik, hogy egy Service Fabric-alkalmazásként becsomagolja.  

MongoDB becsomagolásához szeretné ellenőrizze, hogy Ön Mongod.exe és Mongo.exe csomag. Mindkét bináris fájljai találhatók a `bin` könyvtárát a MongoDB telepítési könyvtárában. A könyvtárstruktúra az alábbihoz hasonlóan néz ki.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
A Service Fabric kell kezdődnie mongodb-hez hasonló parancsot az alábbi, így kell használnia a `/ma` MongoDB csomagolására paramétert.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Az adatok nem folyamatban megőrződik, ha egy csomópont meghibásodik, ha a helyi könyvtárban, a csomópont a MongoDB adatkönyvtárat helyezi. Használjon tartós tárolási vagy adatvesztés elkerülése érdekében állítsa be a MongoDB replika megvalósítása kell.  
>
>

A PowerShellben vagy a parancs-rendszerhéjból az alábbi paramétereket a csomagolás eszközzel futtassa azt:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

A Service Fabric-alkalmazáscsomag ad hozzá a MongoDB, győződjön meg arról, hogy a/TARGET paraméter mutat, amely már tartalmazza az alkalmazás könyvtárába együtt a Node.js-alkalmazás jegyzékfájlja kell. Szükség is győződjön meg arról, hogy használja a ApplicationType névvel.

Nézzük könyvtárba, és vizsgálja meg, mi az eszközt hozott létre.

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
Ahogy láthatjuk, az eszköz hozzá egy új mappát, a MongoDB, a MongoDB bináris fájlokat tartalmazó könyvtárra. Ha megnyitja a `ApplicationManifest.xml` fájlt, láthatja, hogy a csomag most már tartalmazza a Node.js-alkalmazás és a mongodb-hez. Az alábbi kódot az alkalmazásjegyzékben tartalmát jeleníti meg.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Az utolsó lépés, hogy az alkalmazás közzététele a helyi Service Fabric-fürthöz az alábbi PowerShell-parancsfájlok használatával:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Miután az alkalmazás sikeresen közzé lett téve a helyi fürthöz, a Node.js-alkalmazás a porton, hogy a Node.js-alkalmazás – például http szolgáltatásjegyzékben írt elérheti:\//localhost:3000.

Ebben az oktatóanyagban látott egyszerűen egy Service Fabric-alkalmazásként a két meglévő alkalmazások csomagolása. Emellett megtanulhatta, hogyan helyezhet üzembe Service fabric, hogy a Service Fabric funkcióit, például a magas rendelkezésre állású és egészségügyi rendszer-integráció előnyei.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Több futtatható vendégalkalmazás hozzáadása egy meglévő alkalmazás Linux rendszeren Yeoman használatával

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket: 
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtatás `yo azuresfguest:AddService` , és adja meg a szükséges adatokat.

## <a name="next-steps"></a>További lépések
* Hogyan helyezhető üzembe a tárolók [Service Fabric és a tárolók áttekintése](service-fabric-containers-overview.md)
* [Minta csomagolás és a egy futtatható vendégalkalmazás üzembe helyezése](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) keresztül kommunikáljon az elnevezési szolgáltatásban REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
