---
title: A biztonsági adatok (előzetes verzió) beállítása bejelentkezési kérdésből – Azure AD
description: A biztonsági adatok (előzetes verzió) beállítása a munkahelyi vagy iskolai fiókjához, miután a rendszer a szervezet bejelentkezési lapjáról kéri.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: curtand
ms.openlocfilehash: c216dbfef99422fc49fde774dc57d5cbcc9f879a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063985"
---
# <a name="set-up-your-security-info-preview-from-a-sign-in-prompt"></a>A biztonsági adatok (előzetes verzió) beállítása bejelentkezési kérdésből

Az alábbi lépéseket akkor hajthatja végre, ha a rendszer a munkahelyi vagy iskolai fiókba való bejelentkezés után azonnal kéri a biztonsági adatok beállítását.

Ez a kérdés csak akkor jelenik meg, ha nem állította be a szervezet által igényelt biztonsági adatokat. Ha korábban már beállította a biztonsági adatokat, de módosításokat szeretne végrehajtani, kövesse a különböző módszeralapú útmutatók lépéseit. További információt [a Biztonsági adatok hozzáadása és frissítése – című témakörben talál.](security-info-add-update-methods-overview.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Biztonság ellenőrzése kontra jelszó-visszaállítási hitelesítés

A biztonsági adatok módszerei a kétfaktoros biztonsági ellenőrzéshez és a jelszó alaphelyzetbe állításához is használatosak. Azonban nem minden módszer használható mindkettőre.

| Módszer | Alkalmazási cél |
| ------ | -------- |
| Hitelesítő alkalmazás | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| SMS-ek | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Telefonhívások | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| Biztonsági kulcs | Kétfaktoros ellenőrzés és jelszó-visszaállítási hitelesítés. |
| E-mail fiók | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |
| Biztonsági kérdések | Csak jelszó-visszaállítási hitelesítés. A kétfaktoros ellenőrzéshez másik módszert kell választania. |

## <a name="sign-in-to-your-work-or-school-account"></a>Bejelentkezés munkahelyi vagy iskolai fiókjába

Miután bejelentkezett munkahelyi vagy iskolai fiókjába, megjelenik egy üzenet, amely arra kéri, hogy adjon meg további információkat, mielőtt lehetővé teszi a fiók elérését.

![További információ kérése](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>A biztonsági adatok beállítása a varázslóval

Az alábbi lépésekkel állíthatja be a munkahelyi vagy iskolai fiókjához szükséges biztonsági adatokat a kérdésből.

>[!Important]
>Ez csak egy példa a folyamatra. A szervezet követelményeitől függően előfordulhat, hogy a rendszergazda különböző ellenőrzési módszereket állított be, amelyeket a folyamat során be kell állítania. Ebben a példában két módszert kérünk meg: a Microsoft Authenticator alkalmazást és egy mobiltelefonszámot az ellenőrző hívásokhoz vagy szöveges üzenetekhez.

1. Miután a **kérdésből** a Tovább lehetőséget választja, megjelenik **a Fiók biztonságának védelme varázsló,** amely megjeleníti a rendszergazda és a szervezet által bekötött első módszert. Ebben a példában ez a Microsoft Authenticator alkalmazás.

   > [!Note]
   > Ha a Microsoft Authenticator alkalmazástól eltérő hitelesítő alkalmazást szeretne használni, válassza a **Másik hitelesítő alkalmazás** hivatkozását.
   >
   > Ha a szervezet lehetővé teszi, hogy válasszon egy másik módszer mellett a hitelesítő app, akkor válassza ki a **Szeretnék létrehozni egy másik módszer linket**.

    ![A fiók biztonságának megőrzéséhez varázsló, amely az auth alkalmazás letöltési oldalát jeleníti meg](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Válassza a **Letöltés most** lehetőséget a Microsoft Authenticator alkalmazás mobileszközre való letöltéséhez és telepítéséhez, majd a **Tovább**gombra. Az alkalmazás letöltéséről és telepítéséről a [Microsoft Authenticator alkalmazás letöltése és telepítése című](user-help-auth-app-download-install.md)témakörben talál további információt.

    ![A fiók biztonságának védelme varázsló, a hitelesítő beállítása a fióklapon](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Maradjon a **Fiók beállítása** lapon, amíg beállítja a Microsoft Authenticator alkalmazást a mobileszközén.

4. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be az értesítések engedélyezéséhez (ha a rendszer kéri), válassza a **Fiók hozzáadása** lehetőséget a jobb felső **sarokban** lévő Testreszabás és vezérlés ikonon, majd válassza a **Munkahelyi vagy iskolai fiók lehetőséget**.

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

5. Térjen vissza a **Fiók beállítása** lapra a számítógépen, és válassza a **Tovább**gombot.

    Megjelenik **a QR-kódlap beszkaf.**

    ![A QR-kód beszkéselése a Hitelesítő alkalmazással](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. A megadott kód beolvasása a Microsoft Authenticator alkalmazás QR-kódolvasójával, amely azután jelent meg a mobileszközén, hogy az 5.

    A hitelesítő alkalmazásnak sikeresen hozzá kell adnia a munkahelyi vagy iskolai fiókját anélkül, hogy további adatokat kellene megadnia Öntől. Ha azonban a QR-kódolvasó nem tudja olvasni a kódot, kiválaszthatja a **Nem olvasható QR-lemezképet,** és manuálisan adhatja meg a kódot és az URL-címet a Microsoft Authenticator alkalmazásban. A kód manuális hozzáadásáról a [Fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md)című témakörben talál további információt.

7. Válassza a **Tovább** gombot a számítógép **QR-kódlapján.**

    A rendszer értesítést küld a Microsoft Authenticator alkalmazásnak a mobileszközén, hogy tesztelje a fiókját.

    ![A fiók tesztelése a hitelesítő alkalmazással](media/security-info/securityinfo-prompt-test-app.png)

8. Hagyja jóvá az értesítést a Microsoft Authenticator alkalmazásban, majd válassza a **Tovább gombot.**

    ![Sikeres értesítés az alkalmazás és a fiók összekapcsolása](media/security-info/securityinfo-prompt-auth-app-success.png)

    A biztonsági adatok úgy frissülnek, hogy a Microsoft Authenticator alkalmazás alapértelmezés szerint a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során ellenőrizze személyazonosságát.

9. A **Telefon** beállítása lapon adja meg, hogy szöveges üzenetet vagy telefonhívást szeretne-e kapni, majd válassza a **Tovább**gombot. Ebben a példában szöveges üzeneteket használunk, ezért egy olyan eszközhöz kell telefonszámot használnia, amely képes szöveges üzeneteket fogadni.

    ![A telefonszám beállítása sms-hez](media/security-info/securityinfo-prompt-text-msg.png)

    A rendszer sms-t küld a telefonszámára. Ha inkább kap egy telefonhívást, a folyamat ugyanaz. Sms helyett azonban utasításokkal ellátott telefonhívást fog kapni.

10. Írja be a mobileszközére küldött szöveges üzenet által megadott kódot, majd válassza a **Tovább**gombot.

    ![A fiók tesztelése szöveges üzenettel](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Tekintse át a sikerességi értesítést, és válassza **a Kész lehetőséget.**

    ![Sikeres értesítés](media/security-info/securityinfo-prompt-call-answered-success.png)

    A biztonsági adatok frissülnek, hogy a szöveges üzenetküldést biztonsági másolatként használják a személyazonosság ellenőrzéséhez a kétlépéses ellenőrzés vagy a jelszó alaphelyzetbe állítása során.

12. Tekintse át a **Sikeres lapot,** és ellenőrizze, hogy sikeresen beállította-e a Microsoft Authenticator alkalmazást és a telefon (szöveges üzenet vagy telefonhívás) módszert a biztonsági adatokhoz, majd kattintson a **Kész**gombra.

    ![A varázsló sikeresen befejeződött a lap](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Ha a szervezet megköveteli az alkalmazásjelszavak használatát, a varázsló egy további szakasza jelenhet meg, ahol beállíthatja. Ha megjelenik egy harmadik szakasz, az úgynevezett **Alkalmazásjelszavak**, ki kell töltenie, mielőtt képes lesz befejezni a varázslót. Az alkalmazásjelszavak hozzáadásának lépéseit a cikk [Alkalmazásjelszavak kezelése](#manage-your-app-passwords) című szakaszában olvashatja.

### <a name="manage-your-app-passwords"></a>Az alkalmazásjelszavak kezelése

Bizonyos alkalmazások, például az Outlook 2010, nem támogatják a kétlépéses ellenőrzést. A támogatás hiánya azt jelenti, hogy ha kétlépéses ellenőrzést használ a szervezetben, az alkalmazás nem fog működni. A probléma megoldásához létrehozhat egy automatikusan létrehozott jelszót, amelyet minden nem böngészőalkalmazáshoz használhat, a normál jelszótól elkülönítve.

>[!Note]
>Ha ez a beállítás nem jelenik meg a varázslóban, az azt jelenti, hogy a rendszergazda nem állította be. Ha ez nincs beállítva, de tudja, hogy alkalmazásjelszavakat kell használnia, kövesse az Alkalmazásjelszavak beállítása a [Biztonsági adatok (előnézet) lapon](security-info-app-passwords.md)található lépéseket.

Az alkalmazásjelszavak használatakor fontos megjegyezni:

- Az alkalmazásjelszavak automatikusan generálódnak, és alkalmazásonként csak egyszer kerülnek be.

- Felhasználónként legfeljebb 40 jelszó létezik. Ha a korlát után megpróbál létrehozni egyet, a rendszer kéri egy meglévő jelszó törlését, mielőtt az újat létrehozná.

- Eszközönként egy alkalmazásjelszót használjon, ne alkalmazásonként. Hozzon létre például egy jelszót a laptopösszes alkalmazásához, majd hozzon létre egy másik jelszót az asztalon lévő összes alkalmazáshoz.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Alkalmazásjelszavak hozzáadása a bejelentkezési varázslóban

1. A varázsló előző szakaszainak befejezése után válassza a **Tovább** gombot, és fejezze be az **Alkalmazásjelszó szakaszt.**

2. Írja be például `Outlook 2010`a jelszót hozzátartozó alkalmazás nevét, majd válassza a **Tovább**gombot.

    ![Az alkalmazás jelszónevének hozzáadása a varázslóban](media/security-info/app-password-app-password.png)

3. Másolja a jelszókódot az **Alkalmazás jelszó** képernyőjére, és illessze be az alkalmazás **Jelszó** területre (ebben a példában az Outlook 2010-be).

    ![Alkalmazásjelszó lap, másolási jelszóval](media/security-info/app-password-copy-password.png)

4. Miután átmásolta a jelszót, és beillesztette az alkalmazásba, térjen vissza a varázslóhoz, és győződjön meg arról, hogy a bejelentkezési módszer összes információja pontos, majd kattintson a **Kész gombra.**

    ![Alkalmazásjelszó-lap, teljesítési értesítéssel](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>További lépések

- Az alapértelmezett biztonsági adatok módszereinek módosításáról, törléséről vagy frissítésről az e-t olvashatja:

    - [Állítsa be a hitelesítő alkalmazás biztonsági adatait.](security-info-setup-auth-app.md)

    - [Állítsa be a szöveges üzenetek biztonsági adatait.](security-info-setup-text-msg.md)

    - [Állítsa be a biztonsági adatokat a telefonhívások használatához.](security-info-setup-phone-number.md)

    - [Állítsa be a biztonsági adatokat a levelezés használatához.](security-info-setup-email.md)

    - [Állítsa be a biztonsági adatokat az előre meghatározott biztonsági kérdések használatához.](security-info-setup-questions.md)

- A megadott módszerrel történő bejelentkezésről a [Bejelentkezés című témakörben](user-help-sign-in.md)talál további információt.

- Állítsa alaphelyzetbe a jelszavát, ha elvesztette vagy elfelejtette azt a [Jelszó-visszaállítás portálról,](https://passwordreset.microsoftonline.com/) vagy kövesse a [munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) című cikk lépéseit.

- Hibaelhárítási tippeket és segítséget kaphat a Bejelentkezési problémákról a [Nem lehet bejelentkezni a Microsoft-fiókkal](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) kapcsolatos cikkben.
