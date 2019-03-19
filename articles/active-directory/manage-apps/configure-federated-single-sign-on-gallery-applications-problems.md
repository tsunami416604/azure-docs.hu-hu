---
title: Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazás konfigurálása során |} A Microsoft Docs
description: Cikksorozatnak a gyakori problémák jelentkezhetnek, ha konfigurálása összevont egyszeri bejelentkezéses SAML használatával az alkalmazásokat, amelyek szerepelnek az Azure AD Alkalmazáskatalógusában
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31e9746c0739a2ddd6267428f428e977151077b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099780"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Összevont egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazás konfigurálása során

Ha problémát tapasztal az alkalmazás konfigurálásakor. Ellenőrizze, hogy követte a lépéseket az oktatóanyag során az alkalmazáshoz. Az alkalmazást, a beágyazott dokumentáció az alkalmazás konfigurálásával van. Emellett hozzáférhet a [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) egy részletes részletes útmutatást.

## <a name="cant-add-another-instance-of-the-application"></a>Az alkalmazás egy másik példánya nem vehető fel.

Adjon hozzá egy második példányt egy alkalmazást, meg kell tudni:

-   Állítsa be a második példány egyedi azonosítója. Ön nem lehet konfigurálni az első példánynál használt ugyanezzel az azonosítóval.

-   Az első példánynál használttól eltérő tanúsítvány konfigurálása.

Ha az alkalmazás nem támogatja a fentiek közül. Ezt követően nem tudja konfigurálni a egy második példányt.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nem vehető fel az azonosítót vagy a válasz URL-címe

Ha Ön nem adhat meg az azonosítót vagy a válasz URL-cím, erősítse meg az azonosítót és a válasz URL-cím értékek megegyeznek az alkalmazás előre konfigurálva a minták.

Tudni, hogy az alkalmazás előre konfigurálva a minták:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.** Ugorjon a 7. Ha Ön már az alkalmazás konfigurálása panel az Azure ad-ben.

2. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7. Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8. Válassza ki **SAML-alapú bejelentkezés** származó a **mód** legördülő listából.

9. Nyissa meg a **azonosító** vagy **válasz URL-cím** szövegmező alatt a **tartomány és URL-címek szakaszt.**

10. Tudnivalók a támogatott minták az alkalmazás három módja van:

    * A szövegmező, megtekintheti a támogatott példány szükséges helyettesíti *példa:* <https://contoso.com>.

    * a mintázat nem támogatott, ha egy piros felkiáltójel láthatja a szövegmezőbe írja be az értéket megkísérlésekor. Ha az egérmutatóval rámutat a vörös felkiáltójelből, a támogatott minták jelenik meg.

    * Az oktatóanyag az alkalmazáshoz a támogatott minták kapcsolatos információkat is kaphat. Alatt a **konfigurálása az Azure AD egyszeri bejelentkezés** szakaszban. Az értékek alapján konfigurálva ugorjon a **tartomány és URL-címek** szakaszban.

Ha az értékek nem egyeznek meg az előre konfigurált Azure ad-ben a minták. A következőket teheti:

-   Az alkalmazás gyártójától használatával lekérjük az értékeket, amely megfelel a mintának, előre konfigurált Azure ad használata

-   Vagy felveheti a kapcsolatot az Azure AD csapata <aadapprequest@microsoft.com> vagy az oktatóanyagban kérni a frissítést az alkalmazás támogatott mintáit Megjegyzés írása

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Ahol állítsa be a EntityID (felhasználóazonosító) formátum

A EntityID (felhasználóazonosító) formátum, amely az Azure AD felhasználói hitelesítés után az alkalmazás a válaszban küld ki fogunk.

Kiválasztott Azure AD válassza ki a formátumot a NameID-attribútum (felhasználóazonosító) érték alapján, vagy formátumát az SAML AuthRequest az alkalmazás által kért. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy, területen

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Az Azure ad-ben metaadatok elvégezni a konfigurálást az alkalmazással nem található.

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7. Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8. Lépjen a **SAML-aláíró tanúsítvány** területen, majd kattintson a **letöltése** oszlop értékét. Attól függően, melyik az alkalmazás van szüksége, az egyszeri bejelentkezés konfigurálása láthatja az metaadatainak XML-fájl letöltése lehetőséget, vagy a tanúsítványt.

Az Azure AD nem biztosít a metaadatok beolvasása egy URL-címe. A metaadatok is csak olvasható be XML-fájlként.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudom, hogyan kérelmet küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
