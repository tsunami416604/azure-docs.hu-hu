---
title: "A probléma összevont egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs"
description: "Cím gyakori problémák jelentkezhetnek, ha egyetlen konfigurálása összevont bejelentkezést az alkalmazások az Azure AD Application Gallery szereplő SAML használatával"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 86ddb91ae16da797e32c6e1a8a8b2cd8fca989a8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>A probléma összevont egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása

Ha probléma merül fel, ha az alkalmazások konfigurálásáról. Ellenőrizze, hogy a lépéseket követte az oktatóanyag az alkalmazáshoz. Az alkalmazás konfigurációját, a beágyazott dokumentációjában az alkalmazás konfigurálásával kapcsolatos rendelkezik. Emellett érheti el a [SaaS-alkalmazásokhoz az Azure Active Directoryval integrációjával kapcsolatos bemutatók felsorolása](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) kapcsolatos részletek részletes útmutatás.

## <a name="cant-add-another-instance-of-the-application"></a>Az alkalmazás egy másik példánya nem vehető fel.

Adja hozzá az alkalmazás második példányát, kell tennie:

-   Konfigurálja a második példány egyedi azonosítója. Ön nem fog tudni konfigurálása az első példánynál használt ugyanezzel az azonosítóval.

-   Az első példánynál használttól eltérő tanúsítvány konfigurálása.

Ha az alkalmazás nem támogatja a fentiek közül. Ezt követően nem fogja tudni konfigurálni egy második példányt.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nem adható hozzá, az azonosító vagy a válasz URL-címe

Ha még nem sikerült konfigurálnia az azonosítóját vagy a válasz URL-CÍMEN, győződjön meg arról, a azonosítója és a válasz URL-CÍMEN az értékeknek egyezniük a minta az alkalmazás előre konfigurálva van.

Tudni, hogy az alkalmazás előre konfigurálva van a minták:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.** Ugorjon a 7. Ha már az alkalmazás konfigurációs panel az Azure ad-val.

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki **SAML-alapú bejelentkezés** a a **mód** legördülő menüből.

9.  Lépjen a **azonosítója** vagy **válasz URL-CÍMEN** szövegmező alatt a **tartomány és az URL-címeket.**

10. Háromféleképpen tudni, hogy az alkalmazás támogatott minták:

   * A szövegmezőhöz látni a támogatott példány szükséges helyőrzőként *példa:* <https://contoso.com>.

   * a minta nem támogatott, ha egy piros felkiáltójel megjelenik a szövegmezőben adja meg az érték megkísérlésekor. Ha az egérmutatóval rámutat a piros felkiáltójel, tekintse meg a támogatott kombinációját.

   * Az oktatóanyag az alkalmazáshoz a támogatott mintájával kapcsolatos információkért is kaphat. Az a **az Azure AD konfigurálása egyszeri bejelentkezéshez** szakasz. Folytassa a beállítva alatt az értékeket a **tartomány és az URL-címek** szakasz.

Ha az értékek nem egyeznek meg, a mintákat, előre konfigurált Azure ad-val. A következőket teheti:

-   Az alkalmazás, amely megfelel a mintának előre konfigurált Azure ad-val értékek szállítójával közösen

-   Másik lehetőségként felveheti a kapcsolatot az Azure AD-csapatának a <aadapprequest@microsoft.com> vagy megjegyzés hagyja az oktatóprogram kérése a támogatott minták az alkalmazás frissítése

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Ha állította be a entityid beállítást (felhasználói azonosító) formátumát

Ön nem fog tudni válassza ki, hogy az Azure AD elküldi az alkalmazásnak a válaszban szereplő felhasználók hitelesítése után entityid beállítást (felhasználói azonosító) formátumát.

Az Azure AD a NameID attribútum (felhasználói azonosító) formátumát a megadott érték alapján kijelölése, vagy a SAML AuthRequest az alkalmazás által kért formátuma. További információ látogasson el a [egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nem található a az alkalmazás konfigurálása az Azure AD metaadatok

Töltse le a metaadatokat vagy a tanúsítvány az Azure AD, kövesse az alábbi lépéseket:

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

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudjuk kérelmet küldött SAML-jogcímek testreszabása

Megtudhatja, hogyan szabhatja testre a SAML attribútum típusú jogcímek az alkalmazás számára, lásd: [hozzárendelése az Azure Active Directory-jogcímek](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](active-directory-enable-sso-scenario.md)
