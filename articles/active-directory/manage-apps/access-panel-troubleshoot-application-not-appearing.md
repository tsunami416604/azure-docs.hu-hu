---
title: Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen | Microsoft Docs
description: A hozzáférési panelen nem jelenik meg az alkalmazások
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2018
ms.author: kenwith
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69f5196484b841e8f0de72ce52ae48e00963f6f5
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760995"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Egy hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen.

A hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy a felhasználó munkahelyi vagy iskolai fiókkal Azure Active Directory (Azure AD) megtekintse és indítsa el azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. Ezek az alkalmazások az Azure AD-portálon a felhasználó nevében konfigurálhatók. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelyhez a felhasználó tartozik, hogy megjelenjen az alkalmazás a hozzáférési panelen.

A felhasználók által megjelenített alkalmazások típusa a következő kategóriákba tartozhat:

-   Office 365-alkalmazások

-   Összevonási alapú egyszeri bejelentkezéssel konfigurált Microsoft és harmadik féltől származó alkalmazások

-   Jelszó-alapú SSO-alkalmazások

-   Meglévő SSO-megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez

-   Ha egy alkalmazás az imént lett hozzáadva egy felhasználóhoz, próbáljon meg újra bejelentkezni a felhasználó hozzáférési paneljére néhány perc múlva, és ellenőrizze, hogy az alkalmazás hozzá van-e adva.

-   Ha egy licencet eltávolítottak egy olyan felhasználótól vagy csoporttól, amely a felhasználó tagja, hosszú időt vehet igénybe, attól függően, hogy a csoport milyen mérettől és összetettségtől függ. A hozzáférési panelre való bejelentkezés előtt engedélyezze a további időt.

## <a name="problems-related-to-application-configuration"></a>Alkalmazás-konfigurációval kapcsolatos problémák

Előfordulhat, hogy egy alkalmazás nem jelenik meg a felhasználó hozzáférési paneljén, mert az nem megfelelően van konfigurálva. Az alábbi módokon végezheti el az alkalmazás-konfigurációval kapcsolatos problémák megoldását:

-   [Összevont egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazáshoz](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Összevont egyszeri bejelentkezés konfigurálása nem katalógusos alkalmazásokhoz](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jelszó egyszeri bejelentkezési alkalmazás konfigurálása Azure AD Gallery-alkalmazáshoz](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jelszó egyszeri bejelentkezési alkalmazás konfigurálása nem katalógusos alkalmazásokhoz](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása Azure AD Gallery-alkalmazáshoz

Az Azure AD-katalógusban minden olyan alkalmazás, amely a vállalati egyszeri bejelentkezés lehetőséggel van engedélyezve, lépésenkénti oktatóanyag érhető el. A részletes útmutató lépéseit követve elérheti az [SaaS-alkalmazások Azure Active Directory segítségével történő integrálását ismertető oktatóanyagok listáját](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-katalógusból](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakaszban a **név megadása** szövegmezőbe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** szövegmezőből.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz az Azure AD-katalógusból

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő listából.

9. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

   1. Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, akkor a bejelentkezési URL-cím a szükséges érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

   2. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként való konfigurálásához a válasz URL-címe kötelező érték. Egyes alkalmazások esetében az azonosító a szükséges érték is.

10. Nem **kötelező:** kattintson a **speciális URL-beállítások megjelenítése** elemre, ha meg szeretné tekinteni a nem szükséges értékeket.

11. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

12. Nem **kötelező:** az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre kattintva szerkesztheti azokat az attribútumokat, amelyeket a rendszer az SAML-jogkivonatban fog elküldeni az alkalmazásba, amikor a felhasználók bejelentkeznek

    Attribútum hozzáadása:

    1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    2. kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

13. kattintson **az &lt; alkalmazás nevének &gt; konfigurálása** elemre az alkalmazásban az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett a metaadatok URL-címei és tanúsítványa is szükséges az egyszeri bejelentkezés az alkalmazással történő beállításához.

14. a konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

15. Felhasználók kiosztása az alkalmazáshoz.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja az itt megjeleníteni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. A **felhasználói attribútumok** szakaszban válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából. A kiválasztott beállításnak meg kell egyeznie a várt értékkel az alkalmazásban a felhasználó hitelesítéséhez.

   >[!NOTE] 
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest által kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre, hogy szerkessze a felhasználók által az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

   2. kattintson a **Mentés gombra.** Ekkor megjelenik az új attribútum a táblában.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

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

   Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés konfigurálása nem katalógusos alkalmazásokhoz

Nem katalógusbeli alkalmazás konfigurálásához rendelkeznie kell az Azure AD Premium szolgáltatással, és az alkalmazás támogatja az SAML 2,0-et. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el az [Azure ad díjszabására](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának beolvasása](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadat-értékeinek konfigurálása az alkalmazásban (bejelentkezési URL-cím, kiállító, kijelentkezési URL-cím és tanúsítvány)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Az alkalmazás metaadatainak értékének konfigurálása az Azure AD-ben (bejelentkezési URL-cím, azonosító, válasz URL-cím)

Az Azure AD-katalógusban nem szereplő alkalmazásokra vonatkozó egyszeri bejelentkezés konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6. kattintson a **nem Gallery-alkalmazás** lehetőségre a **saját alkalmazás hozzáadása** szakaszban.

7. Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben.

8. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

9. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

10. Válassza az **SAML-alapú bejelentkezés** lehetőséget a **mód** legördülő menüben.

11. Adja meg a szükséges értékeket a **tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás gyártójától kaphatja meg.

    1. Az alkalmazás identitásszolgáltató által kezdeményezett SSO-ként történő konfigurálásához adja meg a válasz URL-címét és az azonosítót.

    2.  Nem **kötelező:** Ha az alkalmazást SP-ben kezdeményezett SSO-ként szeretné konfigurálni, akkor a bejelentkezési URL-cím a szükséges érték.

12. A **felhasználói attribútumok**területen válassza ki a felhasználók egyedi azonosítóját a **felhasználói azonosító** legördülő listából.

13. Nem **kötelező:** az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre kattintva szerkesztheti azokat az attribútumokat, amelyeket a rendszer az SAML-jogkivonatban fog elküldeni az alkalmazásba, amikor a felhasználók bejelentkeznek

    Attribútum hozzáadása:

    1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. kattintson **az &lt; alkalmazás nevének &gt; konfigurálása** elemre az alkalmazásban az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett az alkalmazáshoz az Azure AD URL-címei és tanúsítványai is szükségesek.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

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
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest által kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) a NameIDPolicy szakaszban.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson az **összes többi felhasználói attribútum megtekintése és szerkesztése** lehetőségre, hogy szerkessze a felhasználók által az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. kattintson az **attribútum hozzáadása**lehetőségre. Adja meg a **nevet** , és válassza ki az **értéket** a legördülő listából.

   2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

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

Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálása

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Alkalmazás hozzáadása az Azure AD-katalógusból](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-katalógusból

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  A **Hozzáadás a** katalógusból szakaszban a **név megadása** szövegmezőbe írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt módosíthatja a nevét a **név** szövegmezőből.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki a mód **jelszavas alapú bejelentkezését.**

9. [Felhasználók kiosztása az alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly).

10. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusos alkalmazásokhoz

Egy alkalmazás Azure AD-katalógusból való konfigurálásához a következőket kell tennie:

-   [Nem Gallery-alkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Nem Gallery-alkalmazás hozzáadása

Egy alkalmazás Azure AD-katalógusból való hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **Hozzáadás** gombra a **vállalati alkalmazások** ablaktábla jobb felső sarkában.

6.  kattintson a **nem Gallery-alkalmazás** lehetőségre.

7.  Adja meg az alkalmazás nevét a Name ( **név** ) szövegmezőben. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével megtekintheti az alkalmazás konfigurációs paneljét.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéséhez

Az alkalmazások egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5.  kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

    1.  Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6.  Válassza ki azt az alkalmazást, amelyhez az egyszeri bejelentkezést konfigurálni kívánja.

7.  Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a mód **jelszavas alapú bejelentkezését.**

9.  Adja meg a **bejelentkezési URL-címet**. Itt adhatja meg az URL-címet, ahol a felhasználók bejelentkeznek a felhasználónevével és jelszavával. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. [Felhasználók kiosztása az alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly).

11. Emellett a felhasználók nevében is megadhat hitelesítő adatokat. Ehhez válassza ki a felhasználókat, majd kattintson a **hitelesítő adatok frissítése** lehetőségre, és írja be a felhasználónevet és a jelszót a felhasználók nevében. Ellenkező esetben a rendszer a felhasználók számára az indításkor kéri a hitelesítő adatok megadását.

## <a name="problems-related-to-assigning-applications-to-users"></a>Alkalmazások felhasználókhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó nem lát egy alkalmazást a hozzáférési panelen, mert nincs hozzárendelve az alkalmazáshoz. Az alábbiakban néhány módszert láthat:

-   [Ellenőrizze, hogy van-e hozzárendelve felhasználó az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [Felhasználó közvetlen kiosztása egy alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly)

-   [Ellenőrizze, hogy van-e hozzárendelve egy felhasználó az alkalmazáshoz kapcsolódó licenccel](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Licenc kiosztása felhasználóhoz](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ellenőrizze, hogy van-e hozzárendelve felhasználó az alkalmazáshoz

Az alábbi lépéseket követve ellenőrizheti, hogy van-e hozzárendelve egy felhasználó az alkalmazáshoz:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

6. **Keresse** meg a kérdéses alkalmazás nevét.

7. kattintson **a felhasználók és csoportok**elemre.

8. Ellenőrizze, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz.

   * Ha nem, kövesse a "felhasználó kiosztása egy alkalmazáshoz közvetlenül" című témakörben leírt lépéseket.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen kiosztása egy alkalmazáshoz

Egy vagy több felhasználó közvetlenül egy alkalmazáshoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként**.

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

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a hozzáférési panelen.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Ellenőrizze, hogy a felhasználó rendelkezik-e az alkalmazáshoz kapcsolódó licenccel

A felhasználóhoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

   * Ha a felhasználó egy Office-licenchez van rendelve, ez lehetővé teszi, hogy az első fél Office-alkalmazásai megjelenjenek a felhasználó hozzáférési paneljén.

### <a name="how-to-assign-a-user-a-license"></a>Licencek felhasználóhoz rendelése 

Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden felhasználó**elemre.

6.  **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

8.  kattintson a **hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. A termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** **elemre** . Ha a művelet befejeződött, kattintson **az OK gombra** .

11. Kattintson a **hozzárendelés** gombra a licencek ehhez a felhasználóhoz való hozzárendeléséhez.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó egy alkalmazást lát a hozzáférési panelen, mert egy olyan csoport részét képezik, amely hozzá lett rendelve az alkalmazáshoz. Az alábbiakban néhány módszert láthat:

-   [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

-   [Alkalmazás közvetlen társítása egy csoporthoz](#how-to-assign-an-application-to-a-group-directly)

-   [Ellenőrizze, hogy a felhasználó tagja-e a licenchez rendelt csoportnak](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Licenc társítása egy csoporthoz](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Felhasználó csoport-tagságának keresése

A csoport tagságának vizsgálatához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson a **csoportok**elemre.

8. Ellenőrizze, hogy a felhasználó tagja-e az alkalmazáshoz rendelt csoportnak.

   * Ha el szeretné távolítani a felhasználót a csoportból, **kattintson a csoport sorára** , majd válassza a Törlés lehetőséget.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Alkalmazás közvetlen társítása egy csoporthoz

Egy vagy több csoport egy alkalmazáshoz való közvetlen hozzárendeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként**.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni a listából.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején a **hozzárendelés hozzáadása** ablaktábla megnyitásához.

9. a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a csoportnak a **teljes csoportjának nevét** , amelyet szeretne a **Keresés név vagy e-mail-cím** keresése mezőbe rendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában a **csoport** fölé. Kattintson a csoport profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha egynél **több csoportot szeretne felvenni**, írjon be egy másik **teljes nevet** a **Keresés név vagy e-mail-cím** keresőmezőbe, majd a jelölőnégyzetre kattintva vegye fel a csoportot a **kiválasztott** listára.

13. Ha befejezte a csoportok kijelölését, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott csoportokhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt csoportokhoz való hozzárendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók el tudják indítani ezeket az alkalmazásokat a hozzáférési panelen.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Ellenőrizze, hogy a felhasználó tagja-e a licenchez rendelt csoportnak

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson a **csoportok**elemre.

8. kattintson egy adott csoport sorára.

9. kattintson a **licencek** elemre, és ellenőrizze, hogy a csoport melyik licencekhez van rendelve.

   * Ha a csoport egy Office-licenchez van hozzárendelve, akkor ez lehetővé teheti, hogy bizonyos első féltől származó Office-alkalmazások megjelenjenek a felhasználó hozzáférési paneljén.

### <a name="how-to-assign-a-license-to-a-group"></a>Licenc társítása egy csoporthoz

Ha egy licencet szeretne hozzárendelni egy csoporthoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3.  Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4.  a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5.  kattintson **a minden csoport**elemre.

6.  **Keresse** meg az Önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  a **licencek** elemre kattintva megtekintheti, hogy mely licencek vannak hozzárendelve a csoporthoz.

8.  kattintson a **hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. A termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** **elemre** . Ha a művelet befejeződött, kattintson **az OK gombra** .

11. Kattintson a **hozzárendelés** gombra a licencek ehhez a csoporthoz való hozzárendeléséhez. Ez hosszú időt is igénybe vehet, a csoport méretétől és bonyolultságától függően.

>[!NOTE]
>Ennek gyorsabb elvégzéséhez érdemes lehet ideiglenesen hozzárendelni egy licencet a felhasználóhoz. 
>
>

## <a name="next-steps"></a>További lépések
[Új felhasználók hozzáadása az Azure Active Directoryhoz](./../fundamentals/add-users-azure-active-directory.md)

