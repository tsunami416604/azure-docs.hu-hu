---
title: Elérhető egyéni rendszergazdai szerepkör-engedélyek – Azure AD | Microsoft dokumentumok
description: Egyéni rendszergazdai szerepkör engedélyek az identitáskezelés delegálása érdekében.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025151"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Alkalmazásregisztrációs altípusok és engedélyek az Azure Active Directoryban

Ez a cikk tartalmazza a jelenleg elérhető alkalmazás regisztrációs engedélyek egyéni szerepkör-definíciók az Azure Active Directoryban (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Az egycímtáros alkalmazások kezeléséhez szükséges engedélyek

Az egyéni szerepkör engedélyeinek kiválasztásakor lehetősége van arra, hogy csak az egycímtáralkalmazások kezeléséhez biztosítson hozzáférést. Az egycímtárú alkalmazások csak az Azure AD-szervezet azon felhasználói számára érhetők el, ahol az alkalmazás regisztrálva van. Az egycímtáralkalmazások úgy vannak definiálva, hogy **a Támogatott fióktípusok** "Csak ebben a szervezeti címtárban lévő fiókok" beállítással rendelkeznek. A Graph API-ban az egycímtárú alkalmazások signInAudience tulajdonsága "AzureADMyOrg" lesz.

Ha csak egycímtárú alkalmazások kezeléséhez szeretne hozzáférést biztosítani, használja az alábbi engedélyeket a **applications.myOrganization**altípussal. Például microsoft.directory/applications.myOrganization/basic/update.

Tekintse meg az [egyéni szerepkörök áttekintését,](roles-custom-overview.md) hogy mi az általános kifejezések altípusa, engedélye és tulajdonságkészlete. Az alábbi információk a jelentkezések regisztrációjára vonatkoznak.

### <a name="create-and-delete"></a>Létrehozás és törlés

Két engedély áll rendelkezésre az alkalmazásregisztrációk létrehozásának engedélyezéséhez, amelyek mindegyike eltérő viselkedéssel rendelkezik:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Az engedély hozzárendelése azt eredményezi, hogy a létrehozó a létrehozott alkalmazásregisztráció első tulajdonosaként kerül hozzáadásra, és a létrehozott alkalmazásregisztráció beleszámít a létrehozó 250 létrehozott objektumkvótájába.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Az engedély hozzárendelése azt eredményezi, hogy a létrehozó nem kerül hozzá a létrehozott alkalmazásregisztráció első tulajdonosaként, és a létrehozott alkalmazásregisztráció nem számít bele a létrehozó 250 létrehozott objektumkvótájába. Körültekintően használja ezt az engedélyt, mert semmi sem akadályozza meg a hozzárendeltt abban, hogy alkalmazásregisztrációkat hozzon létre a címtárszintű kvóta leütéséig. Ha mindkét engedély hozzá van rendelve, ez az engedély élvez elsőbbséget.

Ha mindkét engedély hozzá van rendelve, a /create engedély lesz az elsőbbség. Bár a /createAsOwner engedély nem adja hozzá automatikusan a létrehozót az első tulajdonosként, a tulajdonosok megadhatók az alkalmazásregisztráció létrehozása során a Graph API-k vagy a PowerShell-parancsmagok használatakor.

Engedélyek létrehozása hozzáférést biztosít az **Új regisztráció** parancshoz.

[Ezek az engedélyek hozzáférést biztosítanak az Új regisztrációs portál parancshoz](./media/roles-create-custom/new-custom-role.png)

Az alkalmazásregisztrációk törlésének engedélyezéséhez két engedély áll rendelkezésre:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Lehetővé teszi az alkalmazásregisztrációk altípustól függetlenül történő törlését; ez mind az egy- és több-bérlős alkalmazások.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Lehetővé teszi az alkalmazásregisztrációk törlését, amelyek csak a szervezet vagy az egybérlős alkalmazások (myOrganization altípus) számára érhetők el.

![Ezek az engedélyek hozzáférést biztosítanak az alkalmazásregisztráció törlése parancshoz](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Létrehozási engedélyeket tartalmazó szerepkör hozzárendelésekéneknél a szerepkör-hozzárendelést a címtárhatókörben kell elkészíteni. Az erőforráshatókörhöz rendelt létrehozási engedély nem teszi lehetővé az alkalmazásregisztrációk létrehozását.

### <a name="read"></a>Olvasás

A szervezet összes tagfelhasználója alapértelmezés szerint olvashatja az alkalmazásregisztrációs adatokat. Azonban a vendég felhasználók és az alkalmazás egyszerű szolgáltatás nem. Ha egy vendégfelhasználóhoz vagy alkalmazáshoz szeretne szerepkört rendelni, meg kell adnia a megfelelő olvasási engedélyeket.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Az egy- és több-bérlős alkalmazások összes tulajdonságának olvasása olyan tulajdonságokon kívül, amelyek semmilyen helyzetben, például hitelesítő adatok esetén nem olvashatók.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Ugyanazokat az engedélyeket adja meg, mint a microsoft.directory/applications/allProperties/read, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Lehetővé teszi a tulajdonosok tulajdonság olvasását egy- és több-bérlős alkalmazásokon. Hozzáférést biztosít az alkalmazásregisztráció tulajdonosai lap összes mezőjéhez:

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációs tulajdonosok laphoz](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Hozzáférést biztosít a normál alkalmazásregisztrációs tulajdonságok olvasásához. Ez magában foglalja az alkalmazásregisztrációs lapokon található tulajdonságokat is.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/standard/read, de csak egybérlős alkalmazások esetén.

### <a name="update"></a>Frissítés

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Az egykönyvtáras és többkönyvtáros alkalmazások összes tulajdonságának frissítése.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/allProperties/update, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

A támogatott fióktípus (signInAudience) tulajdonság frissítése egycímtáros és többcímtáros alkalmazásokon.

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációval támogatott fióktípus-tulajdonsághoz a hitelesítési lapon](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/audience/update, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

A válasz URL-címének, a kijelentkezési URL-címnek, az implicit folyamatnak és a közzétevői tartomány tulajdonságainak frissítésére az egy- és több-bérlős alkalmazásokban. Hozzáférést biztosít az alkalmazásregisztrációs hitelesítés ilapjának összes mezőjéhez, kivéve a támogatott fióktípusokat:

![Hozzáférést biztosít az alkalmazásregisztrációs hitelesítéshez, de nem támogatott fióktípusokhoz](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/authentication/update, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

A név, az embléma, a kezdőlap URL-címe, a szolgáltatási URL-cím és az adatvédelmi nyilatkozat URL-tulajdonságainak frissítése egy- és több-bérlős alkalmazásokon. Hozzáférést biztosít az alkalmazásregisztrációs márkanév ilapjának összes mezőjéhez:

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációs márkajelzési oldalhoz](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/basic/update, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

A tanúsítványok és az ügyféltitkos kulcsok tulajdonságainak frissítése egy- és több-bérlős alkalmazásokon. Hozzáférést biztosít az alkalmazásregisztrációs tanúsítványok & titkos kulcsok lapján lévő összes mezőhöz:

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációs tanúsítványokhoz & titkos kulcsok laphoz](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/credentials/update, de csak az egycímtárú alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

A tulajdonosi tulajdonság frissítése egy- és több-bérlős. Hozzáférést biztosít az alkalmazásregisztráció tulajdonosai lap összes mezőjéhez:

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációs tulajdonosok laphoz](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/owners/update, de csak az egybérlős alkalmazások esetében.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

A delegált engedélyek, az alkalmazásengedélyek, az engedélyezett ügyfélalkalmazások, a szükséges engedélyek és a hozzájárulási tulajdonságok frissítése egy- és több-bérlős alkalmazásokon. Nem teszi lehetővé a hozzájárulás teljesítését. Hozzáférést biztosít az alkalmazásregisztrációs API-engedélyek és az API-lapok megjelenítése összes mezőjéhez:

![Ez az engedély hozzáférést biztosít az alkalmazásregisztrációs API-engedélyek laphoz](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Ez az engedély hozzáférést biztosít az alkalmazás regisztrációjához Api-lap megjelenítése](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Ugyanazokat az engedélyeket adja, mint a microsoft.directory/applications/permissions/update, de csak az egybérlős alkalmazások esetében.

## <a name="required-license-plan"></a>Szükséges licencterv

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Egyéni szerepkörök létrehozása [az Azure Portal, az Azure AD PowerShell és a Graph API](roles-create-custom.md) használatával
- [Egyéni szerepkör hozzárendelésének megtekintése](roles-view-assignments.md)
