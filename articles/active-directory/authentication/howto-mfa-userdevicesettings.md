---
title: Az Azure Multi-Factor Authentication-Azure Active Directory felhasználói beállításainak kezelése
description: Ismerje meg, hogyan konfigurálhatja Azure Active Directory Azure-beli felhasználói beállításait Multi-Factor Authentication
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
ms.openlocfilehash: 295738ee5943a6cf54bc7e1e3ce4bba621dbe29f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658686"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication felhasználói beállításainak kezelése

Az Azure Multi-Factor Authentication felhasználóinak kezeléséhez megkövetelheti a felhasználóktól, hogy alaphelyzetbe állítsa a jelszavát, regisztrálja újra az MFA-t, vagy vonja vissza a meglévő MFA-munkameneteket. Azon felhasználók esetében, akik definiált alkalmazási jelszavakkal rendelkeznek, dönthet úgy is, hogy törli ezeket a jelszavakat, így a régi hitelesítés sikertelen lesz az alkalmazásokban. Ezek a műveletek akkor lehetnek szükségesek, ha segítséget kell nyújtania egy felhasználónak, vagy szeretné visszaállítani a biztonsági állapotát.

## <a name="manage-user-authentication-options"></a>Felhasználói hitelesítési beállítások kezelése

Ha a *hitelesítési rendszergazda* szerepkört rendelte hozzá, megkövetelheti a felhasználóktól a jelszavuk visszaállítását, az MFA regisztrálását, illetve a meglévő MFA-munkamenetek visszavonását a felhasználói objektumból. A felhasználói beállítások kezeléséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon válassza **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.
1. Válassza ki azt a felhasználót, akinek műveletet kíván végrehajtani, és válassza a **hitelesítési módszerek**lehetőséget. Az ablak tetején válassza a következő lehetőségek egyikét a felhasználó számára:
   - A **jelszó alaphelyzetbe állítása** visszaállítja a felhasználó jelszavát, és egy ideiglenes jelszót rendel hozzá, amelyet a következő bejelentkezéskor módosítani kell.
   - Az **MFA ismételt regisztrálásának megkövetelése** lehetővé teszi, hogy amikor a felhasználó legközelebb bejelentkezik, egy új MFA hitelesítési módszer beállítására van szükség.
   
      > [!NOTE]
      > A felhasználó jelenleg regisztrált hitelesítési módszereit nem törli a rendszer, ha egy rendszergazdának újra kell regisztrálnia az MFA-t. Miután egy felhasználó újra regisztrálja az MFA-t, javasoljuk, hogy tekintsék át a biztonsági adataikat, és töröljön minden korábban regisztrált hitelesítési módszert, amely már nem használható.
   
   - Az **MFA-munkamenetek visszavonása** törli a felhasználó által megjegyzett MFA-munkameneteket, és azt igényli, hogy az MFA-t a következő alkalommal kell végrehajtania, amikor a szabályzat az eszközön szükséges.

   ![Hitelesítési módszerek kezelése a Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Meglévő alkalmazások jelszavainak törlése

Ha szükséges, törölheti az összes olyan alkalmazás jelszavát, amelyet a felhasználó hozott létre. Az alkalmazás jelszavával társított nem böngészőbeli alkalmazások nem működnek, amíg új alkalmazás jelszava nem jön létre. A művelet végrehajtásához *globális rendszergazdai* jogosultságok szükségesek.

A felhasználók alkalmazási jelszavának törléséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali oldalon válassza **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.
1. Válassza a **multi-Factor Authentication**lehetőséget. Előfordulhat, hogy a menüpontra kell görgetni a jobb oldalon. Válassza az alábbi képernyőképet a teljes Azure Portal ablak és menü helyének megtekintéséhez:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multi-Factor Authentication kiválasztása az Azure AD felhasználók ablakában")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
1. Válassza a **felhasználói beállítások kezelése**lehetőséget, majd jelölje be a **kijelölt felhasználók által létrehozott összes meglévő alkalmazás jelszavának törlése**jelölőnégyzetet az alábbi példában látható módon: az ![ összes meglévő alkalmazás jelszavának törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Válassza a **Mentés**, majd a **Bezárás**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ez a cikk segített az egyes felhasználói beállítások konfigurálásában. Az Azure Multi-Factor Authentication szolgáltatás beállításainak konfigurálásával kapcsolatban lásd: az [azure multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)

Ha a felhasználóknak segítségre van szüksége, tekintse meg az [Azure multi-Factor Authentication felhasználói útmutatóját](../user-help/multi-factor-authentication-end-user.md).
