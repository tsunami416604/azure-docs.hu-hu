---
title: Az Azure Active Directoryban alkalmazásjelszók kezelése |} A Microsoft Docs
description: Ezen a lapon segítségével megismerheti az alkalmazásjelszók vannak, és hogy mire szolgálnak a a figyelembe véve a kétlépéses ellenőrzés felhasználók.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 12998547c5525a7132bc350dbbaa7016e4bbeaf0
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767695"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>A kétlépéses ellenőrzéshez alkalmazásjelszók kezelése

Bizonyos böngészőn kívüli alkalmazások, például az Outlook 2010, nem támogatja a kétlépéses ellenőrzést. Támogatás hiánya, az azt jelenti, hogy a kétlépéses ellenőrzés használja, ha az alkalmazás nem fog működni. A probléma megoldásához lekéréséhez egy automatikusan létrehozott jelszó használata minden egyes böngészőn kívüli alkalmazással, elkülönítve a normál jelszavát is létrehozhat.

Alkalmazásjelszavak használata esetén ne feledje:

- Alkalmazásjelszók a automatikusan létrehozott és csak egyszer megadott alkalmazásonként.

- Nincs egy legfeljebb 40 jelszó felhasználónként. Ha megpróbál létrehozni egyet, miután ezt a korlátot, kérni fogja az új létrehozásához engedélyezése előtt törli a meglévő jelszó.

- Eszközönként, nem alkalmazásonként egy alkalmazásjelszót használni. Hozzon létre például a laptopján az alkalmazások egyszeri jelszót és a egy másik egyetlen jelszóval, asztali számítógépeken az alkalmazások.

    >[!Note]
    >Office 2013 ügyfelek (beleértve az Outlook) támogatja az új hitelesítési protokollok és használható a kétlépcsős ellenőrzéshez. Ez a támogatás, az azt jelenti, hogy után a kétlépéses ellenőrzés be van kapcsolva, már nem kell az alkalmazásjelszavak Office 2013-ügyfelek számára. További információ: a [modern hitelesítés működéséről, az Office 2013 és az Office 2016 ügyfélalkalmazások](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) cikk.

## <a name="where-to-create-and-delete-your-app-passwords"></a>Hol hozhatók létre és törölhetők az alkalmazásjelszók

Az első kétlépéses ellenőrzés regisztrációja során felhőszolgáltatására, egy jelszót az alkalmazáshoz. Ha több mint egy jelszót, létrehozhat további jelszavak, a kétlépéses ellenőrzés használata alapján:

- **A kétlépéses ellenőrzés használata a munkahelyi vagy iskolai fiókkal és a MyApps portálról.** Hozzon létre vagy töröljön az alkalmazás a jelszavak utasításait a [a MyApps portálról alkalmazásjelszók létrehozása és törlése](#create-and-delete-app-passwords-using-the-myapps-portal) című szakaszát. A MyApps portálról, és hogyan használható a kapcsolatos további információkért lásd: [Mi az a MyApps portálról az Azure Active Directoryban?](active-directory-saas-access-panel-introduction.md).

- **A kétlépéses ellenőrzés használata a munkahelyi vagy iskolai fiókot és az Office 365 portálra.** Hozzon létre vagy töröljön az alkalmazás a jelszavak utasításait a [Create és delete alkalmazásjelszók használatával az Office 365 portálon](#create-and-delete-app-passwords-using-the-office-365-portal) című szakaszát.

- **A kétlépéses ellenőrzés használata a személyes Microsoft-fiókjával.** Hozzon létre vagy töröljön az alkalmazás a jelszavak a [biztonsággal kapcsolatos alapok](https://account.microsoft.com/account/) lapra a személyes Microsoft-fiókjához. További információ: a [alkalmazásjelszókat, és a kétlépéses ellenőrzés](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) cikk.

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Hozzon létre vagy töröljön a MyApps portálról alkalmazásjelszók
Hozzon létre, és törli az alkalmazásjelszót a MyApps portálon keresztül.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>A MyApps portálról alkalmazásjelszó létrehozása

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Válassza ki a nevét a jobb felső sarokban, és válassza a **profil**.

3. Válassza ki **további biztonsági ellenőrzés**.

   ![Válassza ki a további biztonsági ellenőrzés – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Válassza ki **alkalmazásjelszók**.

   ![Válassza ki az alkalmazásjelszavak – képernyőkép](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kattintson a **Create** (Létrehozás) gombra.

6. Adjon meg egy nevet az alkalmazásjelszót, és válassza **tovább**.

7. Az alkalmazás jelszó másolása a vágólapra, és illessze be az alkalmazásba.
   
    ![Alkalmazásjelszó létrehozása](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>A MyApps portálról alkalmazásjelszó törlése

1. Nyissa meg a profil, és válassza **további biztonsági ellenőrzési**.

2. Válassza ki **alkalmazásjelszók**, majd válassza ki **törlése** melletti törli az alkalmazásjelszót.

   ![Alkalmazásjelszó törlése](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Válassza ki **Igen** annak ellenőrzéséhez, hogy törölje a jelszavát, és válassza ki a kívánt **Bezárás**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Hozzon létre vagy töröljön az alkalmazásjelszókat, az Office 365-portál használatával

Ha a kétlépéses ellenőrzés használja a munkahelyi vagy iskolai fiókot és az Office 365-alkalmazások, törölhetnek és hozhatnak létre az alkalmazásjelszókat, az Office 365-portál használatával. Egyszerre legfeljebb 40 alkalmazásjelszók rendelkezhet. Ha egy másik alkalmazás jelszavának után ezt a korlátot, akkor kell törölje valamelyik meglévő alkalmazásjelszavát.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Az Office 365-portál használatával alkalmazásjelszavakat

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2. Lépjen a https://portal.office.com, jelölje be a **beállítások** ikonra a jobb felső sarkában a **Office 365-portál** lapon, és végül **további biztonsági ellenőrzés**.

    ![Office portál ábrázoló kibontva további biztonsági ellenőrzési terület](media/security-info/security-info-o365password.png)

3. Válassza ki a szöveget, amely szerint, **létrehozása és kezelése az alkalmazásjelszavak** megnyitásához a **alkalmazásjelszók** lap.

4. Válassza ki **létrehozás**, írja be az alkalmazást, amelyet az alkalmazásjelszóra van szüksége egy rövid nevet, és válassza **tovább**.

5. Válassza ki **jelszó másolása a vágólapra**, majd válassza ki **Bezárás**.

6. A másolt alkalmazásjelszót használatával jelentkezzen be a böngészőn kívüli alkalmazáshoz. Csak meg kell adnia a jelszó egyszer, és szem előtt tartani a jövőben.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Az Office 365-portál alkalmazást a jelszavak törlése

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2. Lépjen a https://portal.office.com, jelölje be a **beállítások** ikonra a jobb felső sarkában a **Office 365-portál** oldalra, és kattintson **további biztonsági ellenőrzés**.

3. Válassza ki a szöveget, amely szerint, **létrehozása és kezelése az alkalmazásjelszavak** megnyitásához a **alkalmazásjelszók** lap.

4. Válassza ki **törlése** törli az alkalmazásjelszót, válassza ki a **Igen** a megerősítést kérő párbeszédpanelen, és válassza ki a **Bezárás**.

    Az alkalmazásjelszó törlése sikeresen megtörtént.

5. Kövesse a lépéseket az új jelszót hozzon létre egy alkalmazásjelszót létrehozásához.

## <a name="if-your-app-passwords-arent-working-properly"></a>Ha az alkalmazásjelszavak nem működik megfelelően

Győződjön meg arról, hogy helyesen írta be a jelszót. Ha biztos abban, hogy a jelszó helyes megadott, próbálkozzon jelentkezzen be újra, és hozzon létre egy új jelszót. Ha ezek a lehetőségek egyike sem oldja meg a probléma, forduljon a cég informatikai támogatási, akkor törölheti a meglévő alkalmazásjelszavak, hogy a vadonatúj kapcsolatok hozhatók létre. 

## <a name="next-steps"></a>További lépések

- [A kétlépéses ellenőrzési beállítások kezelése](multi-factor-authentication-end-user-manage-settings.md)

- Próbálja ki a [Microsoft Authenticator alkalmazás](user-help-auth-app-download-install.md) ellenőrzése az alkalmazás értesítéseket, szövegek és a hívások fogadása helyett a bejelentkezések.
