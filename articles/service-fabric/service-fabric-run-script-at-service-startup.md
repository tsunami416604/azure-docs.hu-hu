---
title: Parancsfájl futtatása az Azure Service Fabric-szolgáltatás indításakor |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja a házirendet egy Service Fabric szolgáltatásbeállítás belépési pontjának, és futtasson egy szkriptet a szolgáltatás indítása óta eltelt idő.
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
ms.openlocfilehash: 3ae43f7427996f8be15b22fec4406bbdfe8aa4fe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838442"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Szolgáltatásindítási szkript futtatása helyi felhasználóként vagy rendszerfiókként
Végrehajtható egy Service Fabric-szolgáltatás indítása előtt lehet néhány konfigurációs vagy a telepítő munkahelyi futtatásához szükséges.  Például a környezeti változók konfigurálása. Megadhatja, hogy a parancsfájl futtatása előtt a szolgáltatásjegyzékben a szolgáltatás indítása végrehajtható a szolgáltatást. Az a szolgáltatásbeállítás belépési pontjának módosíthatja, hogy melyik fiók RunAs szabályzat konfigurálásával a végrehajtható telepítő alatt fut.  Egy külön szolgáltatásbeállítás belépési pontjának lehetővé teszi magas szintű jogosultságokat igénylő konfigurációs egy rövid ideig futnak, így a végrehajtható szolgáltatásgazda hosszabb ideig magas szintű jogosultságokkal rendelkező futtatásához nem szükséges.

A beállítási belépési pontra (**SetupEntryPoint** a a [Szolgáltatásjegyzék](service-fabric-application-and-service-manifests.md)) egy emelt szintű belépési pontja, amely alapértelmezés szerint ugyanazokat a hitelesítő adatokat, mint a Service Fabric-fut (általában a  *NetworkService* fiók) bármilyen egyéb belépési pont előtt. A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatásgazda van. A **EntryPoint** végrehajtható fájl futtatása a **SetupEntryPoint** végrehajtható fájl sikeresen kilép. Az eredményül kapott folyamat figyeli, és újra kell indítani, és újra kezdődik **SetupEntryPoint** Ha valaha leáll vagy összeomlik. 

## <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása
Az alábbiakban egy egyszerű szolgáltatás jegyzékfájl például állapotmentes szolgáltatás, amely meghatározza a telepítési parancsfájl a *MySetup.bat* a szolgáltatásban **SetupEntryPoint**.  **Argumentumok** argumentumok átadása a szkript futása során használatos.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>A házirend-szolgáltatásbeállítás belépési pontjának konfigurálása
Alapértelmezés szerint a szolgáltatás a telepítő belépési pontjának végrehajtható fájlja fut, a ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a *NetworkService* fiók).  Az alkalmazásjegyzékben módosíthatja a biztonsági engedélyek a helyi rendszerfiókot vagy egy rendszergazdai fiókot az indítási szkript futtatásához.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>A szabályzat konfigurálása a helyi rendszer fiók használatával
A következő application manifest példa bemutatja, hogyan konfigurálhatja a szolgáltatásbeállítás belépési pontjának futtatásához rendszergazdai fiókkal (SetupAdminUser).

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

Először hozzon létre egy **rendszerbiztonsági tagok** egy felhasználónevet, pl. SetupAdminUser szakaszt. A SetupAdminUser felhasználói fiók tagja a rendszergazdák rendszercsoportjához.

A következő a **ServiceManifestImport** szakaszban, a alkalmazni az egyszerű házirend konfigurálása **SetupEntryPoint**. Ez a szabályzat tájékoztatja a Service Fabricet, hogy amikor a **MySetup.bat** fájl fut (rendszergazdai jogosultságokkal) SetupAdminUser-ként kell futnia. Mivel rendelkezik *nem* egy szabályzatot alkalmazza az hlavní vstupní BOD, a kód a **MyServiceHost.exe** fut a rendszer a **NetworkService** fiókot. Ez az az alapértelmezett fiókot, amely az összes szolgáltatás belépési pont alatt futnak.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>A szabályzat konfigurálása a helyi rendszer fiók használatával
Gyakran előfordul célszerű a rendszergazdai fiók helyett a helyi rendszer fiók használatával indítási parancsfájl futtatásához. A RunAs szabályzat általában a Rendszergazdák csoport tagjaként futtató nem működik jól, mivel a számítógépeken a felhasználói hozzáférés felügyelete (UAC) alapértelmezés szerint engedélyezve van. Ezekben az esetekben a javaslat a SetupEntryPoint LocalSystem fiókként való futtatásra, helyett a rendszergazdák csoporthoz hozzáadott helyi felhasználóként. Az alábbi példa bemutatja, hogy a LocalSystem fiókként való futtatásra SetupEntryPoint beállítása:

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
> A Linux-fürtökre, egy szolgáltatás vagy a telepítő futtatásához belépési pontja **legfelső szintű**, megadhatja a **AccountType** , **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Futtassa a parancsfájlt a beállítási belépési pontra
Adjon hozzá egy indítási szkriptet a projekt futtatásához rendszergazdai jogosultságokkal. 

A Visual Studióban kattintson a jobb gombbal a projekt, és adjon hozzá egy új fájlt *MySetup.bat*.

Ezután ellenőrizze, hogy a *MySetup.bat* fájl megtalálható a csomagban. Alapértelmezés szerint nincs. Válassza ki a fájlt, kattintson a jobb gombbal a helyi menü lekérni, és válassza **tulajdonságok**. A Tulajdonságok párbeszédpanelen ellenőrizze, hogy **Copy to Output Directory** értékre van állítva **másolás, ha újabb**. Tekintse meg a következő képernyőképet.

![A Visual Studio CopyToOutput SetupEntryPoint batch-fájl][image1]

Most már szerkesztheti a *MySetup.bat* fájlt, és adja hozzá a következő parancsokat egy rendszer környezeti változót, és a kimenetet egy szövegfájlba:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ezután hozhat létre, és a megoldás üzembe helyezése egy helyi fejlesztési fürtön. Miután a szolgáltatás elindult, ahogyan az [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), láthatja, hogy a két módon sikeres volt-e a MySetup.bat fájlt. Nyisson meg egy PowerShell-parancssort, írja be:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Jegyezze fel a csomópont, ahol a szolgáltatás üzembe helyezett és lépések nevét [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Ha például a csomópont 2. Ezután keresse meg az alkalmazás példány munkahelyi mappát, amely az értékei out.txt fájl kereséséhez **TestVariable**. Például, ha ez a szolgáltatás telepítve van a csomópont 2, majd megnyithatja ezt az elérési út a **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>A szolgáltatásbeállítás belépési pontjának a PowerShell-parancsok futtatása
A PowerShell futtatásához a **SetupEntryPoint** futtathatja, pont **PowerShell.exe** fájlt egy kötegfájlban, amely egy PowerShell-fájlra mutat. Első lépésként adjon hozzá egy PowerShell-fájlt például a projekt-- **MySetup.ps1**. Állítsa be a vágólapra a *másolás, ha újabb* tulajdonságot, hogy a fájl is megtalálható a csomagban. Az alábbi példa bemutatja egy kötegelt mintafájlt, amely elindítja a nevű MySetup.ps1, amely nevű rendszer környezeti változó beállítása PowerShell fájlba **TestVariable**.

MySetup.bat elindítani egy PowerShell-fájlra:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

A PowerShell fájlban adja hozzá a következő, a rendszer környezeti változó beállítása:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Alapértelmezés szerint a parancsfájl futtatásakor úgy tűnik, az alkalmazás mappájában nevű **működik** fájlok. Ebben az esetben, amikor MySetup.bat fut, szeretnénk juthat a MySetup.ps1 fájl ugyanabban a mappában, amely az alkalmazás **kódcsomag** mappát. Ez a mappa módosításához állítsa a munkamappában:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Egy indítási parancsfájl használatával helyben konzolátirányítás hibakeresése
Bizonyos esetekben hasznos, hibakeresési célra, tekintse meg a konzol kimenete a telepítési parancsfájl futtatását. A beállítási belépési pontra a szolgáltatásjegyzékben, amely a kimenetet egy fájlba írja be a konzol átirányítás házirendjében is megadhatja. Az alkalmazás nevű mappát a kimeneti fájl írt **log** a fürtcsomópontra, ahol az alkalmazás üzembe helyezését és futtatását. 

> [!WARNING]
> Soha ne használja a konzolon átirányítási házirendet egy alkalmazásban, amely az éles környezetben telepített, mert ez befolyásolhatja az alkalmazás feladatátvételt. *Csak* helyi fejlesztés és hibakeresés céljából használja.  
> 
> 

A következő service manifest példa bemutatja, a konzol-átirányítás FileRetentionCount értékű beállítást:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Ha most módosítja MySetup.ps1 soubor Pro zápis egy **Echo** parancsot, és ez fogja írni a kimeneti fájl hibakeresési célra:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Miután a szkript hibakeresést, azonnal távolítsa el a konzol-átirányítás házirend.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [További információ az alkalmazás és szolgáltatás biztonsága](service-fabric-application-and-service-security.md)
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
