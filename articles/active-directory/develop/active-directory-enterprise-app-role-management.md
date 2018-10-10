---
title: Az Azure AD-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott szerepkörjogcím konfigurálása |} A Microsoft Docs
description: Ismerje meg a vállalati alkalmazások esetén az Azure Active Directory a SAML-jogkivonatban kiadott szerepkörjogcím konfigurálása
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 2bb9d69487b8576cdae60a1a613a341898495f06
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904346"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott szerepkörjogcím konfigurálása

Azure Active Directory (Azure AD) segítségével testre szabhatja a jogcím típusa a szerepkör jogcím a válasz token kap, miután engedélyezi, hogy egy alkalmazás számára.

## <a name="prerequisites"></a>Előfeltételek

- Egy directory beállítása az Azure AD-előfizetéshez.
- Egy előfizetés, amely egyszeri bejelentkezés (SSO) engedélyezve van. Az alkalmazás egyszeri bejelentkezési kell konfigurálnia.

## <a name="when-to-use-this-feature"></a>Ez a funkció használata

Ha az alkalmazás egyéni átadni egy SAML-válasz-szerepkörök vár, szüksége a funkció használatához. Tetszőleges számú szerepkörök vissza az Azure ad-ből az alkalmazás átadandó igény szerint hozhat létre.

## <a name="create-roles-for-an-application"></a>Az alkalmazás-szerepkörök létrehozása

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory ikonra][1]

2. Válassza ki **vállalati alkalmazások**. Válassza ki **minden alkalmazás**.

    ![Vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    !["Új alkalmazás" gombra][3]

4. A Keresés mezőbe írja be az alkalmazás nevét, és válassza ki az alkalmazást az eredmény panelen. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában alkalmazás](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Miután hozzáadta az alkalmazást, nyissa meg a **tulajdonságok** lapjáról, és másolja az objektumazonosítót.

    ![Tulajdonságok lap](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban, és hajtsa végre az alábbi lépéseket:

    a. Jelentkezzen be a Graph Explorer hely a bérlőhöz tartozó globális rendszergazdai vagy társfelügyeletű hitelesítő adataival.

    b. A szerepkörök létrehozásához szükséges engedélyekkel van szüksége. Válassza ki **engedélyek módosítása** beolvasni az engedélyeket.

      ![A "engedélyek módosítása" gombra](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Válassza ki a következő engedélyeket a (ha ezek még nem rendelkezik) lista, és válassza a **módosítási hozzáférést**.

      ![Engedélyek és a "Engedélyek módosítása" gombra](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Felhőalapú alkalmazás-rendszergazda és az alkalmazás-rendszergazda szerepkör ebben a forgatókönyvben nem fog működni, mivel később a globális rendszergazdai engedélyek a könyvtár olvasási és írási.

    d. Fogadja el a feltételeket. Bejelentkezve, a rendszer újra.

    e. Módosítsa a verzióra **béta**, és a szolgáltatásnevek listájának beolvasásához a bérlőről használatával a következő lekérdezést:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Ha több címtárat használ, kövesse az ezt a mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Graph Explorer párbeszédpanel, a lekérdezés egyszerű szolgáltatások beolvasása közben](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Jelenleg is már zajlik frissítése az API-k, így a vevők előfordulhat, hogy néhány zavart a szolgáltatásban.

    f. A listából lekért szolgáltatásnevek beolvasása, amely módosítania kell. Ctrl + F használatával keresse meg az összes felsorolt elhelyeznie az alkalmazást. Keresse meg az Objektumazonosítót fájlból másolt a **tulajdonságok** lapon, és használja a következő lekérdezést az egyszerű szolgáltatás beolvasásához:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Bevezetés a szolgáltatásnevet, hogy módosítania kell a lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Bontsa ki a **appRoles** szolgáltatásnév-objektum a tulajdonságot.

      ![A appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Ha az egyéni alkalmazás (nem az Azure Marketplace-alkalmazás) használ, két alapértelmezett szerepkör látja: felhasználói és msiam_access. A Marketplace-alkalmazás msiam_access olyan csak alapértelmezett szerepkör. Végezze el a módosításokat az alapértelmezett szerepköröket nincs szükségünk.

    h. Az alkalmazás új szerepkörök létrehozásához.

      A következő JSON-ja egy példát a **appRoles** objektum. Hozzon létre egy hasonló objektumot adja hozzá a szerepkört, amelyet az alkalmazáshoz.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > Új szerepkörök csak a javítási művelet msiam_access után adhat hozzá. Is mint a szervezet igényeinek megfelelően tetszőleges számú szerepkörök is hozzáadhat. Az Azure AD elküldi ezeket a szerepköröket értékét az SAML-válasz a jogcím értéke. A globálisan egyedi Azonosítót létrehozni új szerepkörök azonosítója értékeit használja a webes eszközökkel, például [Ez](https://www.guidgenerator.com/)

    i. Lépjen vissza a Graph Explorer, és a módszert a **első** való **javítására**. Szeretné, hogy a kívánt szerepkörök frissítésével szolgáltatásnév-objektum javítani a **appRoles** hasonló az előző példában tulajdonság. Válassza ki **lekérdezés futtatása** a javítási művelet végrehajtásához. A sikert jelző üzenet megerősíti, hogy a szerepkör létrehozása.

      ![A javítási művelet sikert jelző üzenettel](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Után az egyszerű szolgáltatás javított tartományvezérlőről érkezett, a további szerepkörök, felhasználók rendelhet szerepét. Ugrás a portálra, és keresse meg az alkalmazás a felhasználókat rendelhet. Válassza ki a **felhasználók és csoportok** fülre. Ez a lap felsorolja az összes felhasználók és csoportok, amelyek az alkalmazás már hozzá van rendelve. Az új szerepkörök is hozzáadhat új felhasználókat. Is is válasszon egy meglévő felhasználó, és válasszon **szerkesztése** módosíthatja a szerepkört.

    !["Felhasználók és csoportok" lap](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Rendelje hozzá a szerepkört minden olyan felhasználó, válassza ki az új szerepkör, és válassza ki a **hozzárendelése** gombra a lap alján.

    !["Hozzárendelés szerkesztése" és "Szerepkör kiválasztása" ablaktábla](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Meg kell frissítenie a munkamenetet új szerepkörök megtekintéséhez az Azure Portalon.

8. Frissítés a **attribútumok** tábla egy testre szabott leképezés a szerepkör jogcím meghatározásához.

9. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket.

    | Attribútum neve | Hodnota atributu |
    | -------------- | ----------------|
    | Szerepkörnév  | User.assignedroles |

    a. Válassza ki **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** ablaktáblán.

      !["Attribútum hozzáadása" gomb](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Attribútum hozzáadása" panel](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Az a **neve** mezőbe írja be azon attribútum nevét, igény szerint. Ez a példa **szerepkörnév** a jogcím neve.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** mező üres.

    e. Kattintson az **OK** gombra.

10. Az alkalmazás teszteléséhez a egy egyszeri bejelentkezést az identitásszolgáltató által kezdeményezett, jelentkezzen be a [hozzáférési Panel](https://myapps.microsoft.com) és az alkalmazás csempe kiválasztásával. A hozzárendelt szerepkörök a jogcím neve, amely az adott felhasználó megtekintheti a SAML-jogkivonat.

## <a name="update-an-existing-role"></a>Egy meglévő szerepkör frissítése

Meglévő szerepkör-frissítéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg [az Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Jelentkezzen be a Graph Explorer hely a bérlőhöz tartozó globális rendszergazdai vagy társfelügyeletű hitelesítő adataival.

3. Módosítsa a verzióra **béta**, és a szolgáltatásnevek listájának beolvasásához a bérlőről használatával a következő lekérdezést:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több címtárat használ, kövesse az ezt a mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanel, a lekérdezés egyszerű szolgáltatások beolvasása közben](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A listából lekért szolgáltatásnevek beolvasása, amely módosítania kell. Ctrl + F használatával keresse meg az összes felsorolt elhelyeznie az alkalmazást. Keresse meg az Objektumazonosítót fájlból másolt a **tulajdonságok** lapon, és használja a következő lekérdezést az egyszerű szolgáltatás beolvasásához:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Bevezetés a szolgáltatásnevet, hogy módosítania kell a lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Bontsa ki a **appRoles** szolgáltatásnév-objektum a tulajdonságot.

    ![A appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. A meglévő szerepkör frissítéséhez használja az alábbi lépéseket.

    ![Kérelem törzse a "Javítás" a "leírás" és "displayname" kiemelésével](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. A módszert a **első** való **javítására**.

    b. A meglévő szerepköröket másolja és illessze be őket a **kérelem törzse**.

    c. Frissítse az értéket, a szerepkör frissítésével a szerepkör leírása, szerepkör-érték vagy szerepkör megjelenített neve, igény szerint.

    d. Miután frissítette az összes szükséges szerepkörök, válassza ki a **lekérdezés futtatása**.

## <a name="delete-an-existing-role"></a>Egy meglévő szerepkör törlése

Egy meglévő szerepkör törléséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

2. Jelentkezzen be a Graph Explorer hely a bérlőhöz tartozó globális rendszergazdai vagy társfelügyeletű hitelesítő adataival.

3. Módosítsa a verzióra **béta**, és a szolgáltatásnevek listájának beolvasásához a bérlőről használatával a következő lekérdezést:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több címtárat használ, kövesse az ezt a mintát: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanel, a lekérdezés a szolgáltatásnevek listájának beolvasása](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A listából lekért szolgáltatásnevek beolvasása, amely módosítania kell. Ctrl + F használatával keresse meg az összes felsorolt elhelyeznie az alkalmazást. Keresse meg az Objektumazonosítót fájlból másolt a **tulajdonságok** lapon, és használja a következő lekérdezést az egyszerű szolgáltatás beolvasásához:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Bevezetés a szolgáltatásnevet, hogy módosítania kell a lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Bontsa ki a **appRoles** szolgáltatásnév-objektum a tulajdonságot.

    ![A szolgáltatásnév-objektum appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. A meglévő szerepkör törléséhez használja a következő lépéseket.

    ![A "Javítás", "false" értékűre IsEnabled az kérelem törzse](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. A módszert a **első** való **javítására**.

    b. Másolja át az alkalmazás a meglévő szerepköröket, és illessze be őket a **kérelem törzse**.

    c. Állítsa be a **IsEnabled** értéket a következőre **hamis** a törölni kívánt szerepkör esetében.

    d. Válassza ki **lekérdezés futtatása**.

    > [!NOTE]
    > Győződjön meg arról, hogy msiam_access szerepkörrel rendelkeznek, és az azonosítója a generált szerepkörben van megfelelő.

7. A szerepkör le van tiltva, miután törölni a szerepkör kódrészleten az **appRoles** szakaszban. Tartsa a módját **javítására**, és válassza ki **lekérdezés futtatása**.

8. A lekérdezés futtatása után a rendszer törli a szerepkört.

    > [!NOTE]
    > A szerepkör kell azt eltávolítása előtt le kell tiltani.

## <a name="next-steps"></a>További lépések

További lépésekért lásd: a [alkalmazás dokumentációjának](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png