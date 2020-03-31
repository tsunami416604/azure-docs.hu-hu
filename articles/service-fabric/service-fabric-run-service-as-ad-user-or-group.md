---
title: Azure Service Fabric-szolgáltatás futtatása AD-felhasználóként vagy -csoportként
description: Megtudhatja, hogy miként futtathat egy szolgáltatást Active Directory-felhasználóként vagy -csoportként egy Service Fabric Windows önálló fürtön.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464244"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Szolgáltatás futtatása Active Directory-felhasználóként vagy -csoportként
Windows Server önálló fürtön egy szolgáltatást Active Directory-felhasználóként vagy -csoportként futtathat RunAs házirend használatával.  Alapértelmezés szerint a Service Fabric-alkalmazások a Fabric.exe folyamat alatt futó fiók alatt futnak. Az alkalmazások különböző fiókok alatt történő futtatása, még egy megosztott üzemeltetett környezetben is, biztonságosabbá teszi őket egymástól. Vegye figyelembe, hogy ez az Active Directoryt a tartományon belül használja, és nem az Azure Active Directoryt (Azure AD).  A szolgáltatást [csoportfelügyelt szolgáltatásfiókként (gMSA)](service-fabric-run-service-as-gmsa.md)is futtathatja.

Tartományi felhasználó vagy csoport használatával ezután hozzáférhet a tartomány más erőforrásaihoz (például fájlmegosztásokhoz), amelyek engedélyeket kaptak.

A következő példa egy *TestUser* nevű Active Directory-felhasználót mutat be, akinek a tartományjelszava a *MyCert*nevű tanúsítvánnyal van titkosítva. A `Invoke-ServiceFabricEncryptText` PowerShell paranccsal létrehozhatja a titkos titkosítási szöveget. A részleteket a [Service Fabric-alkalmazások titkos titkainak kezelése](service-fabric-application-secret-management.md) című témakörben találja.

A tanúsítvány titkos kulcsának üzembe helyezésével vissza kell fejtenie a jelszót a helyi gépre sávon kívüli módszerrel (az Azure-ban ez az Azure Resource Manageren keresztül történik). Ezután amikor a Service Fabric telepíti a szolgáltatáscsomagot a számítógépre, képes visszafejteni a titkos kulcsot, és (a felhasználónévvel együtt) hitelesíti magát az Active Directoryval, hogy ezek a hitelesítő adatok alatt fusson.

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
> Ha RunAs házirendet alkalmaz egy szolgáltatásra, és a szolgáltatásjegyzék deklarálja a végponterőforrásait a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy házirendet**is.  További információt a [Http- és HTTPS-végpontokhoz szükséges biztonsági hozzáférési házirend hozzárendelése című témakörben talál.](service-fabric-assign-policy-to-endpoint.md) 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Következő lépésként olvassa el a következő cikkeket:
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
