---
title: Saml-alapú egyszeri bejelentkezés – Azure Active Directory hibakeresése | Microsoft dokumentumok
description: Saml-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazásokra.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154848"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Saml-alapú egyszeri bejelentkezés hibakeresése az Azure Active Directoryban lévő alkalmazásokba

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Megtudhatja, hogy miként keresheti meg és javíthatja ki az Azure Active Directoryban (Azure AD) a [security assertion markup language (SAML) 2.0-s](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language)azonosítónyelvet támogató alkalmazások [egyszeri bejelentkezési](../manage-apps/what-is-single-sign-on.md) problémáit. 

## <a name="before-you-begin"></a>Előkészületek

Javasoljuk a [My Apps Secure bejelentkezési bővítmény telepítését.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) Ez a böngészőbővítmény megkönnyíti az SAML-kérelem és az SAML-válaszinformációk összegyűjtését, amelyek az egyszeri bejelentkezéssel kapcsolatos problémák megoldásához szükséges. Abban az esetben, ha nem tudja telepíteni a bővítményt, ez a cikk bemutatja, hogyan oldhatja meg a problémákat a bővítmény telepítésével és anélkül is.

A My Apps Secure bejelentkezési bővítmény letöltéséhez és telepítéséhez használja az alábbi hivatkozások egyikét.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Saml-alapú egyszeri bejelentkezés tesztelése

SAML-alapú egyszeri bejelentkezés tesztelése az Azure AD és a célalkalmazás között:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként vagy más, az alkalmazások kezelésére jogosult rendszergazdaként.
1. A bal oldali panelen válassza az **Azure Active Directory**lehetőséget, majd válassza a Vállalati **alkalmazások**lehetőséget. 
1. A vállalati alkalmazások listájából válassza ki azt az alkalmazást, amelynek egyszeri bejelentkezését tesztelni szeretné, majd a bal oldali beállítások közül válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az SAML-alapú egyszeri bejelentkezéstesztelési élmény megnyitásához nyissa meg **az Egyszeri bejelentkezés tesztelése** (5. lépés) című lapot. Ha a **Teszt** gomb szürkén jelenik meg, először ki kell töltenie és mentenie kell a szükséges attribútumokat az **Egyszerű SAML konfiguráció szakaszban.**
1. Az **egyszeri bejelentkezés tesztelése** panelen a vállalati hitelesítő adatok segítségével jelentkezzen be a célalkalmazásba. Bejelentkezhet jelenlegi felhasználóként vagy más felhasználóként. Ha másik felhasználóként jelentkezik be, egy kérdés fogja kérni a hitelesítést.

    ![Képernyőkép a teszt SAML SSO lapjáról](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Ha sikeresen bejelentkezett, a teszt sikeres. Ebben az esetben az Azure AD kiadott egy SAML válaszjogkivonatot az alkalmazásnak. Az alkalmazás az SAML-jogkivonatot használta a sikeres bejelentkezéshez.

Ha hiba történt a vállalati bejelentkezési lapon vagy az alkalmazás oldalán, a hiba elhárításához használja a következő szakaszok egyikét.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Bejelentkezési hiba megoldása a vállalati bejelentkezési lapon

Amikor megpróbál bejelentkezni, előfordulhat, hogy a vállalati bejelentkezési oldalon a következő példához hasonló hibaüzenet jelenik meg.

![Példa a vállalati bejelentkezési lapon található hibára](./media/howto-v1-debug-saml-sso-issues/error.png)

A hiba hibakereséséhez a hibaüzenetre és az SAML-kérelemre van szükség. A My Apps Secure Bejelentkezési bővítmény automatikusan összegyűjti ezeket az információkat, és megjeleníti a felbontásra vonatkozó útmutatást az Azure AD-ről.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>A bejelentkezési hiba megoldása a Telepített My Apps Secure bejelentkezési bővítmény

1. Hiba esetén a bővítmény átirányítja az Azure AD **Test egyszeri bejelentkezési** panelre.
1. Az **Egyszeri bejelentkezés tesztelése** panelen válassza **az SAML-kérelem letöltése**lehetőséget.
1. A hiba és az SAML-kérelemben lévő értékek alapján konkrét megoldási útmutatásnak kell megtekintenie.
1. Megjelenik egy **Fix it** gomb, amely automatikusan frissíti a konfigurációt az Azure AD-ben a probléma megoldásához. Ha nem látja ezt a gombot, majd a bejelentkezési probléma nem az Azure AD helytelen konfigurációja miatt.

Ha a bejelentkezési hibához nem áll rendelkezésre megoldás, javasoljuk, hogy a visszajelzési szövegmező segítségével tájékoztasson minket.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>A hiba megoldása a My Apps Secure bejelentkezési bővítmény telepítése nélkül

1. Másolja a hibaüzenetet a lap jobb alsó sarkába. A hibaüzenet a következőket tartalmazza:
    - Korrelációs azonosító és időbélyeg. Ezek az értékek fontosak, ha támogatási esetet hoz létre a Microsofttal, mert segítenek a mérnököknek a probléma azonosításában és a probléma pontos megoldásában.
    - A probléma kiváltó okát azonosító utasítás.
1. Lépjen vissza az Azure AD-hez, és keresse meg az **egyszeri bejelentkezési** panel tesztelése.
1. A fenti szövegmezőben **A Megoldási útmutató**beillesztése a hibaüzenetet.
1. A **megoldási útmutató megjelenítéséhez** kattintson a Felbontásútmutató beszerezése gombra. Előfordulhat, hogy az útmutatáshoz az SAML-kérelemből vagy az SAML-válaszból származó információkra van szükség. Ha nem a My Apps Secure bejelentkezési bővítményt használja, szükség lehet egy eszközre, például a [Fiddlerre](https://www.telerik.com/fiddler) az SAML-kérelem és válasz lekéréséhez.
1. Ellenőrizze, hogy az SAML-kérelemben lévő cél megfelel-e az Azure AD-től kapott SAML egyszeri bejelentkezési szolgáltatás URL-címének.
1. Ellenőrizze, hogy a kibocsátó az SAML-kérelemben ugyanaz az azonosító, amelyet az azure-beli AD-ben konfigurált az alkalmazáshoz. Az Azure AD a kibocsátó segítségével keres egy alkalmazást a címtárban.
1. Ellenőrizze AssertionConsumerServiceURL, ahol az alkalmazás várhatóan megkapja az SAML-jogkivonatot az Azure AD-től. Ezt az értéket konfigurálhatja az Azure AD-ben, de nem kötelező, ha az SAML-kérelem része.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Bejelentkezési hiba megoldása az alkalmazáslapon

Előfordulhat, hogy sikeresen bejelentkezik, majd hibaüzenet jelenik meg az alkalmazás oldalán. Ez akkor fordul elő, amikor az Azure AD jogkivonatot adott ki az alkalmazásnak, de az alkalmazás nem fogadja el a választ.

A hiba elhárításához kövesse az alábbi lépéseket:

1. Ha az alkalmazás az Azure AD-galériában található, ellenőrizze, hogy az alkalmazás Azure AD-vel való integrálása minden lépést megtett-e. Az alkalmazás integrációs utasításainak megkereséséhez tekintse meg az [SaaS-alkalmazásintegrációs oktatóanyagok listáját.](../saas-apps/tutorial-list.md)
1. Az SAML válasz beolvasása.
    - Ha telepítve van a My Apps Secure bejelentkezésbővítmény, a **Teszt egyszeri bejelentkezés** panelről kattintson az **SAML válasz letöltése**gombra.
    - Ha a bővítmény nincs telepítve, használjon egy eszközt, például [a Fiddler-t](https://www.telerik.com/fiddler) az SAML-válasz lekéréséhez.
1. Figyelje meg ezeket az elemeket az SAML választokenben:
   - A NameID érték és formátum felhasználói egyedi azonosítója
   - A tokenben kiállított követelések
   - A jogkivonat aláírásához használt tanúsítvány.

     Az SAML-válaszról további információt az [Egyszeri bejelentkezéssaml protokoll ban talál.](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

1. Most, hogy áttekintette az SAML-választ, olvassa el a Hiba az alkalmazás lapján bejelentkezés után című [témakört,](../manage-apps/application-sign-in-problem-application-error.md) ahol útmutatást kaphat a probléma megoldásához. 
1. Ha továbbra sem tud sikeresen bejelentkezni, megkérdezheti az alkalmazás szállítójától, hogy mi hiányzik az SAML-válaszból.

## <a name="next-steps"></a>További lépések

Most, hogy az egyszeri bejelentkezés működik az alkalmazás, [automatizálhatja a felhasználók kiépítése és a de-kiépítés saas-alkalmazások,](../manage-apps/user-provisioning.md) vagy [a feltételes hozzáférés első lépések.](../conditional-access/app-based-conditional-access.md)
