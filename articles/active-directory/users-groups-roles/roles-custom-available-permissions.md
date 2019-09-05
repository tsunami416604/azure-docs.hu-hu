---
title: Egyéni rendszergazdai szerepkör engedélyei az alkalmazások regisztrálásának kezeléséhez – Azure Active Directory | Microsoft Docs
description: Egyéni rendszergazdai szerepkör engedélyei az Identitáskezelés delegálásához.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ff6755f1391ff19e65df669fb51967a904f4f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707564"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Alkalmazás regisztrációs altípusai és engedélyei Azure Active Directory

Ez a cikk a jelenleg elérhető alkalmazás-regisztrációs engedélyeket tartalmazza a Azure Active Directory (Azure AD) egyéni szerepkör-definícióinak esetében.

## <a name="single-tenant-v-multi-tenant-permissions"></a>Egybérlős v. több-bérlős engedélyek

Az egyéni szerepkör engedélyei eltérnek az egybérlős és a több-bérlős alkalmazások esetében. Az egybérlős alkalmazások csak az Azure AD-szervezet azon felhasználói számára érhetők el, ahol az alkalmazás regisztrálva van. A több-bérlős alkalmazások az összes Azure AD-szervezet számára elérhetők. Az egybérlős alkalmazások úgy vannak definiálva, hogy **támogatott fióktípus** legyenek beállítva a "fiókok ebben a szervezeti címtárban" beállítás. A Graph API az egybérlős alkalmazások esetében a signInAudience tulajdonság értéke "AzureADMyOrg".

## <a name="application-registration-subtypes-and-permissions"></a>Alkalmazás regisztrációs altípusai és engedélyei

Tekintse meg az [Egyéni szerepkörök áttekintése](roles-custom-overview.md) című témakört, amelyből megtudhatja, hogy mi az általános kifejezés altípusa, az engedély és a tulajdonság beállítása. Az alábbi információk az alkalmazások regisztrálására vonatkoznak.

### <a name="subtypes"></a>Altípus

Csak egy alkalmazás regisztrációs altípusa – Applications. myOrganization. Például: Microsoft. Directory/Applications. myOrganization/Basic/Update. Ez az altípus a **hitelesítés** lapon van beállítva egy adott alkalmazás regisztrálásához, és megfelel a signInAudience tulajdonság "AzureADMyOrg" értékre való beállításának Graph API vagy a PowerShell használatával. Az altípus korlátozza az olyan alkalmazás-regisztrációk engedélyét, amelyek csak a szervezetben lévő fiókok által elérhetőként vannak megjelölve (egybérlős alkalmazások).

A korlátozott engedély használatával olvasási vagy kezelési engedélyeket biztosíthat a belső alkalmazásoknak, anélkül, hogy olvasási vagy kezelési engedélyeket adna a más szervezetek fiókjai által elérhető alkalmazásokhoz.

Vannak alkalmazások. az összes olvasási és frissítési engedély, valamint a törlési engedély myOrganization verziója. Jelenleg nincsenek alkalmazások. a Create myOrganization verziója. A szabványos engedélyek (például a Microsoft. Directory/alkalmazások/alapszintű/Update) az összes alkalmazás regisztrációs típusára vonatkozó olvasási vagy felügyeleti engedélyeket biztosítanak.

![Egyetlen bérlős alkalmazás vagy több-bérlős alkalmazás deklarálása](./media/roles-custom-available-permissions/supported-account-types.png)

Az egyéni szerepkörök előzetes verziójához tartozó következő engedélyek részletei a [Azure Active Directory elérhető egyéni szerepkör-engedélyek](roles-custom-available-permissions.md)listájában találhatók.

### <a name="create-and-delete"></a>Létrehozás és törlés

Az alkalmazások regisztrációjának megadására két engedély áll rendelkezésre:

- **Microsoft. Directory/alkalmazások/createAsOwner**
- **Microsoft. Directory/alkalmazások/létrehozás**

Ha mindkét engedély hozzá van rendelve, akkor a létrehozási engedély elsőbbséget élvez. Bár a createAsOwner engedély nem adja hozzá automatikusan a létrehozót az első tulajdonosként, a tulajdonosok a Graph API-k vagy a PowerShell-parancsmagok használatakor is megadhatók az alkalmazás regisztrációjának létrehozása során.

A létrehozási engedélyek hozzáférést biztosítanak az **új regisztrációs** parancshoz.

[Ezek az engedélyek hozzáférést biztosítanak az új regisztrációs portál parancshoz](./media/roles-create-custom/new-custom-role.png)

Az alkalmazások regisztrációjának törlésére két engedély áll rendelkezésre:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Lehetővé teszi az alkalmazások regisztrációjának törlését a altípustól függetlenül; vagyis az egybérlős és a több-bérlős alkalmazások is.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myOrganization/delete

Lehetővé teszi az alkalmazások regisztrációjának törlését azokra a felhasználókra, amelyek csak a szervezet vagy egy bérlős alkalmazás (myOrganization altípusa) számára érhetők el.

![Ezek az engedélyek hozzáférést biztosítanak az alkalmazás regisztrációjának törlése parancshoz](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> A létrehozási engedélyeket tartalmazó szerepkör hozzárendelésekor a szerepkör-hozzárendelést a címtár hatókörében kell elvégezni. Az erőforrás-hatókörben hozzárendelt létrehozási engedély nem teszi lehetővé az alkalmazások regisztrációjának létrehozását.

### <a name="read"></a>Olvasás

A szervezet minden felhasználója alapértelmezés szerint képes beolvasni az alkalmazás regisztrációs adatait. A vendég felhasználói és az alkalmazás-szolgáltatási rendszerbiztonsági tag azonban nem. Ha egy szerepkört egy vendég felhasználóhoz vagy alkalmazáshoz szeretne hozzárendelni, a megfelelő olvasási engedélyeket is meg kell adnia.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/alkalmazások/allProperties/olvasás

Az egybérlős és több-bérlős alkalmazások összes tulajdonságának olvasása a bizalmas tulajdonságok, például a hitelesítő adatokon kívül.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/allProperties/READ

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/allProperties/READ, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/alkalmazások/standard/READ: Hozzáférést biztosít az alkalmazás regisztrációjának védjegyezése oldalon található összes mezőhöz

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációjának védjegyezési lapjához](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/standard/READ

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/standard/READ, de csak egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/alkalmazások/tulajdonosok/olvasás

Lehetővé teszi az egybérlős és a több-bérlős alkalmazások tulajdonosi tulajdonságainak olvasását. Hozzáférést biztosít az összes mezőhöz az alkalmazás-regisztrációs tulajdonosok lapon:

![Ez az engedély hozzáférést biztosít az alkalmazás-regisztrációs tulajdonosok laphoz](./media/roles-custom-available-permissions/app-registration-owners.png)

Hozzáférést biztosít az alkalmazás entitás következő tulajdonságaihoz:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>frissítés

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/alkalmazások/allProperties/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/allProperties/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Hozzáférést biztosít az alkalmazás regisztrációs hitelesítése oldalon található összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációs hitelesítés lapjához](./media/roles-custom-available-permissions/supported-account-types.png)

Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/hallgatóság/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/hallgatóság/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

A válasz URL-cím, a kijelentkezési URL-cím, az implicit folyamat és a közzétevői tartomány tulajdonságainak frissítése egyetlen bérlős és több-bérlős alkalmazásokban. Hozzáférést biztosít az alkalmazás regisztrációs hitelesítése oldalon található összes mezőhöz a támogatott fióktípus kivételével:

![Hozzáférést biztosít az alkalmazás regisztrációs hitelesítéséhez, de a fiókok típusai nem támogatottak](./media/roles-custom-available-permissions/supported-account-types.png)

 Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- Bővítmények
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization/Authentication/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/Authentication/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

A név, embléma, Kezdőlap URL-cím, szolgáltatási URL-cím és adatvédelmi nyilatkozat URL-tulajdonságainak frissítése az egybérlős és a több-bérlős alkalmazásokban. Hozzáférést biztosít az alkalmazás regisztrációjának védjegyezése lapon lévő összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációjának védjegyezési lapjához](./media/roles-custom-available-permissions/app-registration-branding.png)

Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/Basic/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Az egybérlős és a több-bérlős alkalmazásokhoz tartozó tanúsítványok és az ügyfél-titkok tulajdonságainak frissítése. Hozzáférést biztosít az alkalmazás regisztrációs tanúsítványainak & Secrets oldalon található összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációs tanúsítványainak & Secrets oldalához](./media/roles-custom-available-permissions/app-registration-secrets.png)

Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:
- AsymmetricKey
- EncryptedSecretKey
- Leírás
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/hitelesítő adatok/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés, de csak egykönyvtáras alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Az egybérlős és a több-bérlős tulajdonos tulajdonságának frissítése. Hozzáférést biztosít az összes mezőhöz az alkalmazás-regisztrációs tulajdonosok lapon:

![Ez az engedély hozzáférést biztosít az alkalmazás-regisztrációs tulajdonosok laphoz](./media/roles-custom-available-permissions/app-registration-owners.png)

Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:
- Tulajdonosok

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/owners/Update

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/owners/Update, de csak egybérlős alkalmazásokhoz.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Lehetőség van a delegált engedélyek, alkalmazás-engedélyek, engedélyezett ügyfélalkalmazások, szükséges engedélyek frissítésére, valamint az egybérlős és a több-bérlős alkalmazások engedélyezési tulajdonságainak megadására. A nem teszi lehetővé a jóváhagyás végrehajtását. Hozzáférést biztosít az alkalmazás regisztrációs API-engedélyeihez tartozó összes mezőhöz, és elérhetővé teszi az API-lapokat:

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációs API-engedélyeinek oldalához](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Ezek az engedélyek hozzáférést biztosítanak az alkalmazás regisztrálásához API-oldalként](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Hozzáférést biztosít az alkalmazás erőforrásának következő tulajdonságaihoz:

- AppIdentifierUri
- Jogosultság
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/engedélyek/frissítés

Ugyanazokat az engedélyeket biztosítja, mint a Microsoft. Directory/Applications/permissions/Update, de csak az egybérlős alkalmazások esetében.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Egyéni szerepkörök létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](roles-create-custom.md) használatával
- [Egyéni szerepkör hozzárendeléseinek megtekintése](roles-view-assignments.md)
