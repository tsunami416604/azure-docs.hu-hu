---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Amazon Web Services (AWS) szolgáltatással több fiók csatlakoztatásához | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure AD és az Amazon Web Services (AWS) (Örökölt oktatóanyag) között.
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
ms.date: 04/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51be98654950ba290fa83f77eccdae4d6f549891
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603828"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Oktatóanyag: Az Azure Active Directory integrációja az Amazon Web Services (AWS) szolgáltatással (örökölt oktatóanyag)

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Active Directoryt (Azure AD) az Amazon Web Services (AWS) (Örökölt oktatóanyag) szolgáltatással.

Az Amazon Web Services (AWS) integrálása az Azure AD-vel a következő előnyökkel jár:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Amazon Web Services (AWS) szolgáltatáshoz.
- Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve az Amazon Web Services (AWS) (Single Sign-On) az Azure AD-fiókok.
- Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, tekintse meg, [hogy mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval.](../manage-apps/what-is-single-sign-on.md)

![Amazon Web Services (AWS) az eredménylistában](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Kérjük, vegye figyelembe, hogy egy AWS alkalmazás csatlakoztatása az összes AWS-fiókjához nem az ajánlott megközelítésünk. Ehelyett azt javasoljuk, hogy [használja ezt a](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) módszert az AWS-fiók több példányának konfigurálásához az Azure AD-ben az AWS-alkalmazások több példányához. Ezt a módszert csak akkor használja, ha kevés AWS-fiók és szerepkör van benne, ez a modell nem méretezhető, mivel az AWS-fiókok és szerepkörök ezeken a fiókokon belül növekszik. Ez a megközelítés nem használja az AWS szerepkör importálási funkcióját az Azure AD-felhasználó kiépítés használatával, ezért manuálisan kell hozzáadnia/frissítenie/törölnie a szerepköröket. A megközelítés egyéb korlátait lásd az alábbi részletekben.

**Kérjük, vegye figyelembe, hogy nem javasoljuk, hogy ezt a megközelítést a következő okok miatt:**

* A Microsoft Graph Explorer módszert kell használnia az összes szerepkör alkalmazáshoz való javításához. Nem javasoljuk a jegyzékfájl-megközelítés használatát.

* Láttuk, hogy az ügyfelek arról számoltak be, hogy miután ~1200 alkalmazásszerepkört adtak hozzá egyetlen AWS alkalmazáshoz, az alkalmazás minden művelete elkezdte eldobni a mérethez kapcsolódó hibákat. Az alkalmazásobjektum mérete szigorú.

* Manuálisan kell frissítenie a szerepkört, mivel a szerepkörök hozzáadódnak a fiókok bármelyikéhez, ami egy Csere megközelítés, és nem sajnos hozzáfűzése. Akkor is, ha a fiókok növekszik, akkor ez lesz n x n kapcsolat számlák és szerepek.

* Az összes AWS-fiók ugyanazt az összevonási metaadat-XML-fájlt fogja használni, és a tanúsítványváltás időpontjában meg kell hajtania ezt a tömeges gyakorlatot, hogy egyszerre frissítse a tanúsítványt az összes AWS-fiókon.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az Amazon Web Services (AWS) szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Amazon Web Services (AWS) egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Egy Azure AD próbakörnyezettel, [egy hónapos próbaverziót kaphat.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Amazon Web Services (AWS) támogatja az **SP-t és az IDP** által kezdeményezett SSO-t
* Az Amazon Web Services (AWS) konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a galériából

Az Amazon Web Services (AWS) Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Amazon Web Services (AWS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be az **Amazon Web Services (AWS)** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Web Services (AWS)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

1. Az alkalmazás hozzáadása után lépjen a **Tulajdonságok** lapra, és másolja az **Objektumazonosítót**.

    ![Amazon Web Services (AWS) az eredménylistában](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Amazon Web Services (AWS) szolgáltatással a "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés a munka, az Azure AD tudnia kell, hogy mi a megfelelő felhasználó az Amazon Web Services (AWS) az Azure AD-ben. Más szóval létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az Amazon Web Services (AWS) létre kell hozni.

Az Amazon Web Services (AWS) szolgáltatásban rendelje hozzá a **felhasználónév** értékét az Azure AD-ben a **felhasználónév** értékeként a kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Amazon Web Services (AWS) szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Amazon Web Services (AWS) egyszeri bejelentkezést](#configure-amazon-web-services-aws-single-sign-on)** – az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon, és konfigurálja az egyszeri bejelentkezést az Amazon Web Services (AWS) alkalmazásban.

**Az Azure AD egyszeri bejelentkezésének az Amazon Web Services (AWS) szolgáltatással történő konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az [Azure Portalon](https://portal.azure.com/)az **Amazon Web Services (AWS)** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszerű SAML-konfiguráció** szakaszban a felhasználónak nem kell semmilyen lépést végrehajtania, mivel az alkalmazás már előre integrálva van az Azure-ral, és kattintson a **Mentés gombra.**

5. Az Amazon Web Services (AWS) alkalmazás az SAML-állításokat egy adott formátumban várja. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok & jogcímek** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra **a Felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában konfigurálja az SAML token attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név)  | Forrás attribútuma  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Szerepkör            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration (Munkamenet időtartama)             | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket ad meg" |  `https://aws.amazon.com/SAML/Attributes` |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. A **Névtér** mezőbe írja be a sorhoz megjelenített Névtér értéket.

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** kattintson a **Letöltés** gombra az **összevonási metaadat-XML** letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Az Amazon Web Services (AWS) egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Amazon Web Services (AWS) vállalati webhelyére rendszergazdaként.

1. Kattintson **az AWS Kezdőlap ikonjára.**

    ![Egyszeri bejelentkezés otthoni konfigurálása][11]

1. Kattintson **az Identitás- és hozzáférés-kezelés gombra.**

    ![Egyszeri bejelentkezési identitás konfigurálása][12]

1. Kattintson **az Identitásszolgáltatók**, majd **a Szolgáltató létrehozása**parancsra.

    ![Egyszeri bejelentkezésszolgáltató konfigurálása][13]

1. A **Szolgáltató konfigurálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés párbeszédpanel konfigurálása][14]

    a. **Szolgáltatótípusként**válassza az **SAML**lehetőséget.

    b. A **Szolgáltató neve** mezőbe írja be a szolgáltató nevét (például *WAAD*).

    c. Ha fel szeretné tölteni a letöltött **metaadatfájlt** az Azure Portalról, kattintson **a Fájl kiválasztása gombra.**

    d. Kattintson a **Következő lépés gombra.**

1. A **Szolgáltató adatainak ellenőrzése** párbeszédpanelen kattintson a **Létrehozás gombra.**

    ![Egyszeri bejelentkezés ellenőrzésének konfigurálása][15]

1. Kattintson **a Szerepkörök**, majd **a Szerepkör létrehozása**parancsra.

    ![Egyszeri bejelentkezési szerepkörök konfigurálása][16]

1. A **Szerepkör létrehozása** lapon hajtsa végre az alábbi lépéseket:  

    ![Egyszeri bejelentkezési megbízhatóságkonfigurálás konfigurálása][19]

    a. Válassza **az SAML 2.0 összevonás** lehetőséget **a Megbízható entitás típusának kiválasztása**csoportban.

    b. Az **SAML 2.0 szolgáltató kiválasztása csoportban**válassza ki a korábban létrehozott **SAML-szolgáltatót** (például *WAAD)*

    c. Válassza **az Programozott és Az AWS Felügyeleti konzol elérésének engedélyezése**lehetőséget.
  
    d. Kattintson a **Tovább gombra: Engedélyek**.

1. Keresés **rendszergazdai hozzáférés** a keresősávban, és jelölje be a **AdministratorAccess** jelölőnégyzetet, majd kattintson a **Tovább: Címkék gombra.**

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. A **Címkék hozzáadása (nem kötelező)** szakaszban hajtsa végre az alábbi lépéseket:

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/config2.png)

    a. A **Kulcs** szövegmezőbe írja be az ex kulcsnevét: Azureadtest.

    b. Az **Érték (nem kötelező)** mezőbe írja be a `accountname-aws-admin`kulcs értékét a következő formátumban. A fióknévnek kisbetűsnek kell lennie.

    c. Kattintson **a következő gombra: Véleményezés**.

1. A **Véleményezés** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezési ellenőrzés konfigurálása][34]

    a. A **Szerepkör név** mezőbe írja be az `accountname-aws-admin`értéket a következő mintába .

    b. A **Szerepkör leírásának** mezőbe írja be ugyanazt az értéket, amelyet a szerepkör nevéhez használt.

    c. Kattintson **a Szerepkör létrehozása gombra.**

    d. Hozzon létre annyi szerepkört, amennyi szükséges, és rendelje hozzá juk az identitásszolgáltatóhoz.

    > [!NOTE]
    > Hasonlóképpen hozzon létre fennmaradó más szerepkörök, mint a fióknév-finance-admin, accountname-read-only-user, accountname-devops-user, accountname-tpm-user with different policies to be attached. Később is ezek a szerepkör-házirendek lehet változtatni, mint egy követelmény egy AWS-fiók, de mindig jobb, hogy tartsa ugyanazt a házirendeket az egyes szerepkörek az AWS-fiókokban.

1. Kérjük, jegyezze fel az adott AWS-fiók fiókazonosítóját az EC2 tulajdonságaiból vagy az IAM irányítópultjáról, amint azt az alábbiak ban kiemeltük:

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Most jelentkezzen be az [Azure Portalra,](https://portal.azure.com/) és keresse meg a **Csoportok**.

1. Hozzon létre új csoportokat ugyanazzal a névvel, mint a korábban létrehozott IAM szerepkörök, és jegyezze fel az új csoportok **objektumazonosítóit.**

    ![Rendszergazdai hozzáférés kiválasztása](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Jelentkezzen ki az aktuális AWS-fiókból, és jelentkezzen be más fiókkal, ahol az Azure AD-vel szeretné konfigurálni az egyszeri bejelentkezést.

1. Miután az összes szerepkör létrejött a fiókokban, megjelennek a **fiókok Szerepkörök** listájában.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Meg kell örökíteni az összes szerepkör ARN és megbízható entitások az összes szerepkör az összes fiók, amelyet manuálisan kell leképezni az Azure AD-alkalmazással.

1. Kattintson a szerepkörökre az **ARN** és **a Megbízható entitások** értékek másolásához. Ezekre az értékekre az Azure AD-ben létrehozandó összes szerepkörhöz szüksége van.

    ![Szerepkörök beállítása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Hajtsa végre a fenti lépést az összes fiók összes szerepköre számára, és tárolja őket **a Szerepkör ARN, Megbízható entitások** formátumban egy jegyzettömbben.

1. Nyissa meg a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) t egy másik ablakban.

    a. Jelentkezzen be a Microsoft Graph Explorer webhelyére a bérlő globális rendszergazdai/társrendszergazdai hitelesítő adataival.

    b. A szerepkörök létrehozásához megfelelő engedélyekkel kell rendelkeznie. Kattintson a **módosítási engedélyeket,** hogy a szükséges engedélyeket.

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Válassza ki a következő engedélyeket a listából (ha még nem rendelkezik ilyenekkel), majd kattintson az "Engedélyek módosítása" gombra. 

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Ez megkéri, hogy jelentkezzen be újra, és fogadja el a hozzájárulást. A hozzájárulás elfogadása után ismét be jelentkezik a Microsoft Graph Explorer programba.

    e. Módosítsa a verzió legördülő **béta**. Az összes szolgáltatásnévkérés a bérlőből, használja a következő lekérdezést:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, akkor használhatja a következő mintát, amely az elsődleges tartománybenne`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. A rendszernévi tagok listájából olvassa be a módosítani kívánt szolgáltatást. A Ctrl+F billentyűkombinációval is kereshet az alkalmazásban az összes felsorolt ServicePrincipals elemből. A következő lekérdezést használhatja az **Objektum-azonosító,** amely az Azure AD tulajdonságai lapról másolt a megfelelő egyszerű szolgáltatás.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Az appRoles tulajdonság kinyerése az egyszerű szolgáltatásobjektumból.

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Most új szerepköröket kell létrehoznia az alkalmazáshoz. 

    i. A JSON alatt egy példa appRoles objektum. Hozzon létre egy hasonló objektumot az alkalmazáshoz kívánt szerepkörök hozzáadásához.

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
    > Csak a **javítási** művelet msiam_access után adhat hozzá új szerepköröket. Emellett annyi szerepkört adhat hozzá, amennyit csak akar a szervezet igénye szerint. Az Azure AD elküldi ezeknek a szerepköröknek az **értékét** az SAML-válasz jogcímértékeként.

    j. Lépjen vissza a Microsoft Graph Explorer programhoz, és módosítsa a módszert **GET-ről** **PATCH-re.** Javítsa ki a Szolgáltatásegyszerű objektumot a kívánt szerepkörökkel az appRoles tulajdonság nak a példában fent láthatóhoz hasonló frissítésével. A javítás végrehajtásához kattintson a **Lekérdezés futtatása** gombra. A sikeres üzenet megerősíti a szerepkör létrehozását az Amazon Web Services alkalmazáshoz.

    ![A Microsoft Graph Explorer párbeszédpanelje](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Miután a szolgáltatásegyszerű javítást kapott több szerepkörrel, felhasználókat/csoportokat rendelhet a megfelelő szerepkörökhöz. Ezt úgy teheti meg, hogy a portálra megy, és az Amazon Web Services alkalmazásba navigál. Kattintson a felső sarokban a **Felhasználók és csoportok** fülre.

1. Javasoljuk, hogy hozzon létre új csoportokat minden AWS szerepkörhöz, hogy hozzárendelhesse az adott szerepkört az adott csoporthoz. Ne feledje, hogy ez egy egy csoport egy szerepkörhöz. Ezután hozzáadhatja a csoporthoz tartozó tagokat.

1. A csoportok létrehozása után jelölje ki a csoportot, és rendelje hozzá az alkalmazáshoz.

    ![Egyszeri bejelentkezés hozzáadása konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > A beágyazott csoportok nem támogatottak csoportok hozzárendelésekénte.

1. Ha hozzá szeretné rendelni a szerepkört a csoporthoz, jelölje ki a szerepkört, és kattintson a lap alján található **Hozzárendelés** gombra.

    ![Egyszeri bejelentkezés hozzáadása konfigurálása](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Kérjük, vegye figyelembe, hogy frissítenie kell a munkamenetet az Azure Portalon az új szerepkörök megtekintéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az Amazon Web Services (AWS) csempére kattint a Hozzáférési panelen, az Amazon Web Services (AWS) alkalmazáslapját a szerepkör kiválasztásához kell kapnia.

![Egyszeri bejelentkezés hozzáadása konfigurálása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Ellenőrizheti az SAML választ is, hogy a jogcímként átadott szerepkörök et láthassa.

![Egyszeri bejelentkezés hozzáadása konfigurálása](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](../active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>További források

* [A kiépítés konfigurálása az MS Graph API-k használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Hogyan védheti meg az Amazon Web Services (AWS) szolgáltatást a fejlett láthatóságés vezérlők](https://docs.microsoft.com/cloud-app-security/protect-aws)

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