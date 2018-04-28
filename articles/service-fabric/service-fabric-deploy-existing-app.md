---
title: Azure Service Fabric egy létező végrehajtható fájl központi telepítése |} Microsoft Docs
description: Útmutató csomag vendégként végrehajtható, egy meglévő alkalmazást, a Service Fabric-fürt telepíthető.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell;mikhegn
ms.openlocfilehash: 9d1e2f801db2ed7edf262f8a5f221057ef791106
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Csomag és a Service Fabric egy létező végrehajtható fájl központi telepítése
Csomagolására egy létező végrehajtható fájl, amikor egy [Vendég végrehajtható](service-fabric-guest-executables-introduction.md), a Visual Studio-projektsablont használja válasszon vagy [hozzon létre manuálisan az alkalmazáscsomag](#manually). Visual Studio használatával, az alkalmazás csomag struktúra és a jegyzékfájlt jön létre az új projekt sablonban.

> [!TIP]
> A csomag egy meglévő Windows végrehajtható egy szolgáltatásba legkönnyebben használatához a Visual Studio és Yeoman használata Linux rendszeren
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Csomag és központi telepítése egy létező végrehajtható fájl a Visual Studio használatával
A Visual Studio nyújt segítséget a Service Fabric-fürt központi telepítése egy Vendég végrehajtható Service Fabric Szolgáltatássablon.

1. Válasszon **fájl** > **új projekt**, és a Service Fabric-alkalmazás létrehozása.
2. Válasszon **Vendég végrehajtható** szolgáltatássablonként.
3. Kattintson a **Tallózás** jelölje ki a végrehajtható fájl a mappában, majd töltse ki a többi paraméter létrehozni a szolgáltatást.
   * *Csomag viselkedés Code*. Állítható be a mappa a tartalom másolása a Visual Studio-projekt Ez akkor hasznos, ha a végrehajtható fájl nem változik. Ha a végrehajtható fájl módosítása, és szeretnénk, ha új buildek dinamikusan átvételéhez, dönthet úgy, ehelyett összekapcsolása a mappát. Csatolt mappákat használhatja, ha az alkalmazás-projekt létrehozása a Visual Studio. Ez a forráshely a projektben, így lehetséges, hogy a forrás cél végrehajtható Vendég frissítése mutató hivatkozásokat tartalmaz. Ezek a frissítések a build alkalmazáscsomag részét képezik.
   * *Program* határozza meg a végrehajtható fájlt, hogy fusson a szolgáltatás elindításához.
   * *Argumentumok* megadja a végrehajtható fájl továbbítandó argumentumokat. Azok a argumentumokkal paraméterek listáját.
   * *WorkingFolder* határozza meg a munkakönyvtár, amelyet szeretne elindítani a folyamatot. Három értékeket adhat meg:
     * `CodeBase` Megadja, hogy munkakönyvtára fog állítható be a kódot directory alkalmazáscsomagban (`Code` látható a fenti fájlstruktúra könyvtár).
     * `CodePackage` Megadja, hogy munkakönyvtára fog állítható be a legfelső szintű alkalmazáscsomag (`GuestService1Pkg` látható a fenti fájlstruktúra).
     * `Work` Megadja, hogy a fájlok vannak egy munkahelyi nevű alkönyvtárban.
4. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.
5. Ha a szolgáltatás egy olyan végpont-kommunikációra van szüksége, most adhat a protokoll, port és típusát a ServiceManifest.xml fájlt. Például: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Mostantól a csomag és a helyi fürtön hajtották művelet közzététele a Visual Studio megoldás hibakeresést. Ha készen áll, tegye közzé az alkalmazást egy távoli fürthöz, vagy ellenőrizze, hogy a megoldás a verziókövetési szerepel.
7. Olvasási [ellenőrizze futó alkalmazás](#check-your-running-application) való megjelenítése a Vendég végrehajtható szolgáltatás fut a Service Fabric Explorerben talál.

Egy példa a forgatókönyv, lásd: [az első Vendég végrehajtható-alkalmazás létrehozása a Visual Studio használatával](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Yeoman csomag és központi telepítése egy létező végrehajtható fájl Linux rendszeren

Létrehozása és telepítése egy Vendég végrehajtható Linux eljárás megegyezik a csharp vagy java-alkalmazás telepítése.

1. Írja be a terminálba a következőt: `yo azuresfguest`.
2. Adjon nevet az alkalmazásnak.
3. A szolgáltatás, és adja meg az adatokat, beleértve a végrehajtható fájl elérési útját és a paraméterek a kell meghívni.

Yeoman alkalmazás csomagot hoz létre a kívánt alkalmazást, és együtt fájlok telepítése, és távolítsa el a parancsfájlok.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Manuálisan csomag és egy létező végrehajtható fájl központi telepítése
A folyamat egy Vendég végrehajtható manuálisan csomagolási alapul a következő általános lépéseket:

1. Hozza létre a csomag könyvtárstruktúrát.
2. Adja hozzá az alkalmazás kódja és konfigurációs fájlokat.
3. A service manifest fájl szerkesztése.
4. Az Alkalmazásjegyzék-fájl szerkesztése.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>A csomag könyvtárstruktúrát létrehozása
Az előző, "Alkalmazás csomag fájlstruktúra." című részben leírtak szerint indítsa el a könyvtárstruktúra létrehozásával

### <a name="add-the-applications-code-and-configuration-files"></a>Az alkalmazás kódja és konfigurációs fájlok hozzáadása
Miután létrehozta a könyvtárstruktúra, az alkalmazás kódjában és konfigurációs fájljait a kódot és konfigurációs könyvtár alatt is hozzáadhat. További címtárak vagy a kódot vagy konfigurációverziót könyvtárak alkönyvtárába is létrehozhat.

A Service Fabric does egy `xcopy` az alkalmazás gyökérkönyvtárában, így nincs előre definiált struktúra létrehozása két legfontosabb könyvtárak, a kód és a beállítások nem használandó tartalmát. (Tárolhat különböző neveket, ha azt szeretné. További részletek találhatók a következő szakaszban.)

> [!NOTE]
> Győződjön meg arról, hogy az összes fájl és függőségeket, hogy az alkalmazást kell tartalmaznia. A Service Fabric másolja az összes olyan csomóponton alkalmazáscsomag tartalmának a fürt, ahol az alkalmazás szolgáltatások fog telepíteni. A csomagnak tartalmaznia kell az alkalmazás futtatásához szükséges kódot. Feltételezi azt, hogy a függőségek már telepítve vannak.
>
>

### <a name="edit-the-service-manifest-file"></a>A service manifest fájl szerkesztése
A következő lépés a service manifest fájl tartalmaznia a következő szerkesztése:

* A szolgáltatás típusának neve. Ez az egy Azonosítót, amely a Service Fabric szolgáltatás azonosítására használ.
* A parancs használatával indítsa el az alkalmazás (ExeHost).
* A parancsfájl, amely az alkalmazás (SetupEntrypoint) beállítása futtatnia kell.

Az alábbiakban egy példa egy `ServiceManifest.xml` fájlt:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

A következő szakaszok nyissa meg a fájlt, frissítenie kell a különböző részeit.

#### <a name="update-servicetypes"></a>Update ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Kiválaszthatja a nevét, aki a `ServiceTypeName`. Az érték szerepel a `ApplicationManifest.xml` fájl a szolgáltatás azonosítására.
* Adja meg `UseImplicitHost="true"`. Ez az attribútum be van állítva a Service Fabric, hogy a szolgáltatás alapul egy önálló alkalmazás, így minden Service Fabric kell tennie, a folyamat elindításához és állapotának figyelése.

#### <a name="update-codepackage"></a>Frissítés CodePackage
A CodePackage elem határozza meg, a szolgáltatás kód helye (és verzió).

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

A `Name` elem használatával adja meg annak a könyvtárnak a nevét, amely tartalmazza a szolgáltatáskód alkalmazásfájl egy alkalmazáscsomagban. `CodePackage` szintén tartalmazza a `version` attribútum. Adja meg a kódot verzióját is használható, és a szolgáltatás kód frissítése a Service Fabric az alkalmazás életciklus-kezelési infrastruktúra használatával is potenciálisan használható.

#### <a name="optional-update-setupentrypoint"></a>Választható lehetőség: Frissítés SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
A SetupEntryPoint elem használatával adja meg minden olyan végrehajtható- vagy kötegelt fájlt, amely a szolgáltatáskód elindítása előtt kell végrehajtani. Egy opcionális lépés, így nem kell szerepelni, ha nincs inicializálás szükséges. A SetupEntryPoint végrehajtása minden alkalommal, amikor a szolgáltatás újraindításakor.

Csak egy SetupEntryPoint van így telepítési parancsfájlokat kell csoportosítani egyetlen parancsfájlban, ha az alkalmazás telepítéséhez több parancsfájlt. A SetupEntryPoint végrehajtható fájl bármilyen: végrehajtható fájlok, a parancsfájlokat és a PowerShell-parancsmagokkal. További részletekért lásd: [konfigurálása SetupEntryPoint](service-fabric-application-runas-security.md).

Az előző példában a SetupEntryPoint egy kötegelt nevű fájlt futtat `LaunchConfig.cmd` található, amely a `scripts` (feltéve, hogy a WorkingFolder elem értéke CodeBase) kód alkönyvtára.

#### <a name="update-entrypoint"></a>Update EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

A `EntryPoint` szolgáltatás jegyzékfájl elem használatával adja meg, hogyan indítsa el a szolgáltatást. A `ExeHost` elemet adja meg a végrehajtható fájl (és az argumentumok), amely a szolgáltatás indítása kell használni.

* `Program` Megadja a nevét, a végrehajtható fájl, amely kell elindítani a szolgáltatást.
* `Arguments` Megadja a végrehajtható fájl továbbítandó argumentumokat. Azok a argumentumokkal paraméterek listáját.
* `WorkingFolder` a folyamat, amelyet szeretne elindítani a munkakönyvtár megadása Három értékeket adhat meg:
  * `CodeBase` Megadja, hogy munkakönyvtára fog állítható be a kódot directory alkalmazáscsomagban (`Code` előző fájlstruktúrájával könyvtár).
  * `CodePackage` Megadja, hogy munkakönyvtára fog állítható be a legfelső szintű alkalmazáscsomag (`GuestService1Pkg` előző fájlstruktúrájával).
    * `Work` Megadja, hogy a fájlok vannak egy munkahelyi nevű alkönyvtárban.

A WorkingFolder akkor hasznos, a megfelelő munkakönyvtár beállítani, hogy a relatív elérési utakat az alkalmazás vagy a inicializálási parancsfájlok által használható.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Végpontok frissítése és a kommunikációs szolgáltatás regisztrálása
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Az előző példában a `Endpoint` elem határozza meg, hogy az alkalmazás figyelheti a végpontok. Ebben a példában a Node.js-alkalmazás http 3000 porton figyel.

Továbbá kérje meg az ehhez a végponthoz a elnevezési szolgáltatás más szolgáltatások képes felderíteni a végpont címét, hogy ezt a szolgáltatást, a Service Fabric. Ez lehetővé teszi, hogy tudjon kommunikálni a Vendég futtatható szolgáltatások között.
A közzétett végpont címe a következő formában `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` és `PathSuffix` nem kötelező attribútum. `IPAddressOrFQDN` az IP-cím vagy a végrehajtható fájl helyezve lekérdezi a csomópont teljesen minősített tartománynevét, és az Ön számítja ki.

A következő példában a szolgáltatás telepítése után a Service Fabric Explorerben látható hasonló végpont `http://10.1.4.92:3000/myapp/` a szolgáltatáspéldány közzé. Vagy ha a helyi számítógépen, látható `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Ezekre a címekre használható [fordított proxy](service-fabric-reverseproxy.md) szolgáltatások közötti kommunikációt.

### <a name="edit-the-application-manifest-file"></a>Az Alkalmazásjegyzék-fájl szerkesztése
Miután konfigurálta a `Servicemanifest.xml` fájl, meg kell győződnie végrehajtott egyes módosításokat a `ApplicationManifest.xml` fájlban ellenőrizze, hogy a megfelelő service típusa és neve használja.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
Az a `ServiceManifestImport` elem, megadhat egy vagy több olyan szolgáltatás, amely az alkalmazásban szeretne. A hivatkozott szolgáltatások `ServiceManifestName`, amely annak a könyvtárnak a nevét adja meg ahol a `ServiceManifest.xml` -fájl.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>A naplózás beállítása
Az Vendég végrehajtható célszerű konzolnaplófájlokban juthat, ha az alkalmazás- és konfigurációs parancsfájlokat megjelenítése hibák láthatók.
Segítségével konfigurálható a `ServiceManifest.xml` fájl használata a `ConsoleRedirection` elemet.

> [!WARNING]
> Soha ne használja a konzol átirányítási házirend a kérelmet, mert ez hatással lehet az alkalmazás feladatátvételi éles környezetben telepített. *Csak* használja ezt a helyi fejlesztési és hibakeresési célra.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` segítségével (az stdout és az stderr) konzol kimenet átirányítása egy működő könyvtárba. Ez lehetővé teszi a ellenőrzése, hogy nincsenek-e hibák a telepítés vagy a Service Fabric-fürt a kérelem végrehajtása közben.

`FileRetentionCount` Meghatározza, hogy hány menti a munkakönyvtárat. 5-öt, például azt jelenti, hogy az előző öt végrehajtások a naplófájlokat a munkakönyvtár vannak tárolva.

`FileMaxSizeInKb` Megadja a naplófájlok maximális méretét.

Naplófájlok a szolgáltatás használatának könyvtárak egyikében mentése. A határozza meg, hol találhatók a fájlok használja a Service Fabric Explorer mely csomópontra meghatározni a szolgáltatás fut, és melyik munkakönyvtár használatban van. Ez a folyamat a cikk vonatkozik.

## <a name="deployment"></a>Környezet
Az utolsó lépés [az alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md). A következő PowerShell-parancsfájl bemutatja, hogyan telepíti az alkalmazást a helyi fejlesztési fürtöt, és egy új Service Fabric-szolgáltatás elindítása.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) az image store másolás, ha a csomag nagy, vagy sok fájl van előtt. További [Itt](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

A Service Fabric-szolgáltatás telepíthető különböző "konfigurációkban." Például, egy vagy több példányát is telepíthető, vagy úgy, hogy nincs-e a szolgáltatás a Service Fabric-fürt mindegyik csomópontján több példánya is telepíthető.

A `InstanceCount` paramétere a `New-ServiceFabricService` parancsmag használatával adja meg a szolgáltatás hány példánya a Service Fabric-fürt kell indítani. Beállíthatja a `InstanceCount` érték, amely elérhetővé tett alkalmazás típusától függően. A két leggyakoribb forgatókönyvek a következők:

* `InstanceCount = "1"`. Ebben az esetben a szolgáltatás csak egy példánya telepítve van a fürtben. Service Fabric-ütemező meghatározza, hogy melyik csomópont, a szolgáltatás telepíti az lesz.
* `InstanceCount ="-1"`. Ebben az esetben a szolgáltatás egy példánya telepítve van a Service Fabric-fürt minden csomópontján. Az eredmény a fürt minden csomópontja számára a szolgáltatás egy (és csak egy) példánya nehézségekkel.

Ez a beállítás egy hasznos az előtér-alkalmazások (például egy REST-végpont), mert ügyfélalkalmazások kell "Csatlakozás" sem használja a végpontot a fürtben található csomópontok. Ez a konfiguráció is használhatja, például a Service Fabric-fürt összes csomópontjára csatlakoznak olyan terheléselosztóhoz. A szolgáltatásban, hogy a fürt minden csomópontján fut. hozzáadás után terjeszthetők ügyfél forgalmát.

## <a name="check-your-running-application"></a>Ellenőrizze, fut-alkalmazás
A Service Fabric Explorerben a csomópont, amelyen fut a szolgáltatás azonosítására. Ebben a példában az fut az 1. csomópont:

![Csomópont, ahol a szolgáltatás fut.](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Keresse meg a csomópontra, és keresse meg az alkalmazás a, ha látja az alapvető csomópont információt, beleértve a helye a lemezen.

![Hely a lemezen](./media/service-fabric-deploy-existing-app/locationondisk2.png)

A Server Explorer tallózással könyvtárat, ha található a munkakönyvtárat, és a szolgáltatás naplómappában, az alábbi képernyőfelvételen látható módon: 

![Napló helye](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben rendelkezik megismerte a Vendég végrehajtható csomagot, majd központilag telepítenie kell a Service Fabric. Tekintse meg a következő cikkeket azzal kapcsolatos információkat és feladatokat.

* [Minta csomagolás és központi telepítése egy Vendég végrehajtható](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), beleértve az előzetes verzióját a csomagolás eszköz mutató hivatkozás
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) kapcsolaton keresztül kommunikáljon a Naming szolgáltatás REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [A Visual Studio használatával első Service Fabric-alkalmazás létrehozása](service-fabric-create-your-first-application-in-visual-studio.md)
