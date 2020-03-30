---
title: Egyéni házirendek kezelése a PowerShell használatával
titleSuffix: Azure AD B2C
description: Használja az Azure Active Directory (Azure AD) PowerShell-parancsmag az Azure AD B2C egyéni szabályzatok programozott felügyeletét. Hozzon létre, olvasson, frissítsen és töröljön egyéni szabályzatokat a PowerShell használatával.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187406"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Az Azure AD B2C egyéni szabályzatainak kezelése az Azure PowerShell használatával

Az Azure PowerShell számos parancsmalapot biztosít a parancssori és parancsfájlalapú egyéni házirend-kezeléshez az Azure AD B2C-bérlőben. Ismerje meg, hogyan használhatja az Azure AD PowerShell-modult a következőkhöz:

* Az egyéni szabályzatok listázása egy Azure AD B2C-bérlőben
* Házirend letöltése bérlőből
* Meglévő házirend frissítése a tartalom felülírásával
* Új szabályzat feltöltése az Azure AD B2C-bérlőbe
* Egyéni házirend törlése a bérlőből

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure AD B2C-bérlő](tutorial-create-tenant.md)és a [b2C IEF-házirend-rendszergazdaszerepkörrel](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) rendelkező felhasználó hitelesítő adatai
* A bérlőbe feltöltött [egyéni házirendek](custom-policy-get-started.md)
* [Azure AD PowerShell a Graph **előzetes moduljához**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>PowerShell-munkamenet csatlakoztatása B2C-bérlőhöz

Az Azure AD B2C-bérlőegyéni szabályzataival való munkához először csatlakoztatnia kell a PowerShell-munkamenetet a bérlőhöz a [Connect-AzureAD][Connect-AzureAD] parancs használatával.

Hajtsa végre a következő `{b2c-tenant-name}` parancsot, az Azure AD B2C-bérlő nevével helyettesítve. Jelentkezzen be egy olyan fiókkal, amely a [B2C IEF házirend-rendszergazdaszerepkörrel](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) van elrendelve a címtárban.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Példa sikeres bejelentkezést megjelenítő parancskimenetre:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Az összes egyéni házirend listázása a bérlőben

Az egyéni szabályzatok felfedezése lehetővé teszi, hogy az Azure AD B2C-rendszergazda áttekintse, kezelje és adja hozzá az üzleti logikát a műveleteihez. A [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] paranccsal adja vissza az Egyéni szabályzatok azonosítóinak listáját egy Azure AD B2C-bérlőben.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Példa parancskimenetére:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Házirend letöltése

A házirend-azonosítók listájának áttekintése után a [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] használatával megcélozhat egy adott szabályzatot a tartalom letöltéséhez.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Ebben a példában a *B2C_1A_signup_signin* azonosítóval rendelkező házirend letöltődik:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

A házirend tartalmának helyi szerkesztéséhez irányítsa a `-OutputFilePath` parancskimenetet egy fájlba az argumentummal, majd nyissa meg a fájlt a kedvenc szerkesztőjében.

Példa kimenet fájlba küldésére:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Meglévő házirend frissítése

A létrehozott vagy letöltött házirendfájl szerkesztése után közzéteheti a frissített szabályzatot az Azure AD B2C-ben a [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] parancs használatával.

Ha kiadja `Set-AzureADMSTrustFrameworkPolicy` a parancsot az Azure AD B2C-bérlőben már létező szabályzat azonosítójával, a házirend tartalma felülíródik.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Példa parancs:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

További példákat a [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] parancshivatkozás.

## <a name="upload-a-new-policy"></a>Új házirend feltöltése

Ha módosítja az éles környezetben futó egyéni szabályzatot, érdemes lehet közzétenni a szabályzat több verzióját tartalék vagy A/B tesztelési forgatókönyvekhez. Vagy előfordulhat, hogy szeretne másolatot készíteni egy meglévő házirendről, módosítani néhány kisebb módosítással, majd töltse fel új házirendként egy másik alkalmazás számára.

Új szabályzat feltöltéséhez használja az [Új AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] parancsot:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Példa parancs:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Egyéni házirend törlése

A tiszta műveletek életciklusának fenntartása érdekében azt javasoljuk, hogy rendszeresen távolítsa el a nem használt egyéni házirendeket. Előfordulhat például, hogy el szeretné távolítani a régi házirend-verziókat, miután áttelepítést hajtott végre egy új szabályzatkészletre, és ellenőrizni szeretné az új házirendek működését. Továbbá, ha egyéni házirendeket próbál közzétenni, és hibaüzenetet kap, célszerű lehet eltávolítani a sikertelen kiadás részeként létrehozott házirendeket.

Az [Eltávolítás-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] paranccsal törölheti a házirendet a bérlőből.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Példa parancs:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Házirendek feltöltésének hibaelhárítása

Amikor új egyéni házirendet próbál közzétenni, vagy egy meglévő házirendet frissít, a házirendfájl öröklési láncának helytelen XML-formázása és hibái érvényesítési hibákat okozhatnak.

Az alábbiakban például egy olyan házirend frissítésére tett kísérletet, amely hibás XML-t tartalmazó tartalommal rendelkezik (a kimenet rövidségből csonkolva jelenik meg):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Az egyéni szabályzatok hibaelhárításáról az [Azure AD B2C egyéni szabályzatok és az identitáskezelési keretrendszer hibaelhárítása című témakörben](active-directory-b2c-guide-troubleshooting-custom.md)talál.

## <a name="next-steps"></a>További lépések

Ha tudni szeretné, hogy miként telepíti az egyéni szabályzatokat egy folyamatos integrációs/folyamatos kézbesítési (CI/CD) folyamat részeként, a PowerShell használatával kapcsolatban az [Egyéni szabályzatok üzembe helyezése az Azure DevOps-folyamatból](deploy-custom-policies-devops.md)című témakörben talál tájékoztatást.

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
