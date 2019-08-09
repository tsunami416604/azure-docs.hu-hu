---
title: Licenc önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítás licencelési követelményei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7463b86ea20a712937b8ccba7997c2551aae5be6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879108"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licencelési követelmények az Azure AD önkiszolgáló jelszó-visszaállításhoz

Azure Active Directory (Azure AD) több kiadásban is megtalálható: Ingyenes, prémium P1 és prémium P2. Számos különböző funkció áll rendelkezésre, amelyek az Azure AD különböző kiadásaiban elérhető, az önkiszolgáló jelszó-visszaállítást, például a változást, az alaphelyzetbe állítást, a zárolást és a visszaírási teszik elérhetővé. Ez a cikk a különbségeket ismerteti. Az egyes Azure AD-kiadásokban található szolgáltatások további részletei a [Azure Active Directory díjszabási oldalán](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="compare-editions-and-features"></a>Kiadások és szolgáltatások összehasonlítása

Az Azure AD önkiszolgáló jelszó-visszaállítás felhasználónként licenccel rendelkezik, hogy a megfelelőségi szervezetek megőrizzék a megfelelő licencet a felhasználók számára.

* Önkiszolgáló jelszómódosítás felhőfelhasználók számára
   * **Csak Felhőbeli felhasználó** vagyok, és tudom a jelszavam.
      * Szeretném **megváltoztatni** a jelszavam valami újat.
   * Ezt a funkciót az Azure AD minden kiadása tartalmazza.

* Önkiszolgáló jelszóátállítás felhőfelhasználók számára
   * **Csak Felhőbeli felhasználó** vagyok, és elfelejtettem a jelszavam.
      * Szeretném alaphelyzetbe **állítani** a jelszavamat.
   * Ez a funkció a P1 vagy P2 prémium szintű Azure AD része, vagy Microsoft 365 Vállalati verzió.

* Önkiszolgáló jelszó-visszaállítás/-módosítás/ **-zárolás a helyszíni visszaírási**
   * **Hibrid felhasználó** vagyok a helyszíni Active Directory felhasználói fiókja szinkronizálva van az Azure ad-fiókkal Azure ad Connect használatával. Szeretném megváltoztatni a jelszavam, elfelejtettem a jelszavam, vagy ki lett zárva.
      * Szeretném megváltoztatni a jelszavam, vagy visszaállítom a fiókomat vagy feloldani a fiókomat, **és** ezt a módosítást a helyszíni Active Directoryra szinkronizálják.
   * Ez a funkció a P1 vagy P2 prémium szintű Azure AD része, vagy Microsoft 365 Vállalati verzió.

> [!WARNING]
> Az önálló Office 365 licencelési csomagok *nem támogatják az "önkiszolgáló jelszó-visszaállítás/módosítás/zárolás a helyszíni visszaírási"* lehetőséget, és olyan csomagot igényelnek, amely magában foglalja a prémium szintű Azure ad P1, prémium P2 vagy Microsoft 365 vállalati verzió használatát a funkció működéséhez.
>

A további licencelési információk, beleértve a költségeket, a következő lapokon találhatók:

* [Azure Active Directory díjszabási hely](https://azure.microsoft.com/pricing/details/active-directory/)
* [Szolgáltatások és képességek Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Vállalati verzió szolgáltatás leírása](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Csoport-vagy felhasználó-alapú licencelés engedélyezése

Az Azure AD mostantól támogatja a csoport alapú licencelést. A rendszergazdák tömegesen rendelhetnek hozzá licenceket egy adott felhasználói csoporthoz ahelyett, hogy egyszerre rendelnek hozzájuk egyet. További információ: [a licencekkel kapcsolatos problémák kiosztása, ellenőrzése és megoldása](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Nem minden Microsoft-szolgáltatás érhető el minden területen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a **használat helye** tulajdonságot a felhasználónál. A licencek hozzárendelése a Azure Portal **felhasználói** > **profil** > **beállításai** szakaszában végezhető el. *Ha a csoport licenc-hozzárendelését használja, a megadott használati hely nélküli felhasználók öröklik a címtár helyét.*

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
