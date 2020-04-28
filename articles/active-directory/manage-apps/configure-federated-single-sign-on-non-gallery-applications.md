---
title: Összevont egyszeri bejelentkezés konfigurálása nem Gallery-alkalmazáshoz
description: Összevont egyszeri bejelentkezés konfigurálása egy olyan egyéni, nem katalógusbeli alkalmazáshoz, amelyet integrálni szeretne az Azure AD-vel
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
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274594"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása nem katalógusos alkalmazásokhoz

Ha az egyszeri bejelentkezést egy nem katalógusbeli alkalmazáshoz szeretné beállítani *kód írása nélkül*, előfizetéssel vagy prémium szintű Azure ad kell rendelkeznie, és az alkalmazásnak támogatnia kell az SAML 2,0-et. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el az [Azure ad díjszabására](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>A szükséges lépések áttekintése
Az alábbiakban áttekintheti azokat a lépéseket, amelyek szükségesek az összevont egyszeri bejelentkezés SAML 2,0-hez való konfigurálásához egy nem katalógusbeli (például egyéni) alkalmazáshoz.

-   Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)

-   Felhasználók hozzárendelése az alkalmazáshoz

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazásokhoz

Az Azure AD-katalógusban nem szereplő alkalmazásokra vonatkozó egyszeri bejelentkezés konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6. kattintson a **nem Gallery-alkalmazás** elemre a **saját alkalmazás hozzáadása** szakaszban

7. Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben.

8. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

9. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

10. Válassza az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő menüben.

11. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

    1. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként történő konfigurálásához adja meg a válasz URL-címét és az azonosítót.

    2. Nem **kötelező:** Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, akkor a bejelentkezési URL-cím a szükséges érték.

12. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

13. Nem **kötelező:** az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre kattintva szerkesztheti azokat az attribútumokat, amelyeket a rendszer az SAML-jogkivonatban fog elküldeni az alkalmazásba, amikor a felhasználók bejelentkeznek

    Attribútum hozzáadása:

    1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. kattintson **az &lt;alkalmazás nevének&gt; konfigurálása** elemre az alkalmazásban az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik az alkalmazáshoz szükséges Azure AD-URL-címekkel és tanúsítvánnyal.

15. [Felhasználók kiosztása az alkalmazáshoz.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

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

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

Az Azure AD egy URL-címet is biztosít a metaadatok beszerzéséhez. Kövesse ezt a mintát az alkalmazáshoz tartozó metaadatok URL-címének `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`lekéréséhez:.

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Egy vagy több felhasználó közvetlenül egy alkalmazáshoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni a listából.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9. a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail-címét** , **akit a keresés név vagy e-mail cím keresőmező alapján** szeretne hozzárendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában szereplő **felhasználó** fölé. Kattintson a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha **egynél több felhasználót szeretne felvenni**, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Ha befejezte a felhasználók kiválasztását, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt felhasználókhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek alapján.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazásnak eljuttatott SAML-jogcímek testreszabása

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumok jogcímeit, tekintse meg a következő témakört: [jogcím-hozzárendelés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
