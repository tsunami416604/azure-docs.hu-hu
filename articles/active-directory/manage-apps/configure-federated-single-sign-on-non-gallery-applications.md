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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
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

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

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

14. kattintson a **konfigurálás &lt;alkalmazás neve&gt;** lehetőségre az egyszeri bejelentkezés az alkalmazásban való konfigurálásának dokumentációjában. Emellett rendelkezik az alkalmazáshoz szükséges Azure AD-URL-címekkel és tanúsítvánnyal.

15. [Felhasználók kiosztása az alkalmazáshoz.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazáshoz küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

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

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

Az Azure AD egy URL-címet is biztosít a metaadatok beszerzéséhez. Kövesse ezt a mintát az alkalmazáshoz tartozó metaadatok URL-címének lekéréséhez: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Felhasználók hozzárendelése az alkalmazáshoz

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. Kattintson a **Hozzáadás** a gombot a **felhasználók és csoportok** listán, megnyílik a **hozzárendelés hozzáadása** ablaktáblán.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Után rövid idő alatt a kiválasztott felhasználók tudják elindítani ezeket az alkalmazásokat a megoldás leírása szakaszban leírt módszerek használatával.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Az alkalmazásnak eljuttatott SAML-jogcímek testreszabása

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumok jogcímeit, tekintse meg a következő témakört: [jogcím-hozzárendelés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Következő lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
