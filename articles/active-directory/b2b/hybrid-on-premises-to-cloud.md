---
title: Helyi partnerfiókok szinkronizálása felhőben B2B-felhasználóként – Azure AD
description: Az Azure AD B2B-együttműködéssel helyileg felügyelt külső partnerek nek is hozzáférést biztosíthatja a helyi és a felhőbeli erőforrásokhoz az azure AD B2B-együttműködéssel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272613"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Helyileg kezelt partnerfiókok hozzáférésének biztosítása a felhőbeli erőforrásokhoz az Azure AD B2B együttműködéshasználatával

Az Azure Active Directory (Azure AD) előtt a helyszíni identitásrendszerekkel rendelkező szervezetek hagyományosan felügyelt partnerfiókkal rendelkeznek a helyszíni címtárban. Egy ilyen szervezetben, amikor elkezdi áthelyezni az alkalmazásokat az Azure AD-be, győződjön meg arról, hogy partnerei hozzáférhetnek a szükséges erőforrásokhoz. Nem számít, hogy az erőforrások a helyszínen vagy a felhőben. Azt is szeretné, hogy a partnerfelhasználók ugyanazokat a bejelentkezési hitelesítő adatokat használhassák a helyszíni és az Azure AD-erőforrásokhoz is. 

Ha a külső partnerek számára fiókokat hoz létre a helyszíni címtárban (például létrehoz egy "wmoran" bejelentkezési nevet használó fiókot egy Wendy Moran nevű külső felhasználó számára a partners.contoso.com tartományban), most már szinkronizálhatja ezeket a fiókokat a Felhő. Pontosabban az Azure AD Connect segítségével szinkronizálhatja a partnerfiókokat a felhőben Azure AD B2B-felhasználók (azaz a UserType = Guest) használó felhasználók. Ez lehetővé teszi a partnerfelhasználók számára, hogy a felhőbeli erőforrásokhoz a helyi fiókokhoz hasonló hitelesítő adatokhasználatával férjenek hozzá anélkül, hogy a szükségesnél több hozzáférést biztosítanának számukra. 

## <a name="identify-unique-attributes-for-usertype"></a>A UserType egyedi attribútumainak azonosítása

Mielőtt engedélyezne a UserType attribútum szinkronizálását, először el kell döntenie, hogyan származtatja a UserType attribútumot a helyszíni Active Directoryból. Más szóval, milyen paraméterek a helyszíni környezetben egyediek a külső közreműködők számára? Határozza meg azt a paramétert, amely megkülönbözteti ezeket a külső közreműködőket a saját szervezet tagjaitól.

Ennek két közös megközelítése a következő:

- Jelöljön ki egy nem használt helyszíni Active Directory attribútumot (például az Extensionattribute1 attribútumot), amelyet forrásattribútumként szeretne használni. 
- Másik lehetőségként a UserType attribútum értékét más tulajdonságokból is levezetni. Ha például az összes felhasználót vendégként szeretné szinkronizálni, ha a helyszíni Active Directory UserPrincipalName attribútumuk a * \@tartománypartners.contoso.com.*
 
A részletes attribútumkövetelményekről a [UserType szinkronizálásának engedélyezése](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)című témakörben van. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Az Azure AD Connect konfigurálása a felhasználók felhővel való szinkronizálásához

Miután azonosította az egyedi attribútumot, konfigurálhatja az Azure AD Connectet, hogy ezeket a felhasználókat azure AD B2B-felhasználókként (azaz UserType = Guest) szinkronizálja a felhőben. Engedélyezési szempontból ezek a felhasználók megkülönböztethetetlenek az Azure AD B2B együttműködési meghívási folyamaton keresztül létrehozott B2B-felhasználóktól.

A megvalósítási utasításokról a [UserType szinkronizálásának engedélyezése témakörben talál.](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)

## <a name="next-steps"></a>További lépések

- [Azure Active Directory B2B együttműködés hibrid szervezetek számára](hybrid-organizations.md)
- [B2B-felhasználók nak az Azure AD-ben való hozzáférés megadása a helyszíni alkalmazásokhoz](hybrid-cloud-to-on-premises.md)
- Az Azure AD Connect áttekintése a [helyszíni könyvtárak integrálása az Azure Active Directoryval című témakörben található.](../hybrid/whatis-hybrid-identity.md)

