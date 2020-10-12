---
title: Azure Active Directory B2B – ajánlott eljárások és javaslatok
description: Ismerkedjen meg az ajánlott eljárásokat és javaslatokat a vállalatok közötti (B2B) vendég felhasználói hozzáférés Azure Active Directory.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909690"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B – ajánlott eljárások
Ez a cikk ajánlásokat és ajánlott eljárásokat tartalmaz a vállalatok közötti (B2B) együttműködéshez Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **2021. március 31-ig**a Microsoft többé nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

## <a name="b2b-recommendations"></a>B2B-javaslatok
| Ajánlás | Megjegyzések |
| --- | --- |
| Az összevonása való optimális bejelentkezési élmény érdekében | Amikor csak lehetséges, a összevonása közvetlenül az Identity Providers használatával engedélyezheti a meghívott felhasználóknak, hogy Microsoft-fiókok (MSAs) vagy Azure AD-fiókok létrehozása nélkül jelentkezzenek be a megosztott alkalmazásokba és az erőforrásokra. A [Google összevonási szolgáltatással](google-federation.md) engedélyezheti a B2B vendég felhasználói számára a Google-fiókkal való bejelentkezést. Vagy használhatja a [közvetlen összevonás (előzetes verzió) szolgáltatást](direct-federation.md) úgy, hogy közvetlen összevonást állítson be bármely olyan szervezettel, amelynek az identitás-szolgáltatója (identitásszolgáltató) támogatja az SAML 2,0 vagy WS-Fed protokollt. |
| Az e-mailes egyszeri jelszó (előzetes verzió) funkció használata a B2B-vendégek számára, akik nem tudnak más módon hitelesíteni magukat | Az [egyszer használatos e-mail-jelszó (előzetes verzió)](one-time-passcode.md) funkció hitelesíti a B2B vendég felhasználókat, ha nem hitelesíthetők más módon, például az Azure ad-vel, a Microsoft-fiók (MSA) vagy a Google Federation szolgáltatással. Ha a vendég felhasználó bevált egy meghívót, vagy egy megosztott erőforráshoz fér hozzá, ideiglenes kódot kérhet, amelyet a rendszer elküld az e-mail-címére. Ezután a kód beírásával folytathatja a bejelentkezést. |
| Vállalati arculat megjelenítése a bejelentkezési oldalon | Testreszabhatja a bejelentkezési oldalát, így az jobban használható a B2B vendég felhasználói számára. Lásd: [vállalati arculat hozzáadása a bejelentkezéshez és a hozzáférési panel oldalaihoz](../fundamentals/customize-branding.md). |
| Adatvédelmi nyilatkozat hozzáadása a B2B vendég felhasználói beváltási felületéhez | Felveheti a szervezete adatvédelmi nyilatkozatának URL-címét az első alkalommal meghívó beváltási folyamatba, hogy a meghívott felhasználónak el kell fogadnia az adatvédelmi feltételeit a folytatáshoz. További információ [: a szervezet adatvédelmi adatainak hozzáadása Azure Active Directoryban](https://aka.ms/adprivacystatement). |
| A tömeges meghívó (előzetes verzió) szolgáltatással egyszerre több B2B vendég-felhasználót hívhat meg | Egyszerre több vendég felhasználót is meghívhat a szervezete számára a Azure Portalban található tömeges Meghívási Előnézet funkció használatával. Ez a funkció lehetővé teszi egy CSV-fájl feltöltését a B2B vendég felhasználók létrehozására és a meghívások tömeges küldésére. Lásd: [oktatóanyag a B2B-felhasználók tömeges meghívásához](tutorial-bulk-invite.md). |
| Multi-Factor Authenticationra vonatkozó feltételes hozzáférési szabályzatok betartatása (MFA) | Javasoljuk, hogy az MFA-szabályzatokat a partner B2B-felhasználókkal megosztani kívánt alkalmazásokra érvényesítse. Így az MFA-t következetesen érvényesítjük a bérlő alkalmazásaiban, függetlenül attól, hogy a partnerszervezet használ-e MFA-t. Tekintse [meg a vállalatközi csoportmunka-felhasználók feltételes hozzáférését](conditional-access.md)ismertető témakört. |
| Ha az eszközökön alapuló feltételes hozzáférési szabályzatokat kényszeríti, a kizárási listával engedélyezheti a hozzáférést a B2B-felhasználók számára | Ha az eszközön alapuló feltételes hozzáférési szabályzatok engedélyezve vannak a munkahelyen, a B2B vendég felhasználói eszközei le lesznek tiltva, mert nem a szervezet felügyeli. Létrehozhat olyan kizárási listát, amely adott partner felhasználókat tartalmaz, hogy kizárják őket az eszközön alapuló feltételes hozzáférési szabályzatból. Tekintse [meg a vállalatközi csoportmunka-felhasználók feltételes hozzáférését](conditional-access.md)ismertető témakört. |
| Bérlő-specifikus URL-cím használata, ha közvetlen hivatkozásokat biztosít a B2B vendég felhasználóinak | A meghívót tartalmazó e-mailek alternatívájaként az alkalmazásra vagy a portálra mutató közvetlen hivatkozást is megadhat. A közvetlen hivatkozásnak bérlőre jellemzőnek kell lennie, vagyis tartalmaznia kell egy bérlői azonosítót vagy egy ellenőrzött tartományt, hogy a vendég hitelesíthető legyen a bérlőben, ahol a megosztott alkalmazás található. Tekintse [meg a vendég felhasználó beváltási élményét](redemption-experience.md). |
| Alkalmazás fejlesztésekor a UserType használatával határozza meg a vendég felhasználói élményt  | Ha alkalmazást fejleszt, és különböző élményeket szeretne biztosítani a bérlői felhasználók és a vendég felhasználók számára, használja a UserType tulajdonságot. A UserType jogcím jelenleg nem szerepel a jogkivonatban. Az alkalmazásoknak a Microsoft Graph API-t kell használniuk ahhoz, hogy lekérdezzek a felhasználó címtárát, hogy megkapják a UserType. |
| A UserType tulajdonság *csak* akkor módosítható, ha a felhasználó kapcsolata a szervezet változásával | Habár lehetséges, hogy a PowerShell használatával a tag UserType tulajdonságát a vendégnek (és fordítva) is át lehet alakítani, ezt a tulajdonságot csak akkor kell módosítania, ha a felhasználó a szervezethez való viszonya megváltozik. Tekintse meg [a B2B vendég felhasználó tulajdonságait](user-properties.md).|

## <a name="next-steps"></a>Következő lépések

[B2B-megosztás kezelése](delegate-invitations.md)
