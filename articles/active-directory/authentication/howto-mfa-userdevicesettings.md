---
title: Az Azure többtényezős hitelesítésfelhasználói beállításainak kezelése – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directory felhasználói beállításait az Azure többtényezős hitelesítéshez
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309759"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítéséhez szükséges felhasználói beállítások kezelése

Az Azure többtényezős hitelesítés felhasználóinak kezeléséhez megkövetelheti a felhasználóktól, hogy alaphelyzetbe állítsák jelszavukat, újra regisztráljanak az MFA-ra, vagy visszavonják a meglévő Többtényezős hitelesítési munkameneteket. Az alkalmazásjelszavakat definiáló felhasználók számára is törölheti ezeket a jelszavakat, így az örökölt hitelesítés sikertelen lesz ezekben az alkalmazásokban. Ezekre a műveletekre akkor lehet szükség, ha segítséget kell nyújtania egy felhasználónak, vagy vissza szeretné állítani a biztonsági állapotukat.

## <a name="manage-user-authentication-options"></a>Felhasználói hitelesítési beállítások kezelése

Ha a *hitelesítési rendszergazda szerepkört* kapta, megkövetelheti a felhasználóktól, hogy alaphelyzetbe állítsák jelszavukat, újra regisztráljanak az MFA-ra, vagy visszavonják a meglévő Többfa-munkameneteket a felhasználói objektumból. A felhasználói beállítások kezeléséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza ki azt a felhasználót, akien műveletet kíván végrehajtani, és válassza a **Hitelesítési módszerek**lehetőséget. Az ablak tetején válasszon az alábbi lehetőségek közül a felhasználó számára:
   - **A jelszó alaphelyzetbe állítása** visszaállítja a felhasználó jelszavát, és hozzárendel egy ideiglenes jelszót, amelyet a következő bejelentkezéskor módosítani kell.
   - **Az MFA újbóli regisztrálásának megkövetelése** leteszi, hogy amikor a felhasználó legközelebb bejelentkezik, a rendszer új MFA-hitelesítési módszert kell beállítania.
   - **Az MFA-munkamenetek visszavonása** törli a felhasználó által megjegyzett MFA-munkameneteket, és megköveteli tőlük, hogy az eszközön lévő házirend által igényelt következő alkalommal hajtsák végre az MFA-t.

   ![Hitelesítési módszerek kezelése az Azure Portalról](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Meglévő alkalmazásjelszavak törlése a felhasználókszámára

Szükség esetén törölheti a felhasználó által létrehozott összes alkalmazásjelszót. Az alkalmazásjelszavakhoz társított nem böngészős alkalmazások mindaddig nem működnek, amíg létre nem jön egy új alkalmazásjelszó. A művelet végrehajtásához *globális rendszergazdai* engedélyek szükségesek.

Felhasználó alkalmazásjeleinek törléséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza a **Többtényezős hitelesítés lehetőséget.** Előfordulhat, hogy a menüpont megtekintéséhez jobbra kell görgetnie. Válassza ki az alábbi példaképernyőképet az Azure Portal teljes ablakának és menühelyének megtekintéséhez:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Válassza a többtényezős hitelesítést az Azure AD Felhasználók ablakából")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Jelölje be a kezelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A gyorslépési lehetőségek listája a jobb oldalon jelenik meg.
1. Válassza **a Felhasználói beállítások kezelése**lehetőséget, majd jelölje be a Kijelölt felhasználók által létrehozott összes meglévő alkalmazásjelszó törlése jelölőnégyzetet, **ahogy**az a következő példában is látható: ![Az összes meglévő alkalmazásjelszó törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Válassza a **Mentés**lehetőséget, majd zárja be a **bezárását.**

## <a name="next-steps"></a>További lépések

Ez a cikk segített az egyes felhasználói beállítások konfigurálásához. Az Azure többtényezős hitelesítési szolgáltatásbeállításainak konfigurálása az [Azure többtényezős hitelesítési beállításainak konfigurálása című](howto-mfa-mfasettings.md) témakörben olvashat.

Ha a felhasználóknak segítségre van szükségük, olvassa el az [Azure többtényezős hitelesítéshez szükséges felhasználói útmutatót.](../user-help/multi-factor-authentication-end-user.md)
