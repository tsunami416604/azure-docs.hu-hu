---
title: B2B együttműködés hibrid szervezeteknek – Azure AD
description: Az Azure AD B2B együttműködésével hozzáférést biztosítanak a partnereknek a helyszíni és a felhőbeli erőforrásokhoz is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272473"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B együttműködés hibrid szervezetek számára

Az Azure Active Directory (Azure AD) B2B-együttműködés megkönnyíti a külső partnerek számára a szervezetben lévő alkalmazások hoz és erőforrásokhoz való hozzáférést. Ez még egy hibrid konfigurációban is igaz, ahol a helyszíni és a felhőalapú erőforrások is rendelkeznek. Nem számít, ha jelenleg kezeli a külső partnerfiókok helyileg a helyszíni identitás-rendszer, vagy ha a külső fiókok kezelése a felhőben, mint az Azure AD B2B felhasználók. Mostantól mindkét helyen biztosíthatja ezeknek a felhasználóknak a hozzáférést az erőforrásokhoz, mindkét környezetben ugyanazt a bejelentkezési hitelesítő adatokat használva.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>B2B-felhasználók nak az Azure AD-ben való hozzáférés megadása a helyszíni alkalmazásokhoz

Ha a szervezet az Azure AD B2B együttműködési képességeit használja a partnerszervezetek vendégfelhasználóinak az Azure AD-be való meghívásához, most már hozzáférést biztosíthat ezeknek a B2B-felhasználóknak a helyszíni alkalmazásokhoz.

Saml-alapú hitelesítést használó alkalmazások esetében ezeket az alkalmazásokat elérhetővé teheti a B2B-felhasználók számára az Azure Portalon keresztül, az Azure AD alkalmazásproxy használatával a hitelesítéshez.

A Kerberos korlátozott delegálással (KCD) integrált Windows-hitelesítést (IWA) használó alkalmazások esetében az Azure AD proxyt is használhatja a hitelesítéshez. Ahhoz azonban, hogy az engedélyezés működjön, a helyszíni Windows Server Active Directoryban felhasználói objektumra van szükség. Két módszerrel hozhat létre helyi felhasználói objektumokat, amelyek a B2B vendégfelhasználókat képviselik.

- Használhatja a Microsoft Identity Manager (MIM) 2016 SP1 és a MIM felügyeleti ügynök a Microsoft Graph.
- Használhatja a PowerShell-parancsfájlok. (Ez a megoldás nem igényel MIM-et.)

A megoldások megvalósításáról további információt a [B2B-felhasználók megadása az Azure AD-ben a helyszíni alkalmazásokhoz való hozzáférése.](hybrid-cloud-to-on-premises.md)

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Helyileg kezelt partnerfiókok hozzáférésének biztosítása a felhőalapú erőforrásokhoz

Az Azure AD előtt a helyszíni identitáskezelő rendszerekkel rendelkező szervezetek hagyományosan felügyelt partnerfiókkal rendelkeznek a helyszíni címtárban. Ha Ön ilyen szervezet, győződjön meg arról, hogy partnerei továbbra is hozzáférhetnek az alkalmazások és egyéb erőforrások felhőbe való áthelyezése során. Ideális esetben azt szeretné, hogy ezek a felhasználók ugyanazokat a hitelesítő adatokat használják a felhőbeli és a helyszíni erőforrások eléréséhez. 

Mostantól olyan módszereket kínálunk, ahol az Azure AD Connect segítségével szinkronizálhatja ezeket a helyi fiókokat a felhőbe "vendégfelhasználóként", ahol a fiókok ugyanúgy viselkednek, mint az Azure AD B2B-felhasználók.

A vállalati adatok védelme érdekében szabályozhatja a megfelelő erőforrásokhoz való hozzáférést, és konfigurálhatja azokat az engedélyezési házirendeket, amelyek ezeket a vendégfelhasználókat az alkalmazottaktól eltérően kezelik.

A megvalósítás részleteiről a [Helyileg felügyelt partnerfiókok felhőbeli erőforrásokhoz való hozzáférésének megadása az Azure AD B2B együttműködéshasználatával című témakörben](hybrid-on-premises-to-cloud.md)talál.
 
## <a name="next-steps"></a>További lépések

- [B2B-felhasználók nak az Azure AD-ben való hozzáférés megadása a helyszíni alkalmazásokhoz](hybrid-cloud-to-on-premises.md)
- [Helyileg kezelt partnerfiókok hozzáférésének biztosítása a felhőbeli erőforrásokhoz az Azure AD B2B együttműködéshasználatával](hybrid-on-premises-to-cloud.md)


