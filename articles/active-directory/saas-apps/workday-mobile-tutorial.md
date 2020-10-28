---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkanap Mobile alkalmazással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a munkanap Mobile alkalmazás között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754714"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a munkanap Mobile alkalmazással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD), a feltételes hozzáférést és az Intune-t a munkanap Mobile Appsával. Ha a munkanapokat Mobile Apps a Microsofttal integrálja, a következőket teheti:

* A bejelentkezés előtt győződjön meg arról, hogy az eszközök megfelelnek a szabályzatoknak.
* Vezérlőket adhat hozzá a munkaterülethez, így biztosítva, hogy a felhasználók biztonságosan hozzáférjenek a vállalati adatokhoz. 
* A munkanapokhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkanapokra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Munkanapok integrálása az Azure AD-vel
* Oktatóanyag: [Azure Active Directory egyszeri bejelentkezéses (SSO) integráció munkanapokkal](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli a Microsoft feltételes hozzáférési szabályzatait és az Intune-t a munkanap mobil alkalmazásaival.

* A munkanap összevont alkalmazás mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. A konfigurálásával kapcsolatos további információkért kövesse [ezt](workday-tutorial.md) a hivatkozást.

> [!NOTE] 
> A munkanap nem támogatja az Intune alkalmazás-védelmi szabályzatait. A feltételes hozzáférés használatához mobileszköz-kezelést kell használnia.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Győződjön meg arról, hogy a felhasználók hozzáférhetnek a munkanap Mobile alkalmazáshoz:

Konfigurálja a munkanapokat a Mobile App-ajánlatokhoz való hozzáférés engedélyezéséhez. A következő szabályzatokat kell konfigurálnia a mobilhoz:

Ezeket az utasításokat követve konfigurálhatja a következőket:

1. A működési terület jelentéshez tartozó tartományi biztonsági házirendek elérése.
2. Válasszon ki egy biztonsági házirendet.
    * Mobil használat – Android
    * Mobil használat – iPad
    * Mobil használat – iPhone
3. Kattintson az engedélyek szerkesztése elemre.
4. Jelölje be a megtekintés vagy a módosítás jelölőnégyzetet, hogy a biztonsági csoportok hozzáférhessenek a jelentéshez vagy a feladat biztonságossá tételéhez.
5. Jelölje be a Get vagy a Put jelölőnégyzetet, hogy a biztonsági csoportok hozzáférjenek az integrációhoz, a jelentéshez vagy a feladat biztonságos műveleteihez.

Aktiválja a függőben lévő biztonsági házirend módosításait a függőben **lévő biztonsági szabályzat módosítása** feladat futtatásával.

## <a name="open-workday-login-page-in-mobile-browser"></a>A munkanap bejelentkezési oldalának megnyitása a mobil böngészőben:

Ha feltételes hozzáférést szeretne alkalmazni a munkanapokhoz tartozó Mobile alkalmazáshoz, az alkalmazásnak külső böngészőben kell megnyitnia. Ezt a jelölőnégyzet bejelölésével **engedélyezheti a mobil böngésző egyszeri bejelentkezésének engedélyezése natív alkalmazásokhoz** című részt a **bérlő beállítása – biztonság** lehetőségnél. Ehhez szükséges, hogy az Intune-ban engedélyezett böngészőt telepítsen az iOS-re és az androidos munkahelyi profilba

![Mobil böngésző bejelentkezési adatai](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Feltételes hozzáférési szabályzat beállítása:

Ez a szabályzat csak iOS vagy Android rendszerű eszközökön való bejelentkezésre vonatkozik. Ha ki szeretné terjeszteni az összes platformra, egyszerűen válassza ki a kívánt **eszközt.** Ehhez a Szabályzathoz az eszköznek meg kell felelnie a szabályzatnak, és a Microsoft Intuneon keresztül ellenőrzi azt. Az Android rendszerű munkahelyi profiloknak köszönhetően minden felhasználónak le kell tiltania a munkanapokat (webes vagy alkalmazás), kivéve, ha a munkahelyi profillal jelentkeznek be, és az alkalmazást a Intune Céges portálon keresztül telepítették. Az iOS-hez egy további lépés is gondoskodik arról, hogy ugyanaz a helyzet érvényes legyen. Íme néhány képernyőkép a feltételes hozzáférés beállításáról.

**A munkanap a következő hozzáférési vezérlőket támogatja:**
* Többtényezős hitelesítés megkövetelése
* Eszköz megfelelőként való megjelölésének megkövetelése

**A munkanap-alkalmazás nem támogatja a következőket:**
* Jóváhagyott ügyfélalkalmazás megkövetelése
* Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)

Ha a **munkanapokat** **felügyelt eszközként** szeretné beállítani, hajtsa végre a következő lépéseket:

![Feltételes hozzáférési szabályzat beállítása](./media/workday-tutorial/managed-devices-only.png)

1. Kattintson a **Home > Microsoft Intune > feltételes Access-Policies > csak felügyelt eszközök** elemre. 

1. A **csak felügyelt eszközök** lapon adja meg a **név** mező értékét, `Managed Devices Only` és kattintson a **felhőalapú alkalmazásokra vagy műveletekre** .

1. Hajtsa végre a következő lépéseket a **felhőalapú alkalmazásokban vagy műveletekben** .

    a. A váltáshoz **válassza ki a szabályzatot, amely** a **Felhőbeli alkalmazásokra** vonatkozik.

    b. A Belefoglalás területen kattintson az **alkalmazások kiválasztása** elemre.

    c. Válassza a **munkanap** lehetőséget a kiválasztási listából.

    d. Kattintson a **Kész** gombra.

1. Kapcsolja be a **házirend engedélyezése beállítást** .

1. Kattintson a **Mentés** gombra.

A hozzáférés **biztosítása** érdekében hajtsa végre a következő lépéseket:

![Feltételes hozzáférési szabályzat a munkanap beállításakor](./media/workday-tutorial/managed-devices-only-2.png)

1. Kattintson a **Home > Microsoft Intune > feltételes Access-Policies > csak felügyelt eszközök** elemre. 

1. A **csak felügyelt eszközök** lapon adja meg a **név** mező értékét, `Managed Devices Only` és kattintson a **hozzáférés-vezérlések > az engedélyezés** elemre.

1. Hajtsa végre az alábbi lépéseket a **támogatás** lapon.

    a. Válassza ki azokat a vezérlőket, amelyek számára **engedélyezi a hozzáférést** .

    b. Jelölje be az **eszköz megfelelőként való megjelölésének megkövetelése** jelölőnégyzetet.

    c. Jelölje be **a kiválasztott vezérlők egyikének megkövetelése** jelölőnégyzetet.

    d. Kattintson a **kiválasztás** elemre.

1. Kapcsolja be a **házirend engedélyezése beállítást** .

1. Kattintson a **Mentés** gombra

## <a name="set-up-device-compliance-policy"></a>Eszköz megfelelőségi szabályzatának beállítása:

Annak biztosítása érdekében, hogy az iOS-eszközök csak a MDM által felügyelt munkanapokon keresztül legyenek bejelentkezve, le kell tiltania az App Store-alkalmazást a **com. munkanap. workdayapp** hozzáadásával a korlátozott alkalmazások listájához. Ezzel biztosíthatja, hogy csak azok az eszközök férjenek hozzá a munkanapokhoz, amelyeken a munkanapon futó alkalmazás telepítve van. A böngésző esetében csak akkor férhetnek hozzá a munkanapokhoz, ha az eszközt az Intune felügyeli, és felügyelt böngészőt használnak.

![A munkanap beállítása eszköz megfelelőségi szabályzata](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Microsoft Intune alkalmazás-konfigurációs házirendek beállítása:

| Használati eset | Kulcs érték párok |
|----------------------------------------------------------------------------------------   |-----------|
| A bérlői és a webes címek mezőinek automatikus feltöltése:<br>● Munkanapokat az Android for Work-profilok engedélyezésekor.<br>● Munkanapokat iPaden és iPhone-on.     | A bérlő konfigurálásához használja ezeket az értékeket: <br>● Konfigurációs kulcs = UserGroupCode<br>● Érték típusa = karakterlánc <br>● Konfigurációs érték = a bérlő neve. Példa: GM<br>A következő értékek használatával konfigurálhatja a webcímet:<br>● Konfigurációs kulcs = AppServiceHost<br>● Érték típusa = karakterlánc<br>● Konfigurációs érték = a bérlő alap URL-címe. Például: https://www.myworkday.com                              |   |
| A következő műveletek letiltása a munkanapokon iPaden és iPhone-on:<br>● Kivágás, másolás és beillesztés<br>● Nyomtatás                       | A funkció letiltásához állítsa a (logikai) értéket hamis értékre ezen kulcsok esetében:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| A képernyőképek letiltása az Androidon futó munkanapokon. |A funkció letiltásához állítsa a (logikai) értéket hamis értékre a AllowScreenshots kulcsban.|
| Tiltsa le a javasolt frissítéseket a felhasználók számára.|A funkció letiltásához állítsa a (logikai) értéket hamis értékre a AllowSuggestedUpdates kulcsban.|
|Szabja testre az App Store-beli URL-címét, hogy a felhasználók az Ön által választott App Store-ban irányítsák a mobil felhasználókat.|Az alkalmazás-áruház URL-címének módosításához használja ezeket az értékeket:<br>● Konfigurációs kulcs = AppUpdateURL<br>● Érték típusa = karakterlánc<br> ● Konfigurációs érték = alkalmazás-áruház URL-címe|
|       |


## <a name="ios-configuration-policies"></a>iOS-es konfigurációs szabályzatok:

1. Ugrás https://portal.azure.com/ és bejelentkezés
2. Keresse meg az **Intune** -t, vagy kattintson a listából a widgetre.
3. Ugrás az **ügyfélalkalmazások – > alkalmazások – > alkalmazás-konfigurációs házirendek – > + Hozzáadás – > felügyelt eszközök**
4. Adjon meg egy nevet.
5. A **platform** területen válassza az **iOS/iPadOS** elemet.
6. A **társított alkalmazás** területen válassza ki a felvenni kívánt iOS-alkalmazás munkaidejét
7. Kattintson a **konfigurációs beállítások** elemre, majd a **konfigurációs beállítások formátuma** területen válassza az **XML-adat megadása** lehetőséget.
8. Íme egy példa XML-fájl. Adja hozzá azokat a konfigurációkat, amelyekre alkalmazni szeretné. Cserélje le a **STRING_VALUEt** a használni kívánt karakterláncra. Cserélje `<true />` le `<false />` a vagy a-t vagy a `<true />` -t  `<false />` . Ha nem ad hozzá konfigurációt, úgy fog működni, mint az igaz érték.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Kattintson az Add (Hozzáadás) parancsra
10. Frissítse az oldalt, és kattintson az újonnan létrehozott házirendre.
11. Kattintson a hozzárendelések elemre, és válassza ki, hogy ki szeretné alkalmazni az alkalmazást.
12. Kattintson a Mentés gombra.

## <a name="android-configuration-policies"></a>Androidos konfigurációs szabályzatok:

1. Lépjen a `https://portal.azure.com/` webhelyre, és jelentkezzen be.
2. Keresse meg az **Intune** -t, vagy kattintson a listából a widgetre.
3. Ugrás az **ügyfélalkalmazások – > alkalmazások – > alkalmazás-konfigurációs házirendek – > + Hozzáadás – > felügyelt eszközök**
5. Adjon meg egy nevet. 
6. A **platform** területen válassza az **Android** lehetőséget.
7. A **társított alkalmazás** alatt válassza ki az Android-alkalmazáshoz hozzáadott munkanapokat
8. Kattintson a **konfigurációs beállítások** elemre, majd a **konfigurációs beállítások formátuma** alatt válassza a **JSON-adat megadása** lehetőséget.

