---
title: 'Oktatóanyag: Azure Active Directory integráció a Amazon Web Services (AWS) szolgáltatással több fiók összekapcsolásához | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure AD és a Amazon Web Services (AWS) között (örökölt oktatóanyag).
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
ms.topic: article
ms.date: 08/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6857fba86aebbcfd7b20326e4a4a268467e4fc3
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272467"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Oktatóanyag: Azure Active Directory integráció Amazon Web Services (AWS) (örökölt oktatóanyag)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directoryt (Azure AD) a Amazon Web Services (AWS) szolgáltatással (örökölt oktatóanyag).

Az Amazon Web Services (AWS) és az Azure AD integrálásával a következő előnyöket nyújtja:

- Az Azure AD-ben beállíthatja, hogy ki férhet hozzá Amazon Web Services (AWS) szolgáltatáshoz.
- Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkeznek a Amazon Web Servicesba (AWS) (egyszeri bejelentkezés) az Azure AD-fiókjával.
- A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

![Amazon Web Services (AWS) az eredmények listájában](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Vegye figyelembe, hogy egyetlen AWS-alkalmazás csatlakoztatása az összes AWS-fiókhoz nem az ajánlott módszer. Ehelyett azt javasoljuk, hogy [ezt](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) a módszert használva több AWS-fiók több példányát konfigurálja az Azure ad-ben az AWS-alkalmazások több példányára. Ezt a módszert csak akkor érdemes használni, ha kevés AWS-fiókja és szerepköre van benne, ez a modell nem méretezhető, mivel a fiókokban az AWS-fiókok és-szerepkörök növekednek. Ez a megközelítés nem használja az AWS szerepkör-importálási funkciót az Azure AD-felhasználók kiosztásával, ezért manuálisan kell hozzáadnia/frissítenie/törölnie a szerepköröket. Az ehhez a megközelítéshez kapcsolódó egyéb korlátozásokért tekintse meg az alábbi részleteket.

**Vegye figyelembe, hogy a következő okok miatt nem ajánlott ezt a módszert használni:**

* Az alkalmazás összes szerepkörének javításához a Microsoft Graph Explorer megközelítését kell használnia. A manifest file megközelítést nem ajánlott használni.

* Láttuk ügyfeleinket, hogy egy AWS-alkalmazáshoz hozzáadott ~ 1200 alkalmazási Szerepkörök hozzáadása után az alkalmazás minden művelete megkezdte a mérettel kapcsolatos hibák ledobását. Az Application objektumon rögzített méret van korlátozva.

* Manuálisan kell frissítenie a szerepkört, mivel a szerepkörök bármely fiókban hozzá lettek adva, ami egy lecserélt módszer, és nem fűzhet hozzá sajnos. Emellett ha a fiókok egyre növekednek, akkor ez n x n kapcsolat lesz a fiókokkal és szerepkörökkel.

* Az összes AWS-fiók ugyanazt az összevonási metaadatokat tartalmazó XML-fájlt fogja használni, és a tanúsítvány-átváltáskor el kell végeznie ezt a nagy gyakorlatot, hogy az összes AWS-fiókon egyidejűleg frissítse a tanúsítványt

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Amazon Web Services (AWS) szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Amazon Web Services (AWS) egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy [hónapos próbaverziót is beszerezhet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Amazon Web Services (AWS) támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO-t
* Miután konfigurálta Amazon Web Services (AWS), kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a gyűjteményből

Amazon Web Services (AWS) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Amazon Web Services (AWS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Amazon Web Services (AWS)** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Web Services (AWS)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

1. Az alkalmazás hozzáadása után lépjen a **Tulajdonságok** lapra, és másolja az **objektumazonosítót**.

    ![Amazon Web Services (AWS) az eredmények listájában](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD SSO konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli Amazon Web Services (AWS) egy "Britta Simon" nevű teszt felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek tudnia kell, hogy a Amazon Web Services (AWS) felhasználói a felhasználó az Azure AD-ben. Más szóval az Azure AD-felhasználó és a Amazon Web Services (AWS) kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítése szükséges.

Amazon Web Services (AWS) esetében rendelje hozzá a Felhasználónév értékét az Azure AD **-ben a** kapcsolati kapcsolat létrehozásához használt **Felhasználónév** értékeként.

Az Azure AD egyszeri bejelentkezés Amazon Web Services (AWS) szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Amazon Web Services (AWS) egyszeri bejelentkezés konfigurálása](#configure-amazon-web-services-aws-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portalban, és konfigurálhatja az egyszeri bejelentkezést az Amazon Web Services (AWS) alkalmazásban.

**Az Azure AD egyszeri bejelentkezés Amazon Web Services (AWS) szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:**

1. A [Azure Portal](https://portal.azure.com/) **Amazon Web Services (AWS)** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban, és kattintson a **Save (Mentés**) gombra.

5. A Amazon Web Services (AWS) alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Ezen attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok & jogcímek** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név)  | Forrás attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User. userPrincipalName | `https://aws.amazon.com/SAML/Attributes` |
    | Szerepkör            | User. assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket adjon meg" |  `https://aws.amazon.com/SAML/Attributes` |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. A **névtér** szövegmezőbe írja be az adott sorhoz megjelenő névtér értékét.

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-fájljának** letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Amazon Web Services (AWS) egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Amazon Web Services (AWS) vállalati webhelyre rendszergazdaként.

1. Kattintson az **AWS Kezdőlap**elemre.

    ![Egyszeri bejelentkezés konfigurálása][11]

1. Kattintson **az identitás-és hozzáférés-kezelés**elemre.

    ![Egyszeri bejelentkezés identitásának konfigurálása][12]

1. Kattintson az **identitás-szolgáltatók**elemre, majd a **szolgáltató létrehozása**elemre.

    ![Egyszeri bejelentkezési szolgáltató konfigurálása][13]

1. A **szolgáltató konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési párbeszédpanel konfigurálása][14]

    a. **Szolgáltató típusaként**válassza az **SAML**lehetőséget.

    b. A **szolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *WAAD*).

    c. A letöltött metaadat- **fájl** Azure Portalból való feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre.

    d. Kattintson a **következő lépés**gombra.

1. A **szolgáltatói adatok ellenőrzése** párbeszédpanelen kattintson a **Létrehozás**gombra.

    ![Egyszeri bejelentkezés konfigurálása – ellenőrzés][15]

1. Kattintson a **szerepkörök**elemre, majd kattintson a **szerepkör létrehozása**lehetőségre.

    ![Egyszeri bejelentkezési szerepkörök konfigurálása][16]

1. A **szerepkör létrehozása** oldalon hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezéses megbízhatóság konfigurálása][19]

    a. Válassza ki az **SAML 2,0-összevonás** elemet **a megbízható entitás típusa**területen.

    b. Az **saml 2,0-szolgáltató kiválasztása szakaszban**válassza ki a korábban létrehozott **SAML-szolgáltatót** (például: *WAAD*)

    c. Válassza **a programozott és AWS felügyeleti konzol hozzáférésének engedélyezése**lehetőséget.
  
    d. Kattintson a **Tovább gombra: engedélyek**.

1. Keresse meg a **rendszergazdai hozzáférést** a keresősáv alatt, és jelölje be a **AdministratorAccess** jelölőnégyzetet, majd kattintson a **Tovább gombra: címkék**.

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. A **Címkék hozzáadása (nem kötelező)** szakaszban hajtsa végre a következő lépéseket:

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/config2.png)

    a. A **kulcs** szövegmezőbe írja be a következőt: Azureadtest.

    b. Az **érték (nem kötelező)** szövegmezőbe írja be a kulcs értékét a következő formátum használatával `accountname-aws-admin` . A fiók nevének minden kisbetűsnek kell lennie.

    c. Kattintson a **Tovább gombra: felülvizsgálat**.

1. A **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés felülvizsgálatának konfigurálása][34]

    a. A **szerepkör neve** szövegmezőbe írja be az értéket a következő mintában `accountname-aws-admin` .

    b. A **szerepkör leírása** szövegmezőbe írja be ugyanazt az értéket, amelyet a szerepkör neveként használt.

    c. Kattintson a **szerepkör létrehozása**lehetőségre.

    d. Szükség szerint hozzon létre annyi szerepkört, és rendelje őket az identitás-szolgáltatóhoz.

    > [!NOTE]
    > Hasonlóképpen hozzon létre további egyéb szerepköröket, például a accountname-Finance-admin, a accountname-Read-Only-user, a accountname-devops-user, a accountname-TPM-User és a különböző házirendek csatolása. A későbbiekben ezek a szerepkör-szabályzatok az AWS-fiókokra vonatkozó követelmények szerint is megváltoztathatók, de az AWS-fiókok minden egyes szerepköre esetében mindig jobban megmaradnak a szabályzatok.

1. Jegyezze fel a fiók AZONOSÍTÓját az AWS-fiókhoz a EC2-tulajdonságok vagy a IAM-irányítópult alapján, az alábbi kiemelt módon:

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Most jelentkezzen be [Azure Portal](https://portal.azure.com/) , és lépjen a **csoportok**elemre.

1. Hozzon létre új csoportokat ugyanazzal a névvel, mint a korábban létrehozott IAM-szerepkörökkel, és jegyezze fel az új csoportok **objektum-azonosítóit** .

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Jelentkezzen ki az aktuális AWS-fiókból, és jelentkezzen be olyan fiókkal, ahol egyszeri bejelentkezést szeretne konfigurálni az Azure AD-vel.

1. Miután az összes szerepkört létrehozta a fiókokban, azok megjelennek a **szerepkörök** listájában ezekhez a fiókokhoz.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Az összes fiók összes szerepköre számára az ARN és a megbízható entitások összes szerepkörét rögzíteni kell, amelyet manuálisan kell leképezni az Azure AD-alkalmazással.

1. Kattintson a szerepkörökre az **ARN szerepkör** és a **Megbízható entitások** értékének másolásához. Ezekre az értékekre szüksége lesz az Azure AD-ben létrehozandó összes szerepkörhöz.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Hajtsa végre a fenti lépést az összes fiókban lévő összes szerepkörhöz, és tárolja őket a Jegyzettömbben található **Megbízható entitások** formátumban.

1. Nyissa meg [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a Microsoft Graph Explorer webhelyre a bérlőhöz tartozó globális rendszergazdai/rendszergazdai hitelesítő adatok használatával.

    b. Megfelelő engedélyekkel kell rendelkeznie a szerepkörök létrehozásához. A szükséges engedélyek beszerzéséhez kattintson az **engedélyek módosítása** elemre.

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Válassza ki az alábbi engedélyeket a listából (ha még nem rendelkezik ezekkel), és kattintson az "engedélyek módosítása" elemre. 

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Ekkor ismét be kell jelentkeznie, és el kell fogadnia az engedélyt. A beleegyezés elfogadása után ismét bejelentkezett a Microsoft Graph Explorerbe.

    e. Módosítsa a verzió legördülő menüjét a **bétaverzióra**. Az összes egyszerű szolgáltatásnév beolvasásához használja a következő lekérdezést:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, akkor a következő mintát használhatja, amely az elsődleges tartománya  `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. A beolvasott egyszerű szolgáltatások listájából szerezze be a módosítandó elemet. A CTRL + F billentyűkombinációval is megkeresheti az alkalmazást az összes felsorolt ServicePrincipals. A következő lekérdezéssel az Azure AD tulajdonságlapról másolt **egyszerű szolgáltatásnév-objektum azonosítójának** használatával érheti el a megfelelő szolgáltatásnevet.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    : Bontsa ki a appRoles tulajdonságot az egyszerű szolgáltatásnév objektumból.

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

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

    j. Lépjen vissza a Microsoft Graph-Intézőre, és módosítsa a metódust a **Get** to **patch**elemre. Az egyszerű szolgáltatásnév objektumának javításához a appRoles tulajdonság frissítésével a példában láthatóhoz hasonló módon frissítse a kívánt szerepköröket. A javítási művelet végrehajtásához kattintson a **lekérdezés futtatása** elemre. A sikeres üzenet megerősíti a Amazon Web Services alkalmazás szerepkörének létrehozását.

    ![Microsoft Graph Explorer párbeszédpanel](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Ha a szolgáltatásnév több szerepkörrel is rendelkezik, akkor felhasználókat és csoportokat rendelhet hozzá a megfelelő szerepkörökhöz. Ezt a portálon teheti meg, és megnyithatja a Amazon Web Services alkalmazást. Kattintson a felül található **felhasználók és csoportok** fülre.

1. Javasoljuk, hogy hozzon létre új csoportokat minden AWS-szerepkörhöz, hogy az adott szerepkört hozzá lehessen rendelni a csoporthoz. Vegye figyelembe, hogy ez az egyik egy adott csoport egy szerepkörhöz való hozzárendelése. Ezután hozzáadhatja a csoporthoz tartozó tagokat.

1. A csoportok létrehozása után válassza ki a csoportot, és rendelje hozzá az alkalmazáshoz.

    ![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > A beágyazott csoportok nem támogatottak csoportok hozzárendeléséhez.

1. Ha a szerepkört hozzá szeretné rendelni a csoporthoz, válassza ki a szerepkört, majd kattintson az oldal alján található **hozzárendelés** gombra.

    ![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Vegye figyelembe, hogy az új szerepkörök megjelenítéséhez frissítenie kell a munkamenetét Azure Portalban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Amazon Web Services (AWS) csempére kattint, akkor a szerepkör kiválasztásához be kell szereznie a Amazon Web Services (AWS) alkalmazás lapot.

![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Azt is ellenőrizheti, hogy az SAML-válasz a jogcímek szerint átadott szerepköröket tekinti-e meg.

![Egyszeri bejelentkezéses Hozzáadás konfigurálása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [A kiépítés konfigurálása az MS Graph API-k használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [A Amazon Web Services (AWS) védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-aws)

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
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/