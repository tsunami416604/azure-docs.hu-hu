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
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 7dc467d9977d536011e1c3fcf663fc335f90a492
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>A szolgáltatás futtatásához egy Active Directory-felhasználó vagy csoport
Egy Windows Server önálló fürtön a szolgáltatás az Active Directory-felhasználó vagy csoport RunAs házirend segítségével is futtathatja.  Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. Futó alkalmazások eltérő fiókkal, még akkor is, a megosztott üzemeltetési környezetben révén azokat egymástól biztonságosabb. Vegye figyelembe, hogy ez az Active Directory helyszíni belül a tartomány és a nem Azure Active Directory (Azure AD) használ.  A szolgáltatás is futtathatja egy [csoportos felügyelt szolgáltatásfiók (gMSA)](service-fabric-run-service-as-gmsa.md).

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
