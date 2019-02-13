---
title: Hibrid cégekhez és vállalkozásokhoz – Azure Active Directory B2B-együttműködés |} A Microsoft Docs
description: Hozzáférést biztosíthat partnereinek a helyszínen, és a felhőbeli erőforrások az Azure AD B2B együttműködés.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a95b9bc6637526ef0d975815cc88dd084360397
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196909"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Az Azure Active Directory B2B együttműködés hibrid cégekhez és vállalkozásokhoz

Az Azure Active Directory (Azure AD) B2B-együttműködés megkönnyíti, hogy a külső hozzáférést biztosíthat partnereinek alkalmazásokat és erőforrásokat a szervezetben. Ez a hibrid konfiguráció még akkor is, hol van a helyszíni és felhőalapú erőforrásokat is. Nem számít, ha jelenleg kezelt külső partnerfiókokhoz helyileg a helyszíni identitáskezelő rendszerbe, vagy ha Ön kezeli a külső fiókokat, a felhőben, az Azure AD B2B-felhasználók. Mostantól ezek a felhasználók hozzáférést biztosíthat az erőforrásokhoz az egyik helyen, mindkét környezetben a bejelentkezési hitelesítő adatokkal.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások

Ha a szervezete az Azure AD B2B együttműködési lehetőségeket az Azure ad a fiókpartner-szervezetek vendégfelhasználók meghívása, most már megadhat a B2B-felhasználók hozzáférésének a helyszíni alkalmazásokhoz.

Az SAML-alapú hitelesítést használó alkalmazásokhoz akkor is használhatja ezeket az alkalmazásokat a B2B-felhasználók az Azure Portalon az Azure AD-alkalmazásproxy használatával a hitelesítéshez.

A Kerberos által korlátozott delegálás (KCD) integrált Windows-hitelesítés (IWA) használó alkalmazások esetében is használhat az Azure AD alkalmazásproxy a hitelesítéshez. Azonban engedélyezési működjön, a felhasználói objektum van szükség a helyszíni Windows Server Active Directory. Kétféleképpen a B2B vendégfelhasználók képviselő helyi felhasználói objektumok létrehozására használhatja.

- A Microsoft Identity Manager (MIM) 2016 SP1 és a MIM-kezelőügynök a Microsoft Graph használható.
- Egy PowerShell-parancsfájlt is használhatja. (Ez a megoldás nem követeli meg a MIM.)

Ezek a megoldások megvalósításával kapcsolatos részletekért lásd: [Grant B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Helyileg kezelt partnerfiókokhoz elérhető felhőalapú erőforrások biztosítása

Az Azure AD-ben előtt teszi a szervezetek számára a helyszíni identitáskezelési rendszereket hagyományosan felügyelt partnerfiókokhoz azok a helyszíni címtárban. Ha Ön a szervezet, győződjön meg arról, hogy a partnerek továbbra is rendelkezik hozzáféréssel, mivel az alkalmazások és más erőforrások áthelyezése a felhőbe szeretne. Ideális esetben érdemes ezek a felhasználók ugyanazokat a hitelesítő adatok használata a felhőbeli és a helyszíni erőforrások eléréséhez. 

Mi most ahol használhatja az Azure AD Connect ahol viselkednek a fiókok csak "vendég felhasználók", ezek a felhőbe a helyi fiókok szinkronizálása az ajánlat módszerek, például az Azure AD B2B-felhasználók.

A vállalati adatok védelme érdekében csak a megfelelő erőforrásokhoz való hozzáférésének, és engedélyezési házirendeket, amelyek kezeli az alkalmazottak és a vendégfelhasználóknak konfigurálása.

A megvalósítás részleteit lásd: [Grant helyileg kezelt partnerfiókokhoz elérhető az Azure AD B2B együttműködés segítségével felhőalapú erőforrások](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>További lépések

- [Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](hybrid-cloud-to-on-premises.md)
- [Helyileg kezelt partner fiókok hozzáférést biztosít felhőbeli erőforrásokat az Azure AD B2B együttműködés segítségével](hybrid-on-premises-to-cloud.md)


