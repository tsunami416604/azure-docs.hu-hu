---
title: MongoDB-t használó Node.js alkalmazás telepítése
description: Forgatókönyv arról, hogyan csomagolhat több vendég végrehajtható fájlt egy Azure Service Fabric-fürtre való üzembe helyezéshez
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614468"
---
# <a name="deploy-multiple-guest-executables"></a>Több futtatható vendégalkalmazás üzembe helyezése
Ez a cikk bemutatja, hogyan csomagolható és üzembe helyezhet több vendég végrehajtható fájlokat az Azure Service Fabric. Egyetlen Service Fabric-csomag létrehozásához és üzembe helyezéséhez olvassa el, hogyan [telepíthet egy vendég végrehajtható fájlt a Service Fabric számára.](service-fabric-deploy-existing-app.md)

Ez a forgatókönyv bemutatja, hogyan telepíthet egy alkalmazást egy Node.js előtér-kezelővel, amely a MongoDB-t használja adattárként, a lépéseket bármely olyan alkalmazásra alkalmazhatja, amely egy másik alkalmazástól függ.   

A Visual Studio segítségével több vendég végrehajtható fájlt tartalmazó alkalmazáscsomagot hozhat létre. Lásd: [A Visual Studio használata meglévő alkalmazás csomagolásához](service-fabric-deploy-existing-app.md). Miután hozzáadta az első vendég végrehajtható fájlt, kattintson a jobb gombbal az alkalmazásprojektre, és válassza ki az **Új szolgáltatásfabric hozzáadása >** a második vendég végrehajtható projekt et a megoldáshoz. Megjegyzés: Ha úgy dönt, hogy összekapcsolja a forrást a Visual Studio projektben, a Visual Studio-megoldás létrehozása korrelál, akkor az alkalmazáscsomag naprakész a forrás változásaival. 

## <a name="samples"></a>Példák
* [Minta a vendég végrehajtható fájl jának csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlból (C# és nodejs) kommunikál nak az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>A több vendég végrehajtható alkalmazás manuális csomagolása
Azt is megteheti, hogy manuálisan csomagolja be a vendég végrehajtható fájlt. További információt a Kézi csomag csomagja és meglévő végrehajtható fájl telepítése .for details, for [manually's package and deploy an existing executable](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable).

### <a name="packaging-the-nodejs-application"></a>A Node.js alkalmazás csomagolása
Ez a cikk feltételezi, hogy a Node.js nincs telepítve a Service Fabric-fürt csomópontjaira. Ennek következtében hozzá kell adnia a Node.exe-t a csomópontalkalmazás gyökérkönyvtárához a csomagolás előtt. A Node.js alkalmazás könyvtárszerkezetének (express webes keretrendszer és Jade sablonmotor használatával) az alábbihoz hasonlóan kell kinéznie:

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

Következő lépésként hozzon létre egy alkalmazáscsomagot a Node.js alkalmazáshoz. Az alábbi kód létrehoz egy Service Fabric alkalmazáscsomagot, amely tartalmazza a Node.js alkalmazást.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Az alábbiakban a használt paraméterek leírása látható:

* **A /source** az alkalmazás becsomagolandó könyvtárára mutat.
* **A /target** azt a könyvtárat határozza meg, amelyben a csomagot létre kell hozni. Ennek a könyvtárnak különböznie kell a forráskönyvtártól.
* **A /appname** a meglévő alkalmazás nevét határozza meg. Fontos megérteni, hogy ez fordítja a szolgáltatás nevét a jegyzékben, és nem a Service Fabric alkalmazás nevét.
* **Az /exe** határozza meg azt a végrehajtható fájlt, `node.exe`amelyet a Service Fabricnek el kellene indítania, ebben az esetben.
* **A /ma** határozza meg a végrehajtható fájl elindításához használt argumentumot. Mivel a Node.js nincs telepítve, a Service Fabric nek el kell `node.exe bin/www`indítania a Node.js webkiszolgálót a végrehajtásával.  `/ma:'bin/www'`megmondja a csomagolási eszköznek, hogy a node.exe argumentumként használja. `bin/www`
* **Az /AppType** definiálja a Service Fabric alkalmazástípus nevét.

Ha a /target paraméterben megadott könyvtárat keresi, láthatja, hogy az eszköz az alábbi módon létrehozott egy teljesen működőképes Service Fabric csomagot:

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
A létrehozott ServiceManifest.xml most már rendelkezik egy szakaszt, amely leírja, hogyan kell elindítani a Node.js webkiszolgálót, amint az az alábbi kódrészletben látható:

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
Ebben a példában a Node.js webkiszolgáló figyeli a 3000-es portot, ezért frissítenie kell a végpontadatait a ServiceManifest.xml fájlban az alábbiak szerint.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>A MongoDB alkalmazás csomagolása
Most, hogy becsomagolta a Node.js alkalmazást, becsomagolhatja a MongoDB-t. Mint már említettük, a lépéseket, hogy menjen át most nem kifejezetten Node.js és MongoDB. Valójában minden olyan alkalmazásra vonatkoznak, amely egy Service Fabric-alkalmazásként van csomagolva.  

A MongoDB csomagjának csomagolásához győződjön meg arról, hogy a Mongod.exe és a Mongo.exe csomagot csomagolja. Mindkét bináris fájl a `bin` MongoDB telepítési könyvtárának könyvtárában található. A könyvtárszerkezet hasonlít az alábbihoz.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
A Service Fabric-nek el kell indítania a MongoDB-t `/ma` az alábbihoz hasonló paranccsal, ezért a MongoDB csomagolásakor a paramétert kell használnia.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Az adatok nem őrződnek meg csomóponthiba esetén, ha a MongoDB adatkönyvtárat a csomópont helyi könyvtárába helyezi. Az adatvesztés elkerülése érdekében használjon tartós tárolót, vagy valósítson meg egy MongoDB replikakészletet.  
>
>

A PowerShellben vagy a parancshéjban a csomagolási eszközt a következő paraméterekkel futtatjuk:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Annak érdekében, hogy mongoDB-t adjon a Service Fabric alkalmazáscsomaghoz, győződjön meg arról, hogy a /target parameter ugyanarra a könyvtárra mutat, amely már tartalmazza az alkalmazásjegyzéket a Node.js alkalmazással együtt. Győződjön meg arról is, hogy ugyanazt az ApplicationType nevet használja.

Tegyünk a könyvtárba, és vizsgáljuk meg, hogy mit hozott létre az eszköz.

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
Mint látható, az eszköz hozzáadott egy új mappát, MongoDB, a könyvtárba, amely tartalmazza a MongoDB binárisok. Ha megnyitja `ApplicationManifest.xml` a fájlt, láthatja, hogy a csomag most már tartalmazza a Node.js alkalmazást és a MongoDB-t is. Az alábbi kód az alkalmazásjegyzék tartalmát mutatja.

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
Az utolsó lépés az, hogy tegye közzé az alkalmazást a helyi Service Fabric-fürt az alábbi PowerShell-parancsfájlok használatával:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Miután az alkalmazás sikeresen közzé lett téve a helyi fürtben, hozzáférhet a Node.js alkalmazáshoz a Node.js alkalmazás\/szolgáltatásjegyzékében megadott porton - például http: /localhost:3000.

Ebben az oktatóanyagban látta, hogyan lehet egyszerűen csomagolni két meglévő alkalmazást egy Service Fabric-alkalmazásként. Azt is megtanulta, hogyan telepítheti azt a Service Fabric, hogy kihasználhassa a Service Fabric egyes funkciók, például a magas rendelkezésre állás és az állapotrendszer integrációja.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>További vendég végrehajtható fájlok hozzáadása egy meglévő alkalmazáshoz a Yeoman linuxos használatával

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket: 
1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa `yo azuresfguest:AddService` és adja meg a szükséges részleteket.

## <a name="next-steps"></a>További lépések
* Ismerje meg a tárolók üzembe helyezését a [Service Fabric és a tárolók áttekintése](service-fabric-containers-overview.md)
* [Minta a vendég végrehajtható fájl jának csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Minta két vendég végrehajtható fájlból (C# és nodejs) kommunikál nak az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
