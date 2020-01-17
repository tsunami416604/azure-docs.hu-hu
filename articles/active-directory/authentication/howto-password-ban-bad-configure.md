---
title: Gyenge jelszavak betiltása az Azure AD-ben – Azure Active Directory
description: Gyenge jelszavak betiltása a envirionment az Azure AD-ben dinamikusan tiltott passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155057"
---
# <a name="configuring-the-custom-banned-password-list"></a>Az egyéni tiltott jelszavak listájának konfigurálása

Számos szervezet megkeresi a felhasználói jelszavakat a gyakori helyi szavak, például az iskola, a sport csapata vagy a híres személy számára, így könnyen kitalálhatja őket. A Microsoft egyéni tiltott jelszavainak listája lehetővé teszi, hogy a szervezetek karakterláncokat adjanak a kiértékeléshez és a tiltáshoz a globálisan tiltott jelszavak mellett, amikor a felhasználók és a rendszergazdák megpróbálnak módosítani vagy visszaállítani egy jelszót.

## <a name="add-to-the-custom-list"></a>Hozzáadás az egyéni listához

Az egyéni tiltott jelszavak listájának konfigurálásához egy prémium szintű Azure Active Directory P1 vagy P2 licenc szükséges. A Azure Active Directory licenceléssel kapcsolatos részletesebb információkért tekintse meg a [Azure Active Directory díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory/).

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és tallózással keresse meg **Azure Active Directory** > **biztonsági** > **hitelesítési módszereit** > **jelszavas védelem**.
1. Állítsa be az **Egyéni beállítások betartatása**beállítást **Igen**értékre.
1. Karakterláncok hozzáadása az **Egyéni tiltott jelszavak listájához**, soronként egy sztring
   * Az egyéni tiltott jelszavak listája akár 1000 kifejezést is tartalmazhat.
   * Az egyéni tiltott jelszavak listája kis-és nagybetűk megkülönböztetése.
   * Az egyéni tiltott jelszavak listája a közös karakteres helyettesítést veszi figyelembe.
      * Példa: "o" és "0" vagy "a" és "\@"
   * A karakterlánc minimális hossza négy karakter, a maximum pedig 16 karakter.
1. Ha az összes karakterláncot hozzáadta, kattintson a **Mentés**gombra.

> [!NOTE]
> A rendszer az egyéni tiltott jelszavak listájának frissítéséhez több órát is igénybe vehet.

> [!NOTE]
> Az egyéni tiltott jelszavak listáját a rendszer legfeljebb 1000 feltételre korlátozza. Nem kifejezetten a jelszavak rendkívül nagy listáját blokkolja. Ahhoz, hogy teljes mértékben kihasználja az egyéni tiltott jelszavak listáját, a Microsoft azt javasolja, hogy először tekintse át és Ismerje meg az egyéni tiltott jelszavak listáját (lásd az [Egyéni tiltott jelszavak listáját](concept-password-ban-bad.md#custom-banned-password-list)), valamint a jelszó-értékelési algoritmust is (lásd a [jelszavak kiértékelésének módját](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Módosítsa az egyéni tiltott jelszavak listáját a hitelesítési módszerek területen a Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Működési elv

Minden alkalommal, amikor egy felhasználó vagy rendszergazda alaphelyzetbe állítja vagy megváltoztatja az Azure AD-jelszót, a betiltott jelszavak listáján keresztül ellenőrizheti, hogy nem szerepel-e a listán. Ez az ellenőrzési szolgáltatás az Azure AD-vel beállított vagy módosított jelszavakban szerepel.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál visszaállítani egy jelszót a betiltott valamire, a következő hibaüzenetek egyike jelenik meg:

* Sajnos a jelszó olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálhatja a jelszavát. Próbálkozzon újra egy másik jelszóval.
* Sajnos nem használhatja ezt a jelszót, mert olyan szavakat vagy karaktereket tartalmaz, amelyeket a rendszergazda letiltott. Próbálkozzon újra egy másik jelszóval.

## <a name="next-steps"></a>Következő lépések

[A tiltott jelszavak listáját bemutató fogalmak áttekintése](concept-password-ban-bad.md)

[Az Azure AD jelszavas védelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)

[Helyszíni integráció engedélyezése a tiltott jelszavak listájával](howto-password-ban-bad-on-premises.md)
