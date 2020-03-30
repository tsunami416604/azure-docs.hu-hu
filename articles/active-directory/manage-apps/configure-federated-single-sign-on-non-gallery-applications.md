---
title: Az összevont egyszeri bejelentkezés konfigurálása nem galériaalkalmazásokhoz
description: Az összevont egyszeri bejelentkezés konfigurálása egy egyéni, nem katalógusalkalmazáshoz, amelyet integrálni szeretne az Azure AD-vel
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274594"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Nem katalógusalkalmazások összevont egyszeri bejelentkezésének konfigurálása

Ha nem katalógusalkalmazáshoz szeretne egyszer bejelentkezést *beállítani kód írása nélkül,* előfizetéssel vagy Azure AD Premium szolgáltatással kell rendelkeznie, és az alkalmazásnak támogatnia kell az SAML 2.0-t. Az Azure AD-verziókról az [Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című webhelyen talál további információt.

## <a name="overview-of-steps-required"></a>A szükséges lépések áttekintése
Az alábbiakban egy magas szintű áttekintést a szükséges lépéseket konfigurálni összevont egyszeri bejelentkezés saml 2.0 egy nem katalógus (pl. egyéni) alkalmazás.

-   Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának lekérése](#download-the-azure-ad-metadata-or-certificate)

-   Azure AD metaadat-értékek konfigurálása az alkalmazásban (Bejelentkezés URL-cím, kiállító, kijelentkezési URL és tanúsítvány)

-   Felhasználók hozzárendelése az alkalmazáshoz

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Egyszeri bejelentkezés konfigurálása nem katalógusalkalmazásokba

Ha nem az Azure AD-gyűjteményben található alkalmazások egyszeri bejelentkezését szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6. Kattintson a **Nem galéria alkalmazás** elemre a Saját alkalmazás **hozzáadása** szakaszban

7. Írja be az alkalmazás nevét a **Név** mezőbe.

8. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

9. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

10. Válassza **az SAML-alapú bejelentkezés** lehetőséget a Mód legördülő **menüben.**

11. Adja meg a szükséges értékeket **a Tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás szállítójától kell beszereznie.

    1. Ha az alkalmazást IdP által kezdeményezett egyszeri szolgáltatóként szeretné konfigurálni, írja be a Válasz URL-címét és az azonosítót.

    2. **Nem kötelező:** Az alkalmazás SP által kezdeményezett egyszeri bejelentkezésként való konfigurálásához a bejelentkezési URL-cím kötelező érték.

12. A **Felhasználói attribútumok között**válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben.

13. **Nem kötelező:** kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** parancsra az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:

    1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. Kattintson **az&gt; Alkalmazásnév &lt;konfigurálása** elemre az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik Az Azure AD URL-címek és tanúsítvány szükséges az alkalmazáshoz.

15. [Felhasználók hozzárendelése az alkalmazáshoz.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. A **Felhasználói attribútumok** csoportban válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben. A kiválasztott beállításnak meg kell egyeznie az alkalmazás várt értékével a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Az Azure AD válassza ki a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el a cikk [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** gombra az alkalmazásnak az SAML-jogkivonatban elküldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

   2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

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

Az Azure AD is biztosít egy URL-t a metaadatok leéséhez. Kövesse ezt a mintát az alkalmazásra jellemző `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`metaadatok URL-címének bekésezéséhez: .

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. Kattintson a **Hozzáadás** gombra a **Felhasználók és csoportok** lista tetején a Hozzárendelés **hozzáadása** ablaktábla megnyitásához.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail címét,** akit szeretne hozzárendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni a felhasználót a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a megoldás leírásában a szakaszban leírt módszerekkel.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazásnak küldött SAML-jogcímek testreszabása

Az alkalmazásnak küldött SAML-attribútumjogcímek testreszabásáról további információt az Azure Active Directory ban található [Jogcímleképezés](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) című témakörben talál.

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)
