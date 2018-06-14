---
title: Microsoft Authenticator alkalmazást a mobiltelefonok |} Microsoft Docs
description: Útmutató az Azure Authenticator legújabb verziójára történő frissítéshez.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200997"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Ismerkedés a Microsoft Authenticator alkalmazással
A Microsoft Authenticator alkalmazást egy további szintű be a munkahelyi vagy iskolai fiókjával biztonságot nyújt (például bsimon@contoso.com) vagy a Microsoft-fiókját (például bsimon@outlook.com).

Az alkalmazás működését az alábbi két módszer egyikével:

* **Értesítési**. Az alkalmazás segít a jogosulatlan hozzáférés elkerülése érdekében a fiókokat, és állítsa le a csalárd tranzakciókat a okostelefonján vagy táblagépén értesítést küldésével. Egyszerűen meg az értesítést, és ha az megbízható, válassza ki a **ellenőrizze**. Ellenkező esetben kiválaszthatja **Megtagadás**.
* **Az ellenőrző kódot**. Az alkalmazásnak az OAuth-ellenőrző kód létrehozásához használható legyen a szoftvertokenhez. Miután megadta a felhasználónevét és jelszavát, akkor írja be a kódot, a bejelentkezési képernyőn azokat az alkalmazás által biztosított. Az ellenőrző kódot a hitelesítés második formáját nyújtja.

A Microsoft Authenticator alkalmazást a felváltja az Azure Authenticator alkalmazást. Az Azure Authenticator alkalmazás továbbra is működik, de ha úgy dönt, hogy helyezze át az új Microsoft Authenticator alkalmazást, ez a cikk segítséget nyújthatnak.  

## <a name="opt-in-for-two-step-verification"></a>A kétlépéses ellenőrzéshez részt

A Microsoft Authenticator alkalmazást önmagában nem működik. Konfigurálható, a fiókjait kéri a második ellenőrzési metódus után jelentkezik be a felhasználónevét és jelszavát.

Munkahelyi vagy iskolai fiók akkor nem általában akkor ágyazódnak be ez a szolgáltatás kiválasztása a saját maga. Ehelyett a biztonsági rendszergazda jóváhagyja a nevünkben, és értesíti, hogy kell regisztrálni a fiókhoz az ellenőrzési módszereket. Ha ez a forgatókönyv érvényes Önre, további információk: [mit Azure multi-factor Authentication jelent a számomra](multi-factor-authentication-end-user.md).

Személyes fiók akkor be kell állítania kétlépéses ellenőrzést, a szolgáltatást. Ha a Microsoft-fiókkal rendelkezik, ezeket a lépéseket elérhetők-e a [tudnivalók a kétlépéses ellenőrzést](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

A Microsoft Authenticator a Microsoft-fiókok is használható. Előfordulhat, hogy meghívják a szolgáltatás nem a kétlépéses ellenőrzést, de meg kell biztonsági vagy bejelentkezési beállítások kereséséhez.

## <a name="install-the-app"></a>Az alkalmazás telepítéséhez
A Microsoft Authenticator alkalmazás érhető el [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Fiókok hozzáadása az alkalmazáshoz
Minden fiók hozzáadása a Microsoft Authenticator alkalmazáshoz használni kívánt használja az alábbi eljárások egyikét:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Az alkalmazás személyes Microsoft-fiók hozzáadása

Személyes Microsoft-fiók (egy, amelyekkel bejelentkezhet a Outlook.com-os, Xbox, Skype, stb.) ehhez csak jelentkezzen be fiókjába a Microsoft Authenticator alkalmazást.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Adja hozzá a munkahelyi vagy iskolai fiókkal az alkalmazásba a QR-kód képolvasó használatával
1. Nyissa meg a biztonsági ellenőrzési beállítások képernyő.  Erre a képernyőre beszerzésére információkért lásd: [a biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Jelölje be a jelölőnégyzetet a **hitelesítőalkalmazása** válassza **konfigurálása**.

    ![A Konfigurálás gombra, a biztonsági ellenőrzési beállítások képernyő](./media/authenticator-app-how-to/azureauthe.png)

    Ekkor megjelenik egy képernyőt rajta egy QR-kódot.

    ![A QR-kód biztosító képernyő](./media/authenticator-app-how-to/barcode2.png)
3. Nyissa meg a Microsoft hitelesítő alkalmazást. Az a **fiókok** képernyőn válassza ki  **+** , és adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók beállítása.
4. A kamerával beolvasni a QR-kódot, és jelölje ki **végzett** a QR-kód képernyő bezárása.

    Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-címet manuálisan](#add-an-account-to-the-app-manually).

5. Az alkalmazás egy hatjegyű kódot alatta a fiók nevét jeleníti meg, amikor elkészült.

    ![Fiókok képernyő](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Fiók hozzáadása az alkalmazáshoz manuálisan
1. Nyissa meg a biztonsági ellenőrzési beállítások képernyő.  Erre a képernyőre beszerzésére információkért lásd: [a biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md).
2. Válassza ki **konfigurálása**.

    ![A Konfigurálás gombra, a biztonsági ellenőrzési beállítások képernyő](./media/authenticator-app-how-to/azureauthe.png)

    Ekkor megjelenik egy képernyőt rajta egy QR-kódot.  Vegye figyelembe a kódot és URL-CÍMÉT.

    ![A QR-kódot és URL-címet biztosító képernyő](./media/authenticator-app-how-to/barcode2.png)
3. Nyissa meg a Microsoft hitelesítő alkalmazást. Az a **fiókok** képernyőn válassza ki  **+** , és adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók beállítása.

4. Válassza ki a képolvasó **írja be a kódot kézzel**.

    ![A QR-kód vizsgálatának képernyő](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Adja meg a kódot és URL-CÍMÉT a megfelelő mezőkbe az alkalmazásban, majd válasszon **Befejezés**.

    ![Képernyőn írja be a kódot és URL-címe](./media/authenticator-app-how-to/manual.png)

6. Az alkalmazás egy hatjegyű kódot alatta a fiók nevét jeleníti meg, amikor elkészült.

    ![Fiókok képernyő](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Fiók hozzáadása a alkalmazásba az eszköz ujjlenyomattal vagy arcfelismerést képességek
A szervezet szükség lehet PIN-kódot az ellenőrző kérdés befejezéséhez. A Microsoft Authenticator alkalmazást a PIN-kód helyett az eszköz ujjlenyomat vagy arcfelismerést képességeket is használhatja. Beállítására a az alkalmazás az első ellenőrzést, megjelenik egy lehetőség, hogy használja a Touch ID (iOS) vagy helyett ujjlenyomat-azonosító. 

Touch ID beállítása a Microsoft Authenticator, végrehajtásához szükséges egy normál ellenőrző kérdéssel és a PIN-kód. A Microsoft Authenticator lesz automatikusan elvégezze a Touch ID-t támogató eszközök 

![A Touch ID telepítés ellenőrzése](./media/authenticator-app-how-to/touchid1.png)

A pont előre, amikor a bejelentkezés ellenőrzése van szükség, válassza ki a kapott leküldéses értesítést, és a PIN-kód megadása helyett az ujjlenyomat beolvasása.

![Leküldéses értesítés](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Az alkalmazás használatát, amikor bejelentkezik

Ha a fiók hozzáadása az alkalmazáshoz, a rendszer kérheti, győződjön meg arról, hogy minden helyesen lett-e állítva a test-ellenőrzés elvégzéséhez. Ezt követően elkészült! Nem szeretnénk bármi más jelentkezzen be a következő időpontig.

Ha úgy döntött, hogy használja az ellenőrző kódok kezelésére az alkalmazásban, elkezdi a kezdőlapon megtekintheti őket. Így ha szükség van egy mindig kell egy új kódot 30 másodpercenként változik. De nem kell semmit, amíg bejelentkezhet, és meg kell ellenőrző kódot.  
