---
title: Felhasználók és eszközök kezelése az Azure MFA-Azure Active Directory
description: Hogyan módosíthatják a rendszergazdák a felhasználói beállításokat, például arra kényszerítve a felhasználókat, hogy újra elvégezzék a feligazolási folyamatot.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425262"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Felhasználói beállítások kezelése az Azure Multi-Factor Authentication a felhőben

Rendszergazdaként a következő felhasználói és eszközbeállítások felügyeletét végezheti el:

* A kapcsolattartási módszerek újbóli megkövetelése a felhasználóktól
* Alkalmazás jelszavának törlése
* MFA megkövetelése minden megbízható eszközön

## <a name="manage-authentication-methods"></a>Hitelesítési módszerek kezelése

A hitelesítés-rendszergazdai szerepkörhöz rendelt rendszergazda megkövetelheti, hogy a felhasználók új jelszót állítsanak vissza, regisztrálják újra az MFA-t, vagy visszavonják a meglévő MFA-munkameneteket a felhasználói objektumból.

![Hitelesítési módszerek kezelése a Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A bal oldalon válassza az **Azure Active Directory** > **Felhasználók** > **Minden felhasználó** lehetőséget.
1. Válassza ki azt a felhasználót, akinek műveletet kíván végrehajtani, és válassza a **hitelesítési módszerek**lehetőséget.
   - A **jelszó alaphelyzetbe** állítása visszaállítja a felhasználó jelszavát, és hozzárendel egy ideiglenes jelszót, amelyet a következő bejelentkezéskor módosítani kell.
   - Az **MFA ismételt regisztrálásának megkövetelése** , hogy amikor a felhasználó a következő alkalommal jelentkezik be, a rendszer kérni fogja az új MFA hitelesítési módszer beállítását.
   - Az **MFA-munkamenetek visszavonása** törli a felhasználó által megjegyzett MFA-munkameneteket, és azt igényli, hogy az MFA-t a következő alkalommal kell végrehajtania, amikor a szabályzat az eszközön szükséges.

## <a name="delete-users-existing-app-passwords"></a>Meglévő alkalmazások jelszavainak törlése

Ezzel a beállítással törlődik a felhasználó által létrehozott összes alkalmazás jelszava. Az alkalmazás jelszavával társított nem böngészőbeli alkalmazások nem működnek, amíg új alkalmazás jelszava nem jön létre. A művelet végrehajtásához globális rendszergazdai jogosultságok szükségesek.

### <a name="how-to-delete-users-existing-app-passwords"></a>Meglévő alkalmazások jelszavainak törlése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A bal oldalon válassza az **Azure Active Directory** > **Felhasználók** > **Minden felhasználó** lehetőséget.
3. A jobb oldalon válassza a **multi-Factor Authentication** lehetőséget az eszköztáron. Megnyílik a multi-Factor Authentication oldal.
4. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
5. Válassza a **felhasználói beállítások kezelése**lehetőséget.
6. Jelölje be a jelölőnégyzetet a **kijelölt felhasználók által létrehozott összes meglévő alkalmazás jelszavának törléséhez**.
   ![az összes meglévő alkalmazás jelszavának törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **Bezárás**gombra.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure multi-Factor Authentication beállításainak konfigurálásáról](howto-mfa-mfasettings.md)
- Ha a felhasználóknak segítségre van szüksége, a [kétlépéses ellenőrzés felhasználói útmutatója](../user-help/multi-factor-authentication-end-user.md) felé mutatnak
