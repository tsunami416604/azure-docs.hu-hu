---
title: Meglévő végrehajtható fájl üzembe helyezése az Azure Service Fabric ben
description: Ismerje meg, hogyan csomagolhat egy meglévő alkalmazást vendég végrehajtható fájlként, így egy Service Fabric-fürtre telepíthető.
ms.topic: conceptual
ms.date: 07/02/2017
ms.openlocfilehash: cdbc965d0e8ec4a8f42fbe438b8ac6ddfe05a1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377106"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Meglévő végrehajtható fájl becsomagolása és üzembe helyezése a Service Fabric számára
Ha egy meglévő végrehajtható fájlt [vendég végrehajtható fájlként](service-fabric-guest-executables-introduction.md)csomagol, választhat, hogy Visual Studio-projektsablont használ, vagy [manuálisan hozza létre az alkalmazáscsomagot](#manually). A Visual Studio használatával az alkalmazáscsomag-struktúrát és a jegyzékfájlokat az új projektsablon hozta létre.

> [!TIP]
> A meglévő Windows-végrehajtható fájlok szolgáltatásba való becsomagolásának legegyszerűbb módja a Visual Studio és a Linux használata a Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Meglévő végrehajtható fájl csomagolása és üzembe helyezése a Visual Studióval
A Visual Studio egy Service Fabric szolgáltatássablont biztosít, amely segít egy vendég végrehajtható fájl üzembe helyezésében egy Service Fabric-fürtben.

1. Válassza az**Új projekt** **fájllehetőséget,** > és hozzon létre egy Service Fabric-alkalmazást.
2. Válassza **a Vendég végrehajtható fájl** szolgáltatássablonként lehetőséget.
3. A **Tallózás gombra** kattintva jelölje ki a végrehajtható fájlt tartalmazó mappát, és töltse ki a szolgáltatás létrehozásához a többi paramétert.
   * *A kódcsomag viselkedése*. Beállítható úgy, hogy a mappa összes tartalmát átmásolja a Visual Studio Projektprogramba, ami akkor hasznos, ha a végrehajtható fájl nem változik. Ha azt várja, hogy a végrehajtható fájl megváltozik, és dinamikusan szeretné felvenni az új buildeket, választhatja azt is, hogy inkább a mappához kapcsolódik. A csatolt mappákat az alkalmazásprojekt Visual Studio alkalmazásban történő létrehozásakor használhatja. Ez a projekten belül a forráshelyre mutat, lehetővé téve a vendég végrehajtható fájl frissítését a forráscélban. Ezek a frissítések a builden lévő alkalmazáscsomag részévé válnak.
   * *A program* megadja azt a végrehajtható fájlt, amelyet a szolgáltatás elindításához futtatni kell.
   * *Az argumentumok* azokat az argumentumokat határozzák meg, amelyeket át kell adni a végrehajtható fájlnak. Ez lehet az argumentumokkal rendelkező paraméterek listája.
   * *A WorkingFolder* az elindítandó folyamat munkakönyvtárát adja meg. Három értéket adhat meg:
     * `CodeBase`megadja, hogy a munkakönyvtár az alkalmazáscsomag kódkönyvtárára lesz`Code` állítva ( az előző fájlstruktúrában látható könyvtár).
     * `CodePackage`megadja, hogy a munkakönyvtár az alkalmazáscsomag gyökerére`GuestService1Pkg` lesz állítva ( az előző fájlstruktúrában látható).
     * `Work`megadja, hogy a fájlok a munka nevű alkönyvtárba kerüljenek.
4. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.
5. Ha a szolgáltatásnak végpontra van szüksége a kommunikációhoz, most hozzáadhatja a protokollt, portot és típust a ServiceManifest.xml fájlhoz. Például: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Most már használhatja a csomagot, és közzéteheti a helyi fürttel kapcsolatos műveleteket a megoldás Visual Studióban történő hibakeresésével. Ha készen áll, közzéteheti az alkalmazást egy távoli fürtön, vagy beadhatja a megoldást a forrásvezérléshez.
7. Olvassa [el a futó alkalmazás,](#check-your-running-application) hogyan tekintheti meg a szolgáltatás fabric-kezelőben futó vendég végrehajtható szolgáltatás megtekintéséhez.

Például forgatókönyv: Az első vendég végrehajtható alkalmazás létrehozása a Visual Studio használatával című [témakörben.](quickstart-guest-app.md)

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>A Yeoman használata egy meglévő végrehajtható fájl becsomagolásához és üzembe helyezéséhez Linuxon

A linuxos vendégvégrehajtható fájl létrehozásának és üzembe helyezésének eljárása megegyezik egy csharp vagy java alkalmazás telepítésével.

1. Írja be a terminálba a következőt: `yo azuresfguest`.
2. Adjon nevet az alkalmazásnak.
3. Nevezze el a szolgáltatást, és adja meg a részleteket, beleértve a végrehajtható fájl elérési útját és azokat a paramétereket, amelyekkel meg kell hívni.

Yeoman létrehoz egy alkalmazáscsomagot a megfelelő alkalmazással és jegyzékfájlokkal, valamint a telepítési és eltávolítási parancsfájlokkal.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Meglévő végrehajtható fájl manuális csomagolása és üzembe helyezése
A vendég végrehajtható fájlok manuális csomagolásának folyamata a következő általános lépéseken alapul:

1. Hozza létre a csomagkönyvtár-struktúrát.
2. Adja hozzá az alkalmazás kódját és konfigurációs fájljait.
3. Szerkessze a szolgáltatásjegyzék-fájlt.
4. Az alkalmazásjegyzékfájl szerkesztése.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>A csomagkönyvtár-struktúra létrehozása
Első sorban létrehozhatja a könyvtárstruktúrát, az Azure Service Fabric alkalmazás csomagja című dokumentumban [leírtak szerint.](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps)

### <a name="add-the-applications-code-and-configuration-files"></a>Az alkalmazás kódjának és konfigurációs fájljainak hozzáadása
Miután létrehozta a könyvtárstruktúrát, hozzáadhatja az alkalmazás kódját és konfigurációs fájljait a kód és a konfigurációs könyvtárak alatt. A kód vagy a konfigurációs könyvtárak alatt további könyvtárakat vagy alkönyvtárakat is létrehozhat.

A Service `xcopy` Fabric az alkalmazás gyökérkönyvtárának tartalmát végzi el, így nincs más előre definiált struktúra, amelyet két felső könyvtár, kód és beállítások létrehozása hozhat létre. (Akkor válasszon különböző neveket, ha akarod. További részletek a következő szakaszban találhatók.)

> [!NOTE]
> Győződjön meg arról, hogy tartalmazza az összes fájlt és függőséget, amelyre az alkalmazásnak szüksége van. A Service Fabric az alkalmazáscsomag tartalmát másolja a fürt minden olyan csomópontjára, ahol az alkalmazás szolgáltatásai üzembe kerülnek. A csomagnak tartalmaznia kell az alkalmazás által futtatandó összes kódot. Ne feltételezze, hogy a függőségek már telepítve vannak.
>
>

### <a name="edit-the-service-manifest-file"></a>A szolgáltatásjegyzék-fájl szerkesztése
A következő lépés a szolgáltatásjegyzékfájl szerkesztése, hogy az a következő információkat tartalmazza:

* A szolgáltatástípus neve. Ez egy olyan azonosító, amelyet a Service Fabric egy szolgáltatás azonosítására használ.
* Az alkalmazás elindításához használandó parancs (ExeHost).
* Az alkalmazás (SetupEntrypoint) beállításához futtatni kívánt parancsfájlok.

Az alábbi példa egy `ServiceManifest.xml` fájlra mutat be:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

A következő szakaszok a fájl különböző frissítéseit ismertetik.

#### <a name="update-servicetypes"></a>ServiceTypes frissítése
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* A kívánt nevet `ServiceTypeName`választhatja. Az érték a `ApplicationManifest.xml` fájlban a szolgáltatás azonosítására szolgál.
* Adja meg a következőt: `UseImplicitHost="true"`. Ez az attribútum azt mondja a Service Fabric, hogy a szolgáltatás egy önálló alkalmazáson alapul, így a Service Fabric kell tennie, hogy indítsa el a folyamatot, és az állapotának figyelése.

#### <a name="update-codepackage"></a>CodePackage frissítése
A CodePackage elem a szolgáltatás kódjának helyét (és verzióját) határozza meg.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Az `Name` elem a szolgáltatás kódját tartalmazó alkalmazáscsomag könyvtárának megadására szolgál. `CodePackage`is van `version` az attribútum. Ez a kód verziójának megadására használható, és potenciálisan a szolgáltatás kódjának frissítésére is használható a Service Fabric alkalmazáséletciklus-kezelési infrastruktúrájának használatával.

#### <a name="optional-update-setupentrypoint"></a>Nem kötelező: SetupEntrypoint frissítése
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
A SetupEntryPoint elem olyan végrehajtható vagy kötegfájl megadására szolgál, amelyet a szolgáltatás kódjának elindítása előtt végre kell hajtani. Ez egy választható lépés, így nem kell belefoglalni, ha nincs szükség inicializálásra. A SetupEntryPoint végrehajtása a szolgáltatás minden újraindításakor történik.

Csak egy SetupEntryPoint van, ezért a beállítási parancsfájlokat egyetlen kötegfájlba kell csoportosítani, ha az alkalmazás beállítása több parancsfájlt igényel. A SetupEntryPoint bármilyen típusú fájlt képes végrehajtani: végrehajtható fájlokat, kötegfájlokat és PowerShell-parancsmagokat. További információt a [SetupEntryPoint konfigurálása című](service-fabric-application-runas-security.md)témakörben talál.

Az előző példában a SetupEntryPoint egy `LaunchConfig.cmd` kötegfájl takarásban található, amely a `scripts` kódkönyvtár alkönyvtárában található (feltéve, hogy a WorkingFolder elem CodeBase értékre van állítva).

#### <a name="update-entrypoint"></a>EntryPoint frissítése
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

A `EntryPoint` szolgáltatásjegyzékfájl eleme határozza meg a szolgáltatás indításának módját.

Az `ExeHost` elem megadja a végrehajtható fájlt (és argumentumokat), amelyeket a szolgáltatás elindításához kell használni. Az `IsExternalExecutable="true"` attribútum ot `ExeHost` tetszés szerint hozzáadhatja annak jelzésére, hogy a program a kódcsomagon kívül külső végrehajtható fájl. Például: `<ExeHost IsExternalExecutable="true">`.

* `Program`megadja annak a végrehajtható fájlnak a nevét, amelynek el kell indítania a szolgáltatást.
* `Arguments`megadja azokat az argumentumokat, amelyeket át kell adni a végrehajtható fájlnak. Ez lehet az argumentumokkal rendelkező paraméterek listája.
* `WorkingFolder`megadja az elindítandó folyamat munkakönyvtárát. Három értéket adhat meg:
  * `CodeBase`megadja, hogy a munkakönyvtár az alkalmazáscsomag kódkönyvtárára (az előző fájlstruktúra`Code` könyvtárára) lesz állítva.
  * `CodePackage`megadja, hogy a munkakönyvtár az alkalmazáscsomag gyökerére`GuestService1Pkg` legyen beállítva ( az előző fájlstruktúrában).
    * `Work`megadja, hogy a fájlok a munka nevű alkönyvtárba kerüljenek.

A WorkingFolder a megfelelő munkakönyvtár beállításához hasznos, hogy a relatív elérési utakat az alkalmazás vagy az inicializálási parancsfájlok is használhatják.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Végpontok frissítése és regisztráció az elnevezési szolgáltatással a kommunikációhoz
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Az előző példában `Endpoint` az elem megadja azokat a végpontokat, amelyeket az alkalmazás figyelhet. Ebben a példában a Node.js alkalmazás http-n hallgatja a 3000-es porton.

Továbbá kérheti a Service Fabric, hogy tegye közzé ezt a végpontot az elnevezési szolgáltatás, így más szolgáltatások is felderítheti a végpont címét, hogy a szolgáltatás. Ez lehetővé teszi, hogy képes legyen kommunikálni a vendég végrehajtható szolgáltatások között.
A közzétett végpontcím az `UriScheme://IPAddressOrFQDN:Port/PathSuffix`űrlapból áll. `UriScheme`és `PathSuffix` választható attribútumok. `IPAddressOrFQDN`A végrehajtható csomópont IP-címe vagy teljesen minősített tartományneve, és az Ön számára kerül kiszámításra.

A következő példában, miután a szolgáltatás üzembe helyezése, a Service `http://10.1.4.92:3000/myapp/` Fabric Explorer egy végpont hasonló a szolgáltatáspéldány közzétett. Vagy ha ez egy helyi `http://localhost:3000/myapp/`gép, látod .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Ezeket a címeket [fordított proxyval](service-fabric-reverseproxy.md) használhatja a szolgáltatások közötti kommunikációhoz.

### <a name="edit-the-application-manifest-file"></a>Az alkalmazásjegyzékfájl szerkesztése
A `Servicemanifest.xml` fájl konfigurálása után néhány módosítást kell `ApplicationManifest.xml` végrehajtania a fájlon, hogy a megfelelő szolgáltatástípust és nevet használja.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
Az `ServiceManifestImport` elemben megadhat egy vagy több szolgáltatást, amelyet be szeretne venni az alkalmazásba. A szolgáltatásokra `ServiceManifestName`a program a tanévre `ServiceManifest.xml` hivatkozik, amely megadja annak a könyvtárnak a nevét, amelyben a fájl található.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Naplózás beállítása
A vendég végrehajtható fájlok esetében hasznos, ha a konzolnaplókat láthatja, hogy az alkalmazás és a konfigurációs parancsfájlok hibát jeleznek-e.
A konzolátirányítás az `ServiceManifest.xml` `ConsoleRedirection` elem használatával konfigurálható a fájlban.

> [!WARNING]
> Soha ne használja a konzolátirányítási házirendet az éles környezetben telepített alkalmazásokban, mert ez hatással lehet az alkalmazás feladatátvételre. Ezt *csak* helyi fejlesztési és hibakeresési célokra használja.  
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

`ConsoleRedirection`a konzol kimenetének (stdout és stderr) átirányítására használható egy munkakönyvtárba. Ez lehetővé teszi annak ellenőrzését, hogy nincsenek-e hibák az alkalmazás beállítása vagy végrehajtása során a Service Fabric-fürtben.

`FileRetentionCount`meghatározza, hogy hány fájl kerül mentésre a munkakönyvtárba. Az 5 érték például azt jelenti, hogy az előző öt végrehajtás naplófájljai a munkakönyvtárban tárolódnak.

`FileMaxSizeInKb`a naplófájlok maximális méretét adja meg.

A naplófájlok a szolgáltatás egyik munkakönyvtárában kerülnek mentésre. A fájlok helyének meghatározásához a Service Fabric Intéző segítségével határozza meg, hogy a szolgáltatás melyik csomóponton fut, és melyik munkakönyvtárat használja. Ez a folyamat a cikk későbbi részében található.

## <a name="deployment"></a>Környezet
Az utolsó lépés az [alkalmazás telepítése.](service-fabric-deploy-remove-applications.md) A következő PowerShell-parancsfájl bemutatja, hogyan telepítheti az alkalmazást a helyi fejlesztési fürtre, és hogyan indíthat el egy új Service Fabric-szolgáltatást.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Tömörítse a csomagot,](service-fabric-package-apps.md#compress-a-package) mielőtt a képtárolóba másolna, ha a csomag nagy vagy sok fájlt tartalmaz. Bővebben [itt](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

A Service Fabric szolgáltatás különböző "konfigurációkban" telepíthető. Például lehet telepíteni egy vagy több példányban, vagy úgy is telepíthető, hogy a szolgáltatás egy példánya a Service Fabric-fürt minden csomópontján.

A `InstanceCount` `New-ServiceFabricService` parancsmag paramétere annak meghatározására szolgál, hogy a szolgáltatás hány példányát kell elindítani a Service Fabric-fürtben. Az értéket `InstanceCount` a telepítő alkalmazás típusától függően állíthatja be. A két leggyakoribb forgatókönyv a következő:

* `InstanceCount = "1"`. Ebben az esetben a szolgáltatásnak csak egy példánya van telepítve a fürtben. A Service Fabric ütemezője határozza meg, hogy a szolgáltatás melyik csomóponton lesz üzembe helyezve.
* `InstanceCount ="-1"`. Ebben az esetben a szolgáltatás egy példánya a Service Fabric-fürt minden csomópontján telepítve van. Az eredmény a szolgáltatás egy (és csak egy) példánya a fürt minden csomópontján.

Ez egy hasznos konfiguráció előtér-alkalmazások (például egy REST-végpont), mert az ügyfélalkalmazások kell "csatlakozni" a fürt bármely csomópontjához a végpont használatához. Ez a konfiguráció akkor is használható, ha például a Service Fabric-fürt összes csomópontja egy terheléselosztóhoz csatlakozik. Az ügyfélforgalom ezután elosztható a fürt összes csomópontján futó szolgáltatás között.

## <a name="check-your-running-application"></a>A futó alkalmazás ellenőrzése
A Service Fabric Intézőben azonosítsa azt a csomópontot, ahol a szolgáltatás fut. Ebben a példában a Node1-en fut:

![Az a csomópont, ahol a szolgáltatás fut](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Ha megkeresi a csomópontot, és megkeresi az alkalmazást, megjelennek az alapvető csomópontinformációk, beleértve annak helyét a lemezen.

![Hely a lemezen](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Ha a Kiszolgálókezelővel keresi a könyvtárat, megtalálhatja a munkakönyvtárat és a szolgáltatás naplómappáját, ahogy az az alábbi képernyőképen látható: 

![A napló helye](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan csomagolhatja a vendég végrehajtható és üzembe helyezheti a Service Fabric. A kapcsolódó információkat és feladatokat az alábbi cikkekben talál.

* [Minta a vendég végrehajtható fájl csomagolásához és üzembe helyezéséhez,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)beleértve a csomagolási eszköz előzetes kiadására mutató hivatkozást
* [Minta két vendég végrehajtható fájlból (C# és nodejs) kommunikál nak az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Az első Service Fabric-alkalmazás létrehozása a Visual Studio használatával](service-fabric-tutorial-create-dotnet-app.md)
