---
title: Parancsfájl futtatása az Azure Service Fabric-szolgáltatás elindulásakor |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálja a Service Fabric szolgáltatás telepítője a belépési pont egy házirendet, és futtatni egy parancsfájlt szolgáltatás indítási ideje.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3fe22d8bb52fa5f45ce5f1cdc7b860d1ce295a71
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Szolgáltatásindítási szkript futtatása helyi felhasználóként vagy rendszerfiókként
A Service Fabric-szolgáltatás végrehajtható indítása előtt lehet néhány konfigurációs vagy a telepítő feladat futtatásához szükséges.  Például konfigurálása környezeti változókat. Megadhatja, hogy a parancsfájl futtatása előtt a szolgáltatás jegyzékben a szolgáltatás indítása a szolgáltatás végrehajtható. A szolgáltatás beállítása belépési pont módosíthatja, hogy melyik fiók RunAs házirend konfigurálásával futtatható a telepítés alatt fut.  Egy külön telepítő belépési pont lehetővé teszi egy rövid ideig magas-privilged konfigurációs futtatását, így a végrehajtható szolgáltatásgazda huzamosabb ideig magas jogosultsággal rendelkező futtatásához nem kell.

A telepítő belépési pont (**SetupEntryPoint** a a [szolgáltatás jegyzékfájl](service-fabric-application-and-service-manifests.md)) egy jogosultsági szintű belépési pontja, amely alapértelmezés szerint fut, ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a  *NetworkService* fiók) más belépési pont előtt. A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatás gazdagép legyen. A **EntryPoint** végrehajtható fájl futtatása a **SetupEntryPoint** végrehajtható fájl sikeresen kilép. Az eredményül kapott folyamat figyeli, és újra kell indítani, és újra kezdődik **SetupEntryPoint** Ha valaha is leáll, vagy összeomlik. 

## <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása
Az alábbiakban egy egyszerű szolgáltatás, amely meghatározza a telepítési parancsfájl állapot nélküli szolgáltatás jegyzék példa egy *MySetup.bat* szolgáltatási **SetupEntryPoint**.  **Argumentumok** argumentumok átadása a parancsfájl futása során használatos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>A telepítő belépési pont vonatkozó házirend konfigurálása
Alapértelmezés szerint a szolgáltatás beállítása belépési pont végrehajtható fájl fut. ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a *NetworkService* fiók).  Az alkalmazásjegyzékben, a biztonsági engedélyek módosíthatja a helyi rendszerfiók vagy a rendszergazdai fiók alatt indítási parancsfájl futtatásához.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurálja a házirendet a helyi rendszer fiók használatával
A következő alkalmazás jegyzékének példa bemutatja, hogyan konfigurálhatja a telepítő-belépési pont futtatásához rendszergazdai fiókkal (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Először hozzon létre egy **rendszerbiztonsági tagok** egy felhasználónevet, pl. SetupAdminUser szakasz ismerteti. A SetupAdminUser felhasználói fiók tagja a rendszergazdák rendszer csoport.

Ezután bontsa a **ServiceManifestImport** területen házirend alkalmazása a rendszerbiztonsági konfigurálása **SetupEntryPoint**. Ez a házirend közli a Service Fabric, hogy ha a **MySetup.bat** fájl is fut (rendszergazdai jogosultságokkal) SetupAdminUser futnia kell. Mivel rendelkezik *nem* házirend alkalmazása a fő belépési ponthoz, a kód **MyServiceHost.exe** fut a rendszer a **NetworkService** fiók. Ez az az alapértelmezett fiókot, amelyet a szolgáltatás összes belépési pont alatt futnak.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>A házirend konfigurálása a helyi rendszer fiók használatával
Gyakran célszerű a rendszergazdai fiók helyett a helyi rendszerfiók indítási parancsfájl futtatásához. A RunAs házirend általában a Rendszergazdák csoport tagjaként fut nem működik jól, mert a felhasználói hozzáférés felügyelete (UAC) engedélyezve van, alapértelmezetten a számítógépek rendelkeznek. Ilyen esetekben a javaslat a SetupEntryPoint LocalSystem fiókként való futtatásra, ahelyett, hogy a rendszergazdák csoportba felvett helyi felhasználóként. A következő példa bemutatja a LocalSystem fiókként való futtatásra SetupEntryPoint beállítása:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> Linux-fürtök esetén a szolgáltatás vagy a telepítő futtatásához belépési pont mint **legfelső szintű**, megadhatja a **AccountType** , **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Futtassa a parancsfájlt a telepítő belépési pont
A parancsfájl indítása most hozzáadása a projekthez futtatásához rendszergazdai jogosultságokkal. 

A Visual Studióban, kattintson a jobb gombbal a projekt, és adjon hozzá egy új fájlt nevű *MySetup.bat*.

Ezt követően győződjön meg arról, hogy a *MySetup.bat* fájl tartalmazza a szervizcsomagban. Alapértelmezés szerint nincs. Válassza ki a fájlt, kattintson a jobb gombbal a helyi menü segítségével, és válassza a **tulajdonságok**. A Tulajdonságok párbeszédpanelen győződjön meg arról, hogy **másolása a kimeneti könyvtárba** értéke **másolhatja, ha újabb**. Tekintse meg a következő képernyőképet.

![A Visual Studio CopyToOutput SetupEntryPoint kötegelt fájl][image1]

Most szerkesztése a *MySetup.bat* fájlt, és adja hozzá a következő parancsokat a rendszer környezeti változót, és a kimeneti szövegfájl:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ezt követően hozza létre, és a megoldás telepítése egy helyi fejlesztési fürtöt. Miután a szolgáltatás elindult, ahogy az [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), láthatja, hogy a két módon sikeres volt-e a MySetup.bat fájlt. Nyisson meg egy PowerShell-parancssort, és írja be:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Jegyezze fel a csomópont, ahol a szolgáltatás telepítése és elindítása neve [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Például a csomópont 2. Ezután keresse meg az alkalmazás példány munkahelyi mappát értékét bemutató out.txt fájlt **TestVariable**. Például, ha a szolgáltatás telepítve van a csomópont 2, majd lépjen az elérési útját a **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>A telepítő belépési pontról PowerShell-parancsok futtatása
A PowerShell futtatásához a **SetupEntryPoint** pont, futtathatja **PowerShell.exe** parancsfájlban, amely egy PowerShell-fájlra mutat. Először adjon hozzá egy PowerShell fájlt például a projekt-- **MySetup.ps1**. Ne felejtse el, állítsa be a *másolhatja, ha újabb* tulajdonságot, hogy a fájl a szolgáltatáscsomagot is megtalálhatók. A következő példa bemutatja egy minta-kötegfájl MySetup.ps1, amelyek a nevezett rendszer környezeti változó beállítása nevű PowerShell fájl elinduló **TestVariable**.

A PowerShell fájl indítása MySetup.bat:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

A PowerShell fájlban adja hozzá a következő, a rendszer környezeti változó beállítása:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Alapértelmezés szerint a parancsfájl futtatása azt ellenőrzi, hogy az alkalmazás mappájában nevű **működik** fájlok. Ebben az esetben, amikor MySetup.bat fut, történjen a MySetup.ps1 fájl található abban a mappában, amely az alkalmazás **kódcsomag** mappa. Ez a mappa módosításához állítsa a munkamappa:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>A konzol helyileg átirányítással indítási parancsfájlok hibakeresése
Alkalmanként célszerű a hibakereséshez a konzol kimeneti telepítési parancsfájl futásának megtekintéséhez. A telepítő belépési pont a jegyzékben szolgáltatás, amely a kimeneti fájlba írja a konzol átirányítási házirendet állíthat be. A kimeneti fájl beíródik nevű alkalmazás mappát **napló** a fürtcsomóponton, ha az alkalmazás üzemel, és futtassa. 

> [!WARNING]
> Soha ne használja a konzol átirányítási házirend a kérelmet, mert ez hatással lehet az alkalmazás feladatátvételi éles környezetben telepített. *Csak* használja ezt a helyi fejlesztési és hibakeresési célra.  
> 
> 

A következő service manifest példa bemutatja, a konzol átirányítása FileRetentionCount érték beállítása:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Ha most módosítja a MySetup.ps1 fájl, amelybe írni egy **Echo** parancsban, ez fogja írni a kimeneti fájl hibakeresési célra:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> A parancsfájl hibakeresése azonnal távolítsa el a konzol átirányítási házirend.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Tudnivalók az alkalmazás és szolgáltatás biztonsága](service-fabric-application-and-service-security.md)
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
