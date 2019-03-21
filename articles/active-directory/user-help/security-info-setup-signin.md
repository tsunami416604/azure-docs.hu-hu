---
title: Biztonsági adatok (előzetes verzió) beállítása a bejelentkezési kérések jelenhetnek – Azure Active Directory-ből |} A Microsoft Docs
description: Hogyan állítható be a munkahelyi vagy iskolai fiók biztonsági adatainak, ha a szervezete bejelentkezési oldalára kér.
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
ms.openlocfilehash: f9c6faff10f68d720bc3c86a191e4cd1b1f9abdc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123606"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Állítsa be a biztonsági adatok (előzetes verzió) a bejelentkezési oldal használatával
Ezeket a lépéseket követheti, ha az kéri, jelentkezzen be munkahelyi vagy iskolai fiókjával után azonnal a biztonsági adatok beállításához.

Ez az üzenet csak akkor látható, ha még nem állított be a szervezete által megkövetelt biztonsági adatait. Ha korábban már beállította a biztonsági adatait, de a kívánt módosításokat, a lépések a különböző metódus-alapú útmutatókat. További információkért lásd: [hozzáadása vagy módosítása a biztonsági adatok áttekintése](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Jelentkezzen be munkahelyi vagy iskolai fiókjával
A munkahelyi vagy iskolai fiókkal való bejelentkezés után látni fogja kéri, hogy több információt nyújt a előtt lehetővé teszi, hogy hozzáférjen a fiókjához.

![További információk kérésére irányuló kérés](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Állítsa be a biztonsági adatait, a varázsló segítségével
Kövesse az alábbi lépéseket a biztonsági adatok a munkahelyi vagy iskolai fiók beállítása a parancssorból.

>[!Important]
>Ez az a folyamat csak egy példát. Attól függően, a szervezet követelményeinek a rendszergazda előfordulhat, hogy beállította, hogy ez a folyamat során történő beállításához kell másik ellenőrzési módszerek. Ebben a példában azt Ön igénylő két módszer, a Microsoft Authenticator alkalmazás és az ellenőrző hívást vagy SMS-EK mobiltelefonszám.

1. Kiválasztása után **tovább** a parancssorból egy **tartsa a fiók biztonságos varázsló** megjelenik, és az első módszert a rendszergazda és a szervezet kell beállítani. Ebben a példában a Microsoft Authenticator alkalmazás esetén.

   > [!Note]
   > Ha szeretne egy hitelesítő alkalmazást a Microsoft Authenticator alkalmazástól használni, válassza ki a **egy másik hitelesítő alkalmazást használni kívánt** hivatkozásra.
   > 
   > Ha vállalata engedélyezi, hogy az authenticator alkalmazás mellett eltérő módszer kiválasztása, kiválaszthatja a **más módszert kapcsolat beállításához szeretnék**.

    ![Tartsa meg a fiók biztonságos varázsló, a hitelesítés alkalmazás letöltési oldal megjelenítése](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Válassza ki **letöltése** töltse le és telepítse a Microsoft Authenticator alkalmazást a mobileszközén, és válassza a **tovább**. Töltse le és telepítse az alkalmazást kapcsolatos további információkért lásd: [töltse le és telepítse a Microsoft Authenticator alkalmazás](user-help-auth-app-download-install.md).

    ![A fiók biztonságos varázsló az authenticator beállítása a fiók oldalára mutató megtartása](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Továbbra is a **a fiók beállítása** oldalon, állítsa be a Microsoft Authenticator alkalmazást a mobileszközén.

4. Nyissa meg a Microsoft Authenticator alkalmazást, engedélyezi az értesítések (Ha a rendszer kéri), válassza ki **fiók hozzáadása** a a **testreszabása és vezérlési** ikonjára a jobb felső sarokban, majd válassza ki **munkahelyi vagy iskolai fiókkal**.

5. Lépjen vissza a **a fiók beállítása** lapon a számítógépen, és válassza ki **tovább**.

    A **a QR-kód** lap jelenik meg.

    ![A QR-kód az Authenticator alkalmazás használatával](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. A Microsoft Authenticator alkalmazás QR kód olvasó, amely jelentek meg a mobileszközén, miután létrehozta a munkahelyi vagy iskolai fiókjával az 5. lépés-a megadott kód beolvasása.

    Az authenticator alkalmazás kell sikeresen adja hozzá a munkahelyi vagy iskolai fiók, a további adatok nélkül. Azonban, ha a QR-kód olvasó nem tudja olvasni a kódot, válassza a **nem lehet beolvasni a QR-kód hivatkozás** és manuálisan adja meg a kódot és URL-CÍMÉT a Microsoft Authenticator alkalmazásba. Manuálisan adja hozzá a kódot kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

7. Válassza ki **tovább** a a **a QR-kód** lap a számítógépen.

    A rendszer értesítést küld a Microsoft Authenticator alkalmazást a mobileszközén, a fiók teszteléséhez.

    ![A fiók a hitelesítő alkalmazás tesztelése](media/security-info/securityinfo-prompt-test-app.png)

8. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, és válassza ki **tovább**.

    ![Sikeres címtármódosítást jelző értesítés, az alkalmazás és a fiók csatlakoztatása](media/security-info/securityinfo-prompt-auth-app-success.png).

    A biztonsági adatok frissül, és a Microsoft Authenticator alkalmazással igazolhatja a személyazonosságát a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása használatakor alapértelmezés szerint.

9. Az a **Phone** állítsa be a lapot, válassza ki, hogy szeretné-e SMS-ben vagy egy telefonhívás fogadása, és válassza ki **tovább**. Ebben a példában az alkalmazásában használunk szöveges üzenetek, így egy telefonszámot szöveges üzeneteket fogadhat eszközt kell használnia.

    ![A telefonszám a szöveges üzenetküldés telepítése megkezdődött](media/security-info/securityinfo-prompt-text-msg.png)

    Egy szöveges üzenetet küld telefonjára. Ha inkább telefonon hívja be, a folyamat ugyanaz. Azonban kap telefonhívást, szöveges üzenet helyett utasításokat tartalmazó.

10. Adja meg a szöveges üzenet a mobil eszközére küldött által biztosított kódot, és válassza ki **tovább**.

    ![A szöveges üzenet fiókját tesztelése](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Tekintse át a sikeres címtármódosítást jelző értesítés, és válassza ki **kész**.

    ![Sikeres értesítések](media/security-info/securityinfo-prompt-call-answered-success.png)

    A biztonsági adatok szöveges üzenetben egy biztonsági mentési módszerként használni a személyazonosságát, ha a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása használatakor frissül.

12. Tekintse át a **sikeres** lapon ellenőrizze, hogy sikeresen beállította a Microsoft Authenticator alkalmazás és a egy telefon (SMS-üzenet vagy telefonhíváson alapuló) módszer a biztonsági adatait, és válassza ki **kész** .

    ![Sikeresen befejeződött. varázslólap](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>További lépések

- Ha módosítani szeretné, ha delete vagy update alapértelmezett biztonsági adatai módszerek, lásd:

    - [Állítsa be a biztonsági információkat kelljen egy hitelesítő alkalmazást](security-info-setup-auth-app.md).

    - [Állítsa be a biztonsági információkat kelljen szöveges üzenetküldés](security-info-setup-text-msg.md).

    - [Állítsa be a biztonsági adatok telefonhívásokat használandó](security-info-setup-phone-number.md).

    - [Biztonsági adatok beállítása az e-mailben](security-info-setup-email.md).

    - [Állítsa be a biztonsági adatai előre definiált biztonsági kérdések használata](security-info-setup-questions.md).

- Jelentkezzen be a megadott metódus használatával kapcsolatos információkért lásd: [bejelentkezés](user-help-sign-in.md).

- A jelszó alaphelyzetbe állítása, ha elfelejti, az elveszett vagy a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/) vagy kövesse a a [a munkahelyi vagy iskolai jelszó visszaállítása](user-help-reset-password.md) cikk.

- Hibaelhárítási tippek és a bejelentkezési problémákkal kapcsolatos súgó a [nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikk.