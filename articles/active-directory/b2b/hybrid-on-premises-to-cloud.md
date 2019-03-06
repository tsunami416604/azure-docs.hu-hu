---
title: Szinkronizálása a felhőbe, B2B-felhasználók – az Azure Active Directory helyi partnerfiókokhoz |} A Microsoft Docs
description: Külső partnerekkel helyileg kezelt lehetővé teszik mind a helyi hozzáférési és hitelesítő adatokkal az Azure AD B2B együttműködés a felhőbeli erőforrások.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd684b992d46edbec30a12dc3b166d6193d2eabe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442154"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Helyileg kezelt partner fiókok hozzáférést biztosít felhőbeli erőforrásokat az Azure AD B2B együttműködés segítségével

Az Azure Active Directory (Azure AD), mielőtt teszi a szervezetek számára a helyszíni identitáskezelési rendszereket hagyományosan felügyelt partnerfiókokhoz rendelkezik a helyszíni címtárban. Egy szervezet áthelyezése az Azure AD-alkalmazások indításakor szeretné, hogy a partnerek hozzáférhet az erőforrásokhoz. Nem számít, hogy az erőforrások a helyszíni vagy felhőbeli. Azt is, hogy a partner felhasználók azonos bejelentkezési hitelesítő adatok a helyszíni és az Azure AD-erőforrásokat is használhatja. 

Ha a külső partnerekkel, a helyszíni címtárban fiókokat hoz létre (például hoz létre egy fiókot egy "wmoran" a külső felhasználók nevű Wendy Moran az partners.contoso.com tartományban jelentkezzen be nevére), ezeknek a fiókoknak is végezhetnek szinkronizálást a felhő. Pontosabban, használhatja az Azure AD Connect szinkronizálja a partner-fiókkal a felhőbe való Azure AD B2B-felhasználók (azt jelenti, a felhasználó a UserType = vendég). Ez lehetővé teszi a partner felhasználók hitelesítő adatokkal a helyi fiókok további férhetnek szükségük van, mint a felhőbeli erőforrások eléréséhez. 

## <a name="identify-unique-attributes-for-usertype"></a>Egyedi attribútumok azonosíthatja a UserType

Mielőtt engedélyezné a szinkronizálást, a UserType attribútuma, először el kell döntenie hogyan származtassa a UserType attribútuma a helyszíni Active Directoryból. Más szóval a helyszíni környezetben paramétereket egyediek, a külső együttműködők? Határozza meg a paraméter, amely a külső együttműködők megkülönbözteti a saját szervezet tagjai.

Ez a két gyakori módszer a következők:

- Kijelöli a nem használt helyszíni Active Directory attribútumtár (például extensionAttribute1) az adatforrás-attribútum adatokként. 
- Azt is megteheti származtatni a UserType attribútuma értéket más tulajdonságok alapján. Például szinkronizálandó minden felhasználó vendégként, ha azok a helyszíni Active Directory-UserPrincipalName attribútum végződik-e a tartomány *@partners.contoso.com*.
 
Részletes attribútum követelmények, lásd: [UserType-szinkronizálás engedélyezése](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Az Azure AD Connect szinkronizálási felhasználóknak, hogy a felhő konfigurálása

Keresse meg az egyedi attribútum, ezek a felhasználók a felhőbe, az Azure AD B2B-felhasználók szinkronizálása az Azure AD Connect konfigurálhat (vagyis a UserType rendelkező felhasználók = vendég). Egy engedélyezési szempontjából ezek a felhasználók olyan megkülönböztetni B2B-felhasználók az Azure AD B2B együttműködés meghívási folyamatot lettek létrehozva.

Megvalósítás útmutatásért lásd: [UserType-szinkronizálás engedélyezése](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory B2B együttműködés hibrid cégekhez és vállalkozásokhoz](hybrid-organizations.md)
- [Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](hybrid-cloud-to-on-premises.md)
- Az Azure AD Connect áttekintése, lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

