---
title: Összevont egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazásokhoz | Microsoft Docs
description: Összevont egyszeri bejelentkezés konfigurálása meglévő Azure AD Gallery-alkalmazáshoz, valamint az oktatóanyagok használata a gyors kezdéshez
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
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207129"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazáshoz

Az Azure Active Directory (Azure AD) gyűjtemény összes olyan alkalmazásához, amely vállalati egyszeri bejelentkezési képességgel rendelkezik, lépésenkénti oktatóanyag érhető el. Az [útmutatók listáját az SaaS-alkalmazások Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) segítségével történő integrálásával érheti el részletes útmutatásért.

## <a name="overview-of-steps-required"></a>A szükséges lépések áttekintése
Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-katalógusból](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Felhasználók kiosztása az alkalmazáshoz](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Ha alkalmazást szeretne hozzáadni az Azure AD-katalógusból, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3.  Írja be a "Azure Active Directory" kifejezést a keresőmezőbe, és válassza a **Azure Active Directory**lehetőséget.

4.  Válassza ki a **vállalati alkalmazásokat** az Azure ad bal oldali navigációs menüjéből.

5.  A **vállalati alkalmazások** ablaktábla jobb felső sarkában válassza a **Hozzáadás** lehetőséget.

6.  A **Hozzáadás a** katalógusból szakasz **név megadása** mezőjébe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** mezőben.

9.  Az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

Rövid időn belül látnia kell az alkalmazás konfigurációs paneljét.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz az Azure AD-katalógusból

Az egyszeri bejelentkezés az alkalmazáshoz való konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3. Írja be a "Azure Active Directory" kifejezést a keresőmezőbe, és válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget a Azure Active Directory bal oldali navigációs menüjében.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   * Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után válassza ki az **egyszeri bejelentkezést** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza ki az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő listából.

9. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

   1. Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, akkor a bejelentkezési URL-cím a szükséges érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

   2. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként való konfigurálásához a válasz URL-címe kötelező érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

10. Nem **kötelező**: Ha meg szeretné tekinteni a nem szükséges értékeket, válassza a **speciális URL-beállítások megjelenítése** lehetőséget.

11. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

12. Nem **kötelező**: Válassza az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőséget, hogy szerkessze azokat az attribútumokat, amelyeket az alkalmazásnak az SAML-jogkivonatban kell elküldeni, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:
   
    1. Válassza az **attribútum hozzáadása**elemet. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    1. Válassza a **Mentés lehetőséget.** Megjelenik az új attribútum a táblán.

13. Válassza **az &lt;alkalmazásnév&gt; beállítása** lehetőséget, hogy hozzáférjen az alkalmazásban az egyszeri bejelentkezés konfigurálásához szükséges dokumentációhoz. Emellett rendelkezik a szükséges metaadat-URL-címekkel és tanúsítványokkal, amelyekkel beállíthatja az egyszeri bejelentkezést az alkalmazással.

14. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

15. Felhasználók kiosztása az alkalmazáshoz.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3. Írja be a "Azure Active Directory" kifejezést a keresőmezőbe, és válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget a Azure Active Directory bal oldali navigációs menüjében.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   * Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezéssel konfigurált alkalmazást.

7. Az alkalmazás betöltése után válassza ki az **egyszeri bejelentkezést** az alkalmazás bal oldali navigációs menüjéből.

8. A **felhasználói attribútumok** szakaszban válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából. A kiválasztott beállításnak meg kell egyeznie a várt értékkel az alkalmazásban a felhasználó hitelesítéséhez.

   >[!NOTE] 
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest által kért formátum alapján. További információ: az [egyszeri bejelentkezési SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.
   >
   >

9. Felhasználói attribútumok hozzáadásához válassza az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőséget, hogy szerkessze a felhasználók számára az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztését.

   Attribútum hozzáadása:
  
   1. Válassza az **attribútum hozzáadása**elemet. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

   2. Kattintson a **Mentés** gombra. Megjelenik az új attribútum a táblán.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3. Írja be a "Azure Active Directory" kifejezést a keresőmezőbe, és válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget a Azure Active Directory bal oldali navigációs menüjében.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   *  Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra**.

6. Válassza ki az egyszeri bejelentkezéssel konfigurált alkalmazást.

7. Az alkalmazás betöltése után válassza ki az **egyszeri bejelentkezést** az alkalmazás bal oldali navigációs menüjéből.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd válassza az oszlop értékének **letöltése** lehetőséget. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

Az Azure AD egy URL-címet is biztosít a metaadatok eléréséhez. Az alkalmazáshoz tartozó metaadatok URL-címének lekéréséhez használja a következő modellt:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa [](https://portal.azure.com/) meg a Azure Portalt, és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.

3. Írja be a "Azure Active Directory" kifejezést a keresőmezőbe, és válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget a Azure Active Directory bal oldali navigációs menüjében.

5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.

   * Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után válassza a **felhasználók és csoportok** lehetőséget az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9. Válassza ki a **felhasználók és csoportok** választót a **hozzárendelés hozzáadása** panelen.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail-címét** , amelyet a **Keresés név vagy e-mail-cím keresőmező alapján** szeretne hozzárendelni.

11. A **jelölőnégyzet**bejelöléséhez vigye a kurzort a listában szereplő **felhasználó** fölé. Jelölje be a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetet a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező**: Ha egynél **több felhasználót**szeretne felvenni, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** keresőmezőbe, és jelölje be a jelölőnégyzetet a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Ha végzett a felhasználók kiválasztásával, válassza a **kiválasztás** gombot, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező**: Válassza a **szerepkör** kiválasztása lehetőséget a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Válassza a **hozzárendelés** gombot az alkalmazás a kiválasztott felhasználókhoz való hozzárendeléséhez.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazásnak eljuttatott SAML-jogcímek testreszabása

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumokat, tekintse meg a jogcímek leképezése a [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)



