---
title: OpenID/OAuth alkalmazás konfigurálása az Azure AD alkalmazásgalériából | Microsoft dokumentumok
description: Az OpenID/OAuth-alkalmazások konfigurálásának lépései az Azure AD alkalmazásgalériából.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8a2c962c69ead28c4e79b663010eab77a499f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048416"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>OpenID/OAuth alkalmazás konfigurálása az Azure AD alkalmazásgyűjteményből

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>OpenID alkalmazás hozzáadásának folyamata a galériából

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget. 

    ![Az Azure Active Directory gombja](common/select-azuread.png))

2. Nyissa meg **a Vállalati alkalmazások** > **Minden alkalmazás**.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. A párbeszédpanel tetején válassza az **Új alkalmazás** lehetőséget.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az alkalmazás nevét. Válassza ki a kívánt alkalmazást az eredménypanelről, és regisztráljon az alkalmazásra.

    ![Openid az eredménylistában](common/search-new-app.png)

    > [!NOTE]
    > Az OpenID Connect és az OAuth alkalmazások esetében a **Hozzáadás** gomb alapértelmezés szerint le van tiltva. Itt a bérlői rendszergazdának ki kell választania a regisztrációs gombot, és meg kell adnia az alkalmazáshoz való hozzájárulást. Az alkalmazás ezután hozzáadódik az ügyfél-bérlőhöz, ahol elvégezheti a konfigurációkat. Nincs szükség az alkalmazás explicit hozzáadására.

    ![Hozzáadás gomb](./media/openidoauth-tutorial/addbutton.png)

5. Amikor kiválasztja a regisztrációs kapcsolatot, a bejelentkezési hitelesítő adatok az Azure Active Directory (Azure AD) lapra lesz irányítva.

6. A sikeres hitelesítés tkövetően elfogadja a hozzájárulási oldal hozzájárulását. Ezt követően megjelenik az alkalmazás kezdőlapja.

    > [!NOTE]
    > Az alkalmazásnak csak egy példányát veheti fel. Ha már hozzáadott egyet, és megpróbálta újra megadni a hozzájárulást, akkor nem lesz újra hozzáadva a bérlőhöz. Így logikusan csak egy alkalmazáspéldányt használhat a bérlőben.

## <a name="authentication-flow-using-openid-connect"></a>Hitelesítési folyamat az OpenID Connect használatával

A legalapvetőbb bejelentkezési folyamat a következő lépéseket tartalmazza:

![Hitelesítési folyamat az OpenID Connect használatával](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Több-bérlős alkalmazás 
A több-bérlős alkalmazás számos szervezetben használható, nem csak egy szervezetben. Ezek általában egy független szoftvergyártó (ISV) által írt szoftver-szolgáltatásként (SaaS) alkalmazások. 

Több-bérlős alkalmazásokat kell kiépíteni minden könyvtárban, ahol használni fogják őket. A regisztrációhoz felhasználói vagy rendszergazdai hozzájárulás szükséges. Ez a jóváhagyási folyamat akkor kezdődik, ha egy alkalmazás regisztrálva van a címtárban, és hozzáférést kap a Graph API-hoz vagy esetleg egy másik webes API-hoz. Ha egy másik szervezet felhasználója vagy rendszergazdája regisztrál az alkalmazás használatára, egy párbeszédpanelen láthatók az alkalmazás igényeinek engedélyei. 

A felhasználó vagy a rendszergazda ezután beleegyezhet az alkalmazásba. A hozzájárulás hozzáférést biztosít az alkalmazásnak a megadott adatokhoz, és végül regisztrálja az alkalmazást a címtárban.

> [!NOTE]
> Ha az alkalmazást több könyvtárban lévő felhasználók számára elérhetővé teszi, szüksége van egy mechanizmusra annak meghatározásához, hogy melyik bérlőben vannak. Egy egybérlős alkalmazás csak meg kell keresni a saját könyvtárban a felhasználó számára. Egy több-bérlős alkalmazás nak azonosítania kell egy adott felhasználót az Azure AD-ben lévő összes könyvtárból.
> 
> A feladat végrehajtásához az Azure AD egy közös hitelesítési végpontot biztosít, ahol bármely több-bérlős alkalmazás közvetlen bejelentkezési kérelmeket, ahelyett, hogy egy bérlő-specifikus végpont. Ez a `https://login.microsoftonline.com/common` végpont az Azure AD összes könyvtárára. A bérlő-specifikus végpont `https://login.microsoftonline.com/contoso.onmicrosoft.com`lehet. 
>
> A közös végpont fontos, hogy fontolja meg, amikor az alkalmazás fejlesztése. A bejelentkezés, a kijelentkezés és a jogkivonat-érvényesítés során több bérlő kezeléséhez szükséges logikára van szükség.

Alapértelmezés szerint az Azure AD több-bérlős alkalmazásokat léptet elő. A szervezeten belül könnyen elérhetők, és a hozzájárulás elfogadása után könnyen használhatók.

## <a name="consent-framework"></a>Jóváhagyási keretrendszer

Az Azure AD-hozzájárulási keretrendszer segítségével több-bérlős webes és natív ügyfélalkalmazások at fejleszthet. Ezek az alkalmazások lehetővé teszik a bejelentkezést egy Azure AD-bérlőfelhasználói fiókjai által, eltérve attól, ahol az alkalmazás regisztrálva van. Előfordulhat, hogy webes API-kat is el kell érniük, például:
- A Microsoft Graph API, az Azure AD, az Intune és a szolgáltatások eléréséhez az Office 365-ben. 
- A Microsoft egyéb szolgáltatásainak API-i.
- Saját webes API-k. 

A keretrendszer olyan felhasználón vagy rendszergazdaon alapul, aki hozzájárul egy olyan alkalmazáshoz, amely a címtárban való regisztrációt kéri. A regisztráció magában foglalhatja a címtáradatok elérését. A hozzájárulás után az ügyfélalkalmazás felhívhatja a Microsoft Graph API-t a felhasználó nevében, és szükség szerint használhatja az adatokat.

A [Microsoft Graph API](https://developer.microsoft.com/graph/) hozzáférést biztosít az Office 365-ben lévő adatokhoz, például:

- Naptárak és üzenetek az Exchange-ből.
- Webhelyek és listák a SharePointból.
- Dokumentumok a OneDrive-ról.
- Jegyzetfüzetek a OneNote-ból.
- Feladatok a Plannerből.
- Munkafüzetek az Excelből.

A Graph API is hozzáférést biztosít a felhasználók és csoportok az Azure AD és más adatobjektumok több Microsoft felhőszolgáltatások.

A következő lépések bemutatják, hogyan működik a hozzájárulási élmény az alkalmazás fejlesztője és felhasználója számára:

1. Tegyük fel, hogy rendelkezik egy webügyfél-alkalmazással, amelynek meghatározott engedélyeket kell kérnie egy erőforrás vagy API eléréséhez. Az Azure Portal konfigurációs időben az engedélykérelmek deklarálására szolgál. Más konfigurációs beállításokhoz hasonlóan az alkalmazás Azure AD-regisztrációinak részévé válnak. Az Engedélykérelem elérési útja esetén kövesse az alábbi lépéseket:

    a. Kattintson az **alkalmazás regisztrációk** a bal oldalon a menü, és nyissa meg az alkalmazást beírja az alkalmazás nevét a keresőmezőbe.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Kattintson **az API-engedélyek megtekintése gombra.**

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Kattintson az **Engedély hozzáadása gombra.**

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Kattintson a **Microsoft Graph programra.**

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Válassza ki a szükséges beállításokat a **Delegált engedélyek** és **az alkalmazásengedélyek közül.**

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vegye figyelembe, hogy az alkalmazás engedélyei frissültek. Az alkalmazás fut, és a felhasználó arról szól, hogy használja az első alkalommal. Először az alkalmazás nak be kell szereznie egy engedélyezési kódot az Azure AD /authorize endpoint. Az engedélyezési kód ezután egy új hozzáférési és frissítési jogkivonat megszerzéséhez használható.

3. Ha a felhasználó még nincs hitelesítve, az Azure AD /authorize endpoint kéri a bejelentkezést.

    ![Hitelesítés](./media/openidoauth-tutorial/authentication.png)

4. Miután a felhasználó bejelentkezett, az Azure AD határozza meg, hogy a felhasználónak meg kell-e jelennie egy jóváhagyási oldal. Ez a meghatározás azon alapul, hogy a felhasználó (vagy a szervezet rendszergazdája) már megadta-e az alkalmazás beleegyezését.

   Ha a hozzájárulás nem lett megadva, az Azure AD kéri a felhasználó beleegyezését, és megjeleníti a szükséges engedélyeket, amelyek működéséhez szükséges. A hozzájárulási párbeszédpanelen megjelenő engedélyek megegyeznek az Azure Portalon a delegált engedélyekben kiválasztott engedélyekkel.

    ![Hozzájárulási oldal](./media/openidoauth-tutorial/consentpage.png)

A rendszeres felhasználók beleegyezhetnek bizonyos engedélyekbe. Más engedélyek hez bérlői rendszergazda hozzájárulása szükséges.

## <a name="difference-between-admin-consent-and-user-consent"></a>Az adminisztrátorés a felhasználó hozzájárulása közötti különbség

Rendszergazdaként is hozzájárulhat egy alkalmazás delegált engedélyeihez a bérlő összes felhasználója nevében. A rendszergazdai hozzájárulás megakadályozza, hogy a hozzájárulási párbeszédpanel a bérlő minden felhasználója számára megjelenjen. A rendszergazdai szerepkört használó felhasználók beleegyezést adhatnak az Azure Portalon. Az alkalmazás **Beállítások** lapján válassza a **Szükséges engedélyek** > **megadása rendszergazdai jóváhagyás lehetőséget.**

![Engedélyek megadása gomb](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Az ADAL.js-t használó egyoldalas alkalmazások (SpA-k) mostantól kifejezett hozzájárulás megadása a **Grant admin consent** button használatával. Ellenkező esetben az alkalmazás sikertelen lesz, ha a hozzáférési jogkivonatot kéri.

Csak alkalmazás engedélyek mindig bérlői rendszergazda beleegyezését igényli. Ha az alkalmazás csak alkalmazásengedélyt kér, és egy felhasználó megpróbál bejelentkezni az alkalmazásba, hibaüzenet jelenik meg. Az üzenet szerint a felhasználó nem tud hozzájárulni.

Ha az alkalmazás rendszergazdai jóváhagyást igénylő engedélyeket használ, rendelkeznie kell egy kézmozdulattal, például egy gombbal vagy hivatkozással, ahol a rendszergazda elindíthatja a műveletet. A kérelem, amelyet az alkalmazás küld erre a műveletre a szokásos OAuth2/OpenID Connect engedélyezési kérelem. Ez a kérés tartalmazza a *prompt=admin_consent* lekérdezési karakterlánc-paramétert. 

Miután a rendszergazda hozzájárult, és a szolgáltatás névadója jön létre az ügyfél bérlője, a későbbi bejelentkezési kérelmek nem kell a *prompt=admin_consent* paraméter. Mivel a rendszergazda úgy döntött, hogy a kért engedélyek elfogadhatók, a bérlő más felhasználóitól attól a ponttól kezdve nem kér jóváhagyást.

A bérlői rendszergazda letilthatja a rendszeres felhasználók számára az alkalmazások beleegyezését. Ha ez a funkció le van tiltva, rendszergazdai hozzájárulás mindig szükséges az alkalmazás a bérlőben való használathoz. Ha azt szeretné, hogy tesztelje az alkalmazást a végfelhasználói hozzájárulás letiltva, megtalálhatja a konfigurációs kapcsolót az [Azure Portalon.](https://portal.azure.com/) A [Felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) **szakaszban,** a Vállalati alkalmazások csoportban található.

A *prompt=admin_consent* paramétert olyan alkalmazások is használhatják, amelyek rendszergazdai jóváhagyást nem igényelnek. Egy példa egy olyan alkalmazás, amely olyan felhasználói élményt igényel, ahol a bérlői rendszergazda "egyszer" "regisztrál", és ettől a ponttól kezdve más felhasználók nem kérnek beleegyezést.

Képzelje el, hogy egy alkalmazás rendszergazdai jóváhagyást igényel, és egy rendszergazda jelentkezik be a *prompt=admin_consent* paraméter küldése nélkül. Ha a rendszergazda sikeresen hozzájárul az alkalmazáshoz, az csak a felhasználói fiókjukra vonatkozik. A rendszeres felhasználók továbbra sem tudnak bejelentkezni vagy hozzájárulni az alkalmazáshoz. Ez a funkció akkor hasznos, ha azt szeretné, hogy a bérlői rendszergazda képes legyen az alkalmazás felfedezése, mielőtt más felhasználók hozzáférését engedélyezne.
