---
title: Azure Service Fabric-szolgáltatás futtatása AD-felhasználóként vagy-csoportként
description: Megtudhatja, hogyan futtathat egy szolgáltatást Active Directory felhasználóként vagy csoportként egy Service Fabric Windows önálló fürtön.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464244"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Szolgáltatás futtatása Active Directory felhasználóként vagy csoportként
Egy Windows Server önálló fürtön futtathatja a szolgáltatást Active Directory felhasználóként vagy csoportként egy futtató házirend használatával.  Alapértelmezés szerint Service Fabric alkalmazások a Fabric. exe folyamat alatt futó fiók alatt futnak. Az alkalmazások különböző fiókokban való futtatása, még egy megosztott környezetben is, biztonságosabbá teszi őket egymástól. Vegye figyelembe, hogy ez Active Directory a helyi tartományon belül, és nem Azure Active Directory (Azure AD).  A szolgáltatásokat [csoportosan felügyelt szolgáltatásfiókként (gMSA)](service-fabric-run-service-as-gmsa.md)is futtathatja.

Tartományi felhasználó vagy csoport használatával elérheti a tartomány (például fájlmegosztás) más erőforrásait, amelyek engedélyeket kaptak.

Az alábbi példa egy *tesztfelhasználó* nevű Active Directory felhasználót mutat be a tartomány jelszavával, amelyet a *MyCert*nevű tanúsítvány használatával titkosított. A titkos titkosítási szöveg létrehozásához használja a `Invoke-ServiceFabricEncryptText` PowerShell-parancsot. További részletekért lásd: [a titkok kezelése Service Fabric alkalmazásokban](service-fabric-application-secret-management.md) .

A tanúsítvány titkos kulcsát központilag kell telepíteni a helyi gépre, ha sávon kívüli metódust használ (az Azure-ban, ez Azure Resource Manageron keresztül). Ezután, amikor Service Fabric telepíti a szervizcsomagot a gépre, képes visszafejteni a titkos kulcsot, és (a felhasználónévvel együtt) a hitelesítő adatokkal történő futtatáshoz Active Directory használatával.

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
> Ha futtató házirendet alkalmaz egy szolgáltatásra, és a szolgáltatás jegyzékfájlja deklarálja a végponti erőforrásokat a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy**is.  További információ: [biztonsági hozzáférési házirend társítása http-és HTTPS-végpontokhoz](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Következő lépésként olvassa el a következő cikkeket:
* [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
