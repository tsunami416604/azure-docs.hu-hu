---
title: Hogyan lehet letiltani a gyenge jelszavakat az Azure AD - Azure Active Directory
description: Az Azure ad-ben dinamikusan letiltott passwrords a envirionment a gyenge jelszavakat letiltása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f531174c889948308e27109ab4fd80a481ec6bdc
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798187"
---
# <a name="configuring-the-custom-banned-password-list"></a>A letiltott jelszavak egyéni lista konfigurálása

Számos szervezet található, a felhasználók létre közös helyi szavakat, például egy iskolai, focicsapata vagy híres személy, így könnyen kitalálható jelszavakat. A Microsoft egyéni letiltott jelszavak lista lehetővé teszi a szervezetek számára, hogy adja hozzá a karakterláncok kiértékelése és letiltására, mellett a globális le van tiltva jelszólista, ha a felhasználók és rendszergazdák próbál módosítani vagy a jelszó alaphelyzetbe állítása.

## <a name="add-to-the-custom-list"></a>Az egyéni lista hozzáadása

A letiltott jelszavak egyéni lista konfigurálása egy Azure Active Directory Premium P1 vagy P2-licenc szükséges. Részletes információkat az Azure Active Directory licenceléssel kapcsolatos, tekintse meg a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory**, **hitelesítési módszerek**, majd **jelszavas védelem**.
1. A beállítás **érvényesítése egyéni lista**, az **Igen**.
1. Adja hozzá a karakterláncok a **egyéni le van tiltva a jelszó lista**, soronként egy karakterlánc
   * A letiltott jelszavak egyéni lista legfeljebb 1000 feltételek tartalmazhat.
   * A letiltott jelszavak egyéni lista egy kis-és nagybetűket.
   * A letiltott jelszavak egyéni lista közös karaktert helyettesítő figyelembe veszi.
      * Példa: "o" és "0" vagy "a" és "\@"
   * A karakterlánc minimális hosszának 4 karakter, a maximális 16 karakternél.
1. Minden karakterláncok hozzáadásakor kattintson **mentése**.

> [!NOTE]
> A alkalmazni egyéni letiltott jelszavak lista frissítések több óráig is tarthat.

> [!NOTE]
> A letiltott jelszavak egyéni lista korlátozva a legfeljebb 1000 feltételeket. Nem célja a jelszavak rendkívül nagy méretű listák blokkolásához. Annak érdekében, hogy teljes mértékben kihasználják a letiltott jelszavak egyéni lista előnyeit, a Microsoft javasolja, hogy először tekintse át és a tervezéshez és a használat a letiltott jelszavak egyéni lista (lásd: [egyéni le van tiltva a jelszó lista](concept-password-ban-bad.md#custom-banned-password-list)), és is a jelszó kiértékelése algoritmus (lásd: [hogyan jelszavak értékeli ki a](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Az Azure Portalon a hitelesítési módszerek egyéni letiltott jelszavak listájának módosítása](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Működés

Minden alkalommal, amikor egy felhasználó vagy rendszergazda visszaállítja, vagy egy Azure AD-jelszó megváltozik, végig a letiltott jelszavak listáit, győződjön meg arról, hogy nem szerepel a listában. Ez az ellenőrzés beállítása és módosítása az Azure AD-vel jelszavakat tartalmazza.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál úgy, hogy az lenne le van tiltva a jelszó alaphelyzetbe állítása, az alábbi hibaüzenetek valamelyike megjelenik:

* Sajnos a jelszó tartalmaz egy szót, kifejezést vagy mintát, amely lehetővé teszi a jelszó könnyen kitalálható. Próbálkozzon újra egy másik jelszóval.
* Sajnos a jelszó nem használható, mert a szavakat vagy a rendszergazda által letiltott karaktereket tartalmaz. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>További lépések

[A letiltott jelszavak listáit fogalmi áttekintése](concept-password-ban-bad.md)

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)

[A letiltott jelszavak listáit a helyszíni integráció engedélyezése](howto-password-ban-bad-on-premises.md)
