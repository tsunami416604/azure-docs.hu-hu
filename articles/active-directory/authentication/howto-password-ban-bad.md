---
title: Hogyan jelszavakat az Azure AD-bA
description: A envirionment dinamikusan tiltani az Azure AD passwrords a gyenge jelszavakat bA
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 0c1517f94d4a6d59077b62614eec8fef665b1529
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296140"
---
# <a name="configuring-the-custom-banned-password-list"></a>A tiltott egyénijelszó-lista konfigurálása

|     |
| --- |
| Az Azure AD-jelszó protection és a tiltott egyénijelszó listájának az Azure Active Directory nyilvános előzetes verziójú funkciók. Az előzetes verziójú funkciók kapcsolatos további információkért lásd: [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Számos szervezet található, a felhasználók közös helyi szavakat, például iskolai, csapatának vagy famous személy, így könnyű kitalálni a jelszavak létrehozása. A Microsoft egyéni tiltott jelszó lista lehetővé teszi a szervezetek értékelje ki és letiltásához karakterláncok hozzáadása, mellett a globális tiltott jelszó lista, amikor a felhasználók és rendszergazdák próbálnak módosítása vagy a jelszó alaphelyzetbe állítása.

## <a name="add-to-the-custom-list"></a>Az egyéni lista hozzáadása

A tiltott egyénijelszó-lista konfigurálása az Azure Active Directory Premium P1 és P2-licencre van szükség. Részletes információkat az Azure Active Directory licencelésével kapcsolatban, tekintse meg a [Azure Active Directory árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/active-directory/). |}

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszó protection (előzetes verzió)**.
1. A beállítás **érvényesítése egyéni lista**található **Igen**.
1. Adja hozzá a karakterláncok a **egyéni tiltott jelszó lista**, soronként egy karakterlánc
   * A tiltott egyénijelszó lista legfeljebb 1000 szavak tartalmazhat.
   * Az egyéni tiltott jelszó lista azonban nem.
   * Az egyéni tiltott jelszó lista közös karakter helyettesítő figyelembe veszi.
      * Példa: "o" és "0" vagy "a" és "@"
   * A minimális karakterlánc hossza 4 karakter, de legfeljebb 16 karakter hosszúságú lehet.
1. Ha minden karakterlánc hozzáadását, kattintson **mentése**.

> [!NOTE]
> Az egyéni tiltott jelszó lista alkalmazni kívánt frissítések több óráig is tarthat.

![Az Azure-portálon a hitelesítési módszerek egyéni tiltott jelszó listájának módosítása](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Működés

Minden alkalommal, amikor egy felhasználó vagy rendszergazda visszaállítja, vagy egy Azure AD-jelszó módosítja azt áthaladó annak ellenőrzéséhez, hogy nincs a listán a tiltott jelszavak listája. Ez az ellenőrzés beállítása és módosítása az Azure AD jelszavakat tartalmazza.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor a felhasználó megkísérli a jelszó alaphelyzetbe állítása, amelyet akkor kell tiltani, akkor a következő hibaüzenet jelenik:

Sajnos a jelszó egy szó, kifejezés vagy mintát, amely alapján a jelszó könnyen kitalálható tartalmaz. Próbálkozzon újra egy másik jelszót.

## <a name="next-steps"></a>További lépések

[A tiltott jelszó listák elméleti áttekintése](concept-password-ban-bad.md)

[Az Azure AD jelszóvédelem elméleti áttekintése](concept-password-ban-bad-on-premises.md)

[A helyszíni integrációjának engedélyezése az a tiltott jelszavak listája](howto-password-ban-bad-on-premises.md)
