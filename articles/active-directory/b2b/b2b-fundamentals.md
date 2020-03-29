---
title: Az Azure Active Directory B2B gyakorlati tanácsai és javaslatai
description: Ismerje meg az azure-beli Active Directoryban a vállalkozások (B2B) vendégfelhasználók elérésére vonatkozó gyakorlati tanácsokat és javaslatokat.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050852"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Az Azure Active Directory B2B gyakorlati tanácsok
Ez a cikk javaslatokat és gyakorlati tanácsokat tartalmaz az Azure Active Directoryban (Azure AD) való együttműködéshez.

   > [!IMPORTANT]
   > **2021. március 31-től a**Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure AD-fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Az előkészítés során arra bátorítjuk ügyfeleinket, hogy válasszák az [e-mailes egyszeri jelkód-hitelesítést.](one-time-passcode.md) Örömmel fogadjuk a nyilvános előnézeti funkcióval kapcsolatos visszajelzéseit, és izgatottan várjuk, hogy még több együttműködési módot hozzon létre.

## <a name="b2b-recommendations"></a>B2B ajánlások
| Ajánlás | Megjegyzések |
| --- | --- |
| Az optimális bejelentkezési élmény érdekében összekell egyeztetniaz identitásszolgáltatókkal | Amikor csak lehetséges, közvetlenül az identitásszolgáltatókkal kell összeigazolnia, hogy a meghívott felhasználók bejelentkezhessenek a megosztott alkalmazásokba és erőforrásokba anélkül, hogy Microsoft-fiókokat (MSA- vagy Azure AD-fiókokat) vagy Azure AD-fiókokat kellene létrehozniuk. A Google [összevonási funkciójával](google-federation.md) engedélyezheti, hogy a B2B vendégfelhasználók bejelentkezhessenek google-fiókjukkal. Vagy a Közvetlen [összevonás (előzetes verzió) funkcióval](direct-federation.md) közvetlen összevonást állíthat be minden olyan szervezettel, amelynek identitásszolgáltatója (IdP) támogatja az SAML 2.0 vagy a WS-Fed protokollt. |
| Használja az E-mail egyszeri jelkód (előzetes verzió) funkciót olyan B2B vendégek számára, akik más módon nem tudnak hitelesíteni | Az [egyszeri e-mail -kód (előzetes verzió)](one-time-passcode.md) szolgáltatás hitelesíti a B2B vendégfelhasználókat, ha nem hitelesíthetők más eszközökkel, például az Azure AD-vel, egy Microsoft-fiókkal (MSA) vagy a Google-összevonással. Amikor a vendégfelhasználó bevált egy meghívót, vagy hozzáfér egy megosztott erőforráshoz, kérhet egy ideiglenes kódot, amelyet a rendszer az e-mail címére küld. Ezután beírják ezt a kódot a bejelentkezés folytatásához. |
| Vállalati arculat megjelenítése a bejelentkezési oldalon | Testreszabhatja a bejelentkezési lapot, hogy intuitívabb legyen a B2B vendégfelhasználók számára. Megtudhatja, hogy [miként adhat hozzá vállalati márkajelzést a bejelentkezéshez, valamint a Hozzáférési panel lapjait.](../fundamentals/customize-branding.md) |
| Adja hozzá adatvédelmi nyilatkozatát a B2B vendégfelhasználók beváltási élményéhez | A szervezet adatvédelmi nyilatkozatának URL-címét hozzáadhatja az első meghívásbeváltási folyamathoz, hogy a meghívott felhasználónak hozzá kell járulnia az adatvédelmi feltételek folytatásához. Tekintse [meg az Útmutató: Adja meg szervezete adatvédelmi adatait az Azure Active Directoryban.](https://aka.ms/adprivacystatement) |
| A tömeges meghívás (előnézet) funkció használata több B2B vendégfelhasználó meghívására egyszerre | Egyszerre több vendégfelhasználómeghívása a szervezetbe az Azure Portalon a tömeges meghívás előnézeti funkciójával. Ez a funkció lehetővé teszi, hogy feltöltegy CSV-fájlt a B2B vendégfelhasználók létrehozásához és a meghívók tömeges küldéséhez. Lásd: [Oktatóanyag a B2B felhasználók tömeges meghívásához.](tutorial-bulk-invite.md) |
| Feltételes hozzáférési házirendek kényszerítése többtényezős hitelesítéshez (MFA) | Javasoljuk, hogy érvényesítse az MFA-szabályzatokat a partner B2B-felhasználókkal megosztani kívánt alkalmazásokon. Ily módon az MFA következetesen érvényesíti az alkalmazások at a bérlő, függetlenül attól, hogy a partnerszervezet használ MFA. Lásd: [Feltételes hozzáférés a B2B együttműködési felhasználók számára.](conditional-access.md) |
| Ha eszközalapú feltételes hozzáférési házirendeket kényszerít, a Kizárási listák használatával engedélyezze a hozzáférést a B2B-felhasználók számára | Ha az eszközalapú feltételes hozzáférési házirendek engedélyezve vannak a szervezetben, a B2B vendégfelhasználói eszközök le lesznek tiltva, mert nem a szervezet kezeli őket. Létrehozhat konkrét partnerfelhasználókat tartalmazó kizárási listákat, hogy kizárja őket az eszközalapú feltételes hozzáférési házirendből. Lásd: [Feltételes hozzáférés a B2B együttműködési felhasználók számára.](conditional-access.md) |
| Bérlőspecifikus URL-cím használata a B2B vendégfelhasználókra mutató közvetlen hivatkozások biztosításakor | A meghívó e-mail alternatívájaként közvetlen hivatkozást adhat a vendégnek az alkalmazásra vagy a portálra. Ennek a közvetlen kapcsolatnak bérlőspecifikusnak kell lennie, ami azt jelenti, hogy tartalmaznia kell egy bérlői azonosítót vagy ellenőrzött tartományt, hogy a vendég hitelesíthető legyen a bérlőben, ahol a megosztott alkalmazás található. Lásd: [A vendégfelhasználó beváltási élménye.](redemption-experience.md) |
| Alkalmazás fejlesztésekor a UserType segítségével határozza meg a vendégfelhasználói élményt  | Ha egy alkalmazást fejleszt, és különböző felhasználói élményt szeretne biztosítani a bérlői és vendégfelhasználók számára, használja a UserType tulajdonságot. A UserType jogcím jelenleg nem szerepel a jogkivonatban. Az alkalmazásoknak a Microsoft Graph API-t kell használniuk a címtár lekérdezéséhez, hogy a felhasználó lekérheti a UserType típusukat. |
| A UserType tulajdonság módosítása *csak* akkor, ha a felhasználó és a szervezet kapcsolata megváltozik | Bár a PowerShell segítségével a Felhasználó UserType tulajdonságát tagról vendégre konvertálhatja (és fordítva), csak akkor módosítsa ezt a tulajdonságot, ha a felhasználó és a szervezet kapcsolata megváltozik. Lásd: [A B2B vendégfelhasználó tulajdonságai](user-properties.md).|

## <a name="next-steps"></a>További lépések

[B2B-megosztás kezelése](delegate-invitations.md)
