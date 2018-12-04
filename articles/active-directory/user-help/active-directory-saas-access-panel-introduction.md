---
title: Mi az a MyApps portálról az Azure Active Directoryban? | Microsoft Docs
description: Ismerje meg, hogyan használható a MyApps portálról (webböngésző, Android-alkalmazás, iPhone és iPad-alkalmazás) SaaS-alkalmazások eléréséhez változata.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.openlocfilehash: 129f77ec9ea4d4e2633e1ea0b02795bc0df26433
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834706"
---
# <a name="what-is-the-myapps-portal"></a>Mi az a MyApps portálról?

Ha rendelkezik munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), a saját alkalmazások webes portál segítségével megtekintheti, és indítsa el a felhőalapú alkalmazások, amelyek az Azure AD-rendszergazda engedélyezte a hozzáférést. Önkiszolgáló csoport- és alkalmazáskezelési lehetőségeket a MyApps portálon keresztül is használhatja.

A MyApps portálról elkülönül az Azure Portalon. Nem igényel, hogy rendelkezik Azure-előfizetéssel.

![MyApps portálról][1] a MyApps portálról használatával néhányat a profilbeállítások szerkesztése és tegye a következőket:

- Módosítsa a munkahelyi vagy iskolai fiókkal társított jelszót.

- Jelszó alaphelyzetbe állítása beállításainak szerkesztése.

- A multi-factor authentication (fiókok esetében, amely egy rendszergazda már szükség rá) kapcsolatos kapcsolattartási és szabályozó beállítások szerkesztése.

- Fiók részletes adatait, például a felhasználói Azonosítóját, alternatív e-mail-, mobil- és office telefonszámokat, és eszközök megtekintéséhez.

- Megtekintheti, és indítsa el a felhőalapú alkalmazások, amelyek az Azure AD-rendszergazda engedélyezte a hozzáférést. 

- Saját csoportok kezelése. Rendszergazdák létrehozhatnak és biztonsági csoportok kezelése és biztonsági csoporttagság kérése az Azure ad-ben. További információkért lásd: [az Azure AD-felhasználók önkiszolgáló csoportkezelési](../users-groups-roles/groups-self-service-management.md) és [kezelheti a csoportokat](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Hozzáférés a MyApps portálról

Hozzáférhet a MyApps portálról a `https://myapps.microsoft.com`.

Ha a bejelentkezési lapon konfigurált egyéni márkajelzési, az URL-címet a szervezet tartományához hozzáfűzésével márkajelzési betöltheti (például `https://myapps.microsoft.com/<your domain>.com`).

Minden aktív vagy ellenőrzött tartomány nevét az Azure Portalon konfigurált itt látható módon használható: ![Wingtip Toys címtárra tartománynév][2]  

Az URL-cím minden felhasználó számára jelentkezzen be az Azure ad-vel integrált alkalmazások terjesztése.

## <a name="authentication"></a>Hitelesítés

A MyApps portál eléréséhez, hitelesíteni kell egy munkahelyi vagy iskolai fiókon keresztül az Azure ad-ben. Az Azure AD közvetlenül hitelesíthető. Azt is megteheti Ha egy szervezet összevonási konfigurált Active Directory összevonási szolgáltatások (AD FS) vagy egyéb technológiák használatával, hogy hitelesíteni tudja a Windows Server Active Directory.

Ha már használja az Azure portal vagy egy Office 365-alkalmazást az Azure vagy Office 365-előfizetéssel rendelkezik, megtekintheti az elérhető alkalmazások listája, anélkül, hogy jelentkezzen be újra. Ha nem a hitelesítés kéri, jelentkezzen be a felhasználónevet és jelszót a fiókhoz tartozó Azure AD-ben. Ha munkahelyén konfigurálva van az összevonási, írja be a felhasználónevet is használhatók.

Ha a hitelesítés kezelheti az alkalmazásokat, amelyek a rendszergazda a könyvtárban van integrálva. Alkalmazások integrálása az Azure ad-vel kapcsolatban lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Webböngészőkre vonatkozó követelmények

Legalább a MyApps portálról kell egy böngészőben, amely támogatja a JavaScript és CSS engedélyezve van. Aláírt alkalmazások jelszóalapú egyszeri bejelentkezés (SSO) keresztül, telepítve van a böngészőben MyApps portál kiterjesztéssel kell rendelkeznie. Amikor kiválaszt egy alkalmazást, amely konfigurálva van a jelszóalapú egyszeri bejelentkezés automatikusan letöltődik a bővítményt.

A telepítő architektúra-specifikus. Ha a letöltési hivatkozásra kattint, a telepítő csak a jelenleg futó operációs rendszer architektúrájának megkapja. Ha egy alkalmazás központi telepítési rendszergazda, győződjön meg arról, egy 64 bites és 32 bites eszközről mindkét telepítők beolvasásához, látogasson el a letöltési hivatkozás.


A MyApps portálbővítmény szolgáltatás jelenleg érhető el:
- **Edge**: a Windows 10 Évfordulós kiadása vagy újabb. 
- **Chrome**: Windows 7-es vagy újabb, és a MacOS X rendszeren vagy újabb.
- **Firefox 26.0 vagy újabb**: a Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.
- **Az Internet Explorer 11**: Windows 7 vagy újabb (korlátozott támogatást).

## <a name="my-apps-secure-sign-in-extension"></a>Saját alkalmazások biztonságos bejelentkezési bővítménye
A jelszóalapú egyszeri bejelentkezés, a kiterjesztést kell használnia. A bővítmény telepítése után bejelentkezhet hozzá további funkciók engedélyezéséhez kiválasztásával **jelentkezzen be a kezdéshez**. 

- Az alkalmazás közvetlenül az alkalmazás használatával bejelentkezhet **bejelentkezési URL-**. Az alkalmazás URL-cím használata esetén a bővítményt a műveleteket észlel, és lehetővé teszi a jelentkezik be a bővítményt.
- A MyApps portálról alkalmazások bármelyike használatával is elindíthatja a *Gyorskeresés* a bővítmény szolgáltatása. 
- A bővítményt, utolsó három azokat az alkalmazásokat tartalmazza, amely akkor jelent **a legutóbb használt** szakaszban.
- Használhatja a belső vállalati URL-címek, miközben keresztül távoli [alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> További funkciók csak az Edge, Chrome és a Firefox érhetők el.
>
A bővítmény letöltheti közvetlenül a következő helyekről:
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [A Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Ha nem használ egy saját alkalmazások URL-cím `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
1. Amíg nem *nem* bejelentkezett a bővítményt, kattintson a bővítmény ikonra a jobb gombbal.
2. Válassza a menü **saját alkalmazások URL-cím**.
3. Válassza ki az alapértelmezett URL-címe.
4. A bővítmény ikonra.
5. Válassza ki **jelentkezzen be a kezdéshez**.

Távoli a bővítmény használata során, a belső vállalati URL-címek használatára, tegye a következőket:
1. [Proxy konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) a bérlő.
2. [Az alkalmazás közzététele](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) és proxyn keresztül történő alkalmazás URL-CÍMÉT.
3. Telepítse a bővítményt, és jelentkezzen be, válassza a bejelentkezés a kezdéshez.
4. Most megnyithatja a belső vállalati URL-cím távoli közben is.

> [!NOTE]
> Is kikapcsolhatja vállalati URL-címek automatikus átirányítás kiválasztásával a beállítások fogaskereket a főmenü **ki** a vállalati belső URL-cím átirányítása lehetőség.


## <a name="mobile-app-support"></a>Mobilalkalmazás-támogatás

Az Azure Active Directory-csapat tesz közzé a saját alkalmazások mobilalkalmazást. Amikor telepíti az alkalmazást, bejelentkezhet a jelszóalapú egyszeri bejelentkezési alkalmazások iOS és Android rendszerű eszközökön.

> [!NOTE]
> Bejelentkezhet az Azure ad-vel (beleértve a Salesforce, a Google Apps, a Dropbox, Box, beleértve, Workday, Office 365-höz és több mint 70 mások is) az összevonási támogató alkalmazások bármilyen eszközön, gyakorlatilag bármely webböngészőben anélkül, hogy a beépülő modul vagy a mobile app. Egy mobileszközön, a másik használandó [MyApps portálról élmény](https://myapps.microsoft.com/) nincs szükség a saját alkalmazások mobilalkalmazást is.

### <a name="my-apps-for-iphone-and-ipad"></a>Saját alkalmazások iPhone és iPad

Minden iPhone vagy iPad, 7 vagy újabb IOS-es verziója fut. saját IOS-alkalmazások esetén támogatott.  

Érhető el, a [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Saját IOS-alkalmazások][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Az Intune Managed Browser for saját alkalmazások

Saját alkalmazások is integrálva van az Intune Managed Browsert. Az Intune Managed Browser iOS és Android-eszközök segítségével biztonságosan megtekintheti, és keresse meg, amelyek a vállalati adatokat tartalmazhatnak, védi az egy biztonságosabb internetes böngészési élményt nyújt.  

Érheti el saját alkalmazások kezdőlapjáról is a Managed Browser és a könyvjelzők, ami azt jelenti, hogy az alkalmazások eléréséhez szükséges kevesebb kattintással.

Az Intune Managed Browser mindig elérhető legyen a [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) és [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Felügyelt böngésző saját alkalmazások][5]    


## <a name="tips-for-testing-the-user-experience"></a>Tippek a felhasználói élmény tesztelése

Ha Ön Azure-rendszergazdai és jelentkezett be az Azure Portalra egy olyan fiókkal a könyvtárban, akkor automatikusan bejelentkezett a MyApps portálról, a jelenlegi fiókkal. Ez a nézet megjeleníti az Önhöz rendelt összes alkalmazás.

Tesztelni, egy *különböző* felhasználói fiókot, tegye a következőket:

1. Az Azure portal vagy a MyApps portálról, jobb felső részén válassza ki a **Kijelentkezés**. 
2. Nyissa meg a [MyApps portálról](https://myapps.microsoft.com).
3. A bejelentkezési oldalon írja be a felhasználónevet és jelszót a fiókhoz a tesztelni kívánt címtárat.


## <a name="starting-applications"></a>Alkalmazások elindítása

Ez a szakasz ismerteti a számos különböző típusú alkalmazásokat, amelyek szerepelhetnek a MyApps portálon.

### <a name="office-365-applications"></a>Office 365-alkalmazások

Ha a szervezet az Office 365-alkalmazásokhoz, és rendelkezik licenccel, az Office 365-alkalmazások jelennek meg a MyApps portálról.

Amikor kiválaszt egy Office 365-alkalmazást egy alkalmazás csempéjére, Ön átirányítódik az alkalmazásba, és automatikusan jelentkezzenek be.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>A Microsoft és külső alkalmazások összevonási-alapú egyszeri bejelentkezés konfigurálva

A rendszergazda is hozzáadhat alkalmazásokat az Azure Portalon az Active Directory szakaszában az egyszeri bejelentkezés módban **az Azure AD egyszeri bejelentkezés**. Ezeket az alkalmazásokat láthatja, csak akkor, ha a rendszergazda explicit módon megadta, és áthidalja azokat.

Amikor kiválaszt egy csempét egy alkalmazást, Ön irányítja át, és automatikusan jelentkezzenek be azt.

### <a name="password-based-sso-without-identity-provisioning"></a>Jelszavas egyszeri bejelentkezési identitás üzembe helyezése nélkül

A rendszergazda is hozzáadhat alkalmazásokat az Azure Portalon az Active Directory szakaszában az egyszeri bejelentkezés módban **jelszóalapú egyszeri bejelentkezés**. A címtárban lévő összes felhasználó láthatja a minden alkalmazás, amely ebben a módban vannak konfigurálva.

Egy alkalmazás-csempét választja az első alkalommal kéri a jelszót SSO beépülő modul telepítéséhez az Internet Explorer vagy Chrome. A telepítés szükség lehet, hogy indítsa újra a webböngészőt. Térjen vissza a MyApps portálról, és válassza ki az alkalmazás csempe újra, ha a felhasználónév és jelszó az alkalmazás kéri. Ha megadta a felhasználónevét és jelszavát, a hitelesítő adatok biztonságosan tárolja és az Azure AD-fiókjához társított.

A következő alkalommal a alkalmazás csempét választja, automatikusan jelentkezett be az alkalmazáshoz.  

Nem kell újra adja meg hitelesítő adatait, és vagy a jelszó SSO beépülő modul telepítése.

Ha a hitelesítő adatok megváltoztak a célalkalmazásban külső, az Azure AD-ben tárolt hitelesítő adatok is frissítenie kell. 

A hitelesítő adatok frissítéséhez tegye a következőket:

1. Válassza ki az ikont az alkalmazás csempéjén.
2. Válassza ki **hitelesítő adatainak frissítése** felhasználónevét és jelszavát az alkalmazás ismételt megadásához.


### <a name="password-based-sso-with-identity-provisioning"></a>Jelszóalapú egyszeri bejelentkezés üzembe identitás

A rendszergazda is hozzáadhat alkalmazásokat az Azure Portalon az Active Directory szakaszában az egyszeri bejelentkezés módban **jelszóalapú egyszeri bejelentkezés**, identitás-kiépítés együtt.

Egy alkalmazás-csempét választja az első alkalommal kéri a jelszót SSO beépülő modul telepítéséhez az Internet Explorer vagy Chrome. A telepítés szükség lehet, hogy indítsa újra a webböngészőt.  

Amikor térjen vissza a MyApps portálról, és válassza ki az alkalmazás csempe újra, automatikusan bejelentkezett az alkalmazásba.

Egyes alkalmazások, az első bejelentkezéskor a jelszó módosítására lehet szükség. Ha a hitelesítő adatok megváltoztak a célalkalmazásban külső, az Azure AD-ben tárolt hitelesítő adatok is frissítenie kell. 

A hitelesítő adatok frissítéséhez tegye a következőket:

1. Válassza ki az ikont az alkalmazás csempéjén.
2. Válassza ki **hitelesítő adatainak frissítése** felhasználónevét és jelszavát az alkalmazás ismételt megadásához.


### <a name="application-with-existing-sso-solutions"></a>Alkalmazás a meglévő egyszeri bejelentkezés megoldásokkal

Egyszeri bejelentkezés konfigurálása az alkalmazáshoz, az Azure Portalra meglévő egyszeri bejelentkezés nevű harmadik lehetőséget kínál. Ez a beállítás lehetővé teszi, hogy a rendszergazdát, hogy hozzon létre egy hivatkozást egy alkalmazásba, és helyezze el a MyApps portálról a kiválasztott felhasználók számára.

Például ha egy alkalmazás a felhasználók hitelesítéséhez az AD FS 2.0 használatával van konfigurálva, a rendszergazda a meglévő egyszeri bejelentkezés beállítás segítségével hozhat létre ahhoz a MyApps portálon. A hivatkozás használatakor a hitelesítés az AD FS 2.0-s vagy bármely meglévő SSO-megoldáson keresztül az alkalmazás maga biztosítja.


## <a name="next-steps"></a>További lépések

- Az Alkalmazáskezelés kapcsolatos további információkért lásd: [alkalmazások kezelése az Azure Active Directoryban](../manage-apps/what-is-application-management.md).
 
- Ismerje meg, hogy egy SaaS-alkalmazás integrálása az Azure ad-vel, tekintse meg a [az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md).
 
- Alkalmazások kezelése az Azure ad-ben kapcsolatos további információkért tekintse meg a [bemutatása az Azure Active Directoryval egyszeri bejelentkezést és azzal való kezelésének app access](../manage-apps/what-is-single-sign-on.md).
 
- A felhasználók átadásának kapcsolatos további információkért lásd: [automatizálhatja a felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
