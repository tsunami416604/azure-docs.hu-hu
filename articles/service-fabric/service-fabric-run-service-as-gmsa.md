---
title: Azure Service Fabric-szolgáltatás futtatása gMSA-fiókkal | Microsoft Docs
description: Megtudhatja, hogyan futtathat szolgáltatást gMSA egy Service Fabric Windows önálló fürtön.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d00eceffebb222196191a389058c0feb496e169a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307648"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiókként
Egy Windows Server önálló fürtön futtathatja a szolgáltatást egy csoportosan felügyelt szolgáltatásfiók (gMSA) használatával futtató házirenddel.  Alapértelmezés szerint Service Fabric alkalmazások a Fabric. exe folyamat alatt futó fiók alatt futnak. Az alkalmazások különböző fiókokban való futtatása, még egy megosztott környezetben is, biztonságosabbá teszi őket egymástól. Vegye figyelembe, hogy ez Active Directory a helyi tartományon belül, és nem Azure Active Directory (Azure AD). GMSA használatával nincs jelszó vagy titkosított jelszó az alkalmazás jegyzékfájljában.  A szolgáltatásokat [Active Directory felhasználóként vagy csoportként](service-fabric-run-service-as-ad-user-or-group.md)is futtathatja.

Az alábbi példa bemutatja, hogyan hozhat létre egy *SVC-test $* nevű gMSA-fiókot. a felügyelt szolgáltatásfiók üzembe helyezése a fürt csomópontjain; és az egyszerű felhasználónév konfigurálása.

Előfeltételek:
- A tartománynak szüksége van egy KDS-gyökér kulcsra.
- A tartományban legalább egy Windows Server 2012 (vagy R2) tartományvezérlőnek kell lennie.

1. Rendelkezzen egy Active Directory tartományi rendszergazdával, és hozzon létre egy csoportosan felügyelt szolgáltatásfiókot `New-ADServiceAccount` a `PrincipalsAllowedToRetrieveManagedPassword` parancsmagot, és győződjön meg arról, hogy az tartalmazza az összes Service Fabric-fürtcsomópont csomópontot. `AccountName`, `DnsHostName` és`ServicePrincipalName` egyedinek kell lennie.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. A Service Fabric fürtcsomópontokon (például `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) telepítse és tesztelje a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurálja az egyszerű felhasználónevet, és konfigurálja a RunAsPolicy a felhasználóra való hivatkozáshoz.
    
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
> Ha futtató házirendet alkalmaz egy szolgáltatásra, és a szolgáltatás jegyzékfájlja deklarálja a végponti erőforrásokat a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy**.  További információ: [biztonsági hozzáférési házirend társítása http-és HTTPS-végpontokhoz](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Következő lépésként olvassa el a következő cikkeket:
* [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
