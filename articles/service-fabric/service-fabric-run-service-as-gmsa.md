---
title: Azure Service Fabric-szolgáltatás futtatása gMSA-fiók alatt
description: Megtudhatja, hogy miként futtathat egy szolgáltatást csoportosan felügyelt szolgáltatásfiókként (gMSA) a Service Fabric önálló fürtjein.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988396"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiókként

Windows Server önálló fürtön futtathat egy szolgáltatást *csoport kezelt szolgáltatásfiókként* (gMSA) *RunAs* házirend használatával.  Alapértelmezés szerint a Service Fabric-alkalmazások `Fabric.exe` a folyamat alatt futó fiók alatt futnak. Az alkalmazások különböző fiókok alatt történő futtatása, még egy megosztott üzemeltetett környezetben is, biztonságosabbá teszi őket egymástól. A gMSA használatával nincs jelszó vagy titkosított jelszó az alkalmazásjegyzékben.  A szolgáltatást [Active Directory-felhasználóként vagy -csoportként](service-fabric-run-service-as-ad-user-or-group.md)is futtathatja.

A következő példa bemutatja, hogyan hozhat létre egy *svc-Test$* nevű gMSA-fiókot, hogyan telepítheti a felügyelt szolgáltatásfiókot a fürtcsomópontokra, és hogyan konfigurálhatja a felhasználó egyszerű felhasználóját.

> [!NOTE]
> GMSA használatával egy önálló Service Fabric-fürt höz a tartományon belüli Active Directory (nem pedig az Azure Active Directory (Azure AD)) szükséges.

Előfeltételek:

- A tartománynak Szüksége van egy KDS gyökérkulcsra.
- A tartományban legalább egy Windows Server 2012 (vagy R2) tartományvezérlőnek kell lennie.

1. Az Active Directory tartományi rendszergazdájával hozzon `New-ADServiceAccount` létre egy csoport által `PrincipalsAllowedToRetrieveManagedPassword` felügyelt szolgáltatásfiókot a parancsmag használatával, és győződjön meg arról, hogy az tartalmazza az összes Service Fabric-fürtcsomópontot. `AccountName`, `DnsHostName`, `ServicePrincipalName` és egyedinek kell lennie.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. A Service Fabric fürtcsomópontjainak minden egyes `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`csomópontján (például) telepítse és tesztelje a gMSA-t.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurálja az egyszerű felhasználót, és állítsa be `RunAsPolicy` a felhasználóra való [hivatkozást.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Ha RunAs házirendet alkalmaz egy szolgáltatásra, és a szolgáltatásjegyzék deklarálja a végponterőforrásait a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy házirendet.**  További információt a [Http- és HTTPS-végpontokhoz szükséges biztonsági hozzáférési házirend hozzárendelése című témakörben talál.](service-fabric-assign-policy-to-endpoint.md)
>

A következő cikkek végigvezetik a következő lépéseken:

- [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
- [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
- [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
