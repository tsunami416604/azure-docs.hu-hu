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
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d40f67c354e29f3a58c9d0f1967e0154b5b998d0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-the-access-panel"></a>Mi az a hozzáférési panel?

A hozzáférési panel egy webes portál. Lehetővé teszi a felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directoryban megtekintéséhez, és indítsa el a felhőalapú alkalmazások az Azure AD-rendszergazda rendelkezik hozzáféréssel őket. Önkiszolgáló csoportkezelési és felügyeleti képességeit a hozzáférési panel keresztül is használható.

A hozzáférési panel elkülönül az Azure-portálon, és nem Ön az Azure-előfizetésre does.

![Hozzáférési panel][1]

A hozzáférés lehetővé teszi, hogy bizonyos a profil beállításait, például a szerkesztése:

- A munkahelyi vagy iskolai fiókhoz tartozó jelszó módosítása

- Jelszó alaphelyzetbe állítása beállításainak szerkesztése

- Többtényezős hitelesítés (a rendszergazda lett rá szükséges fiókok) kapcsolatos ügyfél és a beállításokat szabályozó beállítások szerkesztése

- A fiók adatait, például a felhasználói Azonosítóját, másodlagos e-mail, és a mobil- és office telefonszámokat, és az eszközök megtekintése

- Megtekintheti, és indítsa el a felhőalapú alkalmazásokat, amelyek az Azure AD-rendszergazda engedélyezte őket a hozzáférést. A hozzáférési panel a a felhasználók szempontjából kapcsolatos további információkért lásd: a hozzáférési panelen. 

- Önálló csoportok kezelése. Pontosabban, a rendszergazda létrehozhat és biztonsági csoportok kezelése és az Azure AD biztonsági csoporttagság kérése. További információkért lásd: [önkiszolgáló csoportkezelési a felhasználók számára az Azure AD](active-directory-accessmanagement-self-service-group-management.md) és [saját csoportjai kezelését](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>A hozzáférési panel elérése

A következő URL-címet egy webböngészőben felkeresésével érhető el a hozzáférési panel:`http://myapps.microsoft.com`

Ha a bejelentkezési lapon konfigurált egyéni védjegyek, betöltése a védjegyek, az URL-cím végén a szervezet tartományához hozzáfűzésével:`http://myapps.microsoft.com/<your domain>.com`

Ebben az esetben használhatja ki bármely aktív vagy ellenőrzött és érvényes tartománynevet, amely az Azure-portálon lett konfigurálva.

![A Wingtip Toys tartománynév][2]  

Minden felhasználó számára lesz jelentkezzen be az Azure AD integrált alkalmazások URL-címét kell.

## <a name="authentication"></a>Hitelesítés

A hozzáférési panel elérését, keresztül a munkahelyi vagy iskolai fiókkal az Azure AD lehet hitelesíteni. Az Azure AD közvetlenül hitelesíthetők. Azt is megteheti Ha egy szervezet összevonási konfigurált Active Directory összevonási szolgáltatások (AD FS) vagy egyéb technológiák használatával, akkor hitelesítheti a Windows Server Active Directory.

Az Azure vagy Office 365-előfizetéssel rendelkezik, és az Azure-portálon vagy az Office 365 alkalmazás használja, ha az alkalmazások listáját láthatja nélkül aláíró újra be. Ha Ön nem hitelesített jelentkezzen be a felhasználónevet és jelszót a fiókhoz az Azure AD kéri. Ha a szervezet összevonási konfigurált, írja be a felhasználónevet is használhatók.

Amikor megtörténik, kezelheti az alkalmazásokat, amelyek a rendszergazda a könyvtárban van integrálva. Alkalmazások integrálása az Azure ad-vel kapcsolatban a [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

Legalább a hozzáférési panel igényel, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezte. A felhasználó bejelentkezhet a jelszó-alapú egyszeri bejelentkezést (SSO) alkalmazások a böngésző a hozzáférési panel bővítményét kell telepíteni. A bővítmény le automatikusan, amikor kiválaszt egy alkalmazást, amely jelszóalapú SSO van konfigurálva.

A hozzáférési panel bővítmény érhető el jelenleg Internet Explorer 8 és újabb verziók, Firefox, biztonsági és Chrome böngésző.

## <a name="mobile-app-support"></a>Mobilalkalmazás-támogatás

Az Azure Active Directory ügyfélszolgálata közzéteszi az alkalmazásokat a mobilalkalmazás. Az alkalmazás telepítésekor regisztrálhat jelszó alapú egyszeri bejelentkezés alkalmazásokhoz iOS és Android-eszközök.

> [!NOTE]
> Bejelentkezhet az Azure ad-val (például Salesforce, Google Apps, Dropbox, mezőben, Concur, Workday, Office 365, és több mint 70 mások) összevonási támogató alkalmazások szinte bármilyen böngészőben, bármilyen eszközön, anélkül, hogy a beépülő modul vagy mobil alkalmazások. Minden más [hozzáférési panel lép](https://myapps.microsoft.com/) is nincs szükség a mobileszközökön használandó alkalmazásokat mobilalkalmazáson keresztül.
>
>

### <a name="my-apps-for-android"></a>A saját Android-alkalmazások

A saját Android-alkalmazások számítógépre Android 4.1 Android-verziót futtat és újabb rendszer.  
Elérhető a [Google Play áruház](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![A saját Android-alkalmazások][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>IPhone és iPad alkalmazásaimat

Az iOS-alkalmazások bármely iPhone vagy iPad futtató verziója iOS 7 és újabb verziók támogatják.  
Elérhető a [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Az iOS-alkalmazások][4]    



## <a name="managed-browser-for-my-apps"></a>Felügyelt böngésző saját alkalmazások

Az alkalmazások is integrálva van az Intune Managed Browser a. Az Intune Managed Browser iOS és Android-eszközök győződjön meg arról, hogy a mobileszközök adatainak biztonságos marad kulcsfontosságú szerepet játszik. Lehetővé teszi a biztonságosan megtekintheti, és keresse meg, amelyek a vállalati adatokat tartalmazhatnak, és biztonságos webböngésző élményt nyújt.  
Látni alkalmazásaimat gyors elérését a Managed Browser kezdőlap és a könyvjelzőt, így kevesebb kattint, bármely az elérni kívánt alkalmazás eléréséhez.

Elérhető a [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és [Google Play áruház](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Saját alkalmazások Mananged böngésző][5]    





## <a name="tips-for-testing-the-user-experience"></a>A felhasználói élmény tesztelési tippek

Ha egy Azure rendszergazdát és jelentkezett be az Azure portálra egy olyan fiókkal a címtárban, automatikusan jelentkezett a hozzáférési panelre, a jelenlegi fiókkal. Ebben az esetben tekintheti meg, hogy Önhöz rendelt összes alkalmazást.

**Tesztelésére, mint egy *különböző* felhasználói fiók:**

1. A felhasználó menü, az Azure-portálon vagy a hozzáférési panel jobb felső sarkában kattintson, majd **Kijelentkezés**. 
2. Lépjen a [hozzáférési panel](http://myapps.microsoft.com).
3. A bejelentkezési lapon írja be a felhasználónév és a fiók jelszavát a vizsgálni kívánt könyvtárban.


## <a name="starting-applications"></a>Alkalmazások elindítása

Számos különböző típusú alkalmazások is jelennek meg a hozzáférési panel.

### <a name="office-365-applications"></a>Office 365-alkalmazások

Ha a szervezet által használt Office 365-alkalmazásokhoz, és rendelkezik licenccel, az Office 365-alkalmazások jelennek meg a hozzáférési panel.

Ha egy alkalmazás csempe az Office 365 alkalmazás gombra kattint, Ön átirányítja az alkalmazásba, és automatikusan megtörténik a.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Összevonási-alapú egyszeri bejelentkezési modellel konfigurált a Microsoft és külső alkalmazások

A rendszergazda adhat hozzá alkalmazásokat az Azure-portálon az Active Directory szakaszában az SSO módban **az Azure AD az egyszeri bejelentkezés**. Csak megtekintheti ezeket az alkalmazásokat, ha a rendszergazda explicit módon adott az alkalmazások elérését.

Ezen alkalmazások egyikét mozaikokra kattintva Ön átirányítva, és automatikusan bejelentkeztetjük az alkalmazást.

### <a name="password-based-sso-without-identity-provisioning"></a>Egyszeri bejelentkezés jelszó alapú identitás kiépítés nélkül

A rendszergazda adhat hozzá alkalmazásokat az Azure-portálon az Active Directory szakaszában az SSO módban **jelszó-alapú egyszeri bejelentkezést**. A címtárban szereplő összes felhasználó láthatja az összes olyan alkalmazások, amelyek ebben a módban vannak konfigurálva.

Először, kattint egy ezeket az alkalmazásokat, egy csempe kéri a beépülő modul jelszó SSO telepítéséhez az Internet Explorer vagy a Chrome. A telepítés előfordulhat, hogy indítsa újra a webböngészőt. Ha a hozzáférési panelre való visszatéréshez, és kattintson az alkalmazás csempéjére újra, felhasználónevet és jelszót az alkalmazás kéri. Amikor a felhasználónevet és jelszót adta meg, ezek a hitelesítő adatok biztonságos tárolása és kapcsolódik ahhoz a fiókhoz az Azure AD.

A következő alkalommal alkalmazás csempére kattintva automatikusan jelentkezett az alkalmazáshoz.  
Írja be újra a hitelesítő adatait, és vagy a jelszó SSO beépülő modul telepítése nincs.

Ha a hitelesítő adatait a harmadik fél célalkalmazásban módosult, frissíteni kell az Azure AD-ben tárolt hitelesítő adatokat. 

**Hitelesítő adatainak frissítése:**

1. Válassza ki az alkalmazás csempe a ikonra.
2. Válassza ki **hitelesítő adatainak frissítése** újbóli a felhasználónevet és jelszót az alkalmazáshoz.


### <a name="password-based-sso-with-identity-provisioning"></a>Jelszó-alapú egyszeri bejelentkezés identitás kiépítése

A rendszergazda adhat hozzá alkalmazásokat a **Active Directory** az Azure-portálon az SSO módban szakasza **jelszó-alapú egyszeri bejelentkezést**, identitás kiépítés együtt.

Az első alkalommal kattint, az alkalmazás csempéjére a hozzá tartozó egyik ezeket az alkalmazásokat, telepítendő kéri a **jelszó SSO beépülő modul az Internet Explorer vagy a Chrome**. A telepítés előfordulhat, hogy indítsa újra a webböngészőt.  
Ha a hozzáférési panelre való visszatéréshez, és kattintson ismét az alkalmazás csempe, automatikusan jelentkezett az alkalmazáshoz.

Egyes alkalmazások szükség lehet az első bejelentkezés jelszó módosítása. Ha a hitelesítő adatait a harmadik fél célalkalmazásban módosult, frissíteni kell az Azure AD-ben tárolt hitelesítő adatokat. 

**Hitelesítő adatainak frissítése:**

1. Válassza ki az alkalmazás csempe a ikonra.
2. Válassza ki **hitelesítő adatainak frissítése** újbóli a felhasználónevet és jelszót az alkalmazáshoz.


### <a name="application-with-existing-sso-solutions"></a>A már meglévő SSO megoldásaival alkalmazás

Egyszeri bejelentkezés konfigurálása az alkalmazáshoz, az Azure-portálon biztosít a harmadik lehetőség nevű **meglévő egyszeri bejelentkezés**. Ez a beállítás lehetővé teszi, hogy a rendszergazdát, hogy egy alkalmazás mutató hivatkozás létrehozásához, és helyezze el a kiválasztott felhasználók hozzáférési panelje.

Például ha egy alkalmazás a felhasználók hitelesítése az AD FS 2.0 használatával van konfigurálva, a rendszergazda végezheti el a **meglévő egyszeri bejelentkezés** létrehozhat egy hivatkozásra a hozzáférési panelen. A hivatkozás fér hozzá, amikor a rendszer hitelesíti az AD FS 2.0-s vagy bármilyen meglévő egyszeri bejelentkezési megoldással az alkalmazás maga biztosítja.


## <a name="next-steps"></a>Következő lépések

- Az Alkalmazáskezelés kapcsolódó témakörök listáját, olvassa el a [cikk index az Azure Active Directoryban Alkalmazáskezelés](active-directory-apps-index.md).
 
- A Szolgáltatottszoftver-alkalmazás integrálja az Azure AD, lásd: a [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md).
 
- Alkalmazások kezelése az Azure AD kapcsolatos további tudnivalókért tekintse meg a [bemutatása az Azure Active Directoryval egyszeri bejelentkezés és kezelését alkalmazás eléréséhez](active-directory-appssoaccess-whatis.md).
 
- A felhasználók átadása kapcsolatos további információkért lásd: [automatizálhatja a felhasználó kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz való](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
