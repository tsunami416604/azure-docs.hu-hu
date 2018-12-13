---
title: Hogyan jelszavak letiltása az Azure ad-ben
description: Az Azure ad-ben dinamikusan letiltott passwrords a envirionment a gyenge jelszavakat letiltása
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: c4a289d1aeefc35f07e7598adf3780620b11f0da
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252201"
---
# <a name="configuring-the-custom-banned-password-list"></a>A letiltott jelszavak egyéni lista konfigurálása

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Számos szervezet található, a felhasználók létre közös helyi szavakat, például egy iskolai, focicsapata vagy híres személy, így könnyen kitalálható jelszavakat. A Microsoft egyéni letiltott jelszavak lista lehetővé teszi a szervezetek számára, hogy adja hozzá a karakterláncok kiértékelése és letiltására, mellett a globális le van tiltva jelszólista, ha a felhasználók és rendszergazdák próbál módosítani vagy a jelszó alaphelyzetbe állítása.

## <a name="add-to-the-custom-list"></a>Az egyéni lista hozzáadása

A letiltott jelszavak egyéni lista konfigurálása egy Azure Active Directory Premium P1 vagy P2-licenc szükséges. Részletes információkat az Azure Active Directory licenceléssel kapcsolatos, tekintse meg a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/). |}

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem (előzetes verzió)**.
1. A beállítás **érvényesítése egyéni lista**, az **Igen**.
1. Adja hozzá a karakterláncok a **egyéni le van tiltva a jelszó lista**, soronként egy karakterlánc
   * A letiltott jelszavak egyéni lista legfeljebb 1000 szavakat is tartalmazhat.
   * A letiltott jelszavak egyéni lista egy kis-és nagybetűket.
   * A letiltott jelszavak egyéni lista közös karaktert helyettesítő figyelembe veszi.
      * Példa: "o" és "0" vagy "a" és "\@"
   * A karakterlánc minimális hosszának 4 karakter, a maximális 16 karakternél.
1. Minden karakterláncok hozzáadásakor kattintson **mentése**.

> [!NOTE]
> A alkalmazni egyéni letiltott jelszavak lista frissítések több óráig is tarthat.

![Az Azure Portalon a hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Működés

Minden alkalommal, amikor egy felhasználó vagy rendszergazda visszaállítja, vagy egy Azure AD-jelszó megváltozik, végig a letiltott jelszavak listáit, győződjön meg arról, hogy nem szerepel a listában. Ez az ellenőrzés beállítása és módosítása az Azure AD-vel jelszavakat tartalmazza.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, megjelenik a következő hibaüzenetet kapja:

Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

[A letiltott jelszavak listáit fogalmi áttekintése](concept-password-ban-bad.md)

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)

[A letiltott jelszavak listáit a helyszíni integráció engedélyezése](howto-password-ban-bad-on-premises.md)
