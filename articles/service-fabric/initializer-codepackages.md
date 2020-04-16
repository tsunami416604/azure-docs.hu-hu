---
title: Initializer codepackages a Service Fabric
description: A Service Fabric initializer codepackages című témakörének ismertetése.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430629"
---
# <a name="initializer-codepackages"></a>Initializer CodePackages

A 7.1-es verziótól kezdve a Service Fabric támogatja **a inicializáló codepackages** [tárolók][containers-introduction-link] és [a vendég végrehajtható][guest-executables-introduction-link] alkalmazások. Initializer CodePackages lehetőséget biztosít arra, hogy végre inicializálása a ServicePackage hatókörelőtt más CodePackages végrehajtás megkezdése előtt. Kapcsolatuk a ServicePackage hasonló ahhoz, amit a [SetupEntryPoint][setup-entry-point-link] egy CodePackage.

Mielőtt folytatná ezt a cikket, javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazásmodell][application-model-link] és a [Service Fabric üzemeltetési modell.][hosting-model-link]

> [!NOTE]
> Initializer CodePackages jelenleg nem támogatott a [Reliable Services][reliable-services-link] programozási modell használatával írt szolgáltatások.
 
## <a name="semantics"></a>Szemantika

Az Initializer CodePackage várhatóan sikeres befejezésre fut **(kilépési kód 0)**. A sikertelen inicializáló codepackage újraindul, amíg sikeresen befejeződik. Több Initializer CodePackages megengedett, és végre **a sikeres befejezését**, **egymás után**, egy **meghatározott sorrendben,** mielőtt más CodePackages a ServicePackage végrehajtás megkezdése.

## <a name="specifying-initializer-codepackages"></a>Initializer codepackages megadása
A CodePackage-t inicializálóként jelölheti meg, ha az **Initializer** attribútumot **true** értékre állítja a ServiceManifest-ben. Ha több Initializer CodePackages, azok végrehajtási sorrendje megadható az **ExecOrder** attribútum. **Az ExecOrder** nem negatív egész szám lehet, és csak initializer codepackages esetén érvényes. Initializer CodePackages alacsonyabb értékeket **ExecOrder** végre először. Ha az **ExecOrder** nincs megadva az Initializer CodePackage csomaghoz, a program 0 alapértelmezett értéket feltételez. Az **ExecOrder** azonos értékű Initializer CodePackages relatív végrehajtási sorrendje nincs megadva.

A következő ServiceManifest kódrészlet három CodePackages két jelölt inicializáló. A ServicePackage aktiválásakor az *InitCodePackage0* kerül végrehajtásra, mivel az **ExecOrder**legalacsonyabb értékkel rendelkezik. Az *InitCodePackage0*sikeres befejezésekor (0-as kilépési kód) az *InitCodePackage1* végrehajtása történik. Végül az *InitCodePackage1*sikeres befejezésekor a *WorkloadCodePackage* végrehajtásra kerül.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Példa befejezése initializer codepackages használatával

Nézzünk meg egy teljes példát initializer codepackages használatával.

> [!IMPORTANT]
> A következő példa feltételezi a [Windows-tárolóalkalmazások service fabric és docker használatával történő létrehozásának ismereteit.][containers-getting-started-link]
>
> Ez a példa mcr.microsoft.com/windows/nanoserver:1809 hivatkozik. A Windows Server-tárolók nem kompatibilisek a gazdaoperációs rendszer minden verziójában. További információ: [Windows container version compatibility](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

A következő ServiceManifest.xml a korábban ismertetett ServiceManifest kódrészletre épül. *InitCodePackage0*, *InitCodePackage1* és *WorkloadCodePackage* olyan codepackages, amelyek a tárolókat képviselik. Aktiváláskor először az *InitCodePackage0* kerül végrehajtásra. Naplózza az üzenetet egy fájlba, és kilép. Ezután *az InitCodePackage1 végrehajtásra* kerül, amely egy üzenetet is naplóz egy fájlba, és kilép. Végül a *WorkloadCodePackage* megkezdi a végrehajtást. Azt is bejelentkezik egy üzenetet egy fájlt, kimenetek tartalmát a fájl **stdout,** majd pingörökre.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

A következő ApplicationManifest.xml a fent tárgyalt ServiceManifest.xml fájlon alapuló alkalmazást írja le. Ne feledje, hogy **Volume** ugyanazt a kötetcsatlakoztatást adja meg az összes tárolóhoz, azaz a **C:\WorkspaceOnHost** mindhárom tárolón a **C:\WorkspaceOnContainer tárolóhoz** van csatlakoztatva. A nettó hatás az, hogy az összes tároló ugyanabba a naplófájlba ír, abban a sorrendben, ahogyan azok aktiválva vannak.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
A ServicePackage sikeres aktiválása után a **C:\WorkspaceOnHost\log.txt** fájl tartalmának a következőnek kell lennie.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>További lépések

A kapcsolódó információkat az alábbi cikkekben talál.

* [Szolgáltatás fabric és tárolók.][containers-introduction-link]
* [A Service Fabric és a vendég végrehajtható fájlok.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

