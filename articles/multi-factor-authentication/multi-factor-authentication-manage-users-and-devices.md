---
title: "Rendszergazda felhasználók és eszközök kezeléséhez - Azure MFA |} Microsoft Docs"
description: "Ez ismerteti, hogyan módosíthatja a felhasználói beállítások, például a felhasználók a igazolása felfelé folyamat ismét nem kényszerített."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: e9b8504d4a59cf0fae69a4e975d6f834028066d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>A felhőben Azure multi-factor Authentication felhasználói beállítások kezelése
A rendszergazdák a következő felhasználó-eszköz beállításainak kezelheti:

* A felhasználóknak adja meg a kapcsolattartási módszerek újra kell
* Alkalmazásjelszók törlése
* Többtényezős hitelesítés megkövetelése minden megbízható eszköz 

## <a name="require-users-to-provide-contact-methods-again"></a>A felhasználóknak adja meg a kapcsolattartási módszerek újra kell
A beállítás engedélyezése esetén a felhasználó újra a regisztráció befejezéséhez. A böngészőn kívüli alkalmazások továbbra is működik, ha a felhasználó rendelkezik alkalmazásjelszók számukra.  A felhasználók alkalmazásjelszók is kiválasztásával törölheti **törli a kiválasztott felhasználók által létrehozott összes meglévő alkalmazásjelszavak**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Használatának megkövetelése a felhasználóktól kapcsolattartási módszerek újra
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**. Megnyílik a többtényezős hitelesítés lap. 
4. A felhasználó vagy felhasználók kezelni kívánt melletti négyzetet. A Kész lépés beállítások listáját a jobb oldalon jelennek meg. 
5. Válassza ki **felhasználói beállítások kezelése**.
6. Jelölje be a **szükséges a kiválasztott felhasználóktól kapcsolattartási módszerek újra**.
   ![Adja meg a kapcsolattartási módszerek](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
7. Kattintson a **mentés** gombra.
8. Kattintson a **bezárása**.

## <a name="delete-users-existing-app-passwords"></a>Törölje a meglévő alkalmazásjelszavak felhasználók
Ez a beállítás törli az összes az alkalmazásjelszókat, amely a felhasználó hozott létre. E alkalmazásjelszókat társított böngészőn kívüli alkalmazások Uj alkalmazasjelszora van szuksege létrehozásáig leáll.

### <a name="how-to-delete-users-existing-app-passwords"></a>A felhasználók alkalmazásjelszók meglévő törlése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**. Megnyílik a többtényezős hitelesítés lap. 
6. A felhasználó vagy felhasználók kezelni kívánt melletti négyzetet. A Kész lépés beállítások listáját a jobb oldalon jelennek meg. 
7. Válassza ki **felhasználói beállítások kezelése**.
8. Jelölje be a **törli a kiválasztott felhasználók által létrehozott összes meglévő alkalmazásjelszavak**.
   ![Alkalmazásjelszók törlése](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Kattintson a **mentés** gombra.
10. Kattintson a **bezárása**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Többtényezős hitelesítés visszaállítása egy felhasználó összes megjegyzett eszközön
Azure multi-factor Authentication konfigurálható szolgáltatása a rendelkezésre állást a felhasználók megjelölése megbízhatóként eszközöket is. További információkért lásd: [konfigurálása Azure multi-factor Authentication beállításainak](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Felhasználók is kikapcsolja a kétlépéses ellenőrzést, a konfigurálható számú nappal szabályos eszközeiken. Ha a fiók biztonsága sérül, vagy megbízható eszköz elvész, távolítsa el a megbízható állapotát, és szükséges a kétlépéses ellenőrzés visszakapcsolásához szeretné.

A **visszaállítási többtényezős hitelesítés az összes korábban megjegyzett eszközön** beállítás azt jelenti, hogy a felhasználó ellenőrizte a kétlépéses ellenőrzés a következő bejelentkezéskor, függetlenül attól, hogy kiválasztott-e jelölje meg az eszköz megbízható legyen. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Az összes felfüggesztett eszközökön, hogy a felhasználó többtényezős hitelesítés visszaállítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**. Megnyílik a többtényezős hitelesítés lap. 
6. A felhasználó vagy felhasználók kezelni kívánt melletti négyzetet. A Kész lépés beállítások listáját a jobb oldalon jelennek meg. 
7. Válassza ki **felhasználói beállítások kezelése**.
8. Jelölje be a **visszaállítási többtényezős hitelesítés az összes korábban megjegyzett eszközön**
   ![alkalmazásjelszók törlése](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Kattintson a **mentés** gombra.
10. Kattintson a **bezárása**.

## <a name="next-steps"></a>Következő lépések

- További információért arról, hogy hogyan [konfigurálása Azure multi-factor Authentication beállításait](multi-factor-authentication-whats-next.md)

- Ha a felhasználók segítségre van szüksége, irányítsa őket felé a [felhasználói útmutatója a kétlépéses ellenőrzéshez](./end-user/multi-factor-authentication-end-user.md)
