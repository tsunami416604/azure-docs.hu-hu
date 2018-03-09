---
title: "Mi az a hozzáférési panel az Azure Active Directoryban? | Microsoft Docs"
description: "Ismerje meg, hogyan használható a hozzáférési panel (webböngésző, Android-alkalmazás, iPhone és iPad) férhetnek hozzá SaaS-alkalmazásokhoz való változatait."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9b647e8f731995537a8797ab28248a1c42b6460
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="what-is-the-access-panel"></a>Mi az a hozzáférési panel?

A hozzáférési panel egy webes portál. Ha rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD), a hozzáférési panel segítségével megtekintheti, és indítsa el a felhőalapú alkalmazásokat, amelyek egy Azure AD-rendszergazda hozzáférést adott. Önkiszolgáló csoportkezelési és felügyeleti képességeit a hozzáférési panel keresztül is használható.

A hozzáférési panel egy külön Azure-portálról. Azt nem kell Azure-előfizetésre.

![Hozzáférési panel][1] a hozzáférési panelen szerkesztheti bizonyos a profil beállításait, és tegye a következőket:

- A munkahelyi vagy iskolai fiókhoz tartozó jelszó módosítása.

- Jelszó alaphelyzetbe állítása beállításainak szerkesztése

- Többtényezős hitelesítés (a rendszergazda lett rá szükséges fiókok) kapcsolódó ügyfél és a beállításokat szabályozó beállítások szerkesztése.

- A fiók adatait, például a felhasználói Azonosítóját, másodlagos e-mail-, mobil- és office telefonszámokat, és eszközök megtekintéséhez.

- Megtekintheti, és indítsa el a felhőalapú alkalmazásokat, amelyek az Azure AD-rendszergazda hozzáférést adott. 

- Önálló csoportok kezelése. Rendszergazdák létrehozhatnak és biztonsági csoportok kezelése és az Azure AD biztonsági csoporttagság kérése. További információkért lásd: [önkiszolgáló csoportkezelési a felhasználók számára az Azure AD](active-directory-accessmanagement-self-service-group-management.md) és [saját csoportjai kezelését](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>A hozzáférési panel

A hozzáférési panel megnyitásával érheti `http://myapps.microsoft.com`.

Ha a bejelentkezési lapon konfigurált egyéni branding, az URL-címet a szervezet tartományához hozzáfűzésével branding betöltheti (például `http://myapps.microsoft.com/<your domain>.com`).

Egyetlen aktív vagy ellenőrzött tartomány nevét az Azure-portálon beállított is használhatja, ahogy az itt látható:

![Wingtip Toys domain name][2]  

Az URL-cím minden felhasználó számára jelentkezzen be az Azure AD integrált alkalmazások terjesztése.

## <a name="authentication"></a>Hitelesítés

A hozzáférési panel elérését, keresztül a munkahelyi vagy iskolai fiókkal az Azure AD lehet hitelesíteni. Az Azure AD közvetlenül hitelesíthetők. Azt is megteheti Ha egy szervezet összevonási konfigurált Active Directory összevonási szolgáltatások (AD FS) vagy egyéb technológiák használatával, akkor hitelesítheti a Windows Server Active Directory.

Azure vagy Office 365-előfizetéssel rendelkezik, és az Azure-portálon vagy az Office 365 alkalmazást használja, ha az alkalmazások listáját megtekintheti a nélkül, jelentkezzen be újra. Ha a nem hitelesített kéri jelentkezzen be a felhasználónevet és jelszót a fiókhoz az Azure ad-ben. Ha a szervezet összevonási konfigurált, írja be a felhasználónevet is használhatók.

Amikor megtörténik, kezelheti az alkalmazásokat, amelyek a rendszergazda a könyvtárban van integrálva. Alkalmazások integrálása az Azure ad-vel kapcsolatban a [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

Legalább a hozzáférési panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezte. Aláírt alkalmazások jelszó-alapú egyszeri bejelentkezést (SSO) keresztül, a böngésző telepítve hozzáférési panel kiterjesztéssel kell rendelkeznie. A bővítmény le automatikusan, amikor kiválaszt egy alkalmazást, amely jelszóalapú SSO van konfigurálva.

A telepítő architektúra-specifikus. Ha a letöltés hivatkozásra kattint, csak kap a telepítő a jelenleg futó operációs rendszer architektúrájának. Ha egy alkalmazás központi telepítési rendszergazda, győződjön meg arról, hogy egy 64 bites és 32 bites eszközről mindkét telepítők beolvasandó látogasson el a letöltési hivatkozás.


A hozzáférési panel bővítmény érhető el jelenleg:
- **Peremhálózati**: a Windows 10 évforduló Edition vagy újabb. 
- **Chrome**: Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb.
- **Firefox 26.0 vagy újabb**: Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.
- **Internet Explorer 8, 9, 10, 11**: Windows 7 vagy újabb (korlátozottan támogatja).

## <a name="my-apps-secure-sign-in-extension"></a>Saját alkalmazások biztonságos bejelentkezési bővítménye
Jelentkezzen be a jelszó-alapú egyszeri bejelentkezést, a bővítmény kell használnia. A bővítmény telepítését követően bejelentkezhet hozzá kiválasztásával további funkciók engedélyezésére **jelentkezzen be a kezdéshez**. 

- Az alkalmazások közvetlenül az alkalmazás használatával bejelentkezhet **bejelentkezési URL-cím**. Ha az alkalmazás URL-CÍMÉT használja, a bővítmény észleli a művelet, és felajánlja a lehetőséget, a bővítmény bejelentkezéssel.
- A hozzáférési panelen igényelheti az alkalmazások bármelyikét használatával is elindíthatja a *Gyorskeresés* szolgáltatás a bővítményt. 
- A bővítmény elsajátíthatja, hogy az utolsó három alkalmazásokat, amelyek a indul el **legutóbbi** szakasz.

> [!NOTE]
> Csak a peremhálózati, Chrome és Firefox további funkciók érhetők el.
>

Használata saját alkalmazások URL-címe eltérő `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
1. Miközben az *nem* jelentkezve a bővítményt, kattintson a jobb gombbal bővítmény.
2. Válassza a menü **saját alkalmazások URL-cím**.
3. Válassza ki az alapértelmezett URL-cím.
4. Válassza ki a bővítmény ikonra.
5. Válassza ki **jelentkezzen be a kezdéshez**.

## <a name="mobile-app-support"></a>Mobilalkalmazás-támogatás

Az Azure Active Directory ügyfélszolgálata tesz közzé alkalmazásokat a mobilalkalmazás. Az alkalmazás telepítésekor regisztrálhat jelszó alapú egyszeri bejelentkezés alkalmazásokhoz iOS és Android-eszközök.

> [!NOTE]
> Bejelentkezhet az Azure ad-val (például Salesforce, Google Apps, Dropbox, mezőben, Concur, Workday, Office 365, és több mint 70 mások) összevonási támogató alkalmazások szinte bármilyen böngészőben, bármilyen eszközön, anélkül, hogy a beépülő modul vagy mobil alkalmazások. Egy mobileszközön, a másik használandó [hozzáférési panel lép](https://myapps.microsoft.com/) is nem igényelnek az alkalmazások a mobilalkalmazás.
>
>

### <a name="my-apps-for-android"></a>A saját Android-alkalmazások

A saját Android-alkalmazások bármely Android-eszközön, hogy fut az Android 4.1-es vagy újabb verzió esetén támogatott.  

Akkor érhető el, a [Google Play áruház](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![A saját Android-alkalmazások][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>IPhone és iPad alkalmazásaimat

Az iOS-alkalmazások támogatott bármely iPhone vagy iPad, amely 7 vagy újabb iOS-verziót futtat.  

Akkor érhető el, a [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Az iOS-alkalmazások][4]    


## <a name="managed-browser-for-my-apps"></a>Felügyelt böngésző saját alkalmazások

Az alkalmazások is integrálva van az Intune Managed Browser. Az Intune Managed Browser iOS és Android-eszközök segíti, győződjön meg arról, hogy a mobileszközök adatainak biztonságos marad kulcsfontosságú szerepet játszik. A böngésző lehetővé teszi, hogy biztonságosan megtekintheti, és keresse meg, amelyek a vállalati adatokat tartalmazhatnak, és segít a biztonságos webböngésző felhasználói élményt nyújtja.  

Kapott saját alkalmazások gyors elérését a Managed Browser kezdőlapon és a könyvjelzőt, így kevesebb rákattint az elérni kívánt alkalmazásokat eléréséhez szükséges.

Intune Managed Browser érhető el: a [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és [Google Play áruház](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Felügyelt böngésző saját alkalmazások][5]    


## <a name="tips-for-testing-the-user-experience"></a>A felhasználói élmény tesztelési tippek

Ha egy Azure rendszergazdát és jelentkezett be az Azure portálra egy olyan fiókkal a címtárban, automatikusan jelentkezett a hozzáférési panelre, a jelenlegi fiókkal. Ez a nézet megjeleníti az Önhöz rendelt összes alkalmazást.

A tesztelése egy *különböző* felhasználói fiókot, tegye a következőket:

1. A jobb felső sarkában az Azure-portálon vagy a hozzáférési panel, válassza **Kijelentkezés**. 
2. Lépjen a [hozzáférési panel](http://myapps.microsoft.com).
3. A bejelentkezési lapon írja be a felhasználónevet és a jelszót a fiókhoz a címtárában, amely a vizsgálni kívánt.


## <a name="starting-applications"></a>Alkalmazások elindítása

Ez a szakasz ismerteti számos különböző típusú alkalmazásokat, amelyek jelenhet meg a hozzáférési panel.

### <a name="office-365-applications"></a>Office 365-alkalmazások

Ha a szervezet által használt Office 365-alkalmazásokhoz, és rendelkezik licenccel, az Office 365-alkalmazások jelennek meg a hozzáférési panel.

Amikor kiválaszt egy alkalmazás csempe az Office 365-alkalmazáshoz, Ön átirányítja az alkalmazásba, és automatikusan megtörténik a.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Összevonási-alapú egyszeri bejelentkezési modellel konfigurált a Microsoft és külső alkalmazások

A rendszergazda adhat hozzá alkalmazásokat az Azure-portálon az Active Directory szakaszában az SSO módban **az Azure AD az egyszeri bejelentkezés**. Ezek az alkalmazások csak akkor, ha a rendszergazda explicit módon adott őket hozzáférést tekintheti meg.

Amikor kiválaszt egy alkalmazás egy csempe, Ön átirányítva, és automatikusan megtörténik a hozzá.

### <a name="password-based-sso-without-identity-provisioning"></a>Egyszeri bejelentkezés jelszó alapú identitás kiépítés nélkül

A rendszergazda adhat hozzá alkalmazásokat az Azure-portálon az Active Directory szakaszában az SSO módban **jelszó-alapú egyszeri bejelentkezést**. A címtárban szereplő összes felhasználó láthatja az összes olyan alkalmazások, amelyek ebben a módban vannak konfigurálva.

Egy alkalmazás csempe választja először kéri a jelszó SSO beépülő modul telepítése az Internet Explorer vagy a Chrome. A telepítés előfordulhat, hogy indítsa újra a webböngészőt. Ha a hozzáférési panelre való visszatéréshez, és válassza ki az alkalmazás csempe újra, a felhasználónevet és jelszót az alkalmazás kéri. A felhasználónevet és jelszót adta meg, ha a hitelesítő adatokat biztonságosan tárolja, és kapcsolódik ahhoz a fiókhoz az Azure AD.

A következő indításakor az alkalmazás csempe automatikusan jelentkezett az alkalmazáshoz.  

Írja be újra a hitelesítő adatait, és vagy a jelszó SSO beépülő modul telepítése nincs.

Ha a hitelesítő adatait a harmadik fél célalkalmazásban módosult, frissíteni kell az Azure AD-ben tárolt hitelesítő adatokat. 

A hitelesítő adatok frissítéséhez tegye a következőket:

1. Válassza ki az alkalmazás csempe a ikonra.
2. Válassza ki **hitelesítő adatainak frissítése** újbóli a felhasználónevet és jelszót az alkalmazáshoz.


### <a name="password-based-sso-with-identity-provisioning"></a>Jelszó-alapú egyszeri bejelentkezés identitás kiépítése

A rendszergazda adhat hozzá alkalmazásokat az Azure-portálon az Active Directory szakaszában az SSO módban **jelszó-alapú egyszeri bejelentkezést**, identitás-kiépítés együtt.

Egy alkalmazás csempe választja először kéri a jelszó SSO beépülő modul telepítése az Internet Explorer vagy a Chrome. A telepítés előfordulhat, hogy indítsa újra a webböngészőt.  

Ha a hozzáférési panelre való visszatéréshez, és válassza ki újra az alkalmazás csempe, automatikusan jelentkezett az alkalmazáshoz.

Egyes alkalmazások szükség lehet az első bejelentkezéskor a jelszó módosítása. Ha a hitelesítő adatait a harmadik fél célalkalmazásban módosult, frissíteni kell az Azure AD-ben tárolt hitelesítő adatokat. 

A hitelesítő adatok frissítéséhez tegye a következőket:

1. Válassza ki az alkalmazás csempe a ikonra.
2. Válassza ki **hitelesítő adatainak frissítése** újbóli a felhasználónevet és jelszót az alkalmazáshoz.


### <a name="application-with-existing-sso-solutions"></a>A már meglévő SSO megoldásaival alkalmazás

Egyszeri bejelentkezés konfigurálása az alkalmazáshoz, az Azure-portálon egy harmadik beállítás nevű meglévő egyszeri bejelentkezést biztosít. Ez a beállítás lehetővé teszi, hogy a rendszergazdát, hogy egy alkalmazás mutató hivatkozás létrehozásához, és helyezze el a kiválasztott felhasználók hozzáférési panelje.

Például ha egy alkalmazás a felhasználók hitelesítése az AD FS 2.0 használatával van konfigurálva, a rendszergazda a meglévő egyszeri bejelentkezés kapcsoló használatával hozható létre hivatkozás azt a hozzáférési panelen. A hivatkozás fér hozzá, amikor a rendszer hitelesíti az AD FS 2.0-s vagy bármilyen meglévő egyszeri bejelentkezési megoldással az alkalmazás maga biztosítja.


## <a name="next-steps"></a>További lépések

- Alkalmazáskezelés kapcsolódó témakörök listájának megtekintéséhez lásd: a [cikk index az Azure Active Directoryban Alkalmazáskezelés](active-directory-apps-index.md).
 
- A SaaS-alkalmazások integrálása az Azure ad-vel kapcsolatban a [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md).
 
- Alkalmazások kezelése az Azure AD kapcsolatos további tudnivalókért tekintse meg a [bemutatása az Azure Active Directoryval egyszeri bejelentkezés és kezelését alkalmazás eléréséhez](active-directory-appssoaccess-whatis.md).
 
- A felhasználók átadása kapcsolatos további információkért lásd: [automatizálhatja a felhasználó kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz való](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
