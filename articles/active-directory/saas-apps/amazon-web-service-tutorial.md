---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Amazon Web Services (AWS) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Amazon Web Services (AWS) között.
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
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fe362eb90793c831fc48d6fdc5a871c12e1b560
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442766"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Oktatóanyag: Amazon Web Servicesből (AWS) integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Amazon Web Services (AWS) Azure Active Directoryval (Azure AD) lesz. Az Amazon Web Services (AWS) integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozhatja, aki hozzáfér az Amazon Web Services (AWS) Azure AD-ben.
* Engedélyezze a felhasználóknak kell automatikus bejelentkezésre az Amazon Web Services (AWS) Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)?](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Az alábbi, több azonosítók több példány esetén is beállíthatja.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Ezekkel az értékekkel, az Azure AD eltávolítja az értékét **#** és a megfelelő értéket küldése `https://signin.aws.amazon.com/saml` a a SAML-jogkivonat célközönség URL-címként.

**Ezt a módszert használja a következő esetekben javasoljuk:**

a. Mindegyik alkalmazáshoz biztosít Önnek egy egyedi X509 tanúsítványt. Az AWS-alkalmazáspéldány minden példánya akkor is rendelkezik egy másik tanúsítvány lejárati dátuma, amely AWS fiók egyenként kezelheti. A teljes tanúsítványváltás könnyebb lesz ebben az esetben.

b. Engedélyezheti a felhasználók átadásának az AWS-alkalmazáshoz az Azure ad-ben, és hogy szolgáltatásunkat, majd beolvassa a szerepkörök, az AWS-fiókból. Nem kell manuálisan adja hozzá, vagy frissíteni az AWS-szerepkörök az alkalmazást.

c. Az alkalmazás tulajdonosa egyedileg az alkalmazás számára az alkalmazás közvetlenül az Azure ad-ben kezelésére jogosult rendelhet hozzá.

> [!Note]
> Ellenőrizze, hogy csak a katalógus-alkalmazással

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Az Amazon Web Services (AWS) egyszeri bejelentkezéses (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Az Amazon Web Services (AWS) támogatja a **SP és IDP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Az Amazon Web Services (AWS) hozzáadása a katalógusból

A-integráció konfigurálása az Amazon Web Services (AWS) Azure AD-be, szüksége a katalógusból az Amazon Web Services (AWS) hozzáadása a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Amazon Web Services (AWS)** kifejezést a keresőmezőbe.
1. Válassza ki **Amazon Web Services (AWS)** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO-t az Amazon Web Services (AWS) nevű tesztfelhasználó használata **B.Simon**. Az SSO működjön az Amazon Web Services (AWS) Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre kell.

Az Azure AD SSO-t az Amazon Web Services (AWS) tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Amazon Web Services (AWS) konfigurálása](#configure-amazon-web-services-aws)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Az Amazon Web Services (AWS) tesztfelhasználó létrehozása](#create-amazon-web-services-aws-test-user)**  szeretné, hogy egy megfelelője a B.Simon az Amazon Web Services (AWS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Amazon Web Services (AWS)** alkalmazás integráció lapon keresse meg a **kezelés** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban az alkalmazás előre konfigurálva, és a szükséges URL-címek vannak már előre fel van töltve az Azure-ral. A felhasználónak szüksége van a konfiguráció mentéséhez kattintson a **mentése** gombra.

5. Ha több példány konfigurál, adjon meg azonosító értékét. A második példány az újabb verziók esetében adjon meg azonosító értékét a következő formátumban. Használjon egy **#** SPN egyedi értéket adjon meg bejelentkezni.

    `https://signin.aws.amazon.com/saml#2`

6. Az Amazon Web Services (AWS) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Emellett a fent Amazon Web Services (AWS) alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név)  | Adatforrás-attribútum  | Névtér |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Szerepkör            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Adjon meg egy értéket 900 másodperc (15 perc) között 43200 másodperc (12 óra)" |  https://aws.amazon.com/SAML/Attributes |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **Namespace** szövegmezőbe írja be az adott sorhoz feltüntetett Namespace értéket.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **állítsa be az Amazon Web Services (AWS)** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Az Amazon Web Servicesből (AWS) konfigurálása

1. Egy másik böngészőablakban bejelentkezést az Amazon Web Services (AWS) vállalat webhelye rendszergazdaként.

2. Kattintson a **AWS kezdőlap**.

    ![Kezdőlap egyszeri bejelentkezés konfigurálása][11]

3. Kattintson a **identitás és hozzáférés-kezelés**.

    ![Egyszeri bejelentkezési identitás konfigurálása][12]

4. Kattintson a **Identitásszolgáltatók**, és kattintson a **létrehozása szolgáltató**.

    ![Egyszeri bejelentkezés szolgáltató konfigurálása][13]

5. Az a **konfigurálása szolgáltató** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés párbeszédpanel konfigurálása][14]

    a. Mint **szolgáltatótípus**válassza **SAML**.

    b. Az a **szolgáltatónevet** szövegmezőbe írja be a szolgáltató nevét (például: *WAAD*).

    c. A letöltött feltöltéséhez **metaadatait tartalmazó fájl** kattintson az Azure Portalról, **fájl kiválasztása**.

    d. Kattintson a **következő lépés**.

6. Az a **ellenőrizze a szolgáltató adatait** párbeszédpanel lap, kattintson a **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása ellenőrzése][15]

7. Kattintson a **szerepkörök**, és kattintson a **szerepkör létrehozása**.

    ![Egyszeri bejelentkezés szerepkörök konfigurálása][16]

8. Az a **szerepkör létrehozása** lapon, a következő lépésekkel:  

    ![Egyszeri bejelentkezés megbízhatósági kapcsolat beállítása][19]

    a. Válassza ki **SAML 2.0 összevonási** alatt **válassza ki a megbízható entitás típusa**.

    b. A **SAML 2.0-s szolgáltató szakasz választása**, jelölje be a **SAML-szolgáltató** korábban létrehozott (például: *WAAD*)

    c. Válassza ki **programozott engedélyezése és az AWS-felügyeleti konzol hozzáférés**.
  
    d. Kattintson a **tovább: Engedélyek**.

9. Az a **engedélyházirend csatolása** párbeszédpanelen csatlakoztassa a megfelelő házirend beállításával a szervezet. Kattintson a **tovább: Felülvizsgálat**.  

    ![Egyszeri bejelentkezési szabályzat konfigurálása][33]

10. Az a **felülvizsgálati** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Konfigurálja az egyszeri bejelentkezés áttekintése][34]

    a. Az a **szerepkörnév** szövegmezőbe írja be a szerepkör nevét.

    b. Az a **szerepkör leírása** szövegmezőbe írja be a leírást.

    c. Kattintson a **szerepkör létrehozása**.

    d. Hozzon létre annyi szerepköröket, igény szerint, és megfeleltet az identitásszolgáltató.

11. A szerepkörök beolvasása az AWS-fiók az Azure AD-felhasználó kiépítési használni az AWS szolgáltatásfiók hitelesítő adatai. Ehhez nyissa meg az otthoni AWS konzolon.

12. Kattintson a **szolgáltatások** -> **Security, Identity & Compliance** -> **IAM**.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Válassza ki a **házirendek** lap IAM szakaszában.

    ![a szerepkörök beolvasása az AWS-fiókból](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Új szabályzat létrehozásához kattintson a **szabályzat létrehozása** az AWS-fiók az Azure AD-felhasználó kiépítési a szerepkörök beolvasása.

    ![Új házirend létrehozása](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. A szerepkörök beolvasása az AWS-fiókok a következő lépések elvégzésével a saját szabályzat létrehozása:

    ![Új házirend létrehozása](./media/amazon-web-service-tutorial/policy1.png)

    a. Az a **"Szabályzat létrehozása"** szakaszban kattintson a **"JSON"** fülre.

    b. A házirend-dokumentum, adja hozzá az alábbi JSON-ban.

    ```

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

    c. Kattintson a **tekintse át a házirend gomb** érvényesíteni a szabályzatot.

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy5.png)

16. Adja meg a **új szabályzat** a következő lépések végrehajtásával:

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy2.png)

    a. Adja meg a **házirendnév** , **AzureAD_SSOUserRole_Policy**.

    b. Megadhat **leírás** , a szabályzat **Ez a szabályzat lehetővé teszi a szerepkörök beolvasása az AWS-fiókok**.

    c. Kattintson a **"Házirend létrehozása"** gombra.

17. Hozzon létre egy új felhasználói fiók az AWS IAM-Service-ben a következő lépések végrehajtásával:

    a. Kattintson a **felhasználók** navigációs az AWS IAM-konzolon.

    ![Az új szabályzat definiálása](./media/amazon-web-service-tutorial/policy3.png)

    b. Kattintson a **felhasználó hozzáadása** gombra kattintva hozzon létre egy új felhasználót.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/policy4.png)

    c. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser1.png)

    * Adja meg a felhasználónevet, **AzureADRoleManager**.

    * A hozzáférés típusa, válassza ki a **programozás alapú hozzáférést** lehetőséget. Ezzel a módszerrel a felhasználó elindíthat az API-k és a szerepkörök beolvasása az AWS-fiókból.

    * Kattintson a **tovább engedélyek** gombra a jobb alsó sarokban.

18. Most hozzon létre egy új szabályzatot a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kattintson a **a meglévő szabályzatok közvetlen csatolása** gombra.

    b. Keresse meg az újonnan létrehozott szabályzat szakaszban **AzureAD_SSOUserRole_Policy**.

    c. Válassza ki a **házirend** majd kattintson a a **tovább: Felülvizsgálat** gombra.

19. Tekintse át a szabályzatot a csatolt felhasználóhoz a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser3.png)

    a. Tekintse át a felhasználónevet, a hozzáférés típusa, és a szabályzat hozzárendelve a felhasználóhoz.

    b. Kattintson a **felhasználó létrehozása** gombra, és hozza létre a felhasználót a jobb alsó sarokban.

20. Töltse le a felhasználói hitelesítő adatait, a felhasználó a következő lépések végrehajtásával:

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/adduser4.png)

    a. Másolja ki a felhasználó **hozzáférési kulcs azonosítója** és **titkos hívóbetűje**.

    b. Adja meg ezeket a hitelesítő adatokat az Azure AD-felhasználó kiépítésére szakasz a szerepkörök beolvasása az AWS-konzolról.

    c. Kattintson a **Bezárás** gombra a lap alján.

21. Navigáljon a **Felhasználókiépítés** Amazon Web Services-alkalmazás az Azure AD felügyeleti portál szakaszában.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning.png)

22. Adja meg a **hozzáférési kulcs** és **titkos** a a **titkos Ügyfélkód** és **titkos jogkivonat** mező jelölik.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Adja meg az AWS felhasználói hozzáférési kulcsot a a **clientsecret** mező.

    b. Adja meg az AWS – felhasználói titkos a **titkos jogkivonat** mező.

    c. Kattintson a **kapcsolat tesztelése** gombra kattintva kell sikeresen tesztelheti ezt a kapcsolatot.

    d. A beállítás mentéséhez kattintson a a **mentése** gombra az oldal tetején.

23. Most győződjön meg arról, hogy engedélyezze-e az üzembe helyezési állapotra **a** a kapcsoló meg és kattintson a beállítások szakaszában a **mentése** gombra az oldal tetején.

    ![Felhasználó hozzáadása](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával az Amazon Web Services (AWS) Azure egyszeri bejelentkezés használatára B.Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Amazon Web Services (AWS)** .
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-amazon-web-services-aws-test-user"></a>Az Amazon Web Services (AWS) tesztfelhasználó létrehozása

Ez a szakasz célja egy B.Simon az Amazon Web Services (AWS) nevű felhasználó létrehozásához. Az Amazon Web Services (AWS) nem kell a felhasználót, hogy hozhatók létre a rendszer flexibilitását egyszeri Bejelentkezést, így nem szükséges itt bármely művelet elvégzésére.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Az Amazon Web Services (AWS) csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a, az Amazon Web Services (AWS), amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Ismert problémák

 * A a **kiépítési** szakaszban a **leképezések** alterület bemutatják egy "Betöltése..." üzenetet és soha nem jelenít meg az attribútumleképezések. A csak a jelenleg támogatott kiépítési munkafolyamat az importálás a szerepkörök az AWS-től a felhasználó vagy csoport-hozzárendelés során kiválasztható Azure AD-be. Ez az attribútum-leképezéshez az előre meghatározott, és nem módosítható.
 
 * A **kiépítési** szakasz csak támogatja hitelesítőadat megadása egy AWS-bérlő egyszerre. Minden importált szerepkörök az Azure AD appRoles tulajdonságát írt [servicePrincipal objektumot](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) az AWS bérlői. A katalógusból történő üzembe helyezés esetében azonban van egy ismert probléma az nem tudja automatikusan írhat összes importált szerepkört a több AWS-szolgáltatásnevek használt több AWS-bérlő (szolgáltatásnevek jelöli) is hozzáadhatók az Azure ad-hez az egyszeri bejelentkezéshez használt egyetlen servicePrincipal kiépíteni. Áthidaló megoldásként a [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) segítségével csomagolja ki minden egyes AWS servicePrincipal importálja a appRoles ahol kiépítés van konfigurálva. A szerepkör karakterláncait ezt követően lehet hozzáadni az AWS servicePrincipal, ahol egyszeri bejelentkezésre van konfigurálva.

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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