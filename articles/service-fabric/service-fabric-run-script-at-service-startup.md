---
title: Parancsfájl futtatása az Azure Service Fabric-szolgáltatás indításakor
description: Megtudhatja, hogyan konfigurálhatja a Service Fabric szolgáltatás beállítási belépési pontjának házirendjét, és hogyan futtathat parancsfájlt a szolgáltatás indítási idején.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464288"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Szolgáltatásindítási szkript futtatása helyi felhasználóként vagy rendszerfiókként
Mielőtt egy Service Fabric szolgáltatás végrehajtható elindul, szükség lehet bizonyos konfigurációs vagy telepítési munka futtatásához.  Például környezeti változók konfigurálása. Megadhatja a szolgáltatás végrehajtható fájljának indítása előtt futtatandó parancsfájlt a szolgáltatás jegyzékfájljában. Ha a szolgáltatás beállítási belépési pontjához RunAs házirendet konfigurál, módosíthatja, hogy a telepítő végrehajtható fájl melyik fiók alatt fusson.  Egy külön beállítási belépési pont lehetővé teszi, hogy rövid ideig magas szintű jogosultsággal rendelkező konfigurációt futtasson, így a szolgáltatásgazda végrehajtható fájljának nem kell nagy jogosultságokkal futnia hosszabb ideig.

A beállítási belépési pont (**SetupEntryPoint** a [szolgáltatásjegyzékben](service-fabric-application-and-service-manifests.md)) egy kiemelt belépési pont, amely alapértelmezés szerint ugyanazokat a hitelesítő adatokat futtatja, mint a Service Fabric (általában a *NetworkService-fiók)* bármely más belépési pont előtt. Az **EntryPoint** által megadott végrehajtható fájl általában a hosszú ideig futó szolgáltatásgazda. Az **EntryPoint** végrehajtható fájlja a **SetupEntryPoint** végrehajtható fájljának sikeres kilépése után fut. Az eredményül kapott folyamat ot figyeli és újraindítja, és újrakezdődik **a SetupEntryPoint** programmal, ha bármikor leáll vagy összeomlik. 

## <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása
A következő egy egyszerű szolgáltatásjegyzék-példa egy állapotmentes szolgáltatáshoz, amely a **SetupEntryPoint**szolgáltatásban a *MySetup.bat* telepítőparancsfájlt adja meg.  **Az argumentumok** segítségével argumentumokat ad át a parancsfájlnak futás közben.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>A szolgáltatás beállítási belépési pontjának házirendjének konfigurálása
Alapértelmezés szerint a szolgáltatás beállítási belépési pontja fájlfuttatása a Service Fabric (általában a *NetworkService-fiók)* hitelesítő adatai valamerre fut.  Az alkalmazásjegyzékben módosíthatja a biztonsági engedélyeket, hogy az indítási parancsfájlt helyi rendszerfiók vagy rendszergazdai fiók alatt futtassa.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>A házirend konfigurálása helyi rendszerfiók használatával
A következő alkalmazásjegyzék-példa bemutatja, hogyan konfigurálhatja a szolgáltatás beállítási belépési pontját a felhasználói rendszergazdai fiók (SetupAdminUser) alatt való futtatásra.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Először hozzon létre egy **Egyszerű felhasználó szakaszt** egy felhasználónévvel, például SetupAdminUser. A SetupAdminUser felhasználói fiók a Rendszergazdák rendszercsoport tagja.

Ezután a **ServiceManifestImport** szakaszban állítson be egy házirendet, hogy ezt a főt alkalmazza a **SetupEntryPoint**programra. Ez a házirend közli a Service Fabric, hogy amikor a **MySetup.bat** fájl fut, hogy kell futtatni, mint SetupAdminUser (rendszergazdai jogosultságokkal). Mivel *a* fő belépési pontra nem alkalmazott házirendet, a **MyServiceHost.exe** kódja a **NetworkService** fiók alatt fut. Ez az alapértelmezett fiók, amelyként az összes szolgáltatásbelépési pont fut.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>A házirend konfigurálása helyi rendszerfiókok használatával
Gyakran célszerű az indítási parancsfájlt rendszergazdai fiók helyett helyi rendszerfiókkal futtatni. A RunAs házirend nek a Rendszergazdák csoport tagjaként való futtatása általában nem működik megfelelően, mert a számítógépek alapértelmezés szerint engedélyezve vannak a felhasználói hozzáférés-vezérlés (UAC). Ilyen esetekben a javaslat az, hogy a SetupEntryPoint-ot localsystem-ként futtassa, ne pedig a Rendszergazdák csoportba hozzáadott helyi felhasználóként. A következő példa a SetupEntryPoint localsystem rendszerként való futtatását mutatja be:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Linux-fürtök esetén a szolgáltatás vagy a beállítási belépési pont **gyökérként**való futtatásához megadhatja az **AccountType típust** **LocalSystem**néven.

## <a name="run-a-script-from-the-setup-entry-point"></a>Parancsfájl futtatása a beállítás belépési pontjáról
Most adjon hozzá egy indítási parancsfájlt a projekthez, amely rendszergazdai jogosultságokkal fut. 

A Visual Studio jobb gombbal a szolgáltatási projektre kattintva vegyen fel egy új fájlt, a *MySetup.bat*fájlt.

Ezután győződjön meg arról, hogy a *MySetup.bat* fájl szerepel a szolgáltatási csomagban. Alapértelmezés szerint nem. Jelölje ki a fájlt, kattintson a jobb gombbal a helyi menü leválasztásához, és válassza a **Tulajdonságok parancsot.** A Tulajdonságok párbeszédpanelen győződjön meg arról, hogy a **Másolás a kimeneti könyvtárba** beállítás a Másolás beállításra van állítva, **ha újabb**. Tekintse meg a következő képernyőképet.

![Visual Studio CopyToOutput a SetupEntryPoint kötegfájlhoz][image1]

Most szerkeszti a *MySetup.bat* fájlt, és adja hozzá a következő parancsokat meg egy rendszer környezeti változó és kimeneti szöveges fájlt:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ezután építse létre és telepítse a megoldást egy helyi fejlesztési fürtre. A szolgáltatás megkezdése után, ahogy az a [Service Fabric Intézőben](service-fabric-visualizing-your-cluster.md)látható, láthatja, hogy a MySetup.bat fájl két módon sikeres volt. Nyisson meg egy PowerShell-parancssort, és írja be a következőt:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Ezután vegye figyelembe annak a csomópontnak a nevét, ahol a szolgáltatást telepítették és elindították a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)programban. Például a 2. Ezután keresse meg az alkalmazáspéldány munkamappáját, és keresse meg a **TestVariable**értékét megjelenítő out.txt fájlt. Ha például ezt a szolgáltatást a 2- es csomópontra telepítették, akkor a MyApplicationType ezen elérési útját is **megteheti:**

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>PowerShell-parancsok futtatása beállítási belépési pontról
A PowerShell a **SetupEntryPoint** pontból futtatásához futtathatja a **PowerShell.exe-t** egy powershell-fájlra mutató kötegfájlban. Először vegyen fel egy PowerShell-fájlt a szolgáltatási projektbe – például **a MySetup.ps1 fájlba.** Ne felejtse el beállítani a *Másolás, ha újabb* tulajdonságot úgy, hogy a fájl is szerepeljen a szolgáltatási csomagban. A következő példa egy mintakötegfájlt mutat be, amely elindítja a MySetup.ps1 nevű PowerShell-fájlt, amely beállít egy **TestVariable**nevű rendszerkörnyezeti változót.

A MySetup.bat fájl indításához:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

A PowerShell-fájlban adja hozzá a következőket egy rendszerkörnyezeti változó beállításához:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Alapértelmezés szerint a kötegfájl futtatásakor a fájlok **munka** nevű alkalmazásmappáját vizsgálja. Ebben az esetben, amikor a MySetup.bat fut, azt szeretnénk, hogy ez megtalálja a MySetup.ps1 fájlt ugyanabban a mappában, amely az **alkalmazáskód csomag** mappája. A mappa módosításához állítsa be a munkamappát:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Indítási parancsfájl hibakeresése a konzol átirányításával
Esetenként hibakeresés céljából hasznos, ha a konzol kimenetét egy beállítási parancsfájl futtatásából látja. A szolgáltatásjegyzék beállítási belépési pontján konzolátirányítási házirendet állíthat be, amely a kimenetet egy fájlba írja. A fájlkimenet a **napló** nevű alkalmazásmappába kerül, amely az alkalmazás telepítését és futtatását tartalmazó fürtcsomóponton található. 

> [!WARNING]
> Soha ne használja a konzolátirányítási házirendet az éles környezetben telepített alkalmazásokban, mert ez hatással lehet az alkalmazás feladatátvételre. Ezt *csak* helyi fejlesztési és hibakeresési célokra használja.  
> 
> 

A következő szolgáltatásjegyzék-példa bemutatja a konzol átirányítását FileRetentionCount értékkel:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Ha most módosítja a MySetup.ps1 **fájlt,** hogy echo parancsot írjon, az a kimeneti fájlba ír hibakeresési célokra:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> A parancsfájl hibakeresése után azonnal távolítsa el ezt a konzolátirányítási házirendet.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [További információ az alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
