---
title: Önkiszolgáló jelszó-visszaállítás licenc - Azure Active Directory
description: Ismerje meg az Azure Active Directory önkiszolgáló jelszó-visszaállítási licencelési követelményeinek különbségét
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
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393067"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Az Azure Active Directory önkiszolgáló jelszó-alaphelyzetbe állításának licencelési követelményei

Az ügyfélszolgálati hívások csökkentése és a termelékenység csökkenése érdekében, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba, az Azure Active Directoryban (Azure AD) lévő felhasználói fiókok engedélyezhetők az önkiszolgáló jelszó-visszaállításhoz (SSPR). Az SSPR-t kihasználó szolgáltatások közé tartozik a jelszómódosítása, az alaphelyzetbe állítás, a feloldás és a visszaírás egy helyszíni könyvtárba. Az SSPR alapvető funkciói az Office 365-ben és az összes Azure AD-felhasználó számára ingyenesen elérhetők.

Ez a cikk részletezi az önkiszolgáló jelszó-visszaállítás különböző módjait. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure AD díjszabási lapját.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="compare-editions-and-features"></a>Kiadások és funkciók összehasonlítása

Az SSPR felhasználónként licencelt. A megfelelőség fenntartása érdekében a szervezeteknek hozzá kell rendelniük a megfelelő licencet a felhasználóikhoz.

Az alábbi táblázat ismerteti a jelszómódosítás, az alaphelyzetbe állítás vagy a helyszíni visszaírás különböző SSPR-forgatókönyveit, és azt, hogy mely ska-k biztosítják a szolgáltatást.

| Szolgáltatás | Azure AD Free | Office 365 Vállalati Prémium verzió | Microsoft 365 Business | Azure AD Premium P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| **Csak felhőalapú felhasználói jelszó módosítása**<br />Ha az Azure AD egyik felhasználója ismeri a jelszavát, és módosítani szeretné azt valami újra. | ● | ● | ● | ● |
| **Csak felhőalapú felhasználói jelszó alaphelyzetbe állítása**<br />Ha az Azure AD egyik felhasználója elfelejtette a jelszavát, és alaphelyzetbe kell állítania azt. | | ● | ● | ● |
| **Hibrid felhasználói jelszó módosítása vagy alaphelyzetbe állítása on-prem visszaírással**<br />Ha az Azure AD egyik felhasználója, amely egy helyszíni címtárból szinkronizálva van az Azure AD Connect használatával, módosítani vagy alaphelyzetbe kívánja állítani a jelszavát, és vissza szeretné írni az új jelszót a prem-re. | | | ● | ● |

> [!WARNING]
> Az önálló Office 365-licencelési csomagok nem támogatják az SSPR-t helyszíni visszaírással. Az Office 365 licencelési csomagjai ehhez a funkcióhoz az Azure AD Premium P1, a Premium P2 vagy a Microsoft 365 Business használatához szükséges.

További licencelési információkat, beleértve a költségeket, lásd a következő oldalakat:

* [Az Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory szolgáltatásai és képességei](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Nagyvállalati verzió](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Csoport- vagy felhasználóalapú licencelés engedélyezése

Az Azure AD támogatja a csoportalapú licencelést. A rendszergazdák tömegesen rendelhetnek licenceket a felhasználók egy csoportjához, ahelyett, hogy egyenként hozzárendelenék őket. További információt a [Licencek hozzárendelése, ellenőrzése és megoldása című témakörben talál.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)

Egyes Microsoft-szolgáltatások nem érhetők el minden helyen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, a rendszergazdának meg kell **adnia** a felhasználó Használati hely tulajdonságát. A licencek hozzárendelése az Azure Portal **Felhasználói** > **profil** > **beállításai** szakaszában végezhető el. *A csoportos licenchozzárendelés használatakor a megadott használati hely nélküli felhasználók öröklik a könyvtár helyét.*

## <a name="next-steps"></a>További lépések

Az SSPR első lépéseihez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)
