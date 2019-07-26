---
title: Biztonsági adatok (előzetes verzió) beállítása a bejelentkezési parancssorból – Azure Active Directory | Microsoft Docs
description: A munkahelyi vagy iskolai fiók biztonsági adatainak beállítása, ha a rendszer kéri a szervezet bejelentkezési oldaláról.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20256fb712d9381ba2adc90e2e68ce4fdc8911a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382773"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>A biztonsági adatok (előzetes verzió) beállítása a bejelentkezési oldal parancssorában

Ha a munkahelyi vagy iskolai fiókjába való bejelentkezés után azonnal kéri a biztonsági adatok beállítását, kövesse az alábbi lépéseket.

Ez a kérdés csak akkor jelenik meg, ha nem állította be a szervezete által megkövetelt biztonsági adatokat. Ha korábban már beállította a biztonsági adatokat, de módosítani szeretné a módosításokat, kövesse a különböző metódus-alapú útmutató cikkeiben ismertetett lépéseket. További információ: [a biztonsági adatok hozzáadásának vagy frissítésének áttekintése](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Jelentkezzen be a munkahelyi vagy iskolai fiókjába

Miután bejelentkezett a munkahelyi vagy iskolai fiókjába, megjelenik egy üzenet, amely arra kéri, hogy adjon meg további információt, mielőtt hozzáfér a fiókjához.

![További információk kérése](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Biztonsági adatok beállítása a varázsló használatával

A következő lépésekkel állíthatja be a munkahelyi vagy iskolai fiókjához tartozó biztonsági adatokat a parancssorból.

>[!Important]
>Ez csak egy példa a folyamatra. A szervezet követelményeitől függően előfordulhat, hogy a rendszergazda különböző ellenőrzési módszereket állított be, amelyeket be kell állítania a folyamat során. Ebben a példában két módszert igényelünk, a Microsoft Authenticator alkalmazást és egy mobiltelefon-számot a hitelesítési hívásokhoz vagy szöveges üzenetekhez.

1. Miután kiválasztotta  a következőt a parancssorból, megjelenik a **fiók biztonságos létrehozása varázsló** , amely az első olyan metódust mutatja be, amelyet a rendszergazdának és a szervezetnek be kell állítania. Ebben a példában ez a Microsoft Authenticator alkalmazás.

   > [!Note]
   > Ha a Microsoft Authenticator alkalmazástól eltérő hitelesítő alkalmazást szeretne használni, válassza a **másik hitelesítő alkalmazás használata** hivatkozást.
   >
   > Ha a szervezete lehetővé teszi, hogy egy másik módszert válasszon a hitelesítő alkalmazás mellett, akkor válassza a **másik módszer csatolása**lehetőséget.

    ![Tartsa a fiókja biztonságban varázslót, amely az Auth alkalmazás letöltési oldalát jeleníti meg](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Válassza a **Letöltés most** lehetőséget, hogy letöltse és telepítse a Microsoft Authenticator alkalmazást a mobileszközön, majd válassza a **tovább**lehetőséget. További információ az alkalmazás letöltéséről és telepítéséről: [a Microsoft Authenticator alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md).

    ![Tartsa meg a fiókja védelmét, és jelenítse meg a fiókját a hitelesítő beállítása oldalon](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Maradjon a **fiók beállítása** oldalon, miközben beállítja a Microsoft Authenticator alkalmazást a mobileszközön.

4. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be az értesítések engedélyezését (ha a rendszer kéri), válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók**lehetőséget.

5. Térjen vissza a **fiók beállítása** lapra a számítógépen, majd válassza a **tovább**lehetőséget.

    Megjelenik a **QR-kód vizsgálata** lap.

    ![A QR-kód vizsgálata a hitelesítő alkalmazás használatával](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Az 5. lépésben a munkahelyi vagy iskolai fiók létrehozása után a megadott kód beolvasásával megjelentek a Microsoft Authenticator alkalmazás QR-kód olvasója, amely megjelent a mobileszközön.

    A hitelesítő alkalmazásnak sikeresen hozzá kell adnia a munkahelyi vagy iskolai fiókját anélkül, hogy további adatokat kellene megadnia. Ha azonban a QR-kód olvasója nem tudja beolvasni a kódot, akkor a **nem tudja beolvasni a QR-kód hivatkozást** , és manuálisan adja meg a kódot és az URL-címet a Microsoft Authenticator alkalmazásban. A kódok manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

7. Kattintson a **Tovább gombra** a **QR-kód vizsgálata** lapon a számítógépen.

    A rendszer értesítést küld a mobileszközön lévő Microsoft Authenticator alkalmazásnak, hogy tesztelje a fiókját.

    ![A fiók tesztelése a hitelesítő alkalmazással](media/security-info/securityinfo-prompt-test-app.png)

8. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, majd kattintson a **tovább**gombra.

    ![Sikeres értesítés, az alkalmazás és a fiók csatlakoztatása](media/security-info/securityinfo-prompt-auth-app-success.png).

    A biztonsági adatokat a rendszer úgy frissíti, hogy a Microsoft Authenticator alkalmazás alapértelmezés szerint a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során ellenőrizze az identitást.

9. A **telefon** beállítása lapon válassza ki, hogy szöveges üzenetet vagy telefonhívást szeretne kapni, majd válassza a **tovább**lehetőséget. Ebben a példában szöveges üzeneteket használunk, ezért egy telefonszámot kell használnia egy olyan eszközhöz, amely képes szöveges üzeneteket fogadni.

    ![A telefonszám beállításának megkezdése szöveges üzenetküldéshez](media/security-info/securityinfo-prompt-text-msg.png)

    A rendszer szöveges üzenetet küld a telefonszámára. Ha telefonhívást szeretne kapni, a folyamat ugyanaz. Egy szöveges üzenet helyett egy utasításokat tartalmazó telefonhívást fog kapni.

10. Adja meg a mobileszközön küldött szöveges üzenet által megadott kódot, majd kattintson a **tovább**gombra.

    ![A fiók tesztelése a szöveges üzenettel](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Tekintse át a sikeres értesítéseket, majd kattintson a **kész**gombra.

    ![Sikeres értesítés](media/security-info/securityinfo-prompt-call-answered-success.png)

    A biztonsági adatokat a rendszer úgy frissíti, hogy a szöveges üzenetküldés biztonsági mentési módszerként való használatával igazolja az identitását a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során.

12. A **siker** lapon ellenőrizheti, hogy sikeresen beállította-e a Microsoft Authenticator alkalmazást és a telefont (SMS vagy telefonhívás) a biztonsági adatokhoz, majd válassza a **kész**lehetőséget.

    ![A varázsló sikeresen befejezve oldal](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>További lépések

- A biztonsági adatok alapértelmezett módszereinek módosításához, törléséhez vagy frissítéséhez lásd:

    - [Hitelesítő alkalmazás biztonsági adatainak beállítása](security-info-setup-auth-app.md).

    - [A szöveges üzenetküldés biztonsági adatainak beállítása](security-info-setup-text-msg.md).

    - [Állítsa be a biztonsági adatokat a telefonhívások használatára](security-info-setup-phone-number.md).

    - [Állítsa be az e-mailek használatára vonatkozó biztonsági adatokat](security-info-setup-email.md).

    - [Biztonsági információk beállítása az előre definiált biztonsági kérdések használatához](security-info-setup-questions.md).

- További információ a bejelentkezés módjáról a megadott módszer használatával: [Bejelentkezés](user-help-sign-in.md).

- Ha elvesztette vagy elfelejtette a jelszavát, állítsa vissza a jelszót a [jelszó](https://passwordreset.microsoftonline.com/) -visszaállítási portálról, vagy kövesse a [munkahelyi vagy iskolai jelszó](user-help-reset-password.md) alaphelyzetbe állítása című cikk lépéseit.

- Hibaelhárítási tippek és Súgó a bejelentkezési problémákhoz a [nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.
