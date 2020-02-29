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
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77196133"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Az Azure AD B2B együttműködés korlátai
Azure Active Directory (Azure AD) VÁLLALATKÖZI együttműködés jelenleg a jelen cikkben ismertetett korlátozások hatálya alá esik.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges kettős multi-Factor Authentication
Az Azure AD B2B használatával kikényszerítheti a többtényezős hitelesítést az erőforrás-szervezetben (a meghívó szervezetnél). Ennek a megközelítésnek az okai a [vállalatközi együttműködés felhasználói számára feltételes hozzáféréssel kapcsolatosak](conditional-access.md). Ha egy partner már rendelkezik a többtényezős hitelesítés beállításával és betartatásával, előfordulhat, hogy a felhasználóknak egyszer kell elvégezniük a hitelesítést a saját szervezetében, majd ismét a tiéd.

## <a name="instant-on"></a>Azonnali bekapcsolás
A B2B együttműködés folyamataiban felhasználókat adunk hozzá a címtárhoz, és dinamikusan frissítjük őket a meghívások beváltásakor, az alkalmazás-hozzárendeléskor és így tovább. A frissítések és írások általában egy címtárbeli példányon történnek, és az összes példányon replikálni kell őket. A replikáció az összes példány frissítésekor fejeződik be. Időnként előfordulhat, hogy az objektum egy példányban íródott vagy frissül, és az objektum lekérésére irányuló hívás egy másik példányra esik, a replikációs késések is előfordulhatnak. Ha ez történik, frissítse vagy próbálkozzon újra a súgóval. Ha az API-t használó alkalmazást ír, akkor az újrapróbálkozások némelyike jó, védekező megoldás a probléma enyhítése érdekében.

## <a name="azure-ad-directories"></a>Azure AD-címtárak
Az Azure AD B2B az Azure AD szolgáltatási könyvtárának korlátaira vonatkozik. A felhasználók által létrehozható könyvtárak számával és azon könyvtárak számával kapcsolatos részletekért, amelyekhez a felhasználó vagy a vendég felhasználó tartozhat, tekintse meg az [Azure ad szolgáltatás korlátai és korlátozásai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)című témakört.

## <a name="national-clouds"></a>Nemzeti felhők
Az [országos felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) fizikailag elkülönített Azure-példányok. A B2B együttműködés nem támogatott a nemzeti felhő határain belül. Ha például az Azure-bérlő nyilvános, globális felhőben van, nem hívhat meg olyan felhasználót, akinek a fiókja egy nemzeti felhőben található. A felhasználóval való együttműködéshez kérje meg őket egy másik e-mail-címre, vagy hozzon létre egy tag felhasználói fiókot a címtárban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B együttműködési meghívások delegálása](delegate-invitations.md)

