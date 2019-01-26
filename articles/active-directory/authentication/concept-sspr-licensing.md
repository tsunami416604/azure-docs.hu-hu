---
title: Licenc Azure Active Directory önkiszolgáló jelszó-
description: Az Azure AD önkiszolgáló jelszó-visszaállítási licencelési követelményeket
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: f7e539b2f444109e902f4b85d0a879183217f262
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076247"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Az Azure AD önkiszolgáló jelszó-licencelési követelményei alaphelyzetbe állítása

Az Azure Active Directory (Azure AD) négy változatban érhető el: Ingyenes, alapszintű, prémium P1 és prémium P2 szintű. Számos különböző szolgáltatásokat, amelyek be új jelszó önkiszolgáló kérésének, módosítás, például alaphelyzetbe állítása, zárolásának feloldásához és a jelszóvisszaíró, az Azure AD különböző kiadásai által biztosított vannak. Ez a cikk próbál szemléltetik az eltéréseket. Mindegyik Azure AD-kiadás szolgáltatásait, további részletek találhatók a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Kiadások és szolgáltatások összehasonlítása

Az Azure AD önkiszolgáló jelszó-visszaállítás van licenccel rendelkezniük, a jövőben is megfelel a szervezetek a megfelelő licenc hozzárendelése a felhasználók szükségesek.

* Önkiszolgáló jelszómódosítás felhőfelhasználók számára
   * Én vagyok egy **csak felhőalapú felhasználói** és ismernie kell a jelszót.
      * Szeretném, ha **módosítása** egy új jelszó.
   * Ez a funkció az Azure AD minden kiadása tartalmazza.

* Önkiszolgáló jelszóátállítás felhőfelhasználók számára
   * Én vagyok egy **csak felhőalapú felhasználói** és elfelejtette a jelszót.
      * Szeretném, ha **alaphelyzetbe** valami tudom a jelszavam.
   * Ez a funkció az Azure AD alapszintű, prémium P1 vagy P2 vagy a Microsoft 365 vállalati tartalmazza.

* Az önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás **a helyszíni visszaírással**
   * Én vagyok egy **hibrid felhasználói** saját helyszíni Active Directory felhasználói fiók szinkronizálva van az Azure AD-fiókot az Azure AD Connect használatával. Szeretnék saját jelszó módosítása, elfelejti a jelszót, vagy zárolva lett.
      * Szeretném, ha a jelszó módosítása vagy visszaállítása, valami I ismeri, vagy saját fiók feloldása **és** , hogy szinkronizálja vissza módosítása a helyi Active Directoryban.
   * Ez a funkció Azure AD Premium P1 vagy P2, vagy a Microsoft 365 vállalati tartalmazza.

> [!WARNING]
> Önálló Office 365 licencelési csomagok *nem támogatják a "Önkiszolgáló jelszó alaphelyzetbe állítása/módosítás /-Zárolásfeloldás helyszíni visszaírással az"* és szükség van egy csomagra, amely tartalmazza ennek az Azure AD Premium P1, prémium P2 szintű vagy a Microsoft 365 vállalati a funkció működéséhez.
>

További licencelési információk, beleértve a költségek, a következő lapokon található:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)
* [A Microsoft 365 vállalati szolgáltatás leírása](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description)

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
