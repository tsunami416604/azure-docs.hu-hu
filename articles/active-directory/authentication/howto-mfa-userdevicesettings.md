---
title: Rendszergazdák kezelése a felhasználók és eszközök – az Azure MFA |} A Microsoft Docs
description: Ez ismerteti, hogyan lehet például a felhasználók számára a proof-up folyamatot újra el kényszerítése a felhasználói beállítások módosítása.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 620a36b8b39b826a289567a133c78bae8b7e77b8
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075040"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Az Azure multi-factor Authentication a felhőben a felhasználói beállítások kezelése

A rendszergazdák a következő felhasználói és eszközbeállítások kezelheti:

* Felhasználók számára a kapcsolattartási mód újbóli megadásának megkövetelése
* Az alkalmazásjelszavak törlése
* Többtényezős hitelesítés a megbízható eszközök 

## <a name="require-users-to-provide-contact-methods-again"></a>Felhasználók számára a kapcsolattartási mód újbóli megadásának megkövetelése
Ez a beállítás kényszeríti a felhasználót, hogy újra végrehajtania a regisztrálást. A böngészőn kívüli alkalmazások továbbra is működik, ha a felhasználó nem rendelkezik az alkalmazásjelszavak számukra.  A felhasználók alkalmazásjelszók törölheti is kiválasztásával **törli a kiválasztott felhasználók által létrehozott összes meglévő alkalmazásjelszavak**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Felhasználók számára a kapcsolattartási mód újbóli megadásának kötelezővé tétele
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.
3. A jobb oldalon válassza ki a **multi-factor Authentication** az eszköztáron. A többtényezős hitelesítés lap nyílik meg. 
4. Ellenőrizze a felhasználókat, hogy a kezelni kívánt melletti mezőbe. Gyors lépés beállítások listáját a jobb oldalon jelennek meg. 
5. Válassza ki **felhasználói beállítások kezelése**.
6. Jelölje be a **kérése a kiválasztott felhasználóktól a kapcsolattartási mód újbóli megadásának**.
   ![Adja meg a kapcsolattartási mód](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **bezárásához**.

## <a name="delete-users-existing-app-passwords"></a>Törölje a meglévő alkalmazásjelszavak felhasználók
Ez a beállítás minden, a felhasználó által létrehozott alkalmazásjelszó törlése. Az alábbi alkalmazásjelszókhoz társított böngészőn kívüli alkalmazások működni, amíg egy új alkalmazásjelszót nem hoz létre.

### <a name="how-to-delete-users-existing-app-passwords"></a>A felhasználók alkalmazásjelszók meglévő törlése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.
3. A jobb oldalon válassza ki a **multi-factor Authentication** az eszköztáron. A többtényezős hitelesítés lap nyílik meg. 
6. Ellenőrizze a felhasználókat, hogy a kezelni kívánt melletti mezőbe. Gyors lépés beállítások listáját a jobb oldalon jelennek meg. 
7. Válassza ki **felhasználói beállítások kezelése**.
8. Jelölje be a **törli a kiválasztott felhasználók által létrehozott összes meglévő alkalmazásjelszavak**.
   ![Az alkalmazásjelszavak törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Kattintson a **mentés** gombra.
10. Kattintson a **bezárásához**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Állítsa vissza a többtényezős hitelesítés a felhasználó az összes korábban megjegyzett eszközökön
Az Azure multi-factor Authentication szolgáltatás konfigurálható funkcióinak egyike a lehetővé teszi a felhasználók a beállítást, az eszközök megbízhatóként való megjelöléséhez. További információkért lásd: [konfigurálása az Azure multi-factor Authentication szolgáltatás beállításainak](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Felhasználók választhatják a kétlépéses ellenőrzés egy konfigurálható számú napig az rendszeres eszközökön kívül. Ha egy fiók biztonsága sérül, vagy egy megbízható eszköz elveszik, meg kell lenniük megbízható állapotát eltávolíthatja, és szükséges a kétlépéses ellenőrzés visszakapcsolásához.

A **visszaállítási multi-factor authentication szolgáltatás az összes korábban megjegyzett eszközön** beállítás azt jelenti, hogy a felhasználó merül fel, amikor legközelebb bejelentkeznek, függetlenül attól, hogy úgy is, eszköz megjelölése kétlépéses ellenőrzés végrehajtására megbízható. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Többtényezős hitelesítés visszaállítása egy felhasználó az összes felfüggesztett eszközökön
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.
3. A jobb oldalon válassza ki a **multi-factor Authentication** az eszköztáron. A többtényezős hitelesítés lap nyílik meg. 
6. Ellenőrizze a felhasználókat, hogy a kezelni kívánt melletti mezőbe. Gyors lépés beállítások listáját a jobb oldalon jelennek meg. 
7. Válassza ki **felhasználói beállítások kezelése**.
8. Jelölje be a **visszaállítási multi-factor authentication szolgáltatás az összes korábban megjegyzett eszközön**
   ![alkalmazásjelszók törlése](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Kattintson a **mentés** gombra.
10. Kattintson a **bezárásához**.

## <a name="next-steps"></a>További lépések

- További információ az első [konfigurálása az Azure multi-factor Authentication szolgáltatás beállításainak](howto-mfa-mfasettings.md)

- Ha a felhasználók segítségre van szüksége, pont felé őket a [felhasználói útmutatója a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user.md)
