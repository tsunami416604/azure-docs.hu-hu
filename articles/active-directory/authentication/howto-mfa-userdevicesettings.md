---
title: A rendszergazdák kezelhetik a felhasználókat és az eszközöket – Azure MFA – Azure Active Directory
description: Hogyan módosíthatják a rendszergazdák a felhasználói beállításokat, például arra kényszerítve a felhasználókat, hogy újra elvégezzék a feligazolási folyamatot.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d500f6c74bb62322a5e14ce3c489e8dba11dc2f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68555799"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Felhasználói beállítások kezelése az Azure multi-Factor Authentication szolgáltatással a felhőben

Rendszergazdaként a következő felhasználói és eszközbeállítások felügyeletét végezheti el:

* A kapcsolattartási módszerek újbóli megkövetelése a felhasználóktól
* Alkalmazás jelszavának törlése
* MFA megkövetelése minden megbízható eszközön

## <a name="manage-authentication-methods"></a>Hitelesítési módszerek kezelése

A hitelesítés-rendszergazdai szerepkörhöz rendelt rendszergazda megkövetelheti, hogy a felhasználók új jelszót állítsanak vissza, regisztrálják újra az MFA-t, vagy visszavonják a meglévő MFA-munkameneteket a felhasználói objektumból.

![Hitelesítési módszerek kezelése a Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. A jelszó alaphelyzetbe állítása visszaállítja a felhasználó jelszavát, és hozzárendel egy ideiglenes jelszót, amelyet a következő bejelentkezéskor módosítani kell.
2. Az MFA ismételt regisztrálásának megkövetelése, hogy amikor a felhasználó a következő alkalommal jelentkezik be, a rendszer kérni fogja az új MFA hitelesítési módszer beállítását.
3. Az MFA-munkamenetek visszavonása törli a felhasználó által megjegyzett MFA-munkameneteket, és azt igényli, hogy az MFA-t a következő alkalommal kell végrehajtania, amikor a szabályzat az eszközön szükséges.

## <a name="require-users-to-provide-contact-methods-again"></a>A kapcsolattartási módszerek újbóli megkövetelése a felhasználóktól

Ez a beállítás arra kényszeríti a felhasználót, hogy újra elvégezze a regisztrációs folyamatot. A böngészőn kívüli alkalmazások továbbra is működni tudnak, ha a felhasználó rendelkezik az alkalmazás jelszavával.  A felhasználók alkalmazás jelszavának törléséhez jelölje be **a kijelölt felhasználók által létrehozott összes meglévő alkalmazás jelszavának törlése**lehetőséget is.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>A kapcsolatfelvételi módszerek megkövetelése a felhasználóktól

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.
3. A jobb oldalon válassza a **multi-Factor Authentication** lehetőséget az eszköztáron. Megnyílik a multi-Factor Authentication oldal.
4. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
5. Válassza a **felhasználói beállítások kezelése**lehetőséget.
6. Jelölje be a jelölőnégyzetet a **kiválasztott felhasználók számára a kapcsolattartási módszerek újbóli**megadásához.
   ![A kapcsolattartási módszerek újbóli megkövetelése a felhasználóktól](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **Bezárás**gombra.

A szervezetek a következő útmutató segítségével végezhetik el ezeket a lépéseket a PowerShell-lel `StrongAuthenticationMethods` az attribútum törléséhez:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Meglévő alkalmazások jelszavainak törlése

Ezzel a beállítással törlődik a felhasználó által létrehozott összes alkalmazás jelszava. Az alkalmazás jelszavával társított nem böngészőbeli alkalmazások nem működnek, amíg új alkalmazás jelszava nem jön létre.

### <a name="how-to-delete-users-existing-app-passwords"></a>Meglévő alkalmazások jelszavainak törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.
3. A jobb oldalon válassza a **multi-Factor Authentication** lehetőséget az eszköztáron. Megnyílik a multi-Factor Authentication oldal.
4. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
5. Válassza a **felhasználói beállítások kezelése**lehetőséget.
6. Jelölje be a jelölőnégyzetet a **kijelölt felhasználók által létrehozott összes meglévő alkalmazás jelszavának törléséhez**.
   ![Az összes meglévő alkalmazás jelszavának törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **Bezárás**gombra.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>MFA visszaállítása a felhasználó összes megjegyzett eszközén

Az Azure multi-Factor Authentication konfigurálható funkcióinak egyike lehetővé teszi a felhasználóknak az eszközök megbízhatóként való megjelölését. További információ: az [Azure multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

A felhasználók eldönthetik, hogy egy konfigurálható napokra vonatkozó kétlépéses ellenőrzést végeznek a normál eszközökön. Ha egy fiók biztonsága sérül, vagy egy megbízható eszköz elvész, el kell tudnia távolítani a megbízható állapotot, és újra meg kell követelni a kétlépéses ellenőrzést.

Ha be van jelölve, a többtényezős **hitelesítés visszaállítása minden** megjegyzett eszközön a felhasználóknak a következő bejelentkezéskor el kell végezniük a kétlépéses ellenőrzést, még akkor is, ha az eszközük megbízhatóként van megjelölve.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Az MFA visszaállítása a felhasználó összes felfüggesztett eszközén

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.
3. A jobb oldalon válassza a **multi-Factor Authentication** lehetőséget az eszköztáron. Megnyílik a multi-Factor Authentication oldal.
4. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
5. Válassza a **felhasználói beállítások kezelése**lehetőséget.
6. Jelölje be a többtényezős **hitelesítés visszaállítása az összes**
   megjegyzett eszközön![jelölőnégyzetet a többtényezős hitelesítés visszaállítása az összes megjegyzett eszközön](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **Bezárás**gombra.

## <a name="next-steps"></a>További lépések

- További információ az [Azure multi-Factor Authentication beállításainak konfigurálásáról](howto-mfa-mfasettings.md)
- Ha a felhasználóknak segítségre van szüksége, a kétlépéses [ellenőrzés felhasználói útmutatója](../user-help/multi-factor-authentication-end-user.md) felé mutatnak
