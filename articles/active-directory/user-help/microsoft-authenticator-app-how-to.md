---
title: A Microsoft Authenticator alkalmazás – az Azure Active Directory használatának első lépései |} A Microsoft Docs
description: A Microsoft Authenticator legújabb verziójára történő frissítésének ismertetése.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 590f9e2a9cf531a1124f9cb132791f2956227d9c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047507"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás használatának első lépései

A Microsoft Authenticator alkalmazás segít az illetéktelen hozzáférés megakadályozása a csalárd tranzakciókat leállítás, így Ön egy további biztonsági szint a munkájához és fiókok vagy az iskolai fiókjával (például alain@contoso.com) vagy (a személyes Microsoft-fiókja a példában alain@outlook.com).

Az alkalmazás a kétlépéses ellenőrzéshez használatakor együtt tudjon működni a két módszer egyikével:

- **Értesítés.** Az alkalmazás értesítést küld az eszközre. Ellenőrizze, hogy az értesítés megfelelő, és adja meg **ellenőrizze**. Ha nem ismeri fel az értesítést, válassza ki a **Megtagadás**.

- **Ellenőrző kódot.** Miután beírta a felhasználónevét és jelszavát, nyissa meg az alkalmazást, és másolja a megadott ellenőrző kód a **fiókok** képernyő be a bejelentkezési képernyő. Az ellenőrző kód egy második hitelesítési mód funkcionál.

## <a name="opt-in-for-two-step-verification"></a>Részvétel a kétlépéses ellenőrzéshez

A szervezet úgy dönt, hogy kétlépéses ellenőrzést használatát a munkahelyi vagy iskolai fiókjával. A rendszergazda lehetővé teszi, hogy melyik ellenőrzési módszerek sorát kell beállítani és használni. További információkért lásd: [Mi az Azure multi-factor Authentication jelent számomra](multi-factor-authentication-end-user.md).

Személyes Microsoft-fiókja akkor maga a kétlépéses ellenőrzés állíthatja. További részletes információk és útmutatás: [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Nem Microsoft-fiókok a Microsoft Authenticator alkalmazást is használhatja. Ezeket a fiókokat előfordulhat, hogy hívja a szolgáltatás nem a kétlépéses ellenőrzést, de Ön is megkeresheti a biztonsági vagy bejelentkezési beállítások képesnek kell lennie. Ezek a nem Microsoft-fiókok beállításával kapcsolatos további információkért tekintse meg a [videók a Microsoft ügyfélszolgálata](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Az alkalmazás telepítése

A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el. A legoptimálisabb lekéréséhez, hagyja, az alkalmazás értesítéseket kaphat, amikor a rendszer arra kéri. 

## <a name="add-accounts-to-the-app"></a>Fiókok hozzáadása az alkalmazáshoz

A Microsoft Authenticator alkalmazás munkahelyi vagy iskolai fiókokhoz vagy személyes fiókokat adhat hozzá. 

### <a name="add-a-personal-microsoft-account"></a>Személyes Microsoft-fiók hozzáadása

Személyes Microsoft-fiók (egy, amellyel jelentkezzen be Outlook.com, Xbox, Skype, stb.) be kell csak jelentkezzen be a Microsoft Authenticator alkalmazást a fiókjába.

### <a name="add-a-work-or-school-account"></a>Munkahelyi vagy iskolai fiók beállítása

1. Ha lehetséges, nyissa meg a [további biztonsági ellenőrzés](http://aka.ms/mfasetup) képernyőn egy másik számítógépen vagy eszközön. Erre a képernyőre beolvasásával kapcsolatos információkért lásd: [biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) , vagy forduljon a rendszergazdához.

    >[!Note]
    >Ha a rendszergazda a biztonsági adatok előzetes felület van kapcsolva, az utasítások a [beállítása a biztonsági adatok használata egy hitelesítő alkalmazást](security-info-setup-auth-app.md) szakaszban.

2. Jelölje be a jelölőnégyzetet a **hitelesítő alkalmazás**, majd válassza ki **konfigurálása**.

    ![A Konfigurálás gombra a biztonsági ellenőrzési beállítások képernyőn](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    A **mobilalkalmazás konfigurálása** képernyő jelenik meg, hogy a hitelesítő alkalmazás a QR-kódot.

    ![Képernyő, amely a QR-kódot tartalmaz](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást. Az a **fiókok** képernyőn válassza ki **fiók hozzáadása**, majd válassza ki **munkahelyi vagy iskolai fiók**.

4. A QR-kód, és válassza ki az eszköz kamerájának használata **kész** gombra kattintva zárja be a QR-kód képernyőn.

    >[!Note]
    >Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-cím manuálisan](#add-an-account-to-the-app-manually).

    A **fiókok** a képernyőn az alkalmazás megtekintheti a fiók nevét és a egy 6 jegyű ellenőrző kódot. A fokozott biztonság érdekében az ellenőrző kód módosítása meggátolja, hogy ugyanazt a kódot kétszer használatával 30 másodpercenként.  

    ![fiókok képernyő](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Fiók manuális hozzáadása az alkalmazáshoz

1. Nyissa meg a **további biztonsági ellenőrzés** képernyő. Hogyan érhetik el ezt a képernyőt a további információkért lásd: [biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Jelölje be a jelölőnégyzetet a **hitelesítő alkalmazás**, majd válassza ki **konfigurálása**.

    A **mobilalkalmazás konfigurálása** képernyő jelenik meg.

3. Másolja át a kódot és URL-cím adatait a **mobilalkalmazás konfigurálása** képernyőn, így manuálisan megadhatja azokat a QR-olvasót be.

4. Nyissa meg a Microsoft Authenticator alkalmazást. Az a **fiókok** képernyőn válassza ki **fiók hozzáadása**, majd válassza ki **munkahelyi vagy iskolai fiók**.

5. Válassza ki a QR-olvasót képernyő **írja be a kódot manuálisan**.

    ![A QR-kód képernyőn](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Írja be a kódot és URL-be a QR-kódot a képernyőn a **vegyen fel egy fiókot** képernyőn, és válassza ki **Befejezés**.

    ![Kódot és URL-cím megadására szolgáló képernyő](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    A **fiókok** a képernyőn az alkalmazás megtekintheti a fiók nevét és a egy 6 jegyű ellenőrző kódot. A fokozott biztonság érdekében az ellenőrző kód módosítása meggátolja, hogy ugyanazt a kódot kétszer használatával 30 másodpercenként.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Az eszköz ujjlenyomattal vagy arcfelismerési képességekkel használatával

A szervezet szükség lehet PIN-kód identitása igazolásához. Az eszköz ujjlenyomattal vagy arcfelismerési funkciókat használni a PIN-kód helyett állíthatja be a Microsoft Authenticator alkalmazást. Beállíthat Ez az első alkalommal használja az authenticator alkalmazás ellenőrizni a fiókját, az eszköz biometrikus képességeinek használatához PIN-kód helyett azonosítására beállítás kiválasztásával.

## <a name="use-the-app-when-you-sign-in"></a>Amikor bejelentkezik az alkalmazás használata

Miután hozzáadta a fiókok az alkalmazáshoz, jelentkezzen be a fiókok az alkalmazás használatával.

Ha úgy döntött, hogy ellenőrző kódok használata az alkalmazásban, kezdetén megjelennek a **fiókok** lapot. A kód 30 másodpercenként módosítani, hogy amikor szüksége van egy mindig van egy új kódot. Azonban nem kell semmit velük mindaddig, amíg jelentkezzen be, és a rendszer kéri, adja meg egy ellenőrző kódot.

## <a name="next-steps"></a>További lépések

- Ha az alkalmazással kapcsolatos további általános kérdése van, tekintse meg [a Microsoft Authenticator – gyakori kérdések](microsoft-authenticator-app-faq.md)

- Ha azt szeretné, hogy további információ a kétlépéses ellenőrzést, [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md)

- Ha azt szeretné, hogy további információ a biztonsági adatait, tekintse meg [a biztonsági adatok kezelése](security-info-manage-settings.md)
