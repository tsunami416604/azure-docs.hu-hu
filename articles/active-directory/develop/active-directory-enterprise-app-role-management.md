---
title: Szerepkör-jogcím konfigurálása nagyvállalati Azure AD-alkalmazásokhoz | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként konfigurálhatja az Azure Active Directoryban működő nagyvállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcímet
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8db27819b7eef6cdf05ea3f6645ae930ebc4ef58
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884749"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: Konfigurálja a vállalati alkalmazások SAML-jogkivonatában kiadott szerepkör-jogcímet

Az Azure Active Directory (Azure AD) használatával testreszabhatja a szerepkörjogcím jogcím típusát a válaszjogkivonatban, amelyet az alkalmazás engedélyezése után kap.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD-előfizetés címtárbeállítással.
- Olyan előfizetés, amelynek egyszeri bejelentkezése (SSO) engedélyezve van. Az sso-t az alkalmazással kell konfigurálnia.

## <a name="when-to-use-this-feature"></a>Mikor kell használni ezt a funkciót?

Ha az alkalmazás elvárja, hogy az egyéni szerepkörök átadása egy SAML-válaszban, akkor ezt a funkciót kell használnia. Annyi szerepkört hozhat létre, amennyit vissza kell adnia az Azure AD-ből az alkalmazásnak.

## <a name="create-roles-for-an-application"></a>Szerepkörök létrehozása alkalmazáshoz

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza ki az **Azure Active Directory** ikonját.

    ![Az Azure Active Directory ikonja][1]

2. Válassza **a Vállalati alkalmazások lehetőséget**. Ezután válassza **a Minden alkalmazás lehetőséget.**

    ![Vállalati alkalmazások ablaktábla][2]

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszédpanel tetején.

    !["Új alkalmazás" gomb][3]

4. A keresőmezőbe írja be az alkalmazás nevét, majd válassza ki az alkalmazást az eredménypanelen. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Alkalmazás az eredménylistában](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Az alkalmazás hozzáadása után lépjen a **Tulajdonságok** lapra, és másolja az objektumazonosítót.

    ![Tulajdonságok lap](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Nyissa meg a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) alkalmazást egy másik ablakban, és tegye a következő lépéseket:

    a. Jelentkezzen be a Graph Explorer webhelyére a bérlő globális rendszergazdai vagy társrendszergazdai hitelesítő adataival.

    b. A szerepkörök létrehozásához megfelelő engedélyekre van szükség. Az engedélyek bekéseléséhez válassza a **módosítási engedélyeket.**

      ![Az "Engedélyek módosítása" gomb](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Válassza ki a következő engedélyeket a listából (ha még nem rendelkezik **ilyenekkel),** és válassza az Engedélyek módosítása lehetőséget.

      ![Engedélyek listája és "Engedélyek módosítása" gomb](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > A Felhőalkalmazás-rendszergazda és az alkalmazásfelügyeleti szerepkör ebben a forgatókönyvben nem fog működni, mivel a címtár olvasásához és írásához globális rendszergazdai engedélyekre van szükségünk.

    d. Fogadja el a beleegyezést. Újra be vagy jelentkezve a rendszerbe.

    e. Módosítsa a verziót **béta verzióra,** és olvassa el a szolgáltatásnévi tagok listáját a bérlőtől a következő lekérdezés használatával:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Ha több könyvtárat használ, kövesse az alábbi mintát:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![A Graph Explorer párbeszédpanel, a szolgáltatásnév-egyszerű kiszolgálók beolvasására vonatkozó lekérdezéssel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Már folyamatban van az API-k frissítése, így az ügyfelek némi fennakadást láthatnak a szolgáltatásban.

    f. A lekért szolgáltatásnévi tagok listájából olvassa be azt, amelyet módosítania kell. A Ctrl+F billentyűkombinációval az összes felsorolt szolgáltatásnévből kereshet az alkalmazást. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezéssel jusson el az egyszerű szolgáltatáshoz:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![A módosítandó egyszerű szolgáltatás lekérdezi](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Az **appRoles** tulajdonság kinyerése az egyszerű szolgáltatásobjektumból.

      ![Az appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Ha az egyéni alkalmazást (nem az Azure Marketplace-alkalmazást) használja, két alapértelmezett szerepkör jelenik meg: a felhasználó és a msiam_access. A Marketplace-alkalmazás, msiam_access az egyetlen alapértelmezett szerepkör. Az alapértelmezett szerepkörökön nem kell módosítania.

    h. Hozzon létre új szerepköröket az alkalmazáshoz.

      A következő JSON egy példa az **appRoles** objektumra. Hozzon létre egy hasonló objektumot az alkalmazáshoz kívánt szerepkörök hozzáadásához.

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
      > Csak a javítási művelet msiam_access után adhat hozzá új szerepköröket. Emellett annyi szerepkört adhat hozzá, amennyia szervezetnek szüksége van rá. Az Azure AD elküldi ezeknek a szerepköröknek az értékét az SAML-válasz jogcímértékeként. Az új szerepkörök azonosítójának GUID-értékeinek létrehozásához használja az [ilyen](https://www.guidgenerator.com/) webes eszközöket

    i. Lépjen vissza a Graph Explorer programhoz, és módosítsa a módszert **GET-ről** **PATCH-re.** Az **alkalmazásszerepkörök** tulajdonság frissítésével javítsa ki a szolgáltatásegyszerű objektumot a kívánt szerepkörökkel, például az előző példában láthatóhoz. A javítás műveletének végrehajtásához válassza a **Lekérdezés futtatása** lehetőséget. A sikeres üzenet megerősíti a szerepkör létrehozását.

      ![Javításművelet sikeres üzenettel](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Miután az egyszerű szolgáltatás több szerepkörrel van javítva, felhasználókat rendelhet a megfelelő szerepkörökhöz. A felhasználókat a portálhoz való megtekintéshez és az alkalmazásböngészéshez való böngészéssel rendelheti hozzá. Válassza a **Felhasználók és csoportok** lapot. Ez a lap felsorolja az összes olyan felhasználót és csoportot, amely már hozzá van rendelve az alkalmazáshoz. Az új szerepkörökhöz új felhasználókat vehet fel. Kijelölhet egy meglévő felhasználót is, és a **Szerkesztés gombra** léphet a szerepkör módosításához.

    !["Felhasználók és csoportok" lap](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Ha a szerepkört bármely felhasználóhoz szeretné rendelni, jelölje ki az új szerepkört, és válassza a **Hozzárendelés** gombot a lap alján.

    !["Hozzárendelés szerkesztése" ablaktábla és "Szerepkör kiválasztása" ablaktábla](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Az új szerepkörök megtekintéséhez frissítenie kell a munkamenetet az Azure Portalon.

8. Frissítse az **Attribútumok** táblát a szerepkörjogcím heti leképezésének meghatározásához.

9. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Attribútum neve | Attribútum értéke |
    | -------------- | ----------------|
    | Szerepkörnév  | user.assignedroles |

    >[!NOTE]
    >Ha a szerepkör jogcímértéke null, majd az Azure AD nem küldi el ezt az értéket a jogkivonatban, és ez az alapértelmezett, mint egy design.

    a. kattintson a **Szerkesztés** ikonra **a Felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

      !["Attribútum hozzáadása" gomb](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. A **Felhasználói jogcímek kezelése** párbeszédpanelen adja hozzá az SAML token attribútumot az **Új jogcím hozzáadása**gombra kattintva.

      !["Attribútum hozzáadása" gomb](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Attribútum hozzáadása" ablaktábla](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. A **Név** mezőbe szükség szerint írja be az attribútum nevét. Ez a példa **a szerepkör nevét** használja jogcímnévként.

    d. Hagyja üresen a **Névtér** mezőt.

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson a **Mentés** gombra.

10. Ha az alkalmazást egyetlen, identitásszolgáltató által kezdeményezett bejelentkezéssel szeretné tesztelni, jelentkezzen be a [Hozzáférési panelre,](https://myapps.microsoft.com) és válassza ki az alkalmazáscsempét. Az SAML-jogkivonatban a megadott jogcímnévvel rendelkező felhasználó összes hozzárendelt szerepkörét látnia kell.

## <a name="update-an-existing-role"></a>Meglévő szerepkör frissítése

Meglévő szerepkör frissítéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg [a Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)t .

2. Jelentkezzen be a Graph Explorer webhelyére a bérlő globális rendszergazdai vagy társrendszergazdai hitelesítő adataival.

3. Módosítsa a verziót **béta verzióra,** és olvassa el a szolgáltatásnévi tagok listáját a bérlőtől a következő lekérdezés használatával:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, kövesse az alábbi mintát:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![A Graph Explorer párbeszédpanel, a szolgáltatásnév-egyszerű kiszolgálók beolvasására vonatkozó lekérdezéssel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A lekért szolgáltatásnévi tagok listájából olvassa be azt, amelyet módosítania kell. A Ctrl+F billentyűkombinációval az összes felsorolt szolgáltatásnévből kereshet az alkalmazást. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezéssel jusson el az egyszerű szolgáltatáshoz:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![A módosítandó egyszerű szolgáltatás lekérdezi](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Az **appRoles** tulajdonság kinyerése az egyszerű szolgáltatásobjektumból.

    ![Az appRoles tulajdonság részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. A meglévő szerepkör frissítéséhez kövesse az alábbi lépéseket.

    ![A "PATCH" kérelemtörzse, kiemelve a "leírás" és a "displayname"](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Módosítsa a módszert **GET-ről** **PATCH-re**.

    b. Másolja a meglévő szerepköröket, és illessze be őket a **Kérelem törzse csoportba.**

    c. A szerepkör leírásának, szerepkörértékének vagy szerepkörmegjelenítési nevének szükség szerinti frissítésével frissítheti egy szerepkör értékét.

    d. Az összes szükséges szerepkör frissítése után válassza a **Lekérdezés futtatása**lehetőséget.

## <a name="delete-an-existing-role"></a>Meglévő szerepkör törlése

Meglévő szerepkör törléséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) t egy másik ablakban.

2. Jelentkezzen be a Graph Explorer webhelyére a bérlő globális rendszergazdai vagy társrendszergazdai hitelesítő adataival.

3. Módosítsa a verziót **béta verzióra,** és olvassa el a szolgáltatásnévi tagok listáját a bérlőtől a következő lekérdezés használatával:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Ha több könyvtárat használ, kövesse az alábbi mintát:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![A Graph Explorer párbeszédpanel, amelyen a szolgáltatásnévi tagok listájának beolvasására szolgáló lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A lekért szolgáltatásnévi tagok listájából olvassa be azt, amelyet módosítania kell. A Ctrl+F billentyűkombinációval az összes felsorolt szolgáltatásnévből kereshet az alkalmazást. Keresse meg a **Tulajdonságok** lapról másolt objektumazonosítót, és a következő lekérdezéssel jusson el az egyszerű szolgáltatáshoz:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![A módosítandó egyszerű szolgáltatás lekérdezi](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Az **appRoles** tulajdonság kinyerése az egyszerű szolgáltatásobjektumból.

    ![Az appRoles tulajdonság részletei az egyszerű szolgáltatásobjektumból](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. A meglévő szerepkör törléséhez kövesse az alábbi lépéseket.

    ![A "PATCH" kéréstörzse, amelynek IsEnabled értéke hamis.](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Módosítsa a módszert **GET-ről** **PATCH-re**.

    b. Másolja a meglévő szerepköröket az alkalmazásból, és illessze be őket a **Kérelem törzse csoportba.**

    c. Állítsa az **IsEnabled** értéket **hamisértékre** a törölni kívánt szerepkörhöz.

    d. Válassza a **Lekérdezés futtatása** lehetőséget.

    > [!NOTE]
    > Győződjön meg arról, hogy rendelkezik a msiam_access szerepkör, és az azonosító megfelel a létrehozott szerepkörben.

7. A szerepkör letiltása után törölje a szerepkörblokkot az **appRoles** szakaszból. Tartsa meg **PATCH**a metódust PATCH-ként, és válassza **a Lekérdezés futtatása**lehetőséget.

8. A lekérdezés futtatása után a szerepkör törlődik.

    > [!NOTE]
    > A szerepkört le kell tiltani, mielőtt el lehetne távolítani.

## <a name="next-steps"></a>További lépések

További lépéseket az [alkalmazás dokumentációjában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)talál.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
