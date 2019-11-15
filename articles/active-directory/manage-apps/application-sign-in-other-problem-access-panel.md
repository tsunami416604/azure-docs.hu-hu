---
title: Problémák az alkalmazásba való bejelentkezéskor a hozzáférési panelen | Microsoft Docs
description: 'Alkalmazáshoz való hozzáférés hibáinak elhárítása a Microsoft Azure AD hozzáférési paneljén, a következő címen: myapps.microsoft.com'
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082162"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problémák az alkalmazásba való bejelentkezéskor a hozzáférési panelen

A hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy a felhasználó munkahelyi vagy iskolai fiókkal Azure Active Directory (Azure AD) megtekintse és indítsa el azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. 

Ezek az alkalmazások az Azure AD-portálon a felhasználó nevében konfigurálhatók. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelyhez a felhasználó tartozik, hogy megjelenjen az alkalmazás a hozzáférési panelen.

A felhasználók által megjelenített alkalmazások típusa a következő kategóriákba tartozhat:

-   Office 365-alkalmazások

-   Összevonási alapú egyszeri bejelentkezéssel konfigurált Microsoft és harmadik féltől származó alkalmazások

-   Jelszó-alapú SSO-alkalmazások

-   Meglévő SSO-megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez

-   Győződjön meg arról, hogy olyan **böngészőt** használ, amely megfelel a hozzáférési panel minimális követelményeinek.

-   Győződjön meg arról, hogy a felhasználó böngészőjében hozzá lett adva az alkalmazás URL-címe a **megbízható helyekhez**.

-   Ellenőrizze, hogy az alkalmazás megfelelően van-e **konfigurálva** .

-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezésekhez.

-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem felejtette el.**

-   Győződjön meg arról, hogy **multi-Factor Authentication** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **Identity Protection** -házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek multi-Factor Authentication vagy feltételes hozzáférési szabályzatok érvényesítéséhez.

-   Ügyeljen arra, hogy a böngésző cookie-jait is törölje, és próbálja meg újból bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A hozzáférési panel Meeting Browser követelményei

A hozzáférési panel egy olyan böngészőt igényel, amely támogatja a JavaScript használatát, és lehetővé teszi a CSS használatát. Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni a hozzáférési panelen, a hozzáférési panel kiterjesztését telepíteni kell a felhasználó böngészőjébe. A rendszer automatikusan letölti a bővítményt, ha a felhasználó egy jelszó alapú egyszeri bejelentkezéshez konfigurált alkalmazást választ ki.

Jelszó-alapú egyszeri bejelentkezés esetén a végfelhasználók böngészőjében a következő lehet:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Microsoft Edge a Windows 10 évfordulós kiadásán vagy később

-   Chrome – Windows 7 vagy újabb, illetve MacOS X vagy újabb rendszereken

-   Firefox 26,0 vagy újabb verzió – Windows XP SP2 vagy újabb rendszeren, valamint Mac OS X 10,6 vagy újabb rendszeren

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

A hozzáférési panel böngésző bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure ad-ben.

2.  Kattintson egy **jelszó-SSO-alkalmazásra** a hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngészője a letöltési hivatkozásra van átirányítva. **Adja hozzá** a bővítményt a böngészőjéhez.

5.  Ha a böngésző kéri, válassza ki a bővítmény **engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze, hogy **elindíthatja** -e a jelszó-SSO-alkalmazásokat

A Chrome és a Microsoft Edge bővítményét a lenti közvetlen hivatkozásokból is letöltheti:

-   [Chrome hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge hozzáférési panel bővítmény](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazáshoz

A vállalati egyszeri bejelentkezés funkcióval rendelkező Azure AD-katalógusban található összes alkalmazáshoz lépésenkénti oktatóanyag érhető el. A részletes útmutató lépéseit követve elérheti az [SaaS-alkalmazások Azure Active Directory segítségével történő integrálását ismertető oktatóanyagok listáját](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   Alkalmazás hozzáadása az Azure AD-katalógusból

-   [Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Felhasználók hozzárendelése az alkalmazáshoz

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakaszban a **név megadása** szövegmezőbe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** szövegmezőből.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz az Azure AD-katalógusból

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő listából.

9. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

   1. Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, a bejelentkezési URL-cím egy szükséges érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

   2. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként való konfigurálásához a válasz URL-címe egy szükséges érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

10. Nem **kötelező:** kattintson a **speciális URL-beállítások megjelenítése** elemre, ha meg szeretné tekinteni a nem szükséges értékeket.

11. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

12. Nem **kötelező:** az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre kattintva szerkesztheti azokat az attribútumokat, amelyeket a rendszer az SAML-jogkivonatban fog elküldeni az alkalmazásba, amikor a felhasználók bejelentkeznek

    Attribútum hozzáadása:

    1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

13. kattintson a **konfigurálás &lt;alkalmazás neve&gt;** lehetőségre az egyszeri bejelentkezés az alkalmazásban való konfigurálásának dokumentációjában. Emellett az egyszeri bejelentkezéshez szükséges metaadatok URL-címei és tanúsítványa is megadható az alkalmazáshoz.

14. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

15. Felhasználók kiosztása az alkalmazáshoz.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha nem látja az itt megjeleníteni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. A **felhasználói attribútumok** szakaszban válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából. A kiválasztott beállításnak meg kell egyeznie a várt értékkel az alkalmazásban a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre, hogy szerkessze a felhasználók által az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

   2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

   Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása nem katalógusos alkalmazásokhoz

Nem katalógusbeli alkalmazás konfigurálásához rendelkeznie kell az Azure AD Premium szolgáltatással, és az alkalmazás támogatja az SAML 2,0-et. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el az [Azure ad díjszabására](https://azure.microsoft.com/pricing/details/active-directory/).

-   Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)

Az Azure AD-katalógusban nem szereplő alkalmazásokra vonatkozó egyszeri bejelentkezés konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6. kattintson a **nem Gallery-alkalmazás** lehetőségre a **saját alkalmazás hozzáadása** szakaszban.

7. Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben.

8. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

9. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

10. **SAML-alapú bejelentkezés** kiválasztása a **mód** legördülő menüben

11. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

    1. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként történő konfigurálásához adja meg a válasz URL-címét és az azonosítót.

    2. Nem **kötelező:** Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, a bejelentkezési URL-cím egy szükséges érték.

12. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

13. Nem **kötelező:** az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre kattintva szerkesztheti azokat az attribútumokat, amelyeket a rendszer az SAML-jogkivonatban fog elküldeni az alkalmazásba, amikor a felhasználók bejelentkeznek

    Attribútum hozzáadása:

    1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. kattintson a **konfigurálás &lt;alkalmazás neve&gt;** lehetőségre az egyszeri bejelentkezés az alkalmazásban való konfigurálásának dokumentációjában. Emellett az alkalmazáshoz az Azure AD URL-címei és tanúsítványa szükséges.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. A **felhasználói attribútumok** szakaszban válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából. A kiválasztott beállításnak meg kell egyeznie a várt értékkel az alkalmazásban a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre, hogy szerkessze a felhasználók által az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. kattintson az **attribútum hozzáadása**gombra. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

   2 kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

   Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálása

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   Alkalmazás hozzáadása az Azure AD-katalógusból

-   Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakaszban a **név megadása** szövegmezőbe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezésre konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** szövegmezőből.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki a mód **jelszavas alapú bejelentkezését.**

9. Felhasználók kiosztása az alkalmazáshoz.

10. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusos alkalmazásokhoz

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Nem Gallery-alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Nem Gallery-alkalmazás hozzáadása

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  kattintson a **nem Gallery-alkalmazás** lehetőségre.

7.  Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki a mód **jelszavas alapú bejelentkezését.**

9. Adja meg a **bejelentkezési URL-címet**. Itt adhatja meg az URL-címet, ahol a felhasználók bejelentkeznek a felhasználónevével és jelszavával. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók kiosztása az alkalmazáshoz.

11. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen kiosztása egy alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem a probléma megoldására szolgálnak

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail-cím)

-   TenantID

-   Böngésző típusa

-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor

-   Hegedűs nyomkövetései

## <a name="next-steps"></a>Következő lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)

