---
title: Problémák az összevont sso konfigurálásával az Azure AD Gallery-alkalmazásokhoz
description: Az Azure AD alkalmazásgalériában felsorolt alkalmazások SAML használatával történő egyszeri bejelentkezés konfigurálásakor felmerülő gyakori problémák kezelése
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
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274658"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Probléma az összevont egyszeri bejelentkezés konfigurálása egy Azure AD Gallery-alkalmazáshoz

Ha egy alkalmazás konfigurálása kor problémát tapasztal. Ellenőrizze, hogy követte-e az alkalmazás oktatóanyagának összes lépését. Az alkalmazás konfigurációjában rendelkezik az alkalmazás konfigurálásáról szóló szövegközi dokumentációval. Emellett részletesen részletes útmutatást is kaphat az [SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szóló oktatóanyagok listájához.

## <a name="cant-add-another-instance-of-the-application"></a>Nem lehet hozzáadni az alkalmazás másik példányát

Egy alkalmazás második példányának hozzáadásához a következőket kell tudnia:

-   A második példány egyedi azonosítójának konfigurálása. Nem fogja tudni konfigurálni ugyanazt az azonosítót, amelyet az első példányhoz használt.

-   Az első példánytól eltérő tanúsítvány konfigurálása.

Ha az alkalmazás nem támogatja a fentiek egyikét sem. Ezt követően nem fogja tudni konfigurálni a második példányt.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Nem lehet hozzáadni az azonosítót vagy a válasz URL-címét

Ha nem tudja konfigurálni az azonosítót vagy a válasz URL-címét, ellenőrizze, hogy az azonosító és a válasz URL-értéke megegyezik-e az alkalmazáshoz előre konfigurált mintákkal.

Az alkalmazáshoz előre konfigurált minták megismerése:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.** Folytassa a 7. Ha már az azure-as AD alkalmazáskonfigurációs panelen van.

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza **az SAML-alapú bejelentkezés** lehetőséget a **Mód** legördülő menüből.

9. Nyissa meg az **Azonosító** vagy **a Válasz URL-címének** szövegét a **Domain és URL-ek szakaszban.**

10. Az alkalmazás támogatott mintáinak megismerése három féleképpen ismertetheti:

    * A szövegdobozban a támogatott minta(oka)t helyőrzőként láthatja *Példaként:* <https://contoso.com>.

    * ha a minta nem támogatott, piros felkiáltójel jelenik meg, amikor megpróbálja beírni az értéket a szövegmezőbe. Ha az egérmutatót a piros felkiáltójel fölé viszi, megjelennek a támogatott minták.

    * Az alkalmazás oktatóanyagában a támogatott mintákról is kaphat információkat. Az **Azure AD egyszeri bejelentkezéskonfigurálása** szakaszban. Lépjen a tartomány és az **URL-címek** szakasz értékeinek konfigurálására szolgáló lépésre.

Ha az értékek nem egyeznek meg az Azure AD-n előre konfigurált mintákkal. A következőket teheti:

-   Az alkalmazás szállítójával együttműködve olyan értékeket kaphat le, amelyek megfelelnek az Azure AD-n előre konfigurált mintának

-   Vagy kapcsolatba léphet az Azure <aadapprequest@microsoft.com> AD csapatával, vagy megjegyzést fűzhet az oktatóanyaghoz, hogy kérje az alkalmazás támogatott mintáinak frissítését.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Hol állíthatom be az EntityID (felhasználói azonosító) formátumot?

Nem fogja tudni kiválasztani azt az Entitásazonosító (felhasználói azonosító) formátumot, amelyet az Azure AD a felhasználó hitelesítése után a válaszban küld az alkalmazásnak.

Az Azure AD válassza ki a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el az [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) című cikkbe a NameIDPolicy

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Nem találja az Azure AD metaadatait az alkalmazással való konfigurálás befejezéséhez

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Nyissa meg az **SAML aláíró tanúsítvány szakaszt,** majd kattintson **az Oszlopérték letöltése** parancsra. Attól függően, hogy az alkalmazás nak mi szükséges egyszeri bejelentkezés konfigurálásához, megjelenik a metaadat-XML vagy a tanúsítvány letöltésének lehetősége.

Az Azure AD nem ad meg URL-címet a metaadatok leéséhez. A metaadatok csak XML-fájlként olvashatók be.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudja, hogyan szabható testre az alkalmazásnak küldött SAML-jogcímek

Az alkalmazásnak küldött SAML-attribútumjogcímek testreszabásáról további információt az Azure Active Directory ban található [Jogcímleképezés](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) című témakörben talál.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
