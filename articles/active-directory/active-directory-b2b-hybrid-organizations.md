---
title: Hibrid szervezetek - Azure Active Directory B2B együttműködés |} Microsoft Docs
description: Partnerek hozzáférést is a helyszíni és felhőalapú Azure AD B2B együttműködés erőforrásokhoz.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 943ccadbc87cd8d2345078405e2a27930634668e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Az Azure Active Directory B2B együttműködés hibrid szervezeteknek

Az Azure Active Directory (Azure AD) B2B együttműködés megkönnyíti, hogy a külső partnerekkel hozzáférést alkalmazásokat és erőforrásokat a szervezetben. Ez érvényét veszti, még akkor is hibrid konfigurációnak, mind a helyszíni és felhőalapú erőforrások esetében. Nem számít, ha jelenleg kezelheti a külső partner fiókok helyileg a helyszíni identitás rendszerben, vagy ha kezelheti az Azure AD B2B felhasználóként a külső fiókokat a felhőben. Mostantól ezek a felhasználók hozzáférést biztosíthat az egyik helyen, erőforrások mindkét környezetben a bejelentkezési hitelesítő adatokkal.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Támogatás B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások

Ha a szervezet Azure AD B2B együttműködés képességek az Azure AD az erőforráspartner-szervezetek Vendég felhasználóknak szánt meghívó használ, megadhatja a B2B a felhasználók hozzáférést kapjanak a helyszíni alkalmazások most.

SAML-alapú hitelesítést használó alkalmazásokhoz akkor is elérhetővé ezeket az alkalmazásokat B2B végig a felhasználókat az Azure-portálon az Azure AD-alkalmazásproxy használata a hitelesítéshez.

Integrált Windows-hitelesítéssel (IWA) és a Kerberos által korlátozott delegálás (KCD) használó alkalmazásokhoz, akkor is Azure AD proxyt használja hitelesítésre. Azonban engedélyezési működjön, a user objektum szükség van a helyszíni Windows Server Active Directory. Két módon használhatja, amelyek megfelelnek a B2B vendégfelhasználók helyi felhasználói objektumokat létrehozni.

- A Microsoft Identity Manager (MIM) 2016 SP1 és a MIM-kezelőügynök Microsoft Graph használhatja.
- Egy PowerShell-parancsfájlt is használhatja. (Ez a megoldás nem igényel MIM.)

Ezek a megoldások megvalósításához kapcsolatos részletekért lásd: [Grant B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások](active-directory-b2b-hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Helyileg kezelt partner fiókok elérhető felhőalapú erőforrások biztosítása

Előtt az Azure Active Directory a helyszíni identitáskezelési rendszereket rendelkező szervezetek hagyományosan partner fiókok a helyszíni címtár rendelkezik felügyelt. Ha például egy szervezet, győződjön meg arról, hogy a partnerek továbbra is hozzáférhetnek az alkalmazások és egyéb erőforrásokat a felhőhöz mozgatása szeretné. Ideális esetben érdemes ezek a felhasználók a ugyanazokat a hitelesítő adatokat használja a felhő- és helyszíni erőforrások eléréséhez. 

Most már használható az Azure AD Connect ezeknek a helyi fiókoknak a felhő szinkronizálásához felhasználóként"Vendég,", ahol a fiókok ugyanúgy viselkednek ajánlat módszerek hangvételünk Azure AD B2B felhasználók.

A vállalati adatok védelme érdekében csak a megfelelő erőforrásokhoz való hozzáférés szabályozása, és engedélyezési házirendeket, amelyek kezelik a vendégfelhasználók eltér az alkalmazottak konfigurálása.

Megvalósítás részletei: [Grant helyileg kezelt partner fiókoknak az Azure AD B2B együttműködés felhőalapú erőforrásokhoz hozzáférést](active-directory-b2b-hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>További lépések

- [Támogatás B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- [Helyileg kezelt partner fiókok hozzáférést biztosít felhőbeli erőforrásokat használó Azure AD B2B együttműködés](active-directory-b2b-hybrid-on-premises-to-cloud.md)


