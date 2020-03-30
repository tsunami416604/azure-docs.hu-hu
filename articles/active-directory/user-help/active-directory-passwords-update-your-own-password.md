---
title: Jelszó alaphelyzetbe állítása biztonsági adatok használatával - Azure Active Directory | Microsoft dokumentumok
description: Hogyan állíthatja vissza saját jelszavát, ha elfelejti, a biztonsági adatok és a kétlépéses ellenőrzés segítségével.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3ed79be318319009aabb1b1ef0c42c4021bbbabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062659"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>A munkahelyi vagy iskolai jelszó alaphelyzetbe állítása biztonsági adatok használatával

Ha elfelejtette a munkahelyi vagy iskolai jelszavát, soha nem kapott jelszót a szervezettől, vagy ki van zárva a fiókjából, a biztonsági adatok és a mobileszköz segítségével alaphelyzetbe állíthatja munkahelyi vagy iskolai jelszavát. Ahhoz, hogy beállítahatja az adatait, és alaphelyzetbe állíthatja saját jelszavát, a rendszergazdának be kell kapcsolnia ezt a funkciót.

Ha ismeri a jelszavát, de módosítani szeretné, olvassa el a cikk [Jelszó módosítása című szakaszában.](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password)

>[!Important]
>Ez a cikk az on-t használó felhasználók számára készült, akik egy elfelejtett vagy ismeretlen munkahelyi vagy iskolai fiókjelszavát próbálják alaphelyzetbe állítani. Ha Ön rendszergazda, és az önkiszolgáló jelszó-visszaállítás bekapcsolására vonatkozó információkat keresi az alkalmazottak vagy más felhasználók számára, olvassa el az [Azure AD önkiszolgáló jelszó-visszaállításának telepítése és egyéb cikkek című témakört.](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Munkahelyi vagy iskolai fiók jelszavának alaphelyzetbe állítása vagy zárolásának feloldása

Ha nem tudja elérni az Azure Active Directory (Azure AD) fiókját, annak oka lehet, hogy:

- A jelszó nem működik, és alaphelyzetbe szeretné állítani, vagy

- Tudja a jelszavát, de a fiókja zárolva van, és fel kell oldania a zárolását.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>A jelszó alaphelyzetbe állítása és a fiókba való visszakerülés

1. Az **Enter password (Jelszó megadása)** képernyőn válassza **az Elfelejtette a jelszó lehetőséget.**

2. A Vissza a **fiókba** képernyő, írja be a munkahelyi vagy iskolai **felhasználói azonosító** (például az e-mail-cím), bizonyítsa, hogy nem egy robot, írja be a karaktereket, amit a képernyőn látható, majd válassza a **Tovább gombra**.

   ![Vissza a fiók képernyőre](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Ha a rendszergazda nem kapcsolta be a saját jelszavának alaphelyzetbe állítását, a **Fiókba visszakerülés** e-kiszolgáló képernyő helyett a **Rendszergazda kapcsolata** hivatkozás jelenik meg. Ez a hivatkozás lehetővé teszi, hogy e-mailben vagy webportálon keresztül lépjen kapcsolatba a rendszergazdával a jelszó alaphelyzetbe állításával kapcsolatban.

3. Válasszon az alábbi módszerek közül a személyazonosság igazolásához és jelszavának módosításához. Attól függően, hogy a rendszergazda hogyan állította be a szervezetet, előfordulhat, hogy másodszor is végig kell mennie ezen a folyamaton, és egy második ellenőrzési lépéshez kell hozzáadnia az adatokat.

    ![Visszaad -hoz -a számla, igazolás lép #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Attól függően, hogy a rendszergazda hogyan állította be a szervezetet, előfordulhat, hogy az ellenőrzési lehetőségek némelyike nem érhető el. Korábban már be kell állítania a mobileszközt ellenőrzésre ezen módszerek közül legalább egy alkalmazásával.<br><br>Emellett előfordulhat, hogy az új jelszónak meg kell felelnie bizonyos erősségi követelményeknek. Az erős jelszavak általában 8–16 karakterből állnak, beleértve a kis- és nagybetűket, legalább egy számot és legalább egy különleges karaktert.

- **Állítsa alaphelyzetbe a jelszavát egy e-mail címmel.** E-mailt küld a korábban beállított e-mail címre a kétlépéses ellenőrzés vagy a biztonsági adatok között. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, az e-mail cím beállításáról a [Biztonsági adatok beállítása e-mail (előzetes verzió)](security-info-setup-email.md) című cikkben talál további információt. Ha még nem használja a biztonsági adatokat, az e-mail-cím beállításáról a [Fiók beállítása kétlépéses ellenőrzési](multi-factor-authentication-end-user-first-time.md) cikkben talál további információt. 

    1. Válassza **a Másodlagos e-mail küldése e-mail ben,** majd **az E-mail**lehetőséget.

    2. Írja be az ellenőrző kódot az e-mailből a mezőbe, majd válassza a **Tovább gombot.**

    3. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

- **Állítsa alaphelyzetbe a jelszót szöveges üzenetben.** Szöveges üzenetet küld a biztonsági adatokban korábban beállított telefonszámra. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, a szöveges üzenetek beállításáról a [Szöveges üzenetek (előzetes verzió) használatának beállítása](security-info-setup-text-msg.md) című cikkben további információt talál. Ha még nem használja a biztonsági adatokat, a szöveges üzenetek beállításáról a Fiók [beállítása kétlépéses ellenőrzési](multi-factor-authentication-end-user-first-time.md) cikkben talál további információt.

    1. Válassza **a Mobiltelefonom szövegének lehetőséget,** írja be a telefonszámát, majd válassza a **Szöveg**lehetőséget.

    2. Írja be az ellenőrző kódot a szöveges üzenetből a mezőbe, majd válassza a **Tovább**gombot.

    3. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

- **Állítsa alaphelyzetbe a jelszót egy telefonszám használatával.** Szöveges üzenetet küld a biztonsági adatokban korábban beállított telefonszámra. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, a telefonszám beállításáról a [Telefonszám beállítása (előzetes verzió)](security-info-setup-phone-number.md) című cikkben olvashat bővebben. Ha még nem használja a biztonsági adatokat, a telefonszám beállításáról a [Kétlépéses ellenőrzés](multi-factor-authentication-end-user-first-time.md) hez című cikkben talál további információt a telefonszám beállításáról.

    1. Válassza **a Mobiltelefon hívása**lehetőséget, írja be a telefonszámát, majd válassza a **Hívás**lehetőséget.

    2. Fogadja a telefonhívást, kövesse az utasításokat a személyazonosság ellenőrzéséhez, majd válassza a **Tovább**gombot.

    3. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

- **Biztonsági kérdések kelvaló rakoncát;** Megjeleníti a biztonsági adatokban beállított biztonsági kérdések listáját. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, a biztonsági kérdések beállításáról a [Biztonsági adatok beállítása az előre meghatározott biztonsági kérdések (előzetes verzió) használatáról](security-info-setup-questions.md) szóló cikkben talál további információt. Ha még nem használja a biztonsági adatokat, a biztonsági kérdések beállításáról a [Kétlépéses ellenőrzés](multi-factor-authentication-end-user-first-time.md) hez című cikkben olvashat bővebben a biztonsági kérdések beállításáról.

    1. Válassza **a Válasz a biztonsági kérdésekmegválaszolása**lehetőséget, válaszoljon a kérdésekre, majd kattintson a **Tovább**gombra.

    2. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

- **Állítsa alaphelyzetbe a jelszót a hitelesítő alkalmazás értesítésével.** Jóváhagyási értesítést küld a hitelesítő alkalmazásnak. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, további információt talál a hitelesítő alkalmazás beállításáról a [Biztonsági adatok beállítása hitelesítési alkalmazás (előzetes verzió) használatához](security-info-setup-auth-app.md) című cikkben. Ha még nem használja a biztonsági adatokat, további információt talál a hitelesítő alkalmazás beállításáról, hogy értesítést küldjön a [Fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben.

    1. Válassza **az Értesítés jóváhagyása a hitelesítő alkalmazásban**lehetőséget, majd az Értesítés **küldése**lehetőséget.

    2. Hagyja jóvá a bejelentkezést a hitelesítő alkalmazásból.

    3. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

- **Állítsa alaphelyzetbe a jelszót a hitelesítő alkalmazásból származó kód használatával.** A hitelesítési alkalmazás által biztosított véletlenszerű kódot fogad el. Ha a rendszergazda bekapcsolta a biztonsági adatok használatát, további információt talál a hitelesítő alkalmazás beállításáról, amely kód megadására szolgál a [Biztonsági adatok beállítása hitelesítési alkalmazás (előzetes verzió) használatához](security-info-setup-auth-app.md) című cikkben. Ha még nem használja a biztonsági adatokat, további információt talál a hitelesítő alkalmazás beállításáról, amely kód megadására szolgál a [Fiók beállítása kétlépéses ellenőrzési](multi-factor-authentication-end-user-first-time.md) cikkben.

  1. Válassza **a Kód megadása a hitelesítő alkalmazásból**lehetőséget, majd az Értesítés **küldése**lehetőséget.

  2. Nyissa meg a hitelesítő alkalmazást, írja be a fiók ellenőrző kódját a mezőbe, és válassza a **Tovább gombot.**

  3. Írja be és erősítse meg az új jelszót, majd válassza a **Befejezés gombot.**

  4. Miután megjelenik az üzenet arról, hogy a jelszó alaphelyzetbe lett állítva, az új jelszóval jelentkezhet be a fiókjába.

     Ha továbbra sem tud hozzáférni a fiókjához, további segítségért forduljon a szervezet rendszergazdájához.

A jelszó alaphelyzetbe állítása után előfordulhat, hogy egy megerősítő e-mailt \<kap, amely egy olyan fiókból érkezik, mint például: "Microsoft *your_organization*> nevében.". Ha hasonló e-mailt kap, de nemrég nem állította vissza a jelszavát, azonnal kapcsolatba kell lépnie a szervezet rendszergazdájával.

## <a name="how-to-change-your-password"></a>Jelszó módosítása

Ha csak meg szeretné változtatni a jelszavát, megteheti az Office 365-portálon, az Azure Access Panelen vagy a Windows 10 bejelentkezési lapján keresztül.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Jelszó módosítása az Office 365 portálhasználatával

Akkor használja ezt a módszert, ha az alkalmazásokat általában az Office-portálon keresztül éri el:

1. Jelentkezzen be [Office 365-fiókjába](https://portal.office.com)a meglévő jelszavával.

2. Válassza ki a profilját a jobb felső sarokban, majd válassza **a Fiók megtekintése**lehetőséget.

3. Válassza **a Biztonsági & adatvédelmi** > jelszó**lehetőséget.**

4. Írja be a régi jelszót, hozza létre és erősítse meg az új jelszót, majd válassza a **Küldés gombot.**

### <a name="to-change-your-password-from-the-azure-access-panel"></a>A jelszó módosítása az Azure Access Panelről

Akkor használja ezt a módszert, ha általában az Azure Access Panelről (MyApps) éri el az alkalmazásokat:

1. Jelentkezzen be az [Azure Access Panel,](https://myapps.microsoft.com/)a meglévő jelszó használatával.

2. Válassza ki a profilját a jobb felső sarokban, majd válassza a **Profil**lehetőséget.

3. Válassza **a Jelszó módosítása**lehetőséget.

4. Írja be a régi jelszót, hozza létre és erősítse meg az új jelszót, majd válassza a **Küldés gombot.**

### <a name="to-change-your-password-at-windows-sign-in"></a>A jelszó módosítása a Windows bejelentkezéskor

Ha a rendszergazda bekapcsolta a funkciót, megjelenik a **Jelszó alaphelyzetbe állítása** a Windows 7, a Windows 8, a Windows 8.1 vagy a Windows 10 bejelentkezési képernyőjén.

1. A **jelszó alaphelyzetbe állítása** hivatkozással indítsa el a jelszó-visszaállítási folyamatot anélkül, hogy a szokásos webalapú élményt kellene használnia.

2. Erősítse meg a felhasználói azonosítót, és válassza a **Tovább gombot.**

3. Jelöljön ki és erősítsen meg egy ellenőrzési kapcsolatfelvételi módszert. Ha szükséges, válasszon egy második ellenőrzési lehetőséget, amely eltér az előzőtől, és töltse ki a szükséges adatokat.

4. Az **Új jelszó létrehozása** lapon írja be és erősítse meg az új jelszót, majd kattintson a **Tovább gombra.**

    Az erős jelszavak általában 8–16 karakterből állnak, beleértve a kis- és nagybetűket, legalább egy számot és legalább egy különleges karaktert.

5. Miután megjelenik az üzenet, amely szerint a jelszó alaphelyzetbe állt, válassza a **Befejezés**lehetőséget.

    Ha továbbra sem tud hozzáférni a fiókjához, további segítségért forduljon a szervezet rendszergazdájához.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

Íme néhány gyakori hibaeset és azok megoldásai:

|Probléma|Leírás|Megoldás|
| --- | --- | --- |
|Amikor megpróbálom megváltoztatni a jelszavamat, hibaüzenet jelenik meg. |A jelszó hoz egy szót, kifejezést vagy mintát, amely könnyen kitalálja a jelszót.| Próbálkozzon újra egy erősebb jelszóval.|
|Miután megadtam a felhasználói azonosítómat, egy olyan oldalra lépek, amelyen az áll: "Kérjük, forduljon a rendszergazdához."|A Microsoft megállapította, hogy a felhasználói fiók jelszavát a rendszergazda kezeli egy helyszíni környezetben. Ennek eredményeképpen nem állíthatja vissza jelszavát a "Nem fér hozzá a fiókjához" linkről. |További segítségért forduljon a rendszergazdához.|
|Miután megadtam a felhasználói azonosítómat, hibaüzenet jelenik meg: "A fiók nincs engedélyezve a jelszó-visszaállításhoz."|A rendszergazda nem állította be a fiókját, így visszaállíthatja saját jelszavát.|A rendszergazda nem kapcsolta be a szervezet jelszó-visszaállítását a "Nem férhozzá a fiókhoz" hivatkozásról, vagy nem engedélyezte a szolgáltatás használatát.<br><br> A jelszó alaphelyzetbe állításához válassza a "Forduljon a rendszergazda hoz" hivatkozást, és küldjön e-mailt a vállalat rendszergazdájának, és tudassa vele, hogy új jelszót szeretne beállítani.|
|Miután megadtam a felhasználói azonosítómat, hibaüzenet jelenik meg: "Nem tudtuk ellenőrizni a fiókját."|A bejelentkezési folyamat nem tudta ellenőrizni a fiókadatait.|Két oka lehet, hogy ezt az üzenetet látja.<br><br>1. A rendszergazda bekapcsolta a jelszó-visszaállítást a szervezetnél, de még nem regisztrált a szolgáltatás használatára. A jelszó-visszaállításhoz az alábbi cikkek egyikében, az ellenőrzési módszer alapján: [Biztonsági adatok beállítása hitelesítő alkalmazás használatára (előnézet)](security-info-setup-auth-app.md), Biztonsági adatok beállítása telefonhívás [(előnézet) használatára,](security-info-setup-phone-number.md)Biztonsági adatok beállítása szöveges üzenetek [(előnézet) használatához](security-info-setup-text-msg.md), Biztonsági adatok [beállítása e-mail (előnézet) használatához,](security-info-setup-email.md)vagy [Biztonsági adatok beállítása biztonsági kérdések (előzetes verzió) használatához.](security-info-setup-questions.md)<br><br>2. A rendszergazda nem kapcsolta be a jelszó-visszaállítást a szervezetnél. Ebben az esetben a "Kapcsolatfelvétel a rendszergazdával" lehetőséget kell választania, hogy e-mailt küldjön a rendszergazdának, és kérje a jelszó alaphelyzetbe állítását.|

## <a name="next-steps"></a>További lépések

- A biztonsági adatokról a [Biztonsági adatok (előzetes verzió) című cikkben](user-help-security-info-overview.md) olvashat.

- Ha olyan személyes fiókba próbál visszatérni, mint az Xbox, hotmail.com vagy outlook.com, próbálkozzon a [Ha nem tud bejelentkezni a Microsoft-fiókkal című cikkben](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)található javaslatokkal.
