---
title: Az alkalmazásjelszavak kezelése – Azure Active Directory | Microsoft dokumentumok
description: További információ az alkalmazásjelszavakról és akétlépéses ellenőrzéshez használt adatokról.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253220"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Alkalmazásjelszavak kezelése a kétlépéses ellenőrzéshez

>[!Important]
>Előfordulhat, hogy a rendszergazda nem engedélyezi az alkalmazásjelszavak használatát. Ha nem látja **az alkalmazásjelszavakat,** azok nem érhetők el a szervezetben.

Az alkalmazásjelszavak használatakor fontos megjegyezni:

- Az alkalmazásjelszavak automatikusan generálódnak, és alkalmazásonként egyszer kell létrehozni és beírni őket.

- Felhasználónként legfeljebb 40 jelszó létezik. Ha a korlát után megpróbál létrehozni egyet, a rendszer kéri egy meglévő jelszó törlését, mielőtt az újat létrehozná.

    >[!Note]
    >Az Office 2013-ügyfelek (beleértve az Outlook programot is) támogatják az új hitelesítési protokollokat, és kétlépéses ellenőrzéssel használhatók. Ez a támogatás azt jelenti, hogy a kétlépéses ellenőrzés bekapcsolása után már nem lesz szüksége alkalmazásjelszavakra az Office 2013-ügyfelekhez. További információt az [Office 2013 és az Office 2016 ügyfélalkalmazások modern hitelesítési működése](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) című cikkben talál.

## <a name="create-new-app-passwords"></a>Új alkalmazásjelszavak létrehozása

A kezdeti kétfaktoros ellenőrzési regisztrációs folyamat során egyetlen alkalmazásjelszót kap. Ha egynél többre van szüksége, saját magának kell létrehoznia őket. Alkalmazásjelszavakat több területről is létrehozhat, attól függően, hogy a szervezetben hogyan van beállítva a kétfaktoros ellenőrzés. A kétfaktoros ellenőrzés munkahelyi vagy iskolai fiókkal való használatának regisztrálásáról a [Kétfaktoros ellenőrzés áttekintése, valamint a munkahelyi vagy iskolai fiók és](multi-factor-authentication-end-user-first-time.md) a kapcsolódó cikkek című témakörben olvashat bővebben.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Az alkalmazásjelszavak létrehozásának és törlésének helye

A kétfaktoros ellenőrzés használatának módjától függően létrehozhatési és törlési alkalmazásjelszavakat:

- **A szervezet kétfaktoros ellenőrzést és a További biztonsági ellenőrzés lapot használja.** Ha a munkahelyi vagy iskolai fiókját (például alain@contoso.com) kétfaktoros ellenőrzéssel használja a szervezetben, az alkalmazásjelszavakat a További biztonsági ellenőrzés [lapon](https://account.activedirectory.windowsazure.com/Proofup.aspx)kezelheti. Részletes útmutatást az Alkalmazásjelszavak létrehozása és törlése a cikk [További biztonsági ellenőrzés lapján című](#create-and-delete-app-passwords-from-the-additional-security-verification-page) témakörben talál.

- **Szervezete kétfaktoros ellenőrzést és az Office 365-portált használja.** Ha munkahelyi vagy iskolai fiókját (például alain@contoso.com), a kétfaktoros ellenőrzést és a szervezetben az Office 365-alkalmazásokat használja, az Alkalmazásjelszavakat az [Office 365 portállapjáról](https://www.office.com)kezelheti. A cikkben az [Alkalmazásjelszavak létrehozása és törlése az Office 365 portálhasználatával](#create-and-delete-app-passwords-using-the-office-365-portal) című témakörben talál részletes útmutatást.

- **Kétfaktoros ellenőrzést használ személyes Microsoft-fiókkal.** Ha kétfaktoros ellenőrzéssel rendelkező személyes alain@outlook.comMicrosoft-fiókot (például ) használ, az alkalmazásjelszavakat a [Biztonság alapjai lapon](https://account.microsoft.com/security/)kezelheti. Részletes útmutatást az Alkalmazásjelszavak használata olyan [alkalmazásokkal, amelyek nem támogatják a kétlépéses ellenőrzést.](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Alkalmazásjelszavak létrehozása és törlése a További biztonsági ellenőrzés lapról

Az alkalmazásjelszavakat a Munkahelyi vagy iskolai fiók **További biztonsági ellenőrzés** lapjáról hozhatja létre és törölheti.

1. Jelentkezzen be a [További biztonsági ellenőrzés lapra,](https://account.activedirectory.windowsazure.com/Proofup.aspx)és válassza **az Alkalmazásjelszavak**lehetőséget.

    ![Alkalmazásjelszavak lap, kiemelve az Alkalmazásjelszavak lapon](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Válassza a **Létrehozás**lehetőséget, írja be az alkalmazás jelszót igénylő alkalmazásának nevét, majd válassza a **Tovább**gombot.

    ![Alkalmazásjelszavak létrehozása lap, jelszóra szoruló alkalmazás nevével](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Másolja a jelszót az **Alkalmazás jelszólapjára,** és válassza a **Bezárás**gombot.

    ![Az alkalmazás jelszólapja a megadott alkalmazás jelszavával](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Az **Alkalmazásjelszavak** lapon ellenőrizze, hogy az alkalmazás szerepel-e a listában.

     ![Alkalmazásjelszavak lap, új alkalmazással a listában](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Nyissa meg azt az alkalmazást, amelyhez létrehozta az alkalmazásjelszót (például az Outlook 2010-et), majd a kérésre illessze be az alkalmazás jelszót. Ezt alkalmazásonként csak egyszer kell megtennie.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Alkalmazásjelszó törlése az Alkalmazásjelszavak lapon

1. Az **Alkalmazásjelszavak** lapon válassza a Törölni kívánt alkalmazásjelszó melletti **Törlés** lehetőséget.

   ![Alkalmazásjelszó törlése](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Válassza az **Igen** gombra a jelszó törlésének megerősítéséhez, majd kattintson a **Bezárás gombra.**

    Az alkalmazás jelszó törlése sikeresen megtörtént.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Alkalmazásjelszavak létrehozása és törlése az Office 365 portálon

Ha kétlépéses ellenőrzést használ munkahelyi vagy iskolai fiókjával és Office 365-ös alkalmazásaival, az Office 365 portálon létrehozhatja és törölheti az alkalmazásjelszavakat.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Alkalmazásjelszavak létrehozása az Office 365 portálon

1. Jelentkezzen be az Office 365-be, majd lépjen a [Saját fiók lapra,](https://portal.office.com)válassza **a Biztonság & adatvédelem**lehetőséget, majd válassza a További biztonsági ellenőrzés **lehetőséget.**

    ![A bővített biztonsági ellenőrzési területet megjelenítő Office-portál](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Jelölje ki a szöveget, amely azt mondja: **Alkalmazásjelszavak létrehozása és kezelése** az **Alkalmazásjelszavak** lap megnyitásához.

    ![Alkalmazásjelszavak lap, kiemelve az Alkalmazásjelszavak lapon](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Válassza a **Létrehozás**lehetőséget, írja be az alkalmazás jelszót igénylő alkalmazásának nevét, majd válassza a **Tovább**gombot.

    ![Alkalmazásjelszavak létrehozása lap, jelszóra szoruló alkalmazás nevével](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Másolja a jelszót az **Alkalmazás jelszólapjára,** és válassza a **Bezárás**gombot.

    ![Az alkalmazás jelszólapja a megadott alkalmazás jelszavával](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Az **Alkalmazásjelszavak** lapon ellenőrizze, hogy az alkalmazás szerepel-e a listában.

     ![Alkalmazásjelszavak lap, új alkalmazással a listában](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Nyissa meg azt az alkalmazást, amelyhez létrehozta az alkalmazásjelszót (például az Outlook 2010-et), majd a kérésre illessze be az alkalmazás jelszót. Ezt alkalmazásonként csak egyszer kell megtennie.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Alkalmazásjelszavak törlése az Alkalmazásjelszavak lapon

1. Az **Alkalmazásjelszavak** lapon válassza a Törölni kívánt alkalmazásjelszó melletti **Törlés** lehetőséget.

   ![Alkalmazásjelszó törlése](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. A megerősítést kérő párbeszédpanelen válassza az **Igen,** majd a **Bezárás**lehetőséget.

    Az alkalmazás jelszó törlése sikeresen megtörtént.

## <a name="if-your-app-passwords-arent-working-properly"></a>Ha az alkalmazásjelszavak nem működnek megfelelően

Ellenőrizze, hogy helyesen írta-e be a jelszót. Ha biztos benne, hogy helyesen adta meg a jelszavát, megpróbálhat újra bejelentkezni, és létrehozhat egy új alkalmazásjelszót. Ha egyik lehetőség sem oldja meg a problémát, lépjen kapcsolatba a szervezet ügyfélszolgálatával, hogy törölhessék a meglévő alkalmazásjelszavakat, így vadonatújakat hozhat létre.

## <a name="next-steps"></a>További lépések

- [A kétlépéses ellenőrzési beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md)

- Próbálja ki a [Microsoft Authenticator alkalmazást,](user-help-auth-app-download-install.md) hogy sms-ek és hívások fogadása helyett ellenőrizze az alkalmazásértesítéseket.
