---
title: MongoDB használó Node. js-alkalmazás üzembe helyezése
description: Útmutató az Azure Service Fabric-fürtön való üzembe helyezéshez több vendég végrehajtható fájljának becsomagolásához
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614468"
---
# <a name="deploy-multiple-guest-executables"></a>Több futtatható vendégalkalmazás üzembe helyezése
Ez a cikk bemutatja, hogyan csomagolhat és helyezhet üzembe több vendég végrehajtható fájlját az Azure Service Fabricban. Egy Service Fabric csomag kiépítése és üzembe helyezése során olvassa el, hogyan [helyezhet üzembe egy vendég végrehajtható fájlt a Service Fabric](service-fabric-deploy-existing-app.md).

Ez a bemutató bemutatja, hogyan helyezhet üzembe egy alkalmazást egy Node. js előtér-kezelőfelülettel, amely az MongoDB-t használja adattárként. a lépéseket alkalmazhatja bármely olyan alkalmazásra, amely egy másik alkalmazás függőségeivel rendelkezik.   

A Visual Studióval olyan alkalmazáscsomag hozható létre, amely több vendég végrehajtható fájlját is tartalmazza. Lásd: [meglévő alkalmazás becsomagolása a Visual Studio használatával](service-fabric-deploy-existing-app.md). Miután hozzáadta az első vendég végrehajtható fájlt, kattintson a jobb gombbal az alkalmazás projektre, és válassza az **Add-> új Service Fabric szolgáltatást** , hogy hozzáadja a második vendég végrehajtható projektet a megoldáshoz. Megjegyzés: Ha úgy dönt, hogy összekapcsolja a forrást a Visual Studio-projektben, a Visual Studio-megoldás létrehozásakor gondoskodni fog arról, hogy az alkalmazáscsomag naprakész legyen a forrás változásaival. 

## <a name="samples"></a>Minták
* [Minta a vendég végrehajtható fájlok csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlrólC# (és NodeJS) a REST használatával kommunikáló elnevezési szolgáltatás segítségével](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>A több vendég végrehajtható alkalmazás manuális becsomagolása
Azt is megteheti, hogy manuálisan becsomagolja a vendég végrehajtható fájlt. Részletekért lásd: [meglévő végrehajtható fájl manuális csomagolása és üzembe helyezése](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>A Node. js-alkalmazás csomagolása
Ez a cikk azt feltételezi, hogy a Node. js nincs telepítve a Service Fabric-fürt csomópontjain. Ennek következményeként a Node. exe fájlt hozzá kell adnia a Node-alkalmazás gyökérkönyvtárához a csomagolás előtt. A Node. js-alkalmazás (Express web Framework és Jade template Engine használatával) címtár-struktúrájának a következőhöz hasonlóan kell kinéznie:

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

A következő lépésként hozzon létre egy alkalmazáscsomag a Node. js-alkalmazáshoz. Az alábbi kód létrehoz egy Service Fabric alkalmazáscsomag, amely tartalmazza a Node. js alkalmazást.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Alább látható a használt paraméterek leírása:

* a **/Source** a csomagolni kívánt alkalmazás könyvtárába mutat.
* a **/Target** meghatározza azt a könyvtárat, amelyben a csomagot létre kell hozni. Ennek a könyvtárnak eltérőnek kell lennie a forrás címtárban.
* a **/AppName** meghatározza a meglévő alkalmazás alkalmazásának nevét. Fontos tisztában lenni azzal, hogy ez a jegyzékfájlban a szolgáltatás nevére van lefordítva, és nem a Service Fabric az alkalmazás nevét.
* a **/exe** meghatározza azt a végrehajtható fájlt, amelyet Service Fabric el kell indítani, ebben az esetben `node.exe`.
* a **/ma** határozza meg a végrehajtható fájl elindításához használt argumentumot. Mivel a Node. js nincs telepítve, Service Fabric a Node. js webkiszolgálót a `node.exe bin/www`futtatásával kell elindítania.  `/ma:'bin/www'` azt jelzi, hogy a csomagoló eszköz `bin/www` használja a Node. exe argumentumként.
* A **/AppType** meghatározza a Service Fabric az alkalmazás típusának nevét.

Ha megkeresi a/TARGET paraméterben megadott könyvtárat, láthatja, hogy az eszköz teljes mértékben működőképes Service Fabric csomagot hozott létre az alábbi ábrán látható módon:

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
A generált ServiceManifest. xml fájl már tartalmaz egy szakaszt, amely leírja, hogyan kell elindítani a Node. js webkiszolgálót, ahogy az alábbi kódrészletben is látható:

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
Ebben a példában a Node. js-webkiszolgáló figyeli a 3000-es portot, ezért frissítenie kell a végpont adatait az ServiceManifest. xml fájlban az alább látható módon.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>A MongoDB alkalmazás csomagolása
Most, hogy becsomagolta a Node. js-alkalmazást, megtekintheti a csomagokat, és MongoDB. Ahogy azt korábban említettük, a lépések mostantól a Node. js és a MongoDB esetében nem jellemzőek. Valójában minden olyan alkalmazásra érvényesek, amelyet egyetlen Service Fabric alkalmazásként kell becsomagolni.  

A MongoDB csomag létrehozásához győződjön meg arról, hogy a Mongod. exe és a Mongo. exe csomag. Mindkét bináris fájl a MongoDB telepítési könyvtárának `bin` könyvtárában található. A címtár szerkezete az alábbihoz hasonlóan néz ki.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric a MongoDB az alábbihoz hasonló paranccsal kell elindítania, ezért a MongoDB csomagolásakor a `/ma` paramétert kell használnia.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> A csomópont meghibásodása esetén az adat nem marad meg, ha a MongoDB adatkönyvtárat a csomópont helyi könyvtárára helyezi. Az adatvesztés elkerülése érdekében használjon tartós tárolást, vagy hozzon létre egy MongoDB-replikát.  
>
>

A PowerShellben vagy a parancs-rendszerhéjban a következő paraméterekkel futtatjuk a csomagoló eszközt:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Ahhoz, hogy MongoDB lehessen hozzáadni a Service Fabric alkalmazáscsomag számára, meg kell győződnie arról, hogy a/TARGET paraméter ugyanarra a könyvtárra mutat, amely már tartalmazza az alkalmazás jegyzékfájlját a Node. js-alkalmazással együtt. Emellett meg kell győződnie arról, hogy ugyanazt a alkalmazásban (-nevet használja.

Tallózással keresse meg a könyvtárat, és vizsgálja meg, hogy az eszköz hogyan lett létrehozva.

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
Ahogy látható, az eszköz új mappát (MongoDB) adott hozzá a MongoDB bináris fájlokat tartalmazó könyvtárhoz. Ha megnyitja a `ApplicationManifest.xml` fájlt, láthatja, hogy a csomag mostantól tartalmazza a Node. js alkalmazást és a MongoDB. Az alábbi kód az alkalmazás jegyzékfájljának tartalmát jeleníti meg.

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
Az utolsó lépés az alkalmazás közzététele a helyi Service Fabric fürtön az alábbi PowerShell-szkriptek használatával:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Miután az alkalmazás sikeresen közzé lett téve a helyi fürtön, elérheti a Node. js-alkalmazás szolgáltatás jegyzékfájljában megadott porton található Node. js-alkalmazást – például http:\//localhost: 3000.

Ebben az oktatóanyagban megismerte, hogyan lehet egyszerűen becsomagolni két meglévő alkalmazást egyetlen Service Fabric alkalmazásként. Azt is megtanulta, hogyan helyezheti üzembe a Service Fabric, hogy az a Service Fabric egyes funkciói számára is hasznos legyen, például a magas rendelkezésre állás és az rendszerállapot-integráció terén.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>További vendég-végrehajtható fájlok hozzáadása egy meglévő alkalmazáshoz a Linux Yeoman használatával

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket: 
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa a `yo azuresfguest:AddService`t, és adja meg a szükséges adatokat.

## <a name="next-steps"></a>Következő lépések
* Tudnivalók a tárolók üzembe helyezéséről [Service Fabric és tárolókban – áttekintés](service-fabric-containers-overview.md)
* [Minta a vendég végrehajtható fájlok csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlrólC# (és NodeJS) a REST használatával kommunikáló elnevezési szolgáltatás segítségével](https://github.com/Azure-Samples/service-fabric-containers)
