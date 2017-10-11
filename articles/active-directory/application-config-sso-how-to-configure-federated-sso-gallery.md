---
title: "Összevont egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs"
description: "Összevont egyszeri bejelentkezéshez egy meglévő Azure AD-katalógusában és használati oktatóanyagok gyorsan induláshoz konfigurálása"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1b1d00718981b2c7d11f5b88428d02e16dd0b34d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása

Minden alkalmazást vállalati egyszeri bejelentkezésre alkalmas engedélyezve van az Azure AD-katalógus elérhető egy részletes oktatóanyag rendelkezik. Megnyitja a [SaaS-alkalmazásokhoz az Azure Active Directoryval integrációjával kapcsolatos bemutatók felsorolása](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) részletes, lépésenkénti útmutatást.

## <a name="overview-of-steps-required"></a>A szükséges lépések – áttekintés
Egy alkalmazás kell az Azure AD-galériából konfigurálása:

-   [Alkalmazás hozzáadása az Azure AD-galériából](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatai értékeinek konfigurálása az Azure AD (bejelentkezési URL-cím, az azonosítója, válasz URL-címe)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurálja az Azure AD metaadatok értékeket az alkalmazásban (bejelentkezési URL-címet, a kibocsátó, a kijelentkezési URL-cím és a tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás felhasználók hozzárendelése](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-galériából

Hozzáadhat egy alkalmazást az Azure AD-gyűjteményből, kövesse az alábbi lépéseket:

1.  Nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátornak**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** panelen.

6.  Az a **adjon meg egy nevet** a szövegmező a **hozzáadása a gyűjteményből** területen írja be az alkalmazás nevét.

7.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálni szeretné.

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmező.

9.  Kattintson a **Hozzáadás** gombra, vegye fel az alkalmazást.

Rövid időn belül el az alkalmazás konfigurációs panelen láthatók.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés egy alkalmazás az Azure AD-galériából konfigurálása

Egyszeri bejelentkezés egy alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **társadminisztrátor**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menüből.

9.  Adja meg a szükséges értékeket a **tartomány és az URL-címeket.** Ezeket az értékeket az alkalmazás gyártójának szerezheti be.

   1. Az alkalmazás beállítása a Szolgáltató által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-címen nem kötelező megadni. Egyes alkalmazások azonosító is kötelező megadni.

   2. Az alkalmazás beállítása a kiállító terjesztési hely által kezdeményezett egyszeri Bejelentkezést, a válasz URL-CÍMEN nem kötelező megadni. Egyes alkalmazások azonosító is kötelező megadni.

10. **Választható lehetőség:** kattintson **megjelenítése speciális URL-beállításainak** Ha meg szeretné tekinteni a nem szükséges értékeket.

11. Az a **felhasználói attribútumok**, válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből.

12. **Választható lehetőség:** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

  Attribútum hozzáadása:
   
   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   1. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

13. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés dokumentációjának egyszeri bejelentkezést az alkalmazás konfigurálásához. Is hogy rendelkezik, a metadata URL-címek és az egyszeri bejelentkezés beállítása az alkalmazáshoz szükséges tanúsítvány.

14. Kattintson a **mentése** a konfiguráció mentéséhez.

15. Felhasználók hozzárendelése az alkalmazáshoz.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Az a **felhasználói attribútumok** területen válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből. A kiválasztott beállítás a várt érték a felhasználó hitelesítésére az alkalmazásban egyezniük kell.

  >[!NOTE] 
  >Az Azure AD a NameID attribútum (felhasználói azonosító) formátumát a megadott érték alapján kijelölése, vagy a SAML AuthRequest az alkalmazás által kért formátuma. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
  >
  >

9.  Felhasználói attribútumok hozzáadásához kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a alkalmazását a SAML-jogkivonat felhasználói bejelentkezés attribútumok szerkesztéséhez.

   Attribútum hozzáadása:
  
   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **Save** (Mentés) gombra. Az új attribútumot a táblázatban látható.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az alkalmazás metaadatainak vagy tanúsítvány letöltéséhez az Azure AD, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  *  Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazás**.

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Ugrás a **SAML-aláíró tanúsítványa** területen, majd kattintson **letöltése** oszlop értékét. Attól függően, hogy milyen az alkalmazáshoz az szükséges, az egyszeri bejelentkezés konfigurálása lásd: a metaadatok XML-kód letöltése beállítás, vagy a tanúsítvány.

Az Azure AD nem biztosít a metaadatok beolvasása URL-CÍMÉT. A metaadatok XML-fájlként csak olvasható.

## <a name="assign-users-to-the-application"></a>Az alkalmazás felhasználók hozzárendelése

Hozzárendelése egy vagy több felhasználó alkalmazás közvetlenül, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** nyissa meg a listában a **hozzáadása hozzárendelés** panelen.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** panelen.

10. Írja be a **teljes név** vagy **e-mail cím** érdekli hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **felhasználói** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A felhasználói profil fénykép vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Választható lehetőség:** Ha azt szeretné, hogy **egynél több felhasználó hozzáadása**, egy másik típus **teljes név** vagy **e-mail cím** be a **Keresés név vagy e-mail cím alapján** mező, és a jelölőnégyzet bejelölésével adja hozzá a felhasználót, hogy a **kijelölt** listája.

13. Ha elkészült, válassza a felhasználók, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva a kijelölt felhasználók az alkalmazást.

Rövid időn belül a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban ismertetett módszerekkel.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>A kérelmet küldött SAML-jogcímek testreszabása

Megtudhatja, hogyan szabhatja testre a SAML attribútum típusú jogcímek az alkalmazás számára, lásd: [hozzárendelése az Azure Active Directory-jogcímek](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>Következő lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](active-directory-application-proxy-sso-using-kcd.md)



