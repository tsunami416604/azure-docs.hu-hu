---
title: Licenc önkiszolgáló jelszó-visszaállítási – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítási licencelési követelményeket
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e185b67ae73b86b5f1c3b6cda884de05eb89c6fd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049084"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Az Azure AD önkiszolgáló jelszó-licencelési követelményei alaphelyzetbe állítása

Ahhoz, hogy az Azure Active Directory (Azure AD) jelszó kéréséhez hogy *rendelkeznie kell legalább egy, a szervezet licenccel* az adott felhasználó. A megfelelő licencre van szükség, ha a felhasználó akár közvetlen, akár közvetett módon egy adott licenchez tartozó funkció előnyeihez jut.

* **Csak felhőalapú felhasználói**: Office 365 fizetős Termékváltozat vagy alapszintű Azure AD
* **Felhőalapú** vagy **a helyszíni felhasználók**: Azure AD Premium P1 vagy P2, Enterprise Mobility + Security (EMS) vagy a Microsoft 365

## <a name="licensing-requirements-for-password-writeback"></a>Jelszóvisszaíró licencelési követelményeket

**Az önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás helyszíni visszaírással a rendszer egy Azure ad premium-funkció**. Licenceléssel kapcsolatos további információkért lásd: a [Azure Active Directory díjszabását ismertető a hely](https://azure.microsoft.com/pricing/details/active-directory/).

A jelszóvisszaíró használandó kell rendelkeznie a következő licenccel, a bérlő egyik:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3 vagy A3
* Enterprise Mobility + Security E5 vagy a5 csomag
* A Microsoft 365 E3 vagy A3
* A Microsoft 365 E5 vagy a5 csomag
* A Microsoft 365 F1 csomag

> [!WARNING]
> Önálló Office 365 licencelési csomagok *nem támogatják a jelszóvisszaírást* , és rendelkezik a fenti csomagok keretében, az a funkciók működéséhez szükséges.
>

További licencelési információk, beleértve a költségek, a következő lapokon található:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Engedélyezze a csoport- vagy felhasználói alapú licencelése

Most már Azure ad-ben támogatja a Csoportalapú licencelés. A rendszergazdák tömeges licenceket rendelhet a felhasználók, mint az őket egyenként csoportja számára. További információkért lásd: [hozzárendelése, ellenőrzése és licencek kapcsolatos problémák megoldásához](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Nem minden Microsoft-szolgáltatás érhető el minden területen. Mielőtt egy úgy lehet licencet a felhasználóhoz, a rendszergazdának meg kell adnia a **a felhasználási hely** tulajdonság a felhasználóra. Licencek hozzárendelése alapján teheti meg a **felhasználói** > **profil** > **beállítások** szakaszban az Azure Portalon. *A licenc-hozzárendelés használatakor bármely felhasználó felhasználás helyének megadása nélkül örökli a könyvtár helye.*

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
