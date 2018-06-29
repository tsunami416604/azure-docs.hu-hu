---
title: A szerepkör jogcím a SAML-jogkivonat a vállalati alkalmazások az Azure Active Directory által kiadott konfigurálása |} Microsoft Docs
description: A szerepkör jogcím által kiadott a SAML-jogkivonat a vállalati alkalmazások az Azure Active Directory konfigurálása
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: c9a1d605f6cf2ef9dae3a5549e3848931d508394
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082743"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>A szerepkör jogcím által kiadott a SAML-jogkivonat a vállalati alkalmazások az Azure Active Directory konfigurálása

Azure Active Directory (Azure AD) segítségével testre szabhatja a jogcímtípus az a szerepkör jogcím a válasz jogkivonat után megjelenő is engedélyezni szeretné az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek
- Egy könyvtár telepítés az Azure AD-előfizetés.
- Olyan előfizetést, amelyet az egyszeri bejelentkezés (SSO) engedélyezve van. Az alkalmazás az SSO kell konfigurálni.

## <a name="when-to-use-this-feature"></a>Ha a szolgáltatás használatához

Ha az alkalmazás egy SAML-válasz átadni egyéni szerepkörök vár, akkor használja ezt a szolgáltatást. Létrehozhat annyi szerepkörök szükség van az alkalmazás átadandó az Azure AD vissza.

## <a name="create-roles-for-an-application"></a>Szerepkörök az alkalmazás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory ikonra][1]

2. Válassza ki **vállalati alkalmazások**. Válassza ki **összes alkalmazás**.

    ![Vállalati alkalmazások ablaktábla][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    !["Az új alkalmazás" gomb][3]

4. A keresési mezőbe írja be az alkalmazás nevét, és válassza ki az alkalmazást, az eredmény panelen. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában alkalmazás](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Lépjen az alkalmazás hozzáadása után a **tulajdonságok** lapjáról, és másolja az objektum azonosítójával.

    ![Tulajdonságok lap](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Nyissa meg a [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban és a következő lépéseket:

    a. Jelentkezzen be a grafikon Explorer hely a bérlő globális rendszergazda vagy társfelügyeletű hitelesítő adataival.

    b. A szerepkörök létrehozása elegendő jogosultság szükséges. Válassza ki **módosítsa az engedélyeket** a jogosultságok megszerzéséhez. 

      ![A "engedélyek módosítása" gombra](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Válassza ki a következő engedélyeket a lista (ha ezek a már nincs), és válasszon **módosítási engedélyekkel**. 

      ![Engedélyek és a "Engedélyek módosítása" gombra](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Fogadja el a hozzájárulásukat adják. Jelentkezett be a rendszer újra.

    e. Módosíthatja a telepített verziója **beta**, és a szolgáltatás résztvevők listájának beolvasása a bérlő a használatával a következő lekérdezést:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Ha több címtárral használata esetén kövesse az ebben a mintában: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Graph Explorer párbeszédpanelen, a lekérdezés való beolvasásához használt szolgáltatásnevekről](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Lehívott szolgáltatás rendszerbiztonsági tagok közül beolvasása, amely módosítani kell. Ctrl + F használhatja az alkalmazás az összes felsorolt szolgáltatásnevekről kereséséhez. Keresse meg a másolt Objektumazonosító a **tulajdonságok** lapon, és az egyszerű szolgáltatás eléréséhez használja a következő lekérdezést:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Az első, hogy módosítania kell a szolgáltatás egyszerű lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Bontsa ki a **appRoles** tulajdonság a szolgáltatás egyszerű objektumból. 

      ![A appRoles tulajdonság részleteit](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Ha az egyéni alkalmazás (nem az Azure piactér-alkalmazás) használ, két alapértelmezett szerepkör látja: felhasználó- és msiam_access. A piactér alkalmazás msiam_access olyan csak alapértelmezett szerepkör. Nem kell módosítani az alapértelmezett szerepkör.

    h. Az alkalmazás új szerepkörök létrehozása. 

      A következő JSON-ja példát a **appRoles** objektum. Hozzon létre egy hasonló objektumot adja hozzá a szerepkört, amelyet az alkalmazáshoz. 

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
      > Új szerepkörök csak a javítási művelet msiam_access után is hozzáadhat. Annyi szerepkörök is, a szervezet igényei szerint is felvehet. Az Azure AD el fogja küldeni ezeket a szerepköröket értékének SAML-válasz a jogcím értéke. Készítése a GUID-azonosítóhoz új szerepkörök értéket használjuk a webes eszközök, például a [Ez](https://www.guidgenerator.com/)
    
    i. Vissza a diagramon Explorer és a módszer váltása **beolvasása** való **javítás**. A szolgáltatás egyszerű objektum kell rendelkeznie a kívánt szerepkörök frissítésével javítás a **appRoles** tulajdonság hasonló az előző példában látható módon. Válassza ki **lekérdezés futtatása** a javítás művelet végrehajtásához. A sikerről szóló üzenetet megerősíti, hogy a szerepkör létrehozása.

      ![Javítási műveletet a sikerről szóló üzenetet](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Után az egyszerű szolgáltatás van telepítve a további szerepkörök, felhasználók rendelhet szerepét. Ugrás a portálon, és keresse meg az alkalmazás a felhasználók rendelhet. Válassza ki a **felhasználók és csoportok** fülre. Ezen a lapon felsorolja az összes felhasználókat és csoportokat, amelyek az alkalmazás már hozzá van rendelve. Új szerepkörök új felhasználókat adhat hozzá. Is válasszon egy meglévő felhasználót, és válassza ki **szerkesztése** a szerepkör módosítása.

    !["Felhasználók és csoportok" lapon](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    A szerepkör hozzárendelése minden felhasználóhoz, válassza ki az új szerepkör, és válassza ki a **hozzárendelése** gombra az oldal alján.

    !["Hozzárendelés módosítása" és "Szerepkör kiválasztása" ablaktábla](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Akkor kell frissítenie a munkamenetet, hogy új szerepkörök az Azure portálon.

8. Frissítés a **attribútumok** tábla testreszabott leképezéseket a szerepkör jogcím meghatározásához.

9. Az a **felhasználói attribútumok** szakasza a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket.
    
    | Attribútum neve | Attribútum-érték |
    | -------------- | ----------------|    
    | Szerepkör neve      | User.assignedrole |

    a. Válassza ki **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** ablaktáblán.

      !["Attribútum hozzáadása" gombra](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Attribútum hozzáadása" ablak](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Az a **neve** mezőbe írja be az attribútumnak a nevét, igény szerint. Ez a példa **szerepkörnév** jogcím neveként.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** mező üres.
    
    e. Válassza ki **Ok**.

10. Tesztelje az alkalmazás egy egyszeri bejelentkezést az identitásszolgáltató által kezdeményezett, jelentkezzen be a [hozzáférési Panel](https://myapps.microsoft.com) és az alkalmazás csempe kiválasztása. A SAML-jogkivonat meg kell jelennie a hozzárendelt szerepkörök a jogcím neve, amely az adott felhasználó számára.

## <a name="update-an-existing-role"></a>Egy meglévő szerepkör frissítése

Egy meglévő szerepkör frissítéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg [az Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Jelentkezzen be a grafikon Explorer hely a bérlő globális rendszergazda vagy társfelügyeletű hitelesítő adataival.
    
3. Módosíthatja a telepített verziója **beta**, és a szolgáltatás résztvevők listájának beolvasása a bérlő a használatával a következő lekérdezést:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Ha több címtárral használata esetén kövesse az ebben a mintában: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph Explorer párbeszédpanelen, a lekérdezés való beolvasásához használt szolgáltatásnevekről](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Lehívott szolgáltatás rendszerbiztonsági tagok közül beolvasása, amely módosítani kell. Ctrl + F használhatja az alkalmazás az összes felsorolt szolgáltatásnevekről kereséséhez. Keresse meg a másolt Objektumazonosító a **tulajdonságok** lapon, és az egyszerű szolgáltatás eléréséhez használja a következő lekérdezést:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Az első, hogy módosítania kell a szolgáltatás egyszerű lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Bontsa ki a **appRoles** tulajdonság a szolgáltatás egyszerű objektumból.
    
    ![A appRoles tulajdonság részleteit](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. A meglévő szerepkör frissítéséhez az alábbi lépésekkel.

    ![Szervezet a "Javítás", "description" és "displayname" kiemelt igénylése](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. A módszer váltása **beolvasása** való **javítás**.

    b. A meglévő szerepek másolja és illessze be őket a **kérelem törzse**.

    c. Frissítse az értéket, a szerepkör frissítésével a szerepkör leírása, a szerepkör értéke vagy a szerepkör megjelenítési név igény szerint.

    d. Miután frissítette a szükséges szerepkörök, válassza ki a **lekérdezés futtatása**.
        
## <a name="delete-an-existing-role"></a>Egy meglévő szerepkör törlése

Egy meglévő szerepkör törléséhez a következő lépésekkel:

1. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

2. Jelentkezzen be a grafikon Explorer hely a bérlő globális rendszergazda vagy társfelügyeletű hitelesítő adataival.

3. Módosíthatja a telepített verziója **beta**, és a szolgáltatás résztvevők listájának beolvasása a bérlő a használatával a következő lekérdezést:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Ha több címtárral használata esetén kövesse az ebben a mintában: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph Explorer párbeszédpanelen, a lekérdezés a szolgáltatás résztvevők listájának beolvasása](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Lehívott szolgáltatás rendszerbiztonsági tagok közül beolvasása, amely módosítani kell. Ctrl + F használhatja az alkalmazás az összes felsorolt szolgáltatásnevekről kereséséhez. Keresse meg a másolt Objektumazonosító a **tulajdonságok** lapon, és az egyszerű szolgáltatás eléréséhez használja a következő lekérdezést:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Az első, hogy módosítania kell a szolgáltatás egyszerű lekérdezés](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Bontsa ki a **appRoles** tulajdonság a szolgáltatás egyszerű objektumból.
    
    ![A szolgáltatás egyszerű objektumból appRoles tulajdonságának részletei](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. A meglévő szerepkör törléséhez használja a következő lépéseket.

    ![Törzs IsEnabled állítsa hamis értékre a "Javítás" igénylése](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. A módszer váltása **beolvasása** való **javítás**.

    b. A meglévő szerepkörök másolása az alkalmazást, majd illessze be a **kérelem törzse**.
        
    c. Állítsa be a **IsEnabled** egy érték **hamis** a törölni kívánt szerepkör esetében.

    d. Válassza ki **lekérdezés**.
    
    > [!NOTE] 
    > Győződjön meg arról, hogy msiam_access szerepkörrel rendelkeznek, és az Azonosítóját a generált szerepkörben van megfelelő.
    
7. Után a szerepkör le van tiltva, törölje az adott szerepkör kitiltják a **appRoles** szakasz. Tartsa a módszert az **javítás**, és válassza ki **lekérdezés futtatása**.
    
8. A lekérdezés futtatása után a rendszer törli a szerepkört.
    
    > [!NOTE]
    > A szerepkörnek szükséges gazdagépcsoport(ok) eltávolítása előtt le kell tiltani. 

## <a name="next-steps"></a>További lépések

A további lépéseket, tekintse meg a [alkalmazás dokumentációjának](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png