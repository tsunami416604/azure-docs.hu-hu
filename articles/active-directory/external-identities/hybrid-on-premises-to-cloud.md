---
title: Helyi partneri fiókok szinkronizálása a felhőbe B2B-felhasználókként – Azure AD
description: A helyileg felügyelt külső partnerek a helyi és a Felhőbeli erőforrásokhoz is hozzáférhetnek ugyanazzal a hitelesítő adatokkal az Azure AD B2B együttműködés segítségével.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06a6a42724eb172a77079b94f2cf50afb8e9cdf1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357317"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Helyileg felügyelt partneri fiókok hozzáférésének biztosítása a felhőalapú erőforrásokhoz az Azure AD B2B Collaboration használatával

Az Azure Active Directory (Azure AD) előtt a helyszíni identitási rendszerekkel rendelkező szervezeteknek hagyományosan kezelt partneri fiókjai vannak a helyszíni címtárban. Ilyen szervezet esetén, amikor elkezdi áthelyezni az alkalmazásokat az Azure AD-ba, meg kell győződnie arról, hogy partnerei hozzáférhetnek a számukra szükséges erőforrásokhoz. Nem számít, hogy az erőforrások a helyszínen vagy a felhőben vannak-e. Azt is szeretné, hogy a partner felhasználóinak ugyanazokat a bejelentkezési hitelesítő adatokat tudják használni mind a helyszíni, mind az Azure AD-erőforrásokhoz. 

Ha a helyszíni címtárban hoz létre fiókokat a külső partnerekhez (például létrehoz egy "wmoran" bejelentkezési névvel rendelkező fiókot egy, a partners.contoso.com-tartományban található Wendy Moran nevű külső felhasználóhoz), most már szinkronizálhatja ezeket a fiókokat a felhővel. A Azure AD Connect segítségével szinkronizálhatja a partneri fiókokat a felhőbe, amely létrehoz egy felhasználói fiókot a UserType = Guest használatával. Ez lehetővé teszi a partner felhasználók számára, hogy a helyi fiókjaikkal megegyező hitelesítő adatokkal férjenek hozzá a felhőalapú erőforrásokhoz anélkül, hogy a számukra nagyobb hozzáférésre lenne szükségük.

> [!NOTE]
> Lásd még: a [belső felhasználók meghívása a B2B-együttműködésbe](invite-internal-users.md) (nyilvános előzetes verzió). Ezzel a szolgáltatással meghívja a belső vendég felhasználókat a B2B együttműködés használatára, függetlenül attól, hogy szinkronizálta-e a helyszíni címtárból a felhőbe érkező fiókjaikat. Miután a felhasználó elfogadja a B2B együttműködés használatára vonatkozó meghívót, a saját identitásuk és hitelesítő adataik használatával bejelentkezhetnek az elérni kívánt erőforrásokhoz. Nem kell megtartania a jelszavakat, és nem kell kezelnie a fiókok életciklusát.

## <a name="identify-unique-attributes-for-usertype"></a>A UserType egyedi attribútumainak azonosítása

Mielőtt engedélyezi a UserType attribútum szinkronizálását, először el kell döntenie, hogyan származtatja a UserType attribútumot a helyszíni Active Directoryból. Más szóval, hogy a helyszíni környezetben milyen paraméterek egyediek a külső közreműködők számára? Határozza meg azt a paramétert, amely megkülönbözteti ezeket a külső közreműködőket a saját szervezete tagjaitól.

Két gyakori megközelítés:

- Kijelöl egy nem használt helyszíni Active Directory attribútumot (például extensionAttribute1), amelyet a forrás attribútumként kíván használni. 
- Azt is megteheti, hogy származtatja a UserType attribútum értékét más tulajdonságokból. Például ha a helyi Active Directory UserPrincipalName attribútuma a tartomány *\@ Partners.contoso.com* végződik, szinkronizálni szeretné az összes felhasználót a vendégként.
 
A részletes attribútumokra vonatkozó követelményekért lásd: [a UserType szinkronizálásának engedélyezése](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Azure AD Connect konfigurálása a felhasználók felhőbe való szinkronizálásához

Az egyedi attribútum azonosítása után beállíthatja, hogy Azure AD Connect szinkronizálja ezeket a felhasználókat a felhővel, amely létrehoz egy felhasználói fiókot a UserType = Guest használatával. Az engedélyezési szempontból ezek a felhasználók nem különböztethetők meg az Azure AD B2B együttműködés Meghívási folyamatán keresztül létrehozott B2B-felhasználóktól.

A megvalósítási utasításokért lásd: [a UserType szinkronizálásának engedélyezése](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory B2B-együttműködés hibrid szervezeteknél](hybrid-organizations.md)
- [B2B-felhasználók engedélyezése az Azure AD-ben a helyszíni alkalmazásokhoz való hozzáféréshez](hybrid-cloud-to-on-premises.md)
- A Azure AD Connect áttekintését lásd: a [helyszíni címtárak integrálása Azure Active Directoryokkal](../hybrid/whatis-hybrid-identity.md).

