---
title: Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása |} A Microsoft Docs
description: Összevont egyszeri bejelentkezés az Azure ad-vel integrálni kívánt egyéni katalógusban nem szereplő alkalmazás konfigurálása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.openlocfilehash: c4d064fcdc8af08058cfde3ea564005459236195
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960224"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása

Egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása *kódírás nélkül*, szüksége lesz egy előfizetést, vagy a prémium szintű Azure AD és az alkalmazás támogatnia kell a SAML 2.0. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el a [az Azure AD díjszabási](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Szükséges lépések áttekintése
Az alábbi, a katalógusban nem szereplő (pl. egyéni) alkalmazásokhoz SAML 2.0-val összevont egyszeri bejelentkezés konfigurálásához szükséges lépéseket magas szintű áttekintése.

-   [Az alkalmazás metaadatértékeket konfigurálása az Azure ad-ben (bejelentkezési URL-CÍMÉT, az azonosítója, válasz URL-cím)](#_Configuring_single_sign-on)

-   [Válassza ki a felhasználói azonosító és az alkalmazásnak küldendő felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekéréséhez](#download-the-azure-ad-metadata-or-certificate)

-   [Az Azure AD metaadatértékeket konfigurálása az alkalmazásban (bejelentkezési URL-címet, a kiállító, a kijelentkezési URL-címe és a tanúsítvány)](#_Configuring_single_sign-on)

-   Felhasználók hozzárendelése az alkalmazáshoz

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Egyszeri bejelentkezés nem katalógusból származó alkalmazások konfigurálása

Konfigurálása egyszeri bejelentkezéshez, amely nem szerepel az Azure AD katalógusából származó alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarokban lévő a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **katalógusban nem szereplő alkalmazás** a a **saját alkalmazás hozzáadása** szakasz

7.  Adja meg az alkalmazás nevét a **neve** szövegmezőbe.

8.  Kattintson a **Hozzáadás** gombjára, és vegye fel az alkalmazást.

9.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

10. Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő listából.

11. Adja meg a szükséges értékeket a **tartomány és URL-címeket.** Az alkalmazás gyártójának szerezheti be ezeket az értékeket.

   1. Az alkalmazás identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, konfigurálását, adja meg a válasz URL-cím és az azonosító.

   2. **Nem kötelező:** Az alkalmazás konfigurálásához az SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-t kötelező értéke.

12. Az a **felhasználói attribútumok**, válassza ki a felhasználók az egyedi azonosítója a **felhasználóazonosító** legördülő listából.

13. **Választható lehetőség:** kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** kell küldeni az alkalmazás SAML-jogkivonatban, amikor a felhasználók bejelentkeznek az attribútumok szerkesztését.

   Attribútum hozzáadása:

   1. Kattintson a **attribútum hozzáadása**. Adja meg a **neve** , és válassza a a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

14. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés konfigurálása egyszeri bejelentkezéshez, az alkalmazás dokumentációban. Is hogy rendelkezik, az Azure AD URL-címek és az alkalmazás számára szükséges tanúsítványt.

15. [Felhasználók hozzárendelése az alkalmazáshoz.](#assign-users-to-the-application)

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

   2. Kattintson a **mentéséhez.** Megjelenik az új attribútum a táblán.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

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

12. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazás küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
