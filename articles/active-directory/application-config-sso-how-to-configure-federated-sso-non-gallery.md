---
title: Összevont egyszeri bejelentkezés nem galéria alkalmazások konfigurálása |} Microsoft Docs
description: Összevont egyszeri bejelentkezés az Azure ad-vel integrálni kívánt egyéni nem galéria alkalmazások konfigurálása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 65df07b6fca62476953ad98dc9e17d9a82c97a53
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Egyszeri bejelentkezés nem galéria alkalmazások konfigurálása *kód írása nélkül*, telepíteni kell egy előfizetés vagy a prémium szintű Azure AD és az alkalmazásnak támogatnia kell a SAML 2.0-s. Az Azure AD-verziókkal kapcsolatos további információkért látogasson el a [az Azure AD árképzési](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>A szükséges lépések – áttekintés
Az alábbiakban az összevont egyszeri bejelentkezés konfigurálása a SAML 2.0 nem galéria (pl. egyéni) alkalmazáshoz szükséges lépések magas szintű áttekintését.

-   [Az alkalmazás metaadatai értékeinek konfigurálása az Azure AD (bejelentkezési URL-cím, az azonosítója, válasz URL-címe)](#_Configuring_single_sign-on)

-   [Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD-metaadatok és a tanúsítvány lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurálja az Azure AD metaadatok értékeket az alkalmazásban (bejelentkezési URL-címet, a kibocsátó, a kijelentkezési URL-cím és a tanúsítvány)](#_Configuring_single_sign-on)

-   [Az alkalmazás felhasználók hozzárendelése](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Egyszeri bejelentkezés egy alkalmazás, amely nincs az Azure AD-katalógus konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **Hozzáadás** gombra a jobb felső sarkában a **vállalati alkalmazások** ablaktáblán.

6.  Kattintson a **nem-gyűjtemény alkalmazás** a a **saját alkalmazás felvétele** szakasz

7.  Adja meg az alkalmazás nevét a **neve** szövegmező.

8.  Kattintson a **Hozzáadás** gombra, vegye fel az alkalmazást.

9.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

10. Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menüből.

11. Adja meg a szükséges értékeket a **tartomány és az URL-címeket.** Ezeket az értékeket az alkalmazás gyártójának szerezheti be.

   1. Az alkalmazás beállítása a kiállító terjesztési hely által kezdeményezett egyszeri Bejelentkezést, írja be a válasz URL-CÍMEN és azonosítóját.

   2. **Választható lehetőség:** SP által kezdeményezett egyszeri Bejelentkezést, a bejelentkezési URL-cím, az alkalmazás konfigurálásához szükséges érték.

12. Az a **felhasználói attribútumok**, válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből.

13. **Választható lehetőség:** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a SAML-jogkivonat az alkalmazásnak a felhasználók bejelentkeznek az attribútumok szerkesztése.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

14. Kattintson a **konfigurálása &lt;alkalmazásnév&gt;**  hozzáférés dokumentációjának egyszeri bejelentkezést az alkalmazás konfigurálásához. Is hogy rendelkezik, az Azure AD URL-címek és az alkalmazáshoz szükséges tanúsítvány.

15. [Felhasználók hozzárendelése az alkalmazáshoz.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítóját, és elküldi az alkalmazás felhasználói attribútumok hozzáadása

Válassza ki a felhasználói azonosító, vagy adja hozzá a felhasználói attribútumok, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Az a **felhasználói attribútumok** területen válassza ki a felhasználók egyedi azonosítója a **felhasználói azonosító** legördülő menüből. A kiválasztott beállítás a várt érték a felhasználó hitelesítésére az alkalmazásban egyezniük kell.

 >[! Megjegyzés:} az Azure AD kiválasztása a NameID attribútum (felhasználói azonosító) a kijelölt érték alapján vagy formátumban a SAML AuthRequest az alkalmazás által kért. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy szakaszban.
 >
 >

9.  Felhasználói attribútumok hozzáadásához kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** kell küldeni a SAML-jogkivonat az alkalmazásnak a felhasználók bejelentkeznek az attribútumok szerkesztése.

   Attribútum hozzáadása:

   1. Kattintson a **Hozzáadás attribútum**. Adja meg a **neve** majd válassza a **érték** a legördülő listából.

   2. Kattintson a **mentéséhez.** Az új attribútumot a táblázatban látható.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD-metaadatok vagy a tanúsítvány letöltése

Az alkalmazás metaadatainak vagy tanúsítvány letöltéséhez az Azure AD, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az alkalmazást, az egyszeri bejelentkezés konfigurálta.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Ugrás a **SAML-aláíró tanúsítványa** területen, majd kattintson **letöltése** oszlop értékét. Attól függően, hogy milyen az alkalmazáshoz az szükséges, az egyszeri bejelentkezés konfigurálása lásd: a metaadatok XML-kód letöltése beállítás, vagy a tanúsítvány.

Az Azure AD nem biztosít a metaadatok beolvasása URL-CÍMÉT. A metaadatok XML-fájlként csak olvasható.

## <a name="assign-users-to-the-application"></a>Az alkalmazás felhasználók hozzárendelése

Hozzárendelése egy vagy több felhasználó alkalmazás közvetlenül, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** nyissa meg a listában a **hozzáadása hozzárendelés** ablaktáblán.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail cím** érdekli hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **felhasználói** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A felhasználói profil fénykép vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Választható lehetőség:** Ha azt szeretné, hogy **egynél több felhasználó hozzáadása**, egy másik típus **teljes név** vagy **e-mail cím** azokat a **Keresés név vagy e-mail cím** mező, és a jelölőnégyzet bejelölésével adja hozzá a felhasználót, hogy a **kijelölt** listája.

13. Ha elkészült, válassza a felhasználók, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva a kijelölt felhasználók az alkalmazást.

Rövid időn belül a kijelölt felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban ismertetett módszerekkel.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>A kérelmet küldött SAML-jogcímek testreszabása

Megtudhatja, hogyan szabhatja testre a SAML attribútum típusú jogcímek az alkalmazás számára, lásd: [hozzárendelése az Azure Active Directory-jogcímek](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
