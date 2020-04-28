---
title: Licenc önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: További információ a Azure Active Directory az önkiszolgáló jelszó-visszaállítás licencelési követelményeinek különbségéről
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393067"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licencelési követelmények Azure Active Directory önkiszolgáló jelszó-visszaállításhoz

Az ügyfélszolgálati hívások csökkentése és a hatékonyság elvesztése, ha egy felhasználó nem tud bejelentkezni az eszközre vagy alkalmazásba, a Azure Active Directory (Azure AD) felhasználói fiókjait engedélyezheti az önkiszolgáló jelszó-visszaállítás (SSPR) számára. A SSPR-t alkotó szolgáltatások közé tartozik a jelszó módosítása, az Alaphelyzetbe állítás, a zárolás feloldása és a visszaírási egy helyszíni címtárban. Az alapszintű SSPR funkciók az Office 365 és az összes Azure AD-felhasználó számára ingyenesen elérhetők.

Ez a cikk az önkiszolgáló jelszó-visszaállítás különböző módszereit ismerteti és használhatja. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure ad díjszabási oldalát](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Kiadások és szolgáltatások összehasonlítása

A SSPR felhasználónként licenccel rendelkezik. A megfelelőség fenntartása érdekében a szervezeteknek hozzá kell rendelniük a megfelelő licencet a felhasználókhoz.

Az alábbi táblázat a jelszó módosítására, alaphelyzetbe állítására vagy helyszíni visszaírási vonatkozó különböző SSPR-forgatókönyveket ismerteti, amelyek a szolgáltatást biztosítják.

| Szolgáltatás | Azure AD Free | Office 365 Vállalati Prémium verzió | Microsoft 365 Business | prémium szintű Azure AD P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| **Csak felhőalapú felhasználói jelszó módosítása**<br />Ha a felhasználó az Azure AD-ban ismeri a jelszavát, és módosítani szeretné azt valami újat. | ● | ● | ● | ● |
| **Csak felhőalapú felhasználói jelszó alaphelyzetbe állítása**<br />Ha egy Azure AD-felhasználó elfelejtette a jelszavát, és vissza kell állítania azt. | | ● | ● | ● |
| **Hibrid felhasználói jelszó módosítása vagy alaphelyzetbe állítása helyszíni visszaírási**<br />Ha az Azure AD-ben egy olyan felhasználó van, amely egy helyszíni címtárból szinkronizálva van, Azure AD Connect szeretné módosítani vagy visszaállítani a jelszavát, és az új jelszót is vissza kell írnia a helyszíni környezetbe. | | | ● | ● |

> [!WARNING]
> Az önálló Office 365 licencelési csomagok nem támogatják a helyszíni visszaírási SSPR. Az Office 365 licencelési csomagjaihoz a funkció működéséhez prémium szintű Azure AD P1, prémium P2 vagy Microsoft 365 Vállalati verzió szükséges.

További licencelési információkért, beleértve a költségeket, tekintse meg a következő lapokat:

* [Díjszabás Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Szolgáltatások és képességek Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Csoport-vagy felhasználó-alapú licencelés engedélyezése

Az Azure AD támogatja a csoport alapú licencelést. A rendszergazdák tömegesen rendelhetnek hozzá licenceket egy adott felhasználói csoporthoz ahelyett, hogy egyszerre rendelnek hozzájuk egyet. További információ: [a licencekkel kapcsolatos problémák kiosztása, ellenőrzése és megoldása](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Néhány Microsoft-szolgáltatás nem érhető el minden helyen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell adnia a **használat helye** tulajdonságot a felhasználónál. A licencek hozzárendelése a Azure Portal **felhasználói** > **profil** > **beállításai** szakaszában végezhető el. *Ha a csoport licenc-hozzárendelését használja, a megadott használati hely nélküli felhasználók öröklik a címtár helyét.*

## <a name="next-steps"></a>További lépések

A SSPR megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)
