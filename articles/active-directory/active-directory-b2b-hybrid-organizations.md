---
title: "Az Azure Active Directory B2B együttműködés hibrid szervezetek |} Microsoft Docs"
description: "Partnerek adjon hozzáférést ad mindkét helyi és felhőbeli erőforrásokat az Azure AD B2B együttműködés"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Az Azure Active Directory B2B együttműködés hibrid szervezeteknek

Egy hibrid szervezet, melyekben mind a helyszíni és felhőalapú erőforrásaihoz, érdemes mindkét helyi külső partnerekkel hozzáférés biztosítása és a felhőben található erőforrásokat. Helyi erőforrások eléréséhez felügyelheti partner fiókjait helyileg a helyszíni Active Directory-környezetben. Partnerek bejelentkezik a partner fiók hitelesítő adataival, a szervezet erőforrásaihoz. A partnerek hozzáférésének felhőalapú erőforrásokat használó ugyanezeket a hitelesítő adatokat, használhatja Azure Active Directory (Azure AD) Connect Azure AD B2B felhasználóként a felhőbe a partner fiókok szinkronizálása (Ez azt jelenti, hogy a felhasználók a UserType vendég =).

## <a name="identify-unique-attributes-for-usertype"></a>Egyedi attribútumok UserType felderítése

Mielőtt engedélyezné a szinkronizálást a UserType attribútuma, először el kell döntenie UserType attribútuma származik a helyszíni Active Directory módjáról. Más szóval milyen a helyszíni környezetben paraméterei a külső együttműködők egyedi? Határozza meg a paraméter, amely a külső együttműködők megkülönbözteti a saját szervezet tagjaira.

Ez a két gyakori módszer a a következők:

- Jelölje ki a nem használt a helyszíni Active Directory attribútumtár (például extensionAttribute1) kívánja használni, mint a forrásattribútum kulcsoszlopaival. 
- Azt is megteheti UserType attribútuma értéke származik egyéb tulajdonságait. Például szinkronizálandó minden felhasználó vendégként a helyszíni Active Directory UserPrincipalName attribútum a tartomány lejártát  *@partners.fabrikam123.org* .
 
A részletes attribútum követelményeket lásd [UserType szinkronizálásának engedélyezése](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Az Azure AD Connect szinkronizálási felhasználóknak, hogy a felhő konfigurálása

Keresse meg az egyedi attribútum, konfigurálhatja az Azure AD Connect szinkronizálása ezek a felhasználók a felhőalapú Azure AD B2B felhasználóként (Ez azt jelenti, hogy a felhasználók a UserType vendég =). Egy engedélyezési szempontjából ezek a felhasználók nem különböztethetők meg Azure AD B2B együttműködés meghívó folyamat létrehozott B2B felhasználók.

Megvalósítási útmutatásért lásd: [UserType szinkronizálásának engedélyezése](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Következő lépések

- Azure AD B2B együttműködés áttekintését lásd: [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Az Azure AD Connect áttekintéséért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](connect/active-directory-aadconnect.md).

