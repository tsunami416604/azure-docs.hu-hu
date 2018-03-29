---
title: Az Azure Service Fabric szolgáltatás futtatásához egy AD-felhasználó vagy csoport |} Microsoft Docs
description: 'Útmutató: a szolgáltatás futtatásához egy Active Directory-felhasználó vagy csoport Service Fabric Windows önálló fürtön.'
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
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 1cf23a8f564553e65ac2c0fd34d44d81fe2327ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>A szolgáltatás futtatásához egy Active Directory-felhasználó vagy csoport
Azure Service Fabric használatával biztonságossá teheti a különböző felhasználói fiókok a fürtben futó alkalmazás számára. Így futó alkalmazást, még akkor is, a megosztott üzemeltetési környezetben, nagyobb biztonságot nyújt, egymástól. Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. A Windows Server önálló fürt futtatása a szolgáltatás egy [csoportos felügyelt szolgáltatásfiók (gMSA)](service-fabric-run-service-as-gmsa.md) vagy egy Active Directory felhasználó-, vagy a csoport egy RunAs-házirenddel. Vegye figyelembe, hogy ez az Active Directory helyszíni belül a tartomány és a nem Azure Active Directory (Azure AD) használ.

Tartományi felhasználó vagy csoport segítségével érheti el más erőforrások (például fájlmegosztások) a tartományban, amely engedéllyel rendelkezik.

A következő példa bemutatja az Active Directory-felhasználó nevű *tesztfelhasználó néven* a tartomány-tanúsítvány használatával titkosítja jelszó az úgynevezett *MyCert*. Használhatja a `Invoke-ServiceFabricEncryptText` PowerShell-parancsot a titkos titkosított szöveg létrehozásához. Lásd: [Service Fabric-alkalmazások a titkos kulcsok kezelése](service-fabric-application-secret-management.md) részleteiről.

Ha már telepítette a titkosított jelszót a helyi számítógép egy sávon kívüli módszer használatával a tanúsítvány titkos kulcsának (Azure-ban ez az Azure Resource Manager használatával). Ezt követően a Service Fabric telepíti a szolgáltatáscsomagnak a gép, esetén képes visszafejteni a titkos kulcsot, és (valamint a felhasználónév) hitelesítést és az Active Directory futtatásához használandó ezeket a hitelesítő adatokat.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Ha RunAs házirend vonatkozik egy szolgáltatás, és a szolgáltatás jegyzékfájl deklarálja a HTTP protokoll végpont erőforrások, meg kell adni egy **SecurityAccessPolicy**.  További információkért lásd: [rendelje hozzá a HTTP és HTTPS-végpont egy biztonsági házirend](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépésben olvassa el a következő cikkeket:
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
