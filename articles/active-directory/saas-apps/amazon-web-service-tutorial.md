---
title: 'Oktatóanyag: Amazon Web Services (AWS) Azure Active Directory integrációja | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Amazon Web Services (AWS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f346c995cbc8be6e609020db799959d873ce89b3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944961"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Oktatóanyag: Amazon Web Services (AWS) integrálása Azure Active Directory

Ez az oktatóanyag azt ismerteti, hogyan integrálható Amazon Web Services (AWS) a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AWS-t az Azure AD-vel, a következőket teheti:

* Az AWS-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az AWS-be az Azure AD-fiókjával.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

![Az Azure AD és az AWS kapcsolatának ábrája](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Több példányhoz is konfigurálhat több azonosítót. Példa:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel az Azure ad eltávolítja a **#** értékét, és elküldi a megfelelő `https://signin.aws.amazon.com/saml` értéket a célközönség URL-címének az SAML-jogkivonatban.

Ezt a megközelítést a következő okok miatt javasoljuk:

- Az egyes alkalmazások egyedi X509-tanúsítványt biztosítanak. Az AWS-alkalmazások példányainak minden példánya rendelkezhet egy másik tanúsítvány lejárati dátumával, amely külön AWS-fiók alapján kezelhető. Ebben az esetben a teljes tanúsítvány-váltás egyszerűbb.

- Az Azure AD-ben egy AWS-alkalmazással engedélyezheti a felhasználók üzembe helyezését, majd a szolgáltatás az AWS-fiókból beolvassa az összes szerepkört. Nem kell manuálisan hozzáadnia vagy frissítenie az AWS-szerepköröket az alkalmazásban.

- Az alkalmazás tulajdonosát egyenként is hozzárendelheti az alkalmazáshoz. Ez a személy közvetlenül az Azure AD-ben tudja kezelni az alkalmazást.

> [!Note]
> Győződjön meg arról, hogy csak a gyűjtemény alkalmazásait használja.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AWS egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. Az AWS támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.

## <a name="add-aws-from-the-gallery"></a>AWS hozzáadása a katalógusból

Az AWS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az AWS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Amazon Web Services (AWS)** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Web Services (AWS)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t AWS használatával egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és az AWS-ben lévő kapcsolódó felhasználó között.

Az Azure AD SSO az AWS-sel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **Konfigurálja az Azure ad SSO** -t, hogy a felhasználók használhatják ezt a funkciót.
2. Az **AWS konfigurálása** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **Hozzon létre egy Azure ad-tesztelési felhasználót** az Azure ad SSO teszteléséhez B. Simon használatával.
4. **Rendelje hozzá az Azure ad-teszt felhasználót** , hogy B. Simon engedélyezze az Azure ad SSO használatát.
5. **Hozzon létre egy AWS-teszt felhasználót** , hogy a B. Simon egy olyan AWS-beli párja legyen, aki a felhasználó Azure ad-képviseletéhez van társítva.
6. Ellenőrizze az **SSO** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Amazon Web Services (AWS)** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az alapszintű **SAML** -konfigurációhoz tartozó toll ikont a beállítások szerkesztéséhez.

   ![Az egyszeri bejelentkezés SAML-oldallal való beállításának képernyőképe, a toll ikon kiemelve](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés**gombra kattintva.

5. Ha egynél több példányt konfigurál, adjon meg egy azonosító értéket. A második példánytól kezdődően a következő formátumot használja, beleértve egy **#** egyedi SPN érték megadására szolgáló jelet is.

    `https://signin.aws.amazon.com/saml#2`

6. Az AWS-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a toll ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![Képernyőkép a felhasználói attribútumok párbeszédpanelről, a toll ikon kiemelve](common/edit-attribute.png)

7. Az előző attribútumok mellett az AWS-alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. Az SAML -jogkivonat attribútumának hozzáadásához a felhasználói jogcímek szakaszban, a **felhasználói attribútumok** párbeszédpanelen hajtsa végre a következő lépéseket.

    | Name (Név)  | Forrásattribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | User. assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "900 másodperc (15 perc) és 43200 másodperc (12 óra) közötti értéket adjon meg" |  https://aws.amazon.com/SAML/Attributes |

    a. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőkép a felhasználói jogcímek szakaszról, új jogcím hozzáadása és Kiemelt mentése](common/new-save-attribute.png)

    ![A felhasználói jogcímek kezelése párbeszédpanel képernyőképe](common/new-attribute-details.png)

    b. A **név**mezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. A **névtér**mezőbe írja be az adott sorhoz megjelenő névtér értékét.

    d. A **forrás**területen válassza az **attribútum**elemet.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson az **OK** gombra.

    g. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját**. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

   ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltés kiemelve](common/metadataxml.png)

1. A **Amazon Web Services beállítása (AWS)** szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

   ![Képernyőfelvétel a Amazon Web Services beállítása (AWS) szakaszról, a konfigurációs URL-címek kiemelésével](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>AWS konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AWS vállalati webhelyre rendszergazdaként.

2. Válassza az **AWS Kezdőlap**lehetőséget.

    ![Képernyőfelvétel az AWS vállalati webhelyről, az AWS Home ikon kiemelve][11]

3. Válassza **az identitás-és hozzáférés-kezelés**lehetőséget.

    ![Képernyőfelvétel az AWS-szolgáltatások oldalról, a IAM kiemelve][12]

4. Válassza az **identitás-szolgáltatók** > **Létrehozás szolgáltató**elemet.

    ![A IAM oldal, a személyazonosság-szolgáltatók és a Kiemelt szolgáltató létrehozása Képernyőkép][13]

5. A **szolgáltató konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![A szolgáltató konfigurálásának képernyőképe][14]

    a. A **szolgáltató típusa**beállításnál válassza az **SAML**lehetőséget.

    b. A **szolgáltató neve**mezőbe írja be a szolgáltató nevét (például: *WAAD*).

    c. A letöltött metaadat- **fájl** Azure Portal való feltöltéséhez válassza a **fájl kiválasztása**lehetőséget.

    d. Válassza a **következő lépés**lehetőséget.

6. A **szolgáltatói adatok ellenőrzése** lapon válassza a **Létrehozás**lehetőséget.

    ![Képernyőkép a szolgáltatói információk ellenőrzéséről, a létrehozás kiemelve][15]

7. Válassza a **szerepkörök** > **szerepkör létrehozása**lehetőséget.

    ![A szerepkörök lap képernyőképe][16]

8. A **szerepkör létrehozása** oldalon hajtsa végre a következő lépéseket:  

    ![A szerepkör létrehozása lap képernyőképe][19]

    a. A **megbízható entitás típusának kiválasztása**területen válassza az **SAML 2,0-összevonás**lehetőséget.

    b. Az **saml 2,0-szolgáltató kiválasztása**területen válassza ki a korábban létrehozott **SAML** -szolgáltatót (például: *WAAD*).

    c. Válassza **a programozott és AWS felügyeleti konzol hozzáférésének engedélyezése**lehetőséget.
  
    d. Válassza **a Next (tovább) lehetőséget: Engedélyek**.

9. Az **engedélyezési házirendek csatolása** párbeszédpanelen csatolja a megfelelő szabályzatot a szervezeten belül. Ezután válassza **a Next (tovább) gombot: Tekintse át**.  

    ![Az engedélyek házirend csatolása párbeszédpanel képernyőképe][33]

10. A **felülvizsgálati** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A felülvizsgálati párbeszédpanel képernyőképe][34]

    a. A **szerepkör neve**mezőben adja meg a szerepkör nevét.

    b. A **szerepkör leírása**mezőben adja meg a leírást.

    c. Válassza a **szerepkör létrehozása**lehetőséget.

    d. Szükség szerint hozzon létre annyi szerepkört, és rendelje őket az identitás-szolgáltatóhoz.

11. Az AWS szolgáltatásfiók hitelesítő adataival beolvashatja a szerepköröket az AWS-fiókból az Azure AD-felhasználók üzembe helyezése során. Ehhez nyissa meg az AWS-konzolt kezdőlapját.

12. Válassza a **szolgáltatások**lehetőséget. A **biztonság, identitás & megfelelőség**területen válassza a **iam**lehetőséget.

    ![Képernyőfelvétel az AWS-konzol kezdőlapján, a szolgáltatások és a IAM kiemelve](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. A IAM szakaszban válassza a **házirendek**elemet.

    ![Az IAM szakasz képernyőképe – Kiemelt szabályzatok](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Hozzon létre egy új szabályzatot a szabályzat **létrehozása** elem kiválasztásával a szerepkörök beolvasásához az AWS-fiókból az Azure ad-felhasználók üzembe helyezése szolgáltatásban.

    ![Képernyőfelvétel a szerepkör létrehozása lapról, a szabályzat létrehozása kiemelve](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Hozzon létre saját szabályzatot az AWS-fiókok összes szerepkörének beolvasásához.

    ![Képernyőkép a szabályzat létrehozása lapról a JSON kijelölve](./media/amazon-web-service-tutorial/policy1.png)

    a. A **házirend létrehozása**lapon válassza a **JSON** fület.

    b. A házirend dokumentumban adja hozzá a következő JSON-t:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Válassza a házirend ellenőrzése lehetőséget a szabályzat érvényesítéséhez.

    ![A házirend létrehozása lap képernyőképe](./media/amazon-web-service-tutorial/policy5.png)

16. Adja meg az új szabályzatot.

    ![A házirend létrehozása lap képernyőképe, a név és leírás mezők kiemelve](./media/amazon-web-service-tutorial/policy2.png)

    a. A **név**mezőbe írja be a következőt: **AzureAD_SSOUserRole_Policy**.

    b. A **Leírás**mezőbe írja be a szabályzatot, **amely lehetővé teszi, hogy az AWS-fiókokból beolvassa a szerepköröket**.

    c. Válassza a **házirend létrehozása**lehetőséget.

17. Hozzon létre egy új felhasználói fiókot az AWS IAM szolgáltatásban.

    a. Az AWS IAM-konzolon válassza a **felhasználók**lehetőséget.

    ![Képernyőfelvétel az AWS IAM-konzolról, Kiemelt felhasználók](./media/amazon-web-service-tutorial/policy3.png)

    b. Új felhasználó létrehozásához válassza a **felhasználó hozzáadása**elemet.

    ![A felhasználó hozzáadása gomb képernyőképe](./media/amazon-web-service-tutorial/policy4.png)

    c. A **felhasználó hozzáadása** szakaszban:

    ![A felhasználó hozzáadása lap képernyőképe, a Felhasználónév és a hozzáférés típusa kiemelve](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet **AzureADRoleManager**.

    * A hozzáférési típushoz válassza a **programozott hozzáférés**lehetőséget. Így a felhasználó meghívhatja az API-kat, és beolvashatja a szerepköröket az AWS-fiókból.

    * Válassza a **következő engedélyek**lehetőséget.

18. Hozzon létre egy új szabályzatot ehhez a felhasználóhoz.

    ![Képernyőfelvétel – felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser2.png)

    a. Válassza a **meglévő házirendek csatolása közvetlenül**lehetőséget.

    b. Keresse meg az újonnan létrehozott szabályzatot a **AzureAD_SSOUserRole_Policy**szűrő szakaszban.

    c. Válassza ki a szabályzatot, majd **válassza a Next (tovább) gombot: Tekintse át**.

19. Tekintse át a szabályzatot a csatlakoztatott felhasználó számára.

    ![A felhasználó hozzáadása lap képernyőképe, a felhasználó kiemelve](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználóhoz rendelt felhasználónevet, hozzáférési típust és házirendet.

    b. Válassza a **felhasználó létrehozása**lehetőséget.

20. Töltse le a felhasználó felhasználói hitelesítő adatait.

    ![Képernyőfelvétel – felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja a felhasználói **hozzáférési kulcs azonosítóját** és a **titkos elérési kulcsot**.

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó kiépítési szakaszában a szerepkörök az AWS-konzolról való beolvasásához.

    c. Válassza a **Bezárás**lehetőséget.

21. Az Azure AD felügyeleti portálon, az AWS alkalmazásban lépjen a kiépítés elemre.

    ![Képernyőfelvétel az AWS-alkalmazásról a kiépítés kiemelésével](./media/amazon-web-service-tutorial/provisioning.png)

22. Adja meg a hozzáférési kulcsot és a titkos kódot a **clientsecret** és a **titkos jogkivonat** mezőiben.

    ![Képernyőkép a rendszergazdai hitelesítő adatok párbeszédpanelről](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot a **clientsecret** mezőben.

    b. Adja meg az AWS felhasználói titkot a **titkos jogkivonat** mezőben.

    c. Válassza a **kapcsolatok tesztelése**lehetőséget.

    d. Mentse a beállítást a **Mentés**lehetőség kiválasztásával.

23. A **Beállítások** szakaszban a kiépítési **állapotnál**válassza **a**be lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

    ![Képernyőkép a Settings (beállítások) szakaszról a Kiemelt](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy teszt felhasználót (B. Simon) hoz létre a Azure Portal.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   
   a. A **Név** mezőbe írja a következőt: `B.Simon`.  
   b. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.   
   c. Válassza a **jelszó megjelenítése**lehetőséget, és írja le. Ezután kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon számára engedélyezi az Azure SSO használatát az AWS-hez való hozzáférés megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Amazon Web Services (AWS)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelésről szakasz – Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Képernyőfelvétel – felhasználó hozzáadása](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon**lehetőséget. Ezután válassza a **kiválasztás**lehetőséget.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kiválasztás**lehetőséget.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Amikor kiválasztja az AWS csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az AWS-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Ismert problémák

 * A **kiépítési** szakaszban a **leképezések** alszakasz a "betöltés..." szakaszt jeleníti meg. üzenet, és soha nem jeleníti meg az attribútumok leképezéseit. Az egyetlen jelenleg támogatott kiépítési munkafolyamat az AWS-ből az Azure AD-be való, a felhasználó vagy a csoport hozzárendelése során történő kiválasztáshoz. Az attribútumhoz tartozó hozzárendelések előre meg vannak határozva, és nem konfigurálhatók.
 
 * A **kiépítési** szakasz egyszerre csak egy AWS-bérlőhöz tartozó hitelesítő adatok megadását támogatja. Az összes importált szerepkör az AWS- `appRoles` bérlőhöz tartozó Azure ad [ `servicePrincipal` -objektum](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) tulajdonságára íródik. 
 
   A katalógusból több AWS-bérlőt `servicePrincipals`is hozzáadhat az Azure ad-hoz az üzembe helyezéshez. Van azonban egy ismert probléma, amely nem képes automatikusan írni az összes importált szerepkört a több AWS `servicePrincipals` -ből, amelyet az egyszeri `servicePrincipal` bejelentkezéshez használtak. 
   
   Megkerülő megoldásként a [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) -val kinyerheti az `appRoles` összes importált AWS `servicePrincipal` -t, ahol a kiépítés konfigurálva van. Ezeket a szerepkör-karakterláncokat később is hozzáadhatja `servicePrincipal` az AWS-hez, ahol az SSO konfigurálva van.

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
