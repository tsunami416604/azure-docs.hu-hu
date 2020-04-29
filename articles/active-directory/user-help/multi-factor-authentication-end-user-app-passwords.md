---
title: Alkalmazások jelszavainak kezelése – Azure Active Directory | Microsoft Docs
description: Ismerje meg az alkalmazások jelszavait, valamint azt, hogy mire szolgálnak a kétlépéses ellenőrzéssel kapcsolatban.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253220"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Alkalmazások jelszavainak kezelése kétlépéses ellenőrzéshez

>[!Important]
>Előfordulhat, hogy a rendszergazda nem teszi lehetővé az alkalmazás jelszavának használatát. Ha nem látja az **alkalmazás jelszavait** lehetőségként, azok nem érhetők el a szervezetben.

Az alkalmazások jelszavainak használatakor fontos megjegyezni:

- Automatikusan létrejönnek az alkalmazások jelszavai, és egy alkalmazásban egyszer kell létrehozni és beírni.

- Felhasználónként legfeljebb 40 jelszó adható meg. Ha a korlát után megpróbál létrehozni egyet, a rendszer arra kéri, hogy töröljön egy meglévő jelszót, mielőtt az újat hozna létre.

    >[!Note]
    >Az Office 2013-ügyfelek (beleértve az Outlookot is) támogatják az új hitelesítési protokollokat, és kétlépéses ellenőrzéssel használhatók. Ez a támogatás azt jelenti, hogy a kétlépéses ellenőrzés bekapcsolása után már nem lesz szükség az Office 2013-ügyfelekhez tartozó alkalmazások jelszavára. További információ: a [modern hitelesítés működése az office 2013 és az office 2016 Client apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) cikkhez.

## <a name="create-new-app-passwords"></a>Új alkalmazás jelszavának létrehozása

A kezdeti kétfaktoros ellenőrzési regisztrációs folyamat során egyetlen alkalmazás jelszava van megadva. Ha egynél többre van szüksége, saját maga is létre kell hoznia őket. Az alkalmazások jelszavait több területről is létrehozhatja, attól függően, hogy hogyan van beállítva a kétfaktoros ellenőrzés a szervezetében. A kétfaktoros ellenőrzés munkahelyi vagy iskolai fiókkal való regisztrálásával kapcsolatos további információkért lásd: a [kétfaktoros ellenőrzés és a munkahelyi vagy iskolai fiók](multi-factor-authentication-end-user-first-time.md) , valamint a kapcsolódó cikkek áttekintése.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Az alkalmazás jelszavainak létrehozása és törlése

Az alkalmazáshoz tartozó jelszavakat a kétfaktoros ellenőrzés használata alapján hozhatja létre és törölheti:

- **A szervezet kétfaktoros ellenőrzést és a további biztonsági ellenőrzés oldalt használja.** Ha munkahelyi vagy iskolai fiókját (például: alain@contoso.com) használja a szervezeten belüli kétfaktoros ellenőrzéssel, akkor az alkalmazás jelszavait a [további biztonsági ellenőrzés lapról](https://account.activedirectory.windowsazure.com/Proofup.aspx)kezelheti. Részletes útmutatást az [alkalmazás jelszavainak létrehozása és törlése a további biztonsági ellenőrzés használatával](#create-and-delete-app-passwords-from-the-additional-security-verification-page) című cikkben talál.

- **A szervezet kétfaktoros ellenőrzést és az Office 365 portált használja.** Ha munkahelyi vagy iskolai fiókját (például:, alain@contoso.com), kétfaktoros ellenőrzést és Office 365 alkalmazást használ a szervezetben, az alkalmazás jelszavait az [Office 365 portálról](https://www.office.com)kezelheti. Részletes útmutatást az [alkalmazás jelszavainak létrehozása és törlése az Office 365-portál használatával](#create-and-delete-app-passwords-using-the-office-365-portal) című cikkben talál.

- **Kétfaktoros ellenőrzést használ személyes Microsoft-fiók.** Ha személyes Microsoft-fiók (például alain@outlook.com) használ kétfaktoros ellenőrzéssel, a [biztonsági alapok lapról](https://account.microsoft.com/security/)kezelheti az alkalmazáshoz tartozó jelszavakat. Részletes útmutatás: [alkalmazások jelszavainak használata olyan alkalmazásokkal, amelyek nem támogatják a kétlépéses ellenőrzést](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Alkalmazások jelszavainak létrehozása és törlése a további biztonsági ellenőrzés lapról

Az alkalmazáshoz tartozó jelszavakat a munkahelyi vagy iskolai fiókjának **további biztonsági ellenőrzés** oldaláról is létrehozhatja és törölheti.

1. Jelentkezzen be a [további biztonsági ellenőrzés lapra](https://account.activedirectory.windowsazure.com/Proofup.aspx), majd válassza az **alkalmazás jelszavai**lehetőséget.

    ![Az alkalmazás jelszavai lap kiemelve](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Válassza a **Létrehozás**lehetőséget, írja be annak az alkalmazásnak a nevét, amelyhez az alkalmazás jelszava szükséges, majd kattintson a **tovább**gombra.

    ![Alkalmazás-jelszavak létrehozása lap, amelynek a neve az alkalmazás, amelyhez jelszó szükséges](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Másolja a jelszót az **alkalmazás jelszava** lapról, majd válassza a **Bezárás**lehetőséget.

    ![Az alkalmazás jelszava oldal a megadott alkalmazás jelszavával](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Az **alkalmazás jelszavai** lapon győződjön meg arról, hogy az alkalmazás fel van sorolva.

     ![Alkalmazás jelszavai oldal, új alkalmazás a listában](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Nyissa meg azt az alkalmazást, amelyet az alkalmazás jelszavának (például az Outlook 2010) számára hozott létre, majd illessze be az alkalmazás jelszavát, amikor a rendszer erre kéri. Ezt csak egyszer kell megtennie egy alkalmazásban.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Alkalmazás jelszavának törlése az alkalmazás jelszavai lapról

1. Az **alkalmazás jelszavai** **lapon válassza a Törlés lehetőséget** a törölni kívánt alkalmazás jelszava mellett.

   ![Alkalmazás jelszavának törlése](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Az **Igen** gombra kattintva erősítse meg, hogy törölni kívánja a jelszót, majd kattintson a **Bezárás**gombra.

    Az alkalmazás jelszava sikeresen törölve.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Alkalmazások jelszavainak létrehozása és törlése az Office 365 portál használatával

Ha kétlépéses ellenőrzést használ munkahelyi vagy iskolai fiókjával és az Office 365-alkalmazásaival, az Office 365-portál használatával hozhatja létre és törölheti az alkalmazáshoz tartozó jelszavakat.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Alkalmazás jelszavának létrehozása az Office 365-portál használatával

1. Jelentkezzen be az Office 365-be, majd nyissa meg a [saját fiók lapot](https://portal.office.com), válassza a **biztonsági & adatvédelem**lehetőséget, majd bontsa ki a **további biztonsági ellenőrzés**elemet.

    ![Kibővített további biztonsági ellenőrzési területeket bemutató Office-portál](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Válassza ki azt a szöveget, amely az alkalmazás jelszavainak megadásához, az **alkalmazások jelszavainak** **létrehozásához és kezeléséhez** használható.

    ![Az alkalmazás jelszavai lap kiemelve](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Válassza a **Létrehozás**lehetőséget, írja be annak az alkalmazásnak a nevét, amelyhez az alkalmazás jelszava szükséges, majd kattintson a **tovább**gombra.

    ![Alkalmazás-jelszavak létrehozása lap, amelynek a neve az alkalmazás, amelyhez jelszó szükséges](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Másolja a jelszót az **alkalmazás jelszava** lapról, majd válassza a **Bezárás**lehetőséget.

    ![Az alkalmazás jelszava oldal a megadott alkalmazás jelszavával](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Az **alkalmazás jelszavai** lapon győződjön meg arról, hogy az alkalmazás fel van sorolva.

     ![Alkalmazás jelszavai oldal, új alkalmazás a listában](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Nyissa meg azt az alkalmazást, amelyet az alkalmazás jelszavának (például az Outlook 2010) számára hozott létre, majd illessze be az alkalmazás jelszavát, amikor a rendszer erre kéri. Ezt csak egyszer kell megtennie egy alkalmazásban.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Alkalmazások jelszavainak törlése az alkalmazás jelszavai oldalon

1. Az **alkalmazás jelszavai** **lapon válassza a Törlés lehetőséget** a törölni kívánt alkalmazás jelszava mellett.

   ![Alkalmazás jelszavának törlése](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Válassza az **Igen** lehetőséget a megerősítés mezőben, majd kattintson a **Bezárás**gombra.

    Az alkalmazás jelszava sikeresen törölve.

## <a name="if-your-app-passwords-arent-working-properly"></a>Ha az alkalmazás jelszavai nem működnek megfelelően

Győződjön meg arról, hogy helyesen írta be a jelszót. Ha biztos benne, hogy helyesen adta meg a jelszót, próbáljon meg ismét bejelentkezni, és hozzon létre egy új jelszót. Ha a probléma egyike sem oldja meg a problémát, lépjen kapcsolatba a szervezet ügyfélszolgálatával, hogy törölje a meglévő alkalmazás jelszavait, és hozzon létre teljesen új alkalmazásokat.

## <a name="next-steps"></a>További lépések

- [A kétlépéses ellenőrzési beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md)

- Szövegek és hívások fogadása helyett próbálja ki a [Microsoft Authenticator alkalmazást](user-help-auth-app-download-install.md) , hogy ellenőrizze a bejelentkezéseket az alkalmazás értesítéseivel.
