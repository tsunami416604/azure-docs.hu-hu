---
title: Felhasználók és eszközök kezelése Azure MFA – Azure Active Directory
description: Hogyan módosíthatják a rendszergazdák a felhasználói beállításokat, például arra kényszerítik a felhasználókat, hogy újra végezhessék a próbafeldolgozási folyamatot.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653494"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Felhasználói beállítások felügyelete az Azure Multi-Factor Authentication használatával a felhőben

Rendszergazdaként a következő felhasználói és eszközbeállításokat kezelheti:

* A felhasználók ismételt kapcsolattartási módszereinek megkövetelése
* Alkalmazásjelszavak törlése
* Többszintű faszükséglés megkövetelése minden megbízható eszközön

## <a name="manage-authentication-methods"></a>Hitelesítési módszerek kezelése

A hitelesítési rendszergazda szerepkörhöz rendelt rendszergazdaként megkövetelheti a felhasználóktól, hogy alaphelyzetbe állítsák jelszavukat, újra regisztráljanak az MFA-ra, vagy visszavonják a meglévő MFA-munkameneteket a felhasználói objektumból.

![Hitelesítési módszerek kezelése az Azure Portalról](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza ki azt a felhasználót, akien műveletet kíván végrehajtani, és válassza a **Hitelesítési módszerek**lehetőséget.
   - **A jelszó alaphelyzetbe állítása** visszaállítja a felhasználó jelszavát, és hozzárendel egy ideiglenes jelszót, amelyet a következő bejelentkezéskor módosítani kell.
   - **A szükséges újraregisztrálása MFA** teszi, hogy amikor a felhasználó bejelentkezik a következő alkalommal, akkor kell beállítani egy új MFA hitelesítési módszer.
   - **Az MFA-munkamenetek visszavonása** törli a felhasználó által megjegyzett MFA-munkameneteket, és megköveteli tőlük, hogy az eszköz házirendje által megkövetelt következő alkalommal hajtsák végre az MFA-t.

## <a name="delete-users-existing-app-passwords"></a>Meglévő alkalmazásjelszavak törlése a felhasználókszámára

Ez a beállítás törli a felhasználó által létrehozott összes alkalmazásjelszót. Az alkalmazásjelszavakhoz társított nem böngészős alkalmazások mindaddig nem működnek, amíg létre nem jön egy új alkalmazásjelszó. A művelet végrehajtásához globális rendszergazdai engedélyek szükségesek.

### <a name="how-to-delete-users-existing-app-passwords"></a>A felhasználók meglévő alkalmazásjelszavainak törlése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
3. A jobb oldalon válassza az eszköztár **többtényezős hitelesítése** lehetőséget. Megnyílik a többtényezős hitelesítéslap.
4. Jelölje be a kezelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A gyorslépési lehetőségek listája a jobb oldalon jelenik meg.
5. Válassza **a Felhasználói beállítások kezelése lehetőséget.**
6. Jelölje be a **Kijelölt felhasználók által létrehozott összes meglévő alkalmazásjelszó törlése**jelölőnégyzetet.
   ![Az összes meglévő alkalmazásjelszó törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kattintson a **Mentés gombra.**
8. Kattintson **a Bezárás gombra.**

## <a name="next-steps"></a>További lépések

- További információ az [Azure többtényezős hitelesítési beállításainak konfigurálásáról](howto-mfa-mfasettings.md)
- Ha a felhasználóknak segítségre van szükségük, irányítsa őket a felhasználói útmutató felé [a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user.md)
