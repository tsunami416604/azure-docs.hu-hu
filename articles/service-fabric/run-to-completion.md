---
title: RunToCompletion szemantika a Service Fabric-ben
description: A Service Fabric RunToCompletion szemantikája.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431292"
---
# <a name="runtocompletion"></a>RunToCompletion

A 7.1-es verziótól kezdve a Service Fabric támogatja **a RunToCompletion** szemantikát [a tárolók][containers-introduction-link] és [a vendég végrehajtható][guest-executables-introduction-link] alkalmazások számára. Ezek a szemantikák lehetővé teszik az alkalmazások és szolgáltatások, amelyek teljes feladatot, és kilép, ellentétben a mindig futó alkalmazások és szolgáltatások.

Mielőtt folytatná ezt a cikket, javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazásmodell][application-model-link] és a [Service Fabric üzemeltetési modell.][hosting-model-link]

> [!NOTE]
> A RunToCompletion szemantika jelenleg nem támogatott a [Reliable Services][reliable-services-link] programozási modellhasználatával írt szolgáltatások esetében.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion szemantika és specifikáció
A [ServiceManifest importálásakor a RunToCompletion][application-and-service-manifests-link]szemantika **executionpolicy-ként** adható meg. A megadott házirendet a ServiceManifest-t tartalmazó összes CodePackage örökli. A következő ApplicationManifest.xml kódrészlet példát mutat be.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** lehetővé teszi a következő két attribútumok:
* **Típus:** **RunToCompletion** jelenleg az egyetlen engedélyezett érték ehhez attribútum.
* **Újraindítás:** Ez az attribútum határozza meg a ServicePackage csomagot tartalmazó CodePackages újraindítási házirendet hiba esetén. A **nem nulla kilépési kóddal** kilépő codepackage sikertelennek minősül. Az attribútum engedélyezett értékei **onfailure** és **soha nem** **onfailure** az alapértelmezett.

Ha az újraindítási házirend **OnFailure**értékre van állítva, ha bármelyik CodePackage sikertelen **(nem nulla kilépési kód),** újraindul, és az ismételt hibák között visszalép. Ha az újraindítási házirend **beállítása Soha**, ha bármelyik CodePackage meghibásodik, a DeployedServicePackage telepítési állapota sikertelenként van **megjelölve,** de más CodePackages-ek folytathatják a végrehajtást. Ha a ServicePackage csomagot alkotó összes CodePackage sikeres befejezésre fut **(0 kilépési kód),** a DeployedServicePackage telepítési állapota **RanToCompletion**néven lesz megjelölve. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Példa a RunToCompletion szemantika használatával

Nézzünk meg egy teljes példát a RunToCompletion szemantika használatával.

> [!IMPORTANT]
> A következő példa feltételezi a [Windows-tárolóalkalmazások service fabric és docker használatával történő létrehozásának ismereteit.][containers-getting-started-link]
>
> Ez a példa mcr.microsoft.com/windows/nanoserver:1809 hivatkozik. A Windows Server-tárolók nem kompatibilisek a gazdaoperációs rendszer minden verziójában. További információ: [Windows container version compatibility](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

A következő ServiceManifest.xml egy két CodePackage-ből álló ServicePackage csomagot ír le, amely tárolókat jelöl. *RunToCompletionCodePackage1* csak bejelentkezik egy üzenetet, hogy **stdout** és kilép. *A RunToCompletionCodePackage2* egy ideig pingeli a visszacsatolási címet, majd **0**, **1** vagy **2**kilépési kóddal lép ki.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

A következő ApplicationManifest.xml a fent tárgyalt ServiceManifest.xml fájlon alapuló alkalmazást írja le. A *WindowsRunToCompletionServicePackage* **RunToCompletionServicePackage runtocompletionexecutionpolicy** **házirendje** az OnFailure újraindítási házirenddel van **meghatározva.** A *WindowsRunToCompletionServicePackage*aktiválásakor elindul az összetevő CodePackages. *A RunToCompletionCodePackage1* az első aktiváláskor sikeresen ki kell lépnie. A *RunToCompletionCodePackage2* azonban sikertelen lehet **(nem nulla kilépési kód),** amely esetben újraindul, mivel az újraindítási házirend **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>DeployedServicePackage telepítési állapotának lekérdezése
A DeployedServicePackage központi telepítési állapota lekérdezhető a PowerShellből [a Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] vagy a C# szolgáltatással [a FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link] használatával

## <a name="considerations-when-using-runtocompletion-semantics"></a>A RunToCompletion szemantika használatakor figyelembe vett szempontok

A következő pontokat kell megjegyezni az aktuális RunToCompletion támogatást.
* Ezek a szemantikák csak [tárolók][containers-introduction-link] és [vendég végrehajtható][guest-executables-introduction-link] alkalmazások támogatottak.
* A RunToCompletion szemantikával rendelkező alkalmazások frissítési forgatókönyvei nem engedélyezettek. A felhasználóknak szükség esetén törölniük kell és újra létre kell hozniuk az ilyen alkalmazásokat.
* A feladatátvételi események miatt a CodePackages a sikeres befejezés után, ugyanazon a csomóponton vagy a fürt más csomópontjain újra végrehajtható. Feladatátvételi események példák: csomópont-újraindítja, és a Service Fabric futásidejű frissítések egy csomóponton.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

