---
title: 'Oktatóanyag: Azure Active Directory integráció a Amazon Web Services (AWS) szolgáltatással több fiók összekapcsolásához | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure AD és a Amazon Web Services (AWS) több fiókja között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195904"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Oktatóanyag: Azure Active Directory integráció több Amazon Web Services (AWS) fiókkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure Active Directory (Azure AD) Amazon Web Services (AWS) több fiókkal.

Az Amazon Web Services (AWS) és az Azure AD integrálásával a következő előnyöket nyújtja:

- Az Azure AD-ben beállíthatja, hogy ki férhet hozzá Amazon Web Services (AWS) szolgáltatáshoz.
- Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkeznek a Amazon Web Servicesba (AWS) (egyszeri bejelentkezés) az Azure AD-fiókjával.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) az eredmények listájában](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Vegye figyelembe, hogy egyetlen AWS-alkalmazás csatlakoztatása az összes AWS-fiókhoz nem az ajánlott módszer. Ehelyett azt javasoljuk, hogy [ezt](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) a módszert használva több AWS-fiók több példányát konfigurálja az Azure ad-ben az AWS-alkalmazások több példányára.

**Vegye figyelembe, hogy a következő okok miatt nem ajánlott ezt a módszert használni:**

* A Graph Explorer megközelítésével kell kijavítani az összes szerepkört az alkalmazásban. A manifest file megközelítést nem ajánlott használni.

* Láttuk ügyfeleinket, hogy egy AWS-alkalmazáshoz hozzáadott ~ 1200 alkalmazási Szerepkörök hozzáadása után az alkalmazás minden művelete megkezdte a mérettel kapcsolatos hibák ledobását. Az Application objektumon rögzített méret van korlátozva.

* Manuálisan kell frissítenie a szerepkört, mivel a szerepkörök bármely fiókban hozzá lettek adva, ami egy lecserélt módszer, és nem fűzhet hozzá sajnos. Emellett ha a fiókok egyre növekednek, akkor ez n x n kapcsolat lesz a fiókokkal és szerepkörökkel.

* Az összes AWS-fiók ugyanazt az összevonási metaadatokat tartalmazó XML-fájlt fogja használni, és a tanúsítvány-átváltáskor el kell végeznie ezt a nagy gyakorlatot, hogy az összes AWS-fiókon egyidejűleg frissítse a tanúsítványt

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Amazon Web Services (AWS) szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Amazon Web Services (AWS) egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Amazon Web Services (AWS) támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO-t

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a gyűjteményből

Amazon Web Services (AWS) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Amazon Web Services (AWS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Amazon Web Services (AWS) a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Amazon Web Services (AWS)** kifejezést, válassza a **Amazon Web Services (AWS)** elemet az eredmény panelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Amazon Web Services (AWS) az eredmények listájában](common/search-new-app.png)

5. Az alkalmazás hozzáadása után lépjen a **Tulajdonságok** lapra, és másolja az **objektumazonosítót**.

    ![Amazon Web Services (AWS) az eredmények listájában](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli Amazon Web Services (AWS) egy "Britta Simon" nevű teszt felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek tudnia kell, hogy a Amazon Web Services (AWS) felhasználói a felhasználó az Azure AD-ben. Más szóval az Azure AD-felhasználó és a Amazon Web Services (AWS) kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítése szükséges.

Amazon Web Services (AWS) esetében rendelje hozzá a Felhasználónév értékét az Azure AD **-ben a** kapcsolati kapcsolat létrehozásához használt **Felhasználónév** értékeként.

Az Azure AD egyszeri bejelentkezés Amazon Web Services (AWS) szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Amazon Web Services (AWS) egyszeri bejelentkezés konfigurálása](#configure-amazon-web-services-aws-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portalban, és konfigurálhatja az egyszeri bejelentkezést az Amazon Web Services (AWS) alkalmazásban.

**Az Azure AD egyszeri bejelentkezés Amazon Web Services (AWS) szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:**

1. A [Azure Portal](https://portal.azure.com/) **Amazon Web Services (AWS)** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![image](common/preintegrated.png)

5. A Amazon Web Services (AWS) alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Ezen attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok & jogcímek** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név)  | Forrás attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | User. assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket adjon meg" |  https://aws.amazon.com/SAML/Attributes |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. A **névtér** szövegmezőbe írja be az adott sorhoz megjelenő névtér értékét.

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-fájljának** letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Amazon Web Services (AWS) egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Amazon Web Services (AWS) vállalati webhelyre rendszergazdaként.

2. Kattintson az **AWS Kezdőlap**elemre.

    ![Egyszeri bejelentkezés konfigurálása][11]

3. Kattintson **az identitás-és hozzáférés-kezelés**elemre.

    ![Egyszeri bejelentkezés identitásának konfigurálása][12]

4. Kattintson az **identitás-szolgáltatók**elemre, majd a **szolgáltató létrehozása**elemre.

    ![Egyszeri bejelentkezési szolgáltató konfigurálása][13]

5. A **szolgáltató konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési párbeszédpanel konfigurálása][14]

    a. **Szolgáltató típusaként**válassza az **SAML**lehetőséget.

    b. A **szolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *WAAD*).

    c. A letöltött metaadat- **fájl** Azure Portalból való feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre.

    d. Kattintson a **következő lépés**gombra.

6. A **szolgáltatói adatok ellenőrzése** párbeszédpanelen kattintson a **Létrehozás**gombra.

    ![Egyszeri bejelentkezés konfigurálása – ellenőrzés][15]

7. Kattintson a **szerepkörök**elemre, majd kattintson a **szerepkör létrehozása**lehetőségre.

    ![Egyszeri bejelentkezési szerepkörök konfigurálása][16]

8. A **szerepkör létrehozása** oldalon hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezéses megbízhatóság konfigurálása][19]

    a. Válassza ki az **SAML 2,0-összevonás** elemet **a megbízható entitás típusa**területen.

    b. Az **saml 2,0-szolgáltató kiválasztása szakaszban**válassza ki a korábban létrehozott **SAML-szolgáltatót** (például: *WAAD*)

    c. Válassza **a programozott és AWS felügyeleti konzol hozzáférésének engedélyezése**lehetőséget.
  
    d. Kattintson a **Tovább gombra: engedélyek**.

9. Az **engedélyek csatolása** párbeszédpanelen adja meg a megfelelő szabályzatot a szervezete számára. Kattintson a **Tovább gombra: felülvizsgálat**.  

    ![Egyszeri bejelentkezési szabályzat konfigurálása][33]

10. A **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés felülvizsgálatának konfigurálása][34]

    a. A **szerepkör neve** szövegmezőbe írja be a szerepkör nevét.

    b. A **szerepkör leírása** szövegmezőbe írja be a leírást.

    c. Kattintson a **szerepkör létrehozása**lehetőségre.

    d. Szükség szerint hozzon létre annyi szerepkört, és rendelje őket az identitás-szolgáltatóhoz.

11. Jelentkezzen ki az aktuális AWS-fiókból, és jelentkezzen be olyan fiókkal, ahol egyszeri bejelentkezést szeretne konfigurálni az Azure AD-vel.

12. A fiókhoz beállítani kívánt több szerepkör létrehozásához hajtsa végre a 2. lépést a 10. lépéssel. Ha kettőnél több fiókkal rendelkezik, hajtsa végre ugyanezeket a lépéseket az összes fiókhoz a szerepkörök létrehozásához.

13. Miután az összes szerepkört létrehozta a fiókokban, azok megjelennek a **szerepkörök** listájában ezekhez a fiókokhoz.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Az összes fiók összes szerepköre számára az ARN és a megbízható entitások összes szerepkörét rögzíteni kell, amelyet manuálisan kell leképezni az Azure AD-alkalmazással.

15. Kattintson a szerepkörökre az **ARN szerepkör** és a **Megbízható entitások** értékének másolásához. Ezekre az értékekre szüksége lesz az Azure AD-ben létrehozandó összes szerepkörhöz.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Hajtsa végre a fenti lépést az összes fiókban lévő összes szerepkörhöz, és tárolja őket a Jegyzettömbben található **Megbízható entitások** formátumban.

17. Nyissa meg az [Azure ad Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a Graph Explorer-webhelyre a bérlőhöz tartozó globális rendszergazdai vagy társ-rendszergazdai hitelesítő adatok használatával.

    b. Megfelelő engedélyekkel kell rendelkeznie a szerepkörök létrehozásához. A szükséges engedélyek beszerzéséhez kattintson az **engedélyek módosítása** elemre.

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Válassza ki az alábbi engedélyeket a listából (ha még nem rendelkezik ezekkel), és kattintson az "engedélyek módosítása" elemre. 

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Ekkor ismét be kell jelentkeznie, és el kell fogadnia az engedélyt. A beleegyezés elfogadása után ismét bejelentkezett a Graph Explorerben.

    e. Módosítsa a verzió legördülő menüjét a **bétaverzióra**. Az összes egyszerű szolgáltatásnév beolvasásához használja a következő lekérdezést:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, akkor a következő mintát használhatja, amely az elsődleges tartománya `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. A beolvasott egyszerű szolgáltatások listájából szerezze be a módosítandó elemet. A CTRL + F billentyűkombinációval is megkeresheti az alkalmazást az összes felsorolt ServicePrincipals. A következő lekérdezést használhatja az Azure AD tulajdonságlapról másolt **objektumazonosító** használatával, hogy megkapja a megfelelő szolgáltatásnevet.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Bontsa ki a appRoles tulajdonságot az egyszerű szolgáltatásnév objektumból.

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Most új szerepköröket kell létrehoznia az alkalmazáshoz. 

    i. Az alábbi JSON egy példa a appRoles objektumra. Hozzon létre egy hasonló objektumot az alkalmazáshoz használni kívánt szerepkörök hozzáadásához.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > A javítási művelet **msiam_access** után csak új szerepköröket adhat hozzá. Azt is megteheti, hogy annyi szerepkört ad hozzá, amennyit csak szeretne, ha a szervezetnek szüksége van rá. Az Azure AD a következő szerepkörök **értékét** küldi el az SAML-válaszban szereplő jogcím-értékként.

    j. Lépjen vissza a Graph Explorerben, és változtassa meg a **Get** to **patch**metódust. Az egyszerű szolgáltatásnév objektumának javításához a appRoles tulajdonság frissítésével a példában láthatóhoz hasonló módon frissítse a kívánt szerepköröket. A javítási művelet végrehajtásához kattintson a **lekérdezés futtatása** elemre. A sikeres üzenet megerősíti a Amazon Web Services alkalmazás szerepkörének létrehozását.

    ![Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Ha a szolgáltatásnév több szerepkörrel is rendelkezik, akkor felhasználókat és csoportokat rendelhet hozzá a megfelelő szerepkörökhöz. Ezt a portálon teheti meg, és megnyithatja a Amazon Web Services alkalmazást. Kattintson a felül található **felhasználók és csoportok** fülre.

19. Javasoljuk, hogy hozzon létre új csoportokat minden AWS-szerepkörhöz, hogy az adott szerepkört hozzá lehessen rendelni a csoporthoz. Vegye figyelembe, hogy ez az egyik egy adott csoport egy szerepkörhöz való hozzárendelése. Ezután hozzáadhatja a csoporthoz tartozó tagokat.

20. A csoportok létrehozása után válassza ki a csoportot, és rendelje hozzá az alkalmazáshoz.

    ![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > A beágyazott csoportok nem támogatottak csoportok hozzárendeléséhez.

21. Ha a szerepkört hozzá szeretné rendelni a csoporthoz, válassza ki a szerepkört, majd kattintson az oldal alján található **hozzárendelés** gombra.

    ![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Vegye figyelembe, hogy az új szerepkörök megjelenítéséhez frissítenie kell a munkamenetét Azure Portalban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Amazon Web Services (AWS) csempére kattint, akkor a szerepkör kiválasztásához be kell szereznie a Amazon Web Services (AWS) alkalmazás lapot.

![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Azt is ellenőrizheti, hogy az SAML-válasz a jogcímek szerint átadott szerepköröket tekinti-e meg.

![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [A kiépítés konfigurálása az MS Graph API-k használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
