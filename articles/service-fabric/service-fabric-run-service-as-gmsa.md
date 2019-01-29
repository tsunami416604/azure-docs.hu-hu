---
title: Futtassa az Azure Service Fabric-szolgáltatás a csoportosan felügyelt szolgáltatásfiókok |} A Microsoft Docs
description: Ismerje meg, hogyan szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiók egy Service Fabric Windows önálló fürtön.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 56a7478ab7221a1ccb4394a245540d3181e4ad8e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155578"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiókként
Egy Windows Server önálló fürtön egy szolgáltatás csoportosan felügyelt szolgáltatásfiókok (gMSA) futtató házirend segítségével is futtathatja.  Alapértelmezés szerint Service Fabric-alkalmazásokat, amely a Fabric.exe folyamatban fut, a fiók alatt fut. Futó alkalmazások a különböző fiókok, még akkor is, a közös üzemeltetési környezetben teszi őket egy biztonságosabb. Vegye figyelembe, hogy az Active Directory a helyszíni tartomány és a nem az Azure Active Directory (Azure AD) használja. Csoportosan felügyelt szolgáltatásfiókot használ, nincs jelszót vagy az alkalmazásjegyzékben tárolt titkosított jelszót.  Szolgáltatásként is futtathatja egy [Active Directory-felhasználó vagy csoport](service-fabric-run-service-as-ad-user-or-group.md).

Az alábbi példa bemutatja, hogyan hozhat létre csoportosan felügyelt szolgáltatásfiókok nevű *svc-tesztelési$*; a fürtcsomópontok; központi telepítése a felügyelt szolgáltatásfiókok, és hogyan konfigurálhatja az egyszerű felhasználónév.

Előfeltételek:
- A tartományhoz kell KDS-gyökérkulcs.
- A tartományban kell lennie a Windows Server 2012 vagy újabb működési szintje.

1. Egy Active Directory tartományi rendszergazda, hozzon létre egy csoportosan felügyelt szolgáltatás fiókja rendelkezik a `New-ADServiceAccount` parancsmagot, és ellenőrizze, hogy a `PrincipalsAllowedToRetrieveManagedPassword` tartalmazza az összes uzly clusteru service fabric. `AccountName`, `DnsHostName`, és `ServicePrincipalName` egyedinek kell lennie.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. A Service Fabric mindegyik fürtcsomópont (például `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), telepítése és tesztelése a csoportosan felügyelt szolgáltatásfiókot.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Az egyszerű felhasználónév, valamint a RunAsPolicy való hivatkozáshoz a felhasználó konfigurálni.
    
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
> Ha egy futtató házirend vonatkozik egy szolgáltatás, és a szolgáltatásjegyzék deklarálja a HTTP protokollt végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy**.  További információkért lásd: [HTTP vagy HTTPS-végpontokat a biztonsági hozzáférési szabályzat hozzárendelése](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged--> A következő lépésben olvassa el a következő cikkeket:
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
