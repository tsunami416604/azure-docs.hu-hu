---
title: A B2B együttműködés korlátai – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-együttműködés jelenlegi korlátai
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
ms.openlocfilehash: 127f05298483dcf155000c2eda8f590fc069a1e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337665"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Az Azure AD B2B együttműködés korlátai
Azure Active Directory (Azure AD) VÁLLALATKÖZI együttműködés jelenleg a jelen cikkben ismertetett korlátozások hatálya alá esik.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges kettős multi-Factor Authentication
Az Azure AD B2B használatával kikényszerítheti a többtényezős hitelesítést az erőforrás-szervezetben (a meghívó szervezetnél). Ennek a megközelítésnek az okai a [vállalatközi együttműködés felhasználói számára feltételes hozzáféréssel kapcsolatosak](conditional-access.md). Ha egy partner már rendelkezik a többtényezős hitelesítés beállításával és betartatásával, előfordulhat, hogy a felhasználóknak egyszer kell elvégezniük a hitelesítést a saját szervezetében, majd ismét a tiéd.

## <a name="instant-on"></a>Azonnali bekapcsolás
A B2B együttműködés folyamataiban felhasználókat adunk hozzá a címtárhoz, és dinamikusan frissítjük őket a meghívások beváltásakor, az alkalmazás-hozzárendeléskor és így tovább. A frissítések és írások általában egy címtárbeli példányon történnek, és az összes példányon replikálni kell őket. A replikáció az összes példány frissítésekor fejeződik be. Időnként előfordulhat, hogy az objektum egy példányban íródott vagy frissül, és az objektum lekérésére irányuló hívás egy másik példányra esik, a replikációs késések is előfordulhatnak. Ha ez történik, frissítse vagy próbálkozzon újra a súgóval. Ha az API-t használó alkalmazást ír, akkor az újrapróbálkozások némelyike jó, védekező megoldás a probléma enyhítése érdekében.

## <a name="azure-ad-directories"></a>Azure AD-címtárak
Az Azure AD B2B az Azure AD szolgáltatási könyvtárának korlátaira vonatkozik. A felhasználók által létrehozható könyvtárak számával és azon könyvtárak számával kapcsolatos részletekért, amelyekhez a felhasználó vagy a vendég felhasználó tartozhat, tekintse meg az [Azure ad szolgáltatás korlátai és korlátozásai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)című témakört.

## <a name="national-clouds"></a>Országos felhők
Az [országos felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) fizikailag elkülönített Azure-példányok. A B2B együttműködés nem támogatott a nemzeti felhő határain belül. Ha például az Azure-bérlő nyilvános, globális felhőben van, nem hívhat meg olyan felhasználót, akinek a fiókja egy nemzeti felhőben található. A felhasználóval való együttműködéshez kérje meg őket egy másik e-mail-címre, vagy hozzon létre egy tag felhasználói fiókot a címtárban.

## <a name="azure-us-government-clouds"></a>Azure USA-beli kormányzati felhők
Az Amerikai Egyesült államokbeli kormányzati felhőben a B2B-együttműködés olyan bérlők között támogatott, amelyek az USA-beli kormányzati felhőben is elérhetők, és mindkettő támogatja a B2B-együttműködést. A B2B-együttműködést támogató Azure USA kormányzati bérlők Microsoft-vagy Google-fiókokkal is együttműködhetnek a közösségi felhasználókkal. Ha ezen csoportokon kívül hívja meg a felhasználót (például ha a felhasználó olyan bérlőn található, amely nem része az Azure US government-felhőnek, vagy még nem támogatja a B2B-együttműködést), a meghívás sikertelen lesz, vagy a felhasználó nem tudja beváltani a meghívót. További információk az egyéb korlátozásokról: [prémium szintű Azure Active Directory P1 és P2 változatok](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Honnan tudhatom meg, hogy a B2B együttműködés elérhető-e az Azure US government-bérlőben?
Ha szeretné megtudni, hogy az Azure US government Cloud bérlője támogatja-e a B2B-együttműködést, tegye a következőket:

1. A böngészőben nyissa meg a következő URL-címet, és cserélje le a bérlő nevét a * &lt; tenantname &gt; *:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Keresés `"tenant_region_scope"` a JSON-válaszban:

   - Ha `"tenant_region_scope":"USGOV”` megjelenik, a B2B támogatott.
   - Ha `"tenant_region_scope":"USG"` megjelenik, a B2B nem támogatott.

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködési meghívások delegálása](delegate-invitations.md)
