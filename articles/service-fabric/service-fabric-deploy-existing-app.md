---
title: Egy meglévő végrehajtható telepítése Azure Service fabric |} A Microsoft Docs
description: Ismerje meg, hogyan végrehajtható, vendégként meglévő alkalmazás becsomagolása úgy is üzembe helyezhető a Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: aljo
ms.openlocfilehash: fa883f819c53f57a8e281069b2a3db1c55efc6c6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480539"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Csomagolása és üzembe helyezése egy meglévő végrehajtható fájlt a Service Fabric
Amikor csomagolására, meglévő végrehajtható egy [futtatható vendégalkalmazás](service-fabric-guest-executables-introduction.md), választhat egy Visual Studio-projektsablont használja, vagy [hozza létre manuálisan az alkalmazáscsomag](#manually). A Visual Studiót használja, az alkalmazás-csomag szerkezete és a jegyzékfájlok hozza létre az új webesprojekt-sablon az Ön számára.

> [!TIP]
> A csomag egy meglévő Windows végrehajtható egy szolgáltatásba legegyszerűbben a Visual Studióval és a linuxon a Yeoman használatával
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>A Visual Studio használatával csomagolása és üzembe helyezése egy létező végrehajtható fájl
A Visual Studio nyújt segítséget nyújt a Service Fabric-fürt egy futtatható vendégalkalmazás üzembe helyezése a Service Fabric szolgáltatássablont.

1. Válasszon **fájl** > **új projekt**, és a egy Service Fabric-alkalmazás létrehozása.
2. Válasszon **futtatható Vendégalkalmazás** , a Szolgáltatássablon.
3. Kattintson a **Tallózás** válassza ki a mappát, és a végrehajtható fájlt, és töltse ki a többi paraméter szolgáltatás létrehozásához.
   * *Kódcsomag viselkedése Code*. A mappa tartalma minden átmásolása a Visual Studio-projekt Ez akkor hasznos, ha a végrehajtható fájl nem változik, akkor állítható. Ha a végrehajtható fájl módosítását, és dinamikusan csomópontmetrikák új buildek lehetőséget, válassza ki helyette a mappára mutató hivatkozás. Társított mappák is használhatja, ha a projekt létrehozása a Visual Studióban. Ez a forrás helye a projektben teszi, hogy a forrás-cél végrehajtható Vendég frissítse a mutató hivatkozásokat tartalmaz. Ezeket a frissítéseket az alkalmazáscsomag a build részévé válik.
   * *Program* adja meg a végrehajtható fájl, amely kell elindítani a szolgáltatást.
   * *Argumentumok* megadja az argumentumok, amelyek a végrehajtható fájl kell átadni. Argumentumok paraméterek listáját lehet.
   * *WorkingFolder* megadja a munkakönyvtárban történő, amelyet szeretne elindítani a folyamatot. Három értékeket adhat meg:
     * `CodeBase` Itt adhatja meg, hogy a munkakönyvtárban fog állítani az alkalmazáscsomagban kódjának könyvtárában (`Code` látható az előző fájlstruktúra könyvtár).
     * `CodePackage` Megadja, hogy a munkakönyvtárban történik-e állítani a legfelső szintű alkalmazáscsomag (`GuestService1Pkg` az előző fájlstruktúra látható).
     * `Work` Itt adhatja meg, hogy a fájlok kerülnek egy munkahelyi nevű alkönyvtárat.
4. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.
5. Ha a szolgáltatás a végpont kommunikációra van szüksége, akkor adhat hozzá a protokoll, port és típus objektumhoz a ServiceManifest.xml fájlban. Például: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Mostantól a csomag használatára és hibakeresés a Visual Studióban a megoldás közzététele a helyi fürtre küldött művelet. Ha elkészült, tegye közzé az alkalmazást egy távoli fürtön, vagy látogasson el a megoldás a forráskezelőhöz.
7. Olvasási [ellenőrizze a futó alkalmazás](#check-your-running-application) megtekintése a Service Fabric Explorerben futó vendég végrehajtható szolgáltatás megtekintéséhez.

Egy példa forgatókönyv, lásd: [az első Vendég végrehajtható-alkalmazás létrehozása Visual studióval](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Csomag a Yeoman használatával, és üzembe helyezése Linux rendszeren egy létező végrehajtható fájl

Az eljárás létrehozásához és telepítéséhez a Vendég végrehajtható linuxon megegyezik egy csharp vagy java-alkalmazás üzembe helyezése.

1. Írja be a terminálba a következőt: `yo azuresfguest`.
2. Adjon nevet az alkalmazásnak.
3. Adjon nevet a szolgáltatásnak, és adja meg az adatait, például a végrehajtható fájl elérési útja és a paraméterek kell hívható meg.

Yeoman alkalmazás csomagot hoz létre a megfelelő alkalmazás és a jegyzékfájlok telepítése, és távolítsa el a parancsfájlokat.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Manuálisan csomagolása és üzembe helyezése egy létező végrehajtható fájl
A folyamat manuális becsomagolásához egy Vendég végrehajtható fájlt az alábbi általános lépéseket alapul:

1. A csomag könyvtárstruktúrát létrehozása.
2. Adja hozzá az alkalmazás kódja és konfigurációs fájlokat.
3. A service manifest fájl szerkesztésével.
4. Az Alkalmazásjegyzék-fájl szerkesztése.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>A csomag directory-struktúra létrehozása
Leírtak szerint indítsa el a könyvtárstruktúra létrehozásával [egy Azure Service Fabric-alkalmazás becsomagolása](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Adja hozzá az alkalmazás kódja és a konfigurációs fájlok
Miután létrehozta a könyvtárstruktúra, az alkalmazás kódjában és konfigurációs fájlok mellett a kódot és a konfigurációverziókat könyvtárakat adhat hozzá. További címtárak vagy a kódot vagy konfigurációverziót könyvtárak alkönyvtárat is létrehozhat.

A Service Fabric does egy `xcopy` az alkalmazás gyökérkönyvtárában, így nem más, mint létrehozni a két leggyakoribb címtárat, a kód és a beállítások használata előre definiált struktúra tartalmát. (Választhat másik nevek Ha azt szeretné. További részletek találhatók a következő szakaszban.)

> [!NOTE]
> Győződjön meg arról, hogy tartalmazzák az összes a fájlokat és függőségeket, amelyet az alkalmazás. A Service Fabric másolja a tartalmat az összes csomóponton alkalmazáscsomag a fürt, ahol az alkalmazás szolgáltatásaihoz fog üzembe helyezni. A csomagnak tartalmaznia kell a kódot, amely az alkalmazás futtatásához szükséges. Nem feltételezi, hogy a függőségek már telepítve vannak.
>
>

### <a name="edit-the-service-manifest-file"></a>A service manifest fájl szerkesztése
A következő lépés, hogy az alábbi információkat a service manifest fájl szerkesztésével:

* A szolgáltatás típusának neve. Ez az azonosító, amely a Service Fabric-szolgáltatás azonosítására használ.
* A parancs használatával indítsa el az alkalmazást (ExeHost).
* Bármely szkript állíthatja be az alkalmazás (SetupEntrypoint) kell futtatni.

Az alábbiakban egy példát a egy `ServiceManifest.xml` fájlt:

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

A következő szakaszok haladnak át a fájlt, akkor frissítenie kell a különböző részeit.

#### <a name="update-servicetypes"></a>Update ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Kiválaszthatja, hogy bármely név, amelyeket szeretne `ServiceTypeName`. Az érték szerepel a `ApplicationManifest.xml` fájlt a szolgáltatás azonosítására.
* Adja meg `UseImplicitHost="true"`. Ez az attribútum tájékoztatja a Service Fabricet, hogy a szolgáltatás alapul egy önálló alkalmazás, így az összes Service Fabric kell tennie, hogy indítsa el az eszközt egy folyamatot, és figyelheti az állapotát.

#### <a name="update-codepackage"></a>Update CodePackage
A CodePackage elem azt határozza meg, a webszolgáltatás kódjához hely (és verzió).

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

A `Name` elem szolgál annak a könyvtárnak a nevét adja meg az alkalmazáscsomag, amely a szolgáltatás-kódot tartalmaz. `CodePackage` is rendelkezik a `version` attribútum. Adja meg a kódot verzióját használható, és potenciálisan is használható a szolgáltatást kód frissítése a Service fabric az alkalmazás életciklus-kezelési infrastruktúra használatával.

#### <a name="optional-update-setupentrypoint"></a>Nem kötelező: Update SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
A SetupEntryPoint elem segítségével bármilyen végrehajtható fájlt vagy a batch-fájlt ad meg kell végrehajtani, mielőtt elindítja a webszolgáltatás kódjához. Választható lépésként, így nem kell szerepelni, ha nincs inicializálás szükséges. A SetupEntryPoint hajtja végre minden alkalommal, amikor a szolgáltatás újraindításakor.

Nincs csak egy SetupEntryPoint, így a telepítő szkriptek kell csoportosítani egyetlen kötegfájlban, ha az alkalmazás telepítéséhez szükséges több parancsfájlt. A SetupEntryPoint hajthat végre bármely fájltípus: végrehajtható fájlok, parancsfájlok és PowerShell-parancsmagok. További részletekért lásd: [konfigurálása SetupEntryPoint](service-fabric-application-runas-security.md).

Az előző példában a SetupEntryPoint fut nevű fájlt egy kötegfájlban `LaunchConfig.cmd` található a `scripts` alkönyvtárába a kód (feltéve, hogy a WorkingFolder elem kódbázis értékre van állítva).

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

A `EntryPoint` elem a service manifest fájl a adja meg, hogy a szolgáltatás elindítására szolgál.

A `ExeHost` elem azt határozza meg, a végrehajtható fájl (és az argumentumok), amely a szolgáltatás elindításához használandó. Azt is megteheti a `IsExternalExecutable="true"` attribútumot `ExeHost` azt jelzi, hogy a program a kódcsomag kívüli külső végrehajtható. Például: `<ExeHost IsExternalExecutable="true">`.

* `Program` Megadja a végrehajtható fájl, amely el kell indulnia a szolgáltatás nevét.
* `Arguments` Itt adhatja meg kell adni a a végrehajtható fájl argumentumok. Argumentumok paraméterek listáját lehet.
* `WorkingFolder` Megadja a munkakönyvtárban történő, amelyet szeretne elindítani a folyamatot. Három értékeket adhat meg:
  * `CodeBase` Itt adhatja meg, hogy a munkakönyvtárban fog állítani az alkalmazáscsomagban kódjának könyvtárában (`Code` könyvtárat az előző fájlstruktúra a).
  * `CodePackage` Megadja, hogy a munkakönyvtárban történik-e állítani a legfelső szintű alkalmazáscsomag (`GuestService1Pkg` a fenti fájlstruktúrában).
    * `Work` Itt adhatja meg, hogy a fájlok kerülnek egy munkahelyi nevű alkönyvtárat.

A WorkingFolder hasznos a megfelelő munkakönyvtár beállítani, hogy a relatív elérési utakat az alkalmazás vagy az inicializálási parancsfájlok által használható.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Végpont frissítése, és regisztrálja a kommunikációhoz elnevezési szolgáltatásban
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Az előző példában a `Endpoint` elem azt határozza meg, hogy az alkalmazás képes figyelni a végpontok. Ebben a példában a Node.js-alkalmazás http 3000 port figyel.

Továbbá megkérheti, hogy tegye közzé ezt a végpontot a elnevezési szolgáltatásban, így más szolgáltatások a szolgáltatási végpont címe felderíthesse a Service Fabric. Ez lehetővé teszi, hogy tudni Vendég végrehajtható fájlok szolgáltatások közötti kommunikáció során.
A közzétett végpont címe a következő formában `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` és `PathSuffix` nem kötelező attribútum. `IPAddressOrFQDN` az IP-cím vagy a végrehajtható fájl elhelyezett lekérdezi a csomópont teljesen minősített tartománynevét, és azt számítja ki az Ön számára.

A következő példában a szolgáltatás üzembe helyezése után a Service Fabric Explorerben látni hasonló végpont `http://10.1.4.92:3000/myapp/` közzé a szolgáltatáspéldány számára. Vagy ha ez egy helyi számítógépre, akkor tekintse meg `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Ezeket a cím használható [fordított proxy](service-fabric-reverseproxy.md) szolgáltatások közötti kommunikáció során.

### <a name="edit-the-application-manifest-file"></a>Az Alkalmazásjegyzék-fájl szerkesztése
Miután konfigurálta a `Servicemanifest.xml` fájlt kell néhány módosítást, a `ApplicationManifest.xml` fájlt győződjön meg arról, hogy a megfelelő szolgáltatás típusa és a nevet használja.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
Az a `ServiceManifestImport` elem, megadhat egy vagy több olyan szolgáltatás, amelyet szeretne felvenni az alkalmazásba. A hivatkozott szolgáltatások `ServiceManifestName`, amely adja meg annak a könyvtárnak a nevét, a `ServiceManifest.xml` -fájl.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Naplózás beállítása
Futtatható vendégalkalmazás hasznos tudni jelennek meg, ismerje meg, ha az alkalmazás- és konfigurációs parancsfájlokat megjelenítése az esetleges hibákat.
A konzolátirányítás konfigurálható a `ServiceManifest.xml` fájlt a `ConsoleRedirection` elemet.

> [!WARNING]
> Soha ne használja a konzolon átirányítási házirendet egy alkalmazásban, amely az éles környezetben telepített, mert ez befolyásolhatja az alkalmazás feladatátvételt. *Csak* helyi fejlesztés és hibakeresés céljából használja.  
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

`ConsoleRedirection` használható konzol kimeneti (stdout és stderr) átirányítani egy működő könyvtárba. Ez lehetővé teszi a ellenőrizze, hogy nincsenek-e hibák a telepítés vagy a Service Fabric-fürt a kérelem végrehajtása közben.

`FileRetentionCount` azt határozza meg, hány fájl menti a munkakönyvtárban. 5-öt, például azt jelenti, hogy az előző öt végrehajtások a naplófájlokat a munkakönyvtárban vannak tárolva.

`FileMaxSizeInKb` Megadja a naplófájlok maximális méretét.

Naplófájlok a szolgáltatás munkakönyvtárával lesznek mentve. Határozza meg, ahol a fájlok találhatók, a Service Fabric Explorert használjuk melyik csomópont meghatározni a szolgáltatás fut, és mely munkakönyvtár használatban van. Ez a folyamat később Ez a cikk foglalkozik.

## <a name="deployment"></a>Környezet
Az utolsó lépés [az alkalmazás üzembe helyezéséhez](service-fabric-deploy-remove-applications.md). A következő PowerShell-parancsfájl megjeleníti az alkalmazás a helyi fejlesztési fürt üzembe helyezéséhez, és a egy új Service Fabric-szolgáltatás elindítása.

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
> [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) előtt a lemezképtároló másolása, ha a csomag nagyméretű, vagy sok fájlt. További információ [Itt](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

A Service Fabric-szolgáltatást is üzembe helyezhetők a különböző "konfigurációjú." Például egy vagy több példány is telepíthető, vagy úgy, hogy nincs-e a szolgáltatás a Service Fabric-fürt mindegyik csomópontján több példánya is telepíthető.

A `InstanceCount` paraméterében a `New-ServiceFabricService` parancsmag segítségével adja meg a szolgáltatás példányainak számát kell elindítani a Service Fabric-fürtben. Beállíthatja a `InstanceCount` érték végzi az alkalmazás típusától függően. A két leggyakoribb esetek a következők:

* `InstanceCount = "1"`. Ebben az esetben a szolgáltatás csak egy példánya telepítve van a fürtben. A Service Fabric-ütemező meghatározza, hogy melyik csomópont, a szolgáltatás fogja üzembe helyezhető.
* `InstanceCount ="-1"`. Ebben az esetben a szolgáltatás egy példánya telepítve van a Service Fabric-fürt minden csomópontján. Az eredmény a fürt minden csomópont esetében a szolgáltatás egy (és csak egy) példánya tapasztalja.

Előtér-alkalmazásokat (például egy REST-végpont), hasznos konfigurációját, mert az ügyfélalkalmazások számára szükséges "Csatlakozás" használata a végpont a fürt csomópontjainak valamelyik. Ez a konfiguráció is használható, ha például egy terheléselosztót a Service Fabric-fürt minden csomópontján csatlakoznak. A szolgáltatás, amely a fürt minden csomópontján fut majd leosztva ügyfél forgalmát.

## <a name="check-your-running-application"></a>Ellenőrizze a futó alkalmazás
A Service Fabric Explorerben azonosíthatja a csomópont, ahol a szolgáltatás fut-e. Ebben a példában futtatott csomópont1:

![Csomópont, ahol a szolgáltatás fut.](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Keresse meg a csomópont, és keresse meg az alkalmazást, láthatja a csomópont alapvető információk, beleértve a hely a lemezen.

![Hely a lemezen](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Ha a Server Explorer használatával Tallózás a címtárban, annak a munkakönyvtár és a szolgáltatás log mappában az alábbi képernyőképen látható módon: 

![Napló helye](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan vendégalkalmazás csomagolása és üzembe helyezése a Service Fabric az rendelkezik. Lásd az alábbi cikkeket a kapcsolódó információkat és feladatokat.

* [Minta csomagolás és a egy futtatható vendégalkalmazás üzembe helyezése a](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), és a telepítőre mutató előzetes verzióját a csomagolás eszköz
* [Minta két Vendég végrehajtható fájlok (C# és nodejs) keresztül kommunikáljon az elnevezési szolgáltatásban REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
* [Több futtatható vendégalkalmazás üzembe helyezése](service-fabric-deploy-multiple-apps.md)
* [Hozzon létre az első Service Fabric-alkalmazás a Visual Studio használatával](service-fabric-tutorial-create-dotnet-app.md)
