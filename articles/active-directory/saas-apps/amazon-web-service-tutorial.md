---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Amazon Web Services (AWS) szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Amazon Web Services (AWS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40fd8217285643aa7d706d194d7f78ba0634dd32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Amazon Web Services (AWS) szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Amazon Web Services (AWS) szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja az Amazon Web Services (AWS) szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Amazon Web Services (AWS) szolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek az Amazon Web Services (AWS) szolgáltatásba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

![Az Azure AD és az AWS kapcsolat diagramja](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Több példányhoz több azonosító is konfigurálható. Példa:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel az Azure **#** AD eltávolítja `https://signin.aws.amazon.com/saml` a , és elküldi a megfelelő értéket, mint a közönség URL-címét az SAML-jogkivonat.

Ezt a módszert a következő okok miatt ajánljuk:

- Minden alkalmazás egyedi X509 tanúsítványt biztosít. Az AWS alkalmazáspéldányok minden példánya ezután más-más tanúsítvány lejárati dátummal rendelkezhet, amely az egyes AWS-fiók alapon kezelhető. Ebben az esetben egyszerűbb a teljes tanúsítványváltás.

- Engedélyezheti a felhasználói kiépítést egy AWS-alkalmazással az Azure AD-ben, majd a szolgáltatás lekéri az összes szerepkört az adott AWS-fiókból. Nem kell manuálisan hozzáadnia vagy frissítenie az AWS szerepköröket az alkalmazásban.

- Az alkalmazás tulajdonosát egyenként rendelheti hozzá az alkalmazáshoz. Ez a személy kezelheti az alkalmazást közvetlenül az Azure AD-ben.

> [!Note]
> Győződjön meg arról, hogy csak galériaalkalmazást használ.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egy AWS egyszeri bejelentkezés (SSO) engedélyezve lévő előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Amazon Web Services (AWS) támogatja az **SP-t és az IDP** által kezdeményezett SSO-t
* Az Amazon Web Services (AWS) konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) hozzáadása a galériából

Az Amazon Web Services (AWS) Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Amazon Web Services (AWS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi fiókkal, iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. Az Azure Active Directory áttekintő menüjében válassza a **Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**
1. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be az **Amazon Web Services (AWS)** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Web Services (AWS)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az Amazon Web Services (AWS) szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást az Amazon Web Services (AWS) szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az Amazon Web Services (AWS) között.

Az Azure AD SSO konfigurálásához és teszteléséhez az Amazon Web Services (AWS) szolgáltatással hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Amazon Web Services (AWS) Egyszeri bejelentkezést](#configure-amazon-web-services-aws-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Amazon Web Services (AWS) teszt felhasználó](#create-amazon-web-services-aws-test-user)** - hogy egy megfelelője B.Simon az Amazon Web Services (AWS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
    1. **[Szerepkör-kiépítés konfigurálása az Amazon Web Services szolgáltatásban (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Amazon Web Services (AWS)** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció szakaszban** az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre ki vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés**lehetőség kiválasztásával.

1. Ha egynél több példányt konfigurál, adjon meg egy azonosító értéket. A második példánytól kezdve használja a **#** következő formátumot, beleértve egy jelet egy egyedi SPN-érték megadásához.

    `https://signin.aws.amazon.com/saml#2`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Amazon Web Services (AWS) beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. Az Azure Active Directory áttekintő menüjében válassza a **Felhasználók** > **minden felhasználó nak**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t az Amazon Web Services (AWS) használatával.

1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. Az Azure Active Directory áttekintő menüjében válassza a **Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**
1. Az alkalmazáslistában válassza az **Amazon Web Services (AWS) lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-amazon-web-services-aws-sso"></a>Az Amazon Web Services (AWS) sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AWS vállalati webhelyére rendszergazdaként.

2. Válassza **az AWS Kezdőlap**lehetőséget.

    ![Képernyőkép az AWS vállalati webhelyéről, kiemelve az AWS Home ikonja][11]

3. Válassza **az Identitás- és hozzáférés-kezelés lehetőséget.**

    ![Képernyőkép az AWS szolgáltatások lapról, kiemelve az IAM-et][12]

4. Válassza **az Identitásszolgáltatók** > **létrehozása szolgáltató t.**

    ![Képernyőkép az IAM lapról, amelyen az Identitásszolgáltatók és a Szolgáltató létrehozása lehetőség van kiemelve][13]

5. A **Szolgáltató konfigurálása** lapon hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Szolgáltató konfigurálása][14]

    a. A **Szolgáltató típusa mezőben**válassza az **SAML lehetőséget.**

    b. A **Szolgáltató neve**mezőbe írja be a szolgáltató nevét (például *WAAD*).

    c. Ha a letöltött **metaadatfájlt** az Azure Portalról szeretné feltölteni, válassza a **Fájl kiválasztása lehetőséget.**

    d. Válassza a **Következő lépés lehetőséget.**

6. A **Szolgáltatóadatainak ellenőrzése** lapon válassza a **Létrehozás gombot.**

    ![Képernyőkép a Szolgáltatóadatai ellenőrzés ről kiemelt Létrehozás lehetőséggel][15]

7. Válassza a **Szerepkörlétrehozása** > **szerepkör lehetőséget.**

    ![Képernyőkép a Szerepkörök lapról][16]

8. A **Szerepkör létrehozása** lapon hajtsa végre az alábbi lépéseket:  

    ![Képernyőkép: Szerepkör létrehozása lap][19]

    a. A **Megbízható entitás típusának kiválasztása**csoportban válassza az **SAML 2.0 összevonás**lehetőséget.

    b. A **Válasszon SAML 2.0 szolgáltatót**csoportban válassza ki a korábban létrehozott **SAML-szolgáltatót** (például *WAAD).*

    c. Válassza **az Programozott és Az AWS Felügyeleti konzol elérésének engedélyezése**lehetőséget.
  
    d. Válassza a **Tovább lehetőséget: Engedélyek**.

9. Az **Engedélyek csatolása házirendek** párbeszédpanelen csatolja a megfelelő házirendet a szervezetenként. Ezután válassza **a Tovább: Véleményezés**lehetőséget.  

    ![Képernyőkép: Az Engedélyek csatolása házirend párbeszédpanel][33]

10. A **Véleményezés** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Véleményezés párbeszédpanel][34]

    a. A **Szerepkör neve**mezőbe írja be a szerepkör nevét.

    b. A **Szerepkör leírása**mezőbe írja be a leírást.

    c. Válassza **a Szerepkör létrehozása**lehetőséget.

    d. Hozzon létre annyi szerepkört, amennyi szükséges, és rendelje hozzá juk az identitásszolgáltatóhoz.

11. Az AWS szolgáltatásfiók hitelesítő adataival lekéri a szerepköröket az AWS-fiókból az Azure AD-felhasználói kiépítésben. Ehhez nyissa meg az AWS konzol otthonát.

12. Válassza a **Szolgáltatások**lehetőséget. A **Biztonság, identitás & megfelelőség csoportban**válassza az **IAM**lehetőséget.

    ![Képernyőkép az AWS konzol kezdőlapjáról, kiemelve a Szolgáltatások és az IAM szolgáltatással](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Az IAM szakaszban válassza a **Házirendek**lehetőséget.

    ![Képernyőkép az IAM szakaszról, kiemelve a Házirendek](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Hozzon létre egy új szabályzatot a **Szabályzat létrehozása** lehetőséget az Azure AD-felhasználói kiépítés AWS-fiókjából való szerepkörök lehívására.

    ![Képernyőkép: Szerepkör létrehozása lap, kiemelve a Házirend létrehozása](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Hozzon létre saját szabályzatot az Összes szerepkör AWS-fiókból történő lekéréséhez.

    ![Képernyőkép: Házirend létrehozása lap, kiemelve a JSON-t](./media/amazon-web-service-tutorial/policy1.png)

    a. A **Házirend létrehozása csoportban**válassza a **JSON** lapot.

    b. A házirend-dokumentumban adja hozzá a következő JSON-t:

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

    c. Válassza **a Házirend ellenőrzése** lehetőséget a házirend érvényesítéséhez.

    ![Képernyőkép: Házirend létrehozása lap](./media/amazon-web-service-tutorial/policy5.png)

16. Határozza meg az új házirendet.

    ![Képernyőkép: Házirend létrehozása lap, kiemelt Név és Leírás mezőkkel](./media/amazon-web-service-tutorial/policy2.png)

    a. A **Név mezőbe**írja be **AzureAD_SSOUserRole_Policy.**

    b. A **Leírás mezőbe**írja be Ezt a **házirendet, amely lehetővé teszi a szerepkörök beolvasását az AWS-fiókokból**.

    c. Válassza a **Create policy** (Szabályzat létrehozása) lehetőséget.

17. Hozzon létre egy új felhasználói fiókot az AWS IAM szolgáltatásban.

    a. Az AWS IAM konzolon válassza a **Felhasználók**lehetőséget.

    ![Képernyőkép az AWS IAM konzolról, kiemelve a Felhasználók](./media/amazon-web-service-tutorial/policy3.png)

    b. Új felhasználó létrehozásához válassza a **Felhasználó hozzáadása**lehetőséget.

    ![Képernyőkép: Felhasználó hozzáadása gomb](./media/amazon-web-service-tutorial/policy4.png)

    c. A **Felhasználó hozzáadása** szakaszban:

    ![Képernyőkép: Felhasználó hozzáadása lap, kiemelt Felhasználónév és Access-típus](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet **AzureADRoleManager**néven.

    * A hozzáférési típushoz válassza a **Programozott hozzáférés**lehetőséget. Így a felhasználó meghívhatja az API-kat, és lehívhatja a szerepköröket az AWS-fiókból.

    * Válassza a **Következő engedélyek lehetőséget.**

18. Hozzon létre egy új házirendet a felhasználó számára.

    ![Képernyőkép: Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser2.png)

    a. Válassza **a Meglévő házirendek közvetlen csatolása**lehetőséget.

    b. Keresse meg az újonnan létrehozott házirendet a szűrőszakaszban **AzureAD_SSOUserRole_Policy.**

    c. Jelölje ki a házirendet, majd válassza **a Tovább: Véleményezés**lehetőséget.

19. Tekintse át a házirendet a csatolt felhasználónak.

    ![Képernyőkép: Felhasználó hozzáadása lap, kiemelt Felhasználó létrehozása](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználó név, a hozzáférés típusát és a felhasználóhoz leképezett házirendet.

    b. Válassza **a Felhasználó létrehozása**lehetőséget.

20. Töltse le a felhasználó hitelesítő adatait.

    ![Képernyőkép: Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja a felhasználói **hozzáférési kulcs azonosítóját** és **a titkos hozzáférési kulcsot.**

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó üzembe létesítési szakaszba a szerepkörök a Wsk konzolról történő lekéréséhez.

    c. Kattintson a **Bezárás** gombra.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Szerepkör-kiépítés konfigurálása az Amazon Web Services szolgáltatásban (AWS)

1. Az Azure AD felügyeleti portálon, az AWS alkalmazásban, nyissa **meg a kiépítés.**

    ![Képernyőkép az AWS alkalmazásról, kiemelve a Kiépítés lehetőségről](./media/amazon-web-service-tutorial/provisioning.png)

2. Adja meg a hozzáférési kulcsot és a titkos kulcsot az **ügyféltitkos és** **titkos jogkivonat** mezőkben.

    ![Képernyőkép a Rendszergazdai hitelesítő adatok párbeszédpanelről](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Írja be az AWS felhasználói hozzáférési kulcsát az **ügyféltitkos** mezőbe.

    b. Írja be az AWS felhasználói titkoskulcsát a **Titkos jogkivonat** mezőbe.

    c. Válassza **a Kapcsolat tesztelése**lehetőséget.

    d. Mentse a beállítást a **Mentés**lehetőség kiválasztásával.

3. A **Beállítások** csoport **Kiépítés állapota**csoportban válassza **a Be**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

    ![Képernyőkép a Beállítások szakaszról, kiemelve a Bekapcsolva lehetőséggel](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> A kiépítési szolgáltatás csak az AWS-ből importálja a szerepköröket az Azure AD-be. A szolgáltatás nem nyújt ki felhasználókat és csoportokat az Azure AD-től az AWS-hez.

> [!NOTE]
> A létesítési hitelesítő adatok mentése után meg kell várnia a kezdeti szinkronizálási ciklus futtatását. A szinkronizálás általában körülbelül 40 percet vesz igénybe. Az állapot a **Kiépítés** lap alján, az **Aktuális állapot csoportban**látható.

### <a name="create-amazon-web-services-aws-test-user"></a>Amazon Web Services (AWS) tesztfelhasználó létrehozása

A szakasz célja egy B.Simon nevű felhasználó létrehozása az Amazon Web Services (AWS) szolgáltatásban. Az Amazon Web Services (AWS) szolgáltatásnak nincs szüksége arra, hogy az SSO rendszerében felhasználót hozhassanak létre, így itt nem kell semmilyen műveletet végrehajtania.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen az Amazon Web Services (AWS) csempére kattint, automatikusan be kell jelentkeznie az Amazon Web Services (AWS) szolgáltatásba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="known-issues"></a>Ismert problémák

 * A **Kiépítés** szakaszban a **Leképezések** alszakasz "Betöltés..." üzenetet, és soha nem jeleníti meg az attribútumleképezéseket. Az egyetlen üzembe építési munkafolyamat ma támogatott a szerepkörök importálása az AWS-ből az Azure AD-be a felhasználó vagy csoport hozzárendelése során kiválasztásra. Az attribútum-hozzárendelések előre meghatározottak, és nem konfigurálhatók.

 * A **kiépítés** szakasz csak azt támogatja, hogy egyszerre csak egy AWS-bérlő hitelesítő adatait adja meg. Az összes importált szerepkör `appRoles` az AWS-bérlő Azure AD-objektumának [ `servicePrincipal` ](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) tulajdonságába van írva.

   Több AWS-bérlő (által képviselt) `servicePrincipals`hozzáadható az Azure AD-hez a katalógusból a kiépítéshez. Van azonban egy ismert probléma, mivel nem tudja automatikusan írni az összes `servicePrincipals` importált szerepkört a `servicePrincipal` több AWS kiépítéséhez használt egyszeri használatú egyszeri szolgáltatásba.

   Kerülő megoldásként a [Microsoft Graph API segítségével](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) kibonthatja az `appRoles` `servicePrincipal` összes importált minden AWS,ahol a kiépítés konfigurálva van. Ezt követően hozzáadhatja ezeket a `servicePrincipal` szerepkör-karakterláncokat az AWS-hez, ahol az SSO konfigurálva van.

* A szerepköröknek meg kell felelniük az alábbi követelményeknek ahhoz, hogy jogosultak legyenek az AWS-ből az Azure AD-be történő importálásra:

  * A szerepköröknek pontosan egy saml-szolgáltatóval kell rendelkezniük az AWS-ben definiálva.

  * Az ARN szerepkör és az importált szerepkör SAML-szolgáltató ARN szerepkörének együttes hossza legfeljebb 119 karakter lehet.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Amazon Web Services (AWS) szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védheti meg az Amazon Web Services (AWS) szolgáltatást a fejlett láthatóságés vezérlők](https://docs.microsoft.com/cloud-app-security/protect-aws)

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
