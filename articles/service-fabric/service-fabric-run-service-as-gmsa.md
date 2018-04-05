---
title: A csoportosan felügyelt szolgáltatásfiók alatt egy Azure Service Fabric-szolgáltatás futtatásához |} Microsoft Docs
description: 'Útmutató: a szolgáltatás futtatásához, a csoportosan felügyelt szolgáltatásfiókot a Service Fabric Windows önálló fürtön.'
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: e22c656218abcd0564faec6fae6d6979f09b386a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiókként
Egy Windows Server önálló fürtön a szolgáltatás csoportos felügyelt szolgáltatásfiókok (gMSA) futtató házirend segítségével is futtathatja.  Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. Futó alkalmazások eltérő fiókkal, még akkor is, a megosztott üzemeltetési környezetben révén azokat egymástól biztonságosabb. Vegye figyelembe, hogy ez az Active Directory helyszíni belül a tartomány és a nem Azure Active Directory (Azure AD) használ. A csoportosan felügyelt szolgáltatásfiókot használ, nincs jelszót vagy az alkalmazás jegyzékében tárolt titkosított jelszót.  A szolgáltatás is futtathatja egy [Active Directory-felhasználó vagy csoport](service-fabric-run-service-as-ad-user-or-group.md).

A következő példa bemutatja, hogyan nevű csoportosan felügyelt szolgáltatásfiókok létrehozása *svc-teszt$*; központi telepítése a fürt csomópontjai; a felügyelt szolgáltatásfiók és a konfigurálása egyszerű felhasználónév.

Előfeltételek:
- A tartomány kell KDS-gyökérkulcsot.
- A tartományban kell lennie egy Windows Server 2012 vagy újabb működési szintjét.

1. Active Directory tartományi rendszergazdának létre csoportosan felügyelt szolgáltatás fiókja rendelkezik a `New-ADServiceAccount` parancsmagot, és győződjön meg arról, hogy a `PrincipalsAllowedToRetrieveManagedPassword` tartalmazza az összes, a service fabric-csomópont. `AccountName`, `DnsHostName`, és `ServicePrincipalName` egyedinek kell lennie.

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. A Service Fabric mindegyik fürtcsomópont (például `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), telepítése és tesztelése a csoportosan felügyelt szolgáltatásfiókot.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Az egyszerű, valamint a felhasználói hivatkozni RunAsPolicy konfigurálni.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> Ha RunAs házirend vonatkozik egy szolgáltatás, és a szolgáltatás jegyzékfájl deklarálja a HTTP protokoll végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy**.  További információkért lásd: [rendelje hozzá a HTTP és HTTPS-végpont egy biztonsági házirend](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépésben olvassa el a következő cikkeket:
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
