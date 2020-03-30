---
title: A B2B együttműködés korlátai - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B együttműködésjelenlegi korlátai
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263360"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Az Azure AD B2B együttműködés korlátai
Az Azure Active Directory (Azure AD) B2B-együttműködésjelenleg a jelen cikkben ismertetett korlátozások hatálya alá tartozik.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges többtényezős hitelesítés
Az Azure AD B2B használatával többtényezős hitelesítést kényszeríthet ki az erőforrás-szervezetnél (a meghívó szervezetnél). Ennek a megközelítésnek az okait a [Feltételes hozzáférés a B2B együttműködési felhasználók számára](conditional-access.md)című részben részletezi. Ha egy partner már rendelkezik többtényezős hitelesítésbeállítással és kényszerítéssel, előfordulhat, hogy a felhasználóknak egyszer kell végrehajtaniuk a hitelesítést az otthoni szervezetben, majd ismét a saját szervezetében.

## <a name="instant-on"></a>Azonnali bekapcsolás
A B2B együttműködési folyamatokban felhasználókat adunk a címtárhoz, és dinamikusan frissítjük őket a meghívás-beváltás, az alkalmazás-hozzárendelés és így tovább során. A frissítések és írások általában egy címtárpéldányban történnek, és az összes példányban replikálni kell őket. A replikáció az összes példány frissítése után befejeződik. Néha, amikor az objektumot egy példányban írják vagy frissítik, és az objektum lekérésére irányuló hívás egy másik példányhoz tartozik, replikációs késések léphetnek fel. Ha ez megtörténik, frissítse vagy próbálkozzon újra a súgóban. Ha az API-n keresztül ír egy alkalmazást, akkor a biztonsági másolatok egy jó, védekező gyakorlat a probléma enyhítésére.

## <a name="azure-ad-directories"></a>Azure AD-könyvtárak
Az Azure AD B2B az Azure AD szolgáltatás címtárkorlátai. A felhasználó által létrehozható könyvtárak számáról és a felhasználó vagy vendégfelhasználó által tartható könyvtárak számáról az [Azure AD szolgáltatás korlátai és korlátozásai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)című témakörben talál.

## <a name="national-clouds"></a>Nemzeti felhők
[A nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) fizikailag elszigetelt Azure-példányok. A B2B-együttműködés nem támogatott a nemzeti felhőhatárokon átnyúlóan. Ha például az Azure-bérlő a nyilvános, globális felhőben található, nem hívhat meg olyan felhasználót, akinek a fiókja egy nemzeti felhőben van. A felhasználóval való együttműködéshez kérjen meg egy másik e-mail címet, vagy hozzon létre számukra tagfelhasználói fiókot a címtárban.

## <a name="azure-us-government-clouds"></a>Az Azure AMERIKAI Kormány felhői
Az Azure US Government-felhőn belül a B2B-együttműködés jelenleg csak az Azure US Government felhőben található bérlők között támogatott, és mindkettő támogatja a B2B-együttműködést. Ha olyan bérlőt hív meg egy bérlőhöz, amely nem része az Azure US Government felhőjének, vagy amely még nem támogatja a B2B együttműködést, a meghívás sikertelen lesz, vagy a felhasználó nem tudja beváltani a meghívót. Az egyéb korlátozásokról az [Azure Active Directory Premium P1 és P2 változatok](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)ban talál további információt.

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Hogyan állapítható meg, hogy a B2B-együttműködés elérhető-e az Azure US Government-beli bérlőmben?
Ha meg szeretné tudni, hogy az Azure US Government felhőalapú bérlője támogatja-e a B2B-együttműködést, tegye a következőket:

1. A böngészőben lépjen a következő URL-címre, és helyettesítse a bérlő nevét a * &lt;bérlőnévvel:&gt;*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Keresse `"tenant_region_scope"` meg a JSON választ:

   - Ha `"tenant_region_scope":"USGOV”` megjelenik, a B2B támogatott.
   - Ha `"tenant_region_scope":"USG"` megjelenik, a B2B nem támogatott.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködési meghívók delegálása](delegate-invitations.md)

