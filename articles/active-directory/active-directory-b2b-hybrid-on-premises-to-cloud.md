---
title: Helyileg kezelt partner fiókok elérhető Azure AD B2B felhasználóként felhőalapú erőforrások biztosítása |} Microsoft Docs
description: Külső partnerek helyileg kezelt adja meg mindkét helyi hozzáférési és a hitelesítő adatokkal rendelkező Azure AD B2B együttműködés felhőben található erőforrásokat.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/24/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: dc4c8b3f5cb20eaf76fc0ee47d195aca26d06f90
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928574"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Helyileg kezelt partner fiókok hozzáférést biztosít felhőbeli erőforrásokat használó Azure AD B2B együttműködés

Azure Active Directory (Azure AD), mielőtt a helyszíni identitáskezelési rendszereket rendelkező szervezetek hagyományosan felügyelt partner fiókkal rendelkeznek a helyszíni címtárba. Egy szervezet alkalmazások áthelyezése az Azure AD indításakor kívánt ellenőrizze, hogy a partnerek érhetik el a szükséges erőforrásokat. Fontos, hogy ne legyenek az erőforrások a helyszíni vagy a felhőben. Emellett szeretné a partner felhasználó használhatja a bejelentkezési hitelesítő adatokkal a helyszíni és az Azure AD-erőforrásokat is. 

Ha a külső partnerek a helyszíni címtárban fiókokat hoz létre (például hoz létre egy fiókot a bejelentkezési neve "wmoran" külső felhasználó a partners.contoso.com tartományban Wendy Moran nevű), is végezhetnek szinkronizálást ezeknek a fiókoknak a felhő. Pontosabban, használhatja az Azure AD Connect szinkronizálása az Azure AD B2B felhasználóként a felhőbe a partner fiókok (Ez azt jelenti, hogy a felhasználók a UserType vendég =). Ez lehetővé teszi, hogy a partner felhasználók hitelesítő adatokkal a helyi fiókok további férhetnek van szükségük, mint a felhőalapú erőforrások eléréséhez. 

## <a name="identify-unique-attributes-for-usertype"></a>Egyedi attribútumok UserType felderítése

Mielőtt engedélyezné a szinkronizálást a UserType attribútuma, először el kell döntenie UserType attribútuma származik a helyszíni Active Directory módjáról. Más szóval milyen a helyszíni környezetben paraméterei a külső együttműködők egyedi? Határozza meg a paraméter, amely a külső együttműködők megkülönbözteti a saját szervezet tagjaira.

Ez a két gyakori módszer a a következők:

- Jelölje ki a nem használt a helyszíni Active Directory attribútumtár (például extensionAttribute1) kívánja használni, mint a forrásattribútum kulcsoszlopaival. 
- Azt is megteheti UserType attribútuma értéke származik egyéb tulajdonságait. Például szinkronizálandó minden felhasználó vendégként a helyszíni Active Directory UserPrincipalName attribútum a tartomány lejártát *@partners.contoso.com*.
 
A részletes attribútum követelményeket lásd [UserType szinkronizálásának engedélyezése](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Az Azure AD Connect szinkronizálási felhasználóknak, hogy a felhő konfigurálása

Keresse meg az egyedi attribútum, konfigurálhatja az Azure AD Connect szinkronizálása ezek a felhasználók a felhőalapú Azure AD B2B felhasználóként (Ez azt jelenti, hogy a felhasználók a UserType vendég =). Egy engedélyezési szempontjából ezek a felhasználók nem különböztethetők meg Azure AD B2B együttműködés meghívó folyamat létrehozott B2B felhasználók.

Megvalósítási útmutatásért lásd: [UserType szinkronizálásának engedélyezése](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory B2B együttműködés hibrid szervezeteknek](active-directory-b2b-hybrid-organizations.md)
- [Támogatás B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- Az Azure AD Connect áttekintéséért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](connect/active-directory-aadconnect.md).

