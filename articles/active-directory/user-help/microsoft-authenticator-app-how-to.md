---
title: Mobiltelefonok – Azure ad-ben készült Microsoft Authenticator alkalmazás |} A Microsoft Docs
description: Az Azure Authenticator legújabb verziójára történő frissítésének ismertetése.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 8241dcaaf5623a22f4fc485f021766276472fb51
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059822"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>A Microsoft Authenticator alkalmazás használatának első lépései
A Microsoft Authenticator alkalmazás egy további szintű munkahelyi vagy iskolai fiókját a biztonságot nyújt (például bsimon@contoso.com) vagy a Microsoft-fiókjával (például bsimon@outlook.com).

Az alkalmazás működik a két módszer egyikével:

* **Értesítési**. Az alkalmazás segítségével a fiókokhoz való illetéktelen hozzáférés megakadályozása és a egy rövid értesítést a okostelefonján vagy táblagépén küldésével állítsa le a csalárd tranzakciókat. Egyszerűen csak tekintse meg az értesítést, és ha az megbízható, válassza ki a **ellenőrizze**. Ellenkező esetben választhat **Megtagadás**.
* **Ellenőrző kód**. Az alkalmazás szoftvertokenként használható az OAuth-ellenőrző kódot létrehozni. Miután megadta a felhasználónevét és jelszavát, akkor írja be a bejelentkezési képernyőn az alkalmazás által biztosított kódot. Az ellenőrző kódot biztosít egy második hitelesítési mód.

A Microsoft Authenticator alkalmazást az Azure Authenticator alkalmazás váltja fel. Az Azure Authenticator alkalmazás továbbra is működik, de ha úgy dönt, hogy helyezze át az új Microsoft Authenticator alkalmazást, ez a cikk a segítségére lehessen.  

## <a name="opt-in-for-two-step-verification"></a>Részvétel a kétlépéses ellenőrzéshez

A Microsoft Authenticator alkalmazás maga nem működik. Konfigurálja az összes megadását kéri a második ellenőrzési módszert után jelentkezik be a felhasználónevét és jelszavát a fiókjait.

Munkahelyi vagy iskolai fiókok nem általában kap a szolgáltatás kiválasztása a saját magának. Ehelyett a biztonsági rendszergazdák jóváhagyja a Ön nevében, majd értesíti, ellenőrzési módszerek sorát a fiók regisztrálásához. Ha ez a forgatókönyv érvényes Önre, további információ: [Mi az Azure multi-factor Authentication jelent számomra](multi-factor-authentication-end-user.md).

Egy személyes fiók maga a kétlépéses ellenőrzés beállításához szüksége. Ha Microsoft-fiókkal rendelkezik, ezeket a lépéseket érhetők el a [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

A Microsoft Authenticator a nem Microsoft-fiókokkal is használható. Előfordulhat, hogy hívják meg a szolgáltatás nem a kétlépéses ellenőrzést, de Ön is megkeresheti a biztonsági vagy bejelentkezési beállítások képesnek kell lennie.

## <a name="install-the-app"></a>Az alkalmazás telepítése
A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el.

## <a name="add-accounts-to-the-app"></a>Fiókok hozzáadása az alkalmazáshoz
Minden fiók, amely a Microsoft Authenticator alkalmazást a hozzáadni kívánt használja az alábbi eljárások egyikét:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Személyes Microsoft-fiók hozzáadása az alkalmazáshoz

Személyes Microsoft-fiók (egy, amellyel jelentkezzen be Outlook.com, Xbox, Skype, stb.) be kell csak jelentkezzen be a Microsoft Authenticator alkalmazást a fiókjába.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>A QR-kódolvasót használja az alkalmazást munkahelyi vagy iskolai fiók hozzáadása
1. Nyissa meg a biztonsági ellenőrzési beállítások képernyő.  Hogyan érhetik el ezt a képernyőt a további információkért lásd: [biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Melletti jelölőnégyzetet a **hitelesítő alkalmazás** majd **konfigurálása**.

    ![A Konfigurálás gombra a biztonsági ellenőrzési beállítások képernyőn](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Ekkor megjelenik egy képernyő, és ezt a QR-kódot.

    ![Képernyő, amely a QR-kódot tartalmaz](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Nyissa meg a Microsoft Authenticator alkalmazást. Az a **fiókok** képernyőn válassza ki **+**, és adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók beállítása.
4. A QR-kód, és válassza ki a kamera használatával **kész** gombra kattintva zárja be a QR-kód képernyőn.

    Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-cím manuálisan](#add-an-account-to-the-app-manually).

5. Ha az alkalmazás egy hatjegyű kóddal alatta a nevét mutatja, akkor végzett.

    ![fiókok képernyő](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Fiók manuális hozzáadása az alkalmazáshoz
1. Nyissa meg a biztonsági ellenőrzési beállítások képernyő.  Hogyan érhetik el ezt a képernyőt a további információkért lásd: [biztonsági beállítások módosítása](multi-factor-authentication-end-user-manage-settings.md).
2. Válassza ki **konfigurálása**.

    ![A Konfigurálás gombra a biztonsági ellenőrzési beállítások képernyőn](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Ekkor megjelenik egy képernyő, és ezt a QR-kódot.  Vegye figyelembe a kódot és URL-CÍMÉT.

    ![Képernyő, amely biztosítja a QR-kódot és URL-címe](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Nyissa meg a Microsoft Authenticator alkalmazást. Az a **fiókok** képernyőn válassza ki **+**, és adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók beállítása.

4. Válassza ki a képolvasó **írja be a kódot manuálisan**.

    ![A QR-kód képernyőn](./media/microsoft-authenticator-app-how-to/scan2.png)
5. Adja meg a kódot és URL-címe az alkalmazás a megfelelő mezőkbe, majd válassza ki **Befejezés**.

    ![Kódot és URL-cím megadására szolgáló képernyő](./media/microsoft-authenticator-app-how-to/manual.png)

6. Ha az alkalmazás egy hatjegyű kóddal alatta a nevét mutatja, akkor végzett.

    ![fiókok képernyő](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Vegyen fel egy fiókot a alkalmazásba az eszköz ujjlenyomattal vagy arcfelismerési képességekkel
A szervezet előfordulhat, hogy PIN-kódot az ellenőrző kérdés végrehajtásához. A Microsoft Authenticator alkalmazást az eszköz ujjlenyomattal vagy arcfelismerési funkciókat használhat PIN-kód helyett. Beállítására az alkalmazásban az első ellenőrzést, látni fogja a Touch ID használata (az IOS-es), vagy inkább ujjlenyomat-azonosító. 

Touch ID beállítása a Microsoft Authenticator, végezze el a normál ellenőrző kihívást PIN-kóddal kell. A Microsoft Authenticator lesz automatikusan állíthatja be, amelyek támogatják a Touch ID eszközök 

![A Touch ID-telepítés ellenőrzése](./media/microsoft-authenticator-app-how-to/touchid1.png)

A pont előre, amikor meg kell ellenőrizze a bejelentkezést, válassza ki a kapott leküldéses értesítést, és a PIN-kód megadása helyett az ujjlenyomat beolvasása.

![Leküldéses értesítés](./media/microsoft-authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Amikor bejelentkezik az alkalmazás használata

A fiók az alkalmazás ad hozzá, miután egy teszt ellenőrzése, hogy minden helyesen lett-e konfigurálva ehhez kérheti. Ezt követően kész! Nem kell használat megkezdéséhez jelentkezzen be a következő időpontig.

Ha úgy döntött, hogy ellenőrző kódok használata az alkalmazásban, elindíthatja a kezdőlapon megtekintheti őket. Így ha szükség van egy mindig kell egy új kód 30 másodpercenként módosítását. Azonban nem kell semmit velük mindaddig, amíg jelentkezzen be, és a rendszer kéri, adja meg egy ellenőrző kódot.  
