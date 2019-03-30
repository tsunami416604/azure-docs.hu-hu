---
title: Egy Azure Service Fabric-szolgáltatás futtatása AD-felhasználó vagy csoport |} A Microsoft Docs
description: Ismerje meg, hogyan szolgáltatás Active Directory-felhasználó vagy csoport futtatása a önálló Service Fabric Windows fürtön.
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
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664743"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Szolgáltatás futtatása egy Active Directory-felhasználó vagy csoport
Egy Windows Server önálló fürtön egy szolgáltatás Active Directory-felhasználó vagy csoport futtató házirend segítségével is futtathatja.  Alapértelmezés szerint Service Fabric-alkalmazásokat, amely a Fabric.exe folyamatban fut, a fiók alatt fut. Futó alkalmazások a különböző fiókok, még akkor is, a közös üzemeltetési környezetben teszi őket egy biztonságosabb. Vegye figyelembe, hogy az Active Directory a helyszíni tartomány és a nem az Azure Active Directory (Azure AD) használja.  Szolgáltatásként is futtathatja egy [csoportosan felügyelt szolgáltatásfiók (gMSA)](service-fabric-run-service-as-gmsa.md).

Egy tartományi felhasználó vagy csoport használatával érheti el más erőforrásokat (például fájlmegosztások) a tartomány, amely rendelkezik engedélyekkel.

Az alábbi példa bemutatja egy Active Directory-felhasználó nevű *TestUser* azzal a tartománnyal egy tanúsítvány használatával titkosítja a jelszót nevű *MyCert*. Használhatja a `Invoke-ServiceFabricEncryptText` PowerShell-paranccsal hozhat létre a titkos titkosított szöveget. Lásd: [kezelése a Service Fabric-alkalmazások titkos kulcsainak](service-fabric-application-secret-management.md) részleteiről.

Telepítenie kell a tanúsítvány visszafejtése a jelszót a helyi számítógép egy sávon kívüli módszer használatával a titkos kulcs (az Azure-ban, ez a az Azure Resource Manageren keresztül). Ezt követően a Service Fabric üzembe helyezi a szervizcsomagot, a gép, esetén a titkos kulcs visszafejtésére és (valamint a felhasználónév) hitelesítést az Active Directoryval való futtatásra ezeket a hitelesítő adatokat.

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
> Ha egy futtató házirend vonatkozik egy szolgáltatás, és a szolgáltatásjegyzék deklarálja a HTTP protokollt végpont erőforrások, meg kell adni egy **SecurityAccessPolicy**.  További információkért lásd: [HTTP vagy HTTPS-végpontokat a biztonsági hozzáférési szabályzat hozzárendelése](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépésben olvassa el a következő cikkeket:
* [Az alkalmazásmodell megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
