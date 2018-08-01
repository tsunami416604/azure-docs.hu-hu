---
title: Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazás konfigurálása |} A Microsoft Docs
description: Összevont egyszeri bejelentkezés egy meglévő Azure AD katalógusából származó alkalmazás és -felhasználási oktatóanyagok a gyors induláshoz konfigurálása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: e79aff1a9bf7a7d151b4973381d0ee9010c2255e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363852"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazás konfigurálása

Az Azure AD katalógusából származó engedélyezve van a vállalati egyszeri bejelentkezési képesség szereplő összes alkalmazás rendelkezik elérhető részletes oktatóanyaga. Elérheti a [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) a részletes, lépésenkénti útmutatóját.

## <a name="overview-of-steps-required"></a>Szükséges lépések áttekintése
Az Azure AD katalógusából származó kell az alkalmazás konfigurálása:

-   [Az Azure AD katalógusából származó alkalmazás hozzáadása](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Felhasználók hozzárendelése az alkalmazáshoz](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Az Azure AD katalógusából származó alkalmazás hozzáadása

Az Azure AD katalógusából származó alkalmazás hozzáadásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Az a **adjon meg egy nevet** a szövegmezőbe a **Hozzáadás a katalógusból** területén adja meg az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezést a konfigurálni kívánt alkalmazást.

8.  Ad hozzá az alkalmazást, mielőtt a nevét módosíthatja a **neve** szövegmezőbe.

9.  Kattintson a **Hozzáadás** gombjára, és vegye fel az alkalmazást.

Rövid idő után kell az alkalmazás konfigurációs ablaktáblája.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés az Azure AD katalógusából az alkalmazás konfigurálása

Egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **társadminisztrátor**.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** származó a **mód** legördülő listából.

9.  Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

   1. Az alkalmazás konfigurálásához az SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-t kötelező értéke. Egyes alkalmazások esetében az azonosító értéke is szükséges.

   2. Az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, a válasz URL-cím konfigurálása a szükséges érték. Egyes alkalmazások esetében az azonosító értéke is szükséges.

10. **Választható lehetőség:** kattintson **speciális URL-beállítások megjelenítése** Ha meg szeretné tekinteni a nem szükséges értékeket.

11. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

12. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

  Attribútum hozzáadása:
   
   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   1. Kattintson a **mentéséhez.** Láthatja, hogy a táblázatban az új attribútumot.

13. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy a metaadatok URL-címek és az egyszeri bejelentkezés beállítása az alkalmazáshoz szükséges tanúsítvány.

14. Kattintson a **mentése** a konfiguráció mentéséhez.

15. Felhasználók hozzárendelése az alkalmazáshoz.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Alatt a **felhasználói attribútumok** területen válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából. A kiválasztott beállítást meg kell felelnie a várt értékkel, az alkalmazás a felhasználó hitelesítéséhez.

  >[!NOTE] 
  >Kiválasztott Azure AD válassza ki a formátumot a NameID-attribútum (felhasználóazonosító) érték alapján, vagy formátumát az SAML AuthRequest az alkalmazás által kért. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
  >
  >

9.  Felhasználói attribútumok hozzáadásához kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:
  
   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **Save** (Mentés) gombra. Láthatja, hogy a táblázatban az új attribútumot.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  *  Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Alkalmazások**.

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **SAML-aláíró tanúsítvány** területen, majd kattintson a **letöltése** oszlop értékét. Attól függően, melyik az alkalmazás van szüksége, az egyszeri bejelentkezés konfigurálása láthatja az metaadatainak XML-fájl letöltése lehetőséget, vagy a tanúsítványt.

Azure ad-ben is biztosít a metaadatok beolvasása egy URL-címe. Ez a minta az alkalmazáshoz a metaadatok URL-Címének lekéréséhez kövesse: https://login.microsoftonline.com/ <Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>.

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7.  Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazás küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)



