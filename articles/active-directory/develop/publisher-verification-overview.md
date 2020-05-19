---
title: A kiadói ellenőrzés áttekintése – Microsoft Identity platform | Azure
description: Áttekintést nyújt a Microsoft Identity platform közzétevő-ellenőrzési programjáról (előzetes verzió). Felsorolja az előnyeit, a program követelményeit és a gyakran ismételt kérdéseket. Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan Microsoft Partner Network-fiókkal, amely befejezte az ellenőrzési folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: d050efe1224e2783cdad52a12c842f7abe44b7af
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597701"
---
# <a name="publisher-verification-preview"></a>Közzétevő ellenőrzése (előzetes verzió)

A kiadói ellenőrzés (előzetes verzió) segít a rendszergazdáknak és a végfelhasználóknak megérteni az alkalmazások fejlesztőinek a Microsoft Identity platformmal való integrálásának eredetiségét. Más szóval, a közzétevő egy ismert forrás vagy egy rossz színész, amely a jól ismert közzétevőt fedi le? Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan [Microsoft Partner Network](https://partner.microsoft.com/membership) -fiókkal, amely befejezte az [ellenőrzési](/partner-center/verification-responses) folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával. 

Egy kék "ellenőrzött" jelvény jelenik meg az Azure AD-hozzájárulási kérdésben és a többi képernyőn: ![ beleegyezés kérése](./media/publisher-verification-overview/consent-prompt.png)

Ez a funkció elsősorban olyan fejlesztők számára készült, akik több-bérlős alkalmazásokat fejlesztenek, amelyek a [OAuth 2,0-es és az OpenID-](active-directory-v2-protocols.md) t használják a [Microsoft Identity platformmal](v2-overview.md). Ezek az alkalmazások az OpenID Connect használatával írják alá a felhasználókat, vagy az OAuth 2,0-et használhatják az olyan API-k használatával való hozzáféréshez, mint a [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Előnyök
A kiadói ellenőrzés a következő előnyöket biztosítja:
- **Megnövekedett átláthatóság és a kockázatok csökkentése az ügyfelek számára**– ez a funkció segíti az ügyfeleket abban, hogy a szervezetében használt alkalmazásokat az általuk megbízhatónak tartott fejlesztők teszik közzé. 

- **Továbbfejlesztett védjegyezés**– az "ellenőrzött" jelvény jelenik meg az Azure ad- [hozzájárulási kérdés](application-consent-experience.md), a vállalati alkalmazások lapon, valamint a végfelhasználók és a rendszergazdák által használt további UX-felületek. 

- **Gördülékeny nagyvállalati elfogadás**– a rendszergazdák új felhasználói engedélyezési házirendeket állíthatnak be, és a közzétevő ellenőrzési állapota az egyik elsődleges házirend-feltétel lesz. 

- **Továbbfejlesztett kockázatértékelés**– a Microsoft által a "kockázatos" jóváhagyási kérelmekre vonatkozó észlelések a kiadói ellenőrzés jeleként is szerepelnek. 

## <a name="requirements"></a>Követelmények
A kiadói ellenőrzéshez néhány előfeltétel van, amelyek némelyikét már számos Microsoft-partner végezte el. Ezek a következők: 

-  Egy olyan érvényes [Microsoft Partner Network](https://partner.microsoft.com/membership) -fiók MPN-azonosítója, amely befejezte az [ellenőrzési](/partner-center/verification-responses) folyamatot. Az MPN-fióknak a szervezete [partner globális fiókjának (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) kell lennie. 

-  Egy DNS által ellenőrzött [Egyéni tartománnyal](/azure/active-directory/fundamentals/add-custom-domain)rendelkező Azure ad-bérlő. Az egyéni tartománynak meg kell egyeznie az előző lépésben az ellenőrzés során használt e-mail-cím tartományával. 

-  Egy Azure AD-bérlőben regisztrált alkalmazás, amelynek [közzétevő-tartománya](howto-configure-publisher-domain.md) a korábban használttal megegyező tartománnyal van konfigurálva. 

-  Az ellenőrzést végző felhasználó számára engedélyezni kell, hogy az Azure AD-ben és az MPN-fiókban is módosíthassa az alkalmazások regisztrációját. 

    -  Az Azure AD-ben a felhasználónak vagy az alkalmazás tulajdonosának kell lennie, vagy a következő [szerepkörök](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)egyikével kell rendelkeznie: Application admin, Cloud Application admin, globális rendszergazda. 

    -  A partner Centerben ennek a felhasználónak a következő [szerepkörökkel](/partner-center/permissions-overview)kell rendelkeznie: MPN-rendszergazda, fiókok rendszergazdája vagy globális rendszergazda (ez az Azure ad-ben elsajátított megosztott szerepkör).
    
-  A közzétevő elfogadja a [Microsoft Identity platformot a fejlesztők használati feltételeinek](/legal/microsoft-identity-platform/terms-of-use).

Azok a fejlesztők, akik már teljesítik ezeket az előfeltételeket, percek alatt ellenőrizhetik. Ha a követelmények nem teljesülnek, a beállítás ingyenes. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések 
Az alábbiakban néhány gyakori kérdést talál a közzétevő ellenőrzési programjával kapcsolatban. A követelményekkel és a folyamattal kapcsolatos gyakori kérdésekért lásd: [alkalmazás megjelölése közzétevőként – ellenőrzött](mark-app-as-publisher-verified.md).

- **Milyen __információkat biztosít a__ közzétevői ellenőrzés?**  Ha egy alkalmazás meg van jelölve, a közzétevő ellenőrizte, ez nem jelenti azt, hogy az alkalmazás vagy a közzétevője elvégezte-e az adott minősítést, megfelel-e az iparági szabványoknak, betartja az ajánlott eljárásokat stb. Más Microsoft-programok ezeket az információkat biztosítják, beleértve az [Microsoft 365 alkalmazás minősítését](/microsoft-365-app-certification/overview).

- **Mennyibe kerül ez a díj? Igényel licencet?** A Microsoft nem számítja fel a fejlesztők számára a közzétevők ellenőrzését, és nem igényel semmilyen konkrét licencet. 

- **Hogyan kapcsolódik a Microsoft 365 közzétevői igazoláshoz? Mi a helyzet Microsoft 365 alkalmazás-minősítéssel?** Ezek olyan kiegészítő programok, amelyek segítségével a fejlesztők megbízható alkalmazásokat hozhatnak létre, amelyeket az ügyfelek magabiztosan tudnak elfogadni. A folyamat első lépése a közzétevő ellenőrzése, és az összes fejlesztőnek el kell végeznie a fenti feltételeknek megfelelő alkalmazások létrehozását. 

  A Microsoft 365t is integráló fejlesztők további előnyöket kaphatnak a programoktól. További információkért tekintse meg [Microsoft 365 közzétevői igazolást](/microsoft-365-app-certification/docs/attestation) és [Microsoft 365 alkalmazás minősítését](/microsoft-365-app-certification/docs/certification). 

- **Ugyanaz, mint az Azure AD Application Gallery?** Nem – a közzétevő ellenőrzése egy kiegészítő, de különálló program a [Azure Active Directory alkalmazás-gyűjteményben](/azure/active-directory/azuread-dev/howto-app-gallery-listing). A fenti feltételeknek megfelelő fejlesztőknek a programban való részvételtől függetlenül kell befejezniük a közzétevő ellenőrzési folyamatát. 

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [jelölheti meg az alkalmazást közzétevőként](mark-app-as-publisher-verified.md).
* A közzétevő ellenőrzésének [hibakeresése](troubleshoot-publisher-verification.md) .