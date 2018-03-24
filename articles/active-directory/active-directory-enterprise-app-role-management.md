---
title: A SAML-jogkivonat a vállalati alkalmazások az Azure Active Directory által kiadott konfigurálása szerepkör jogcím |} Microsoft Docs
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
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 58656e2aa3b052d9bd9aa14edeb6215858d55ea4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>A SAML-jogkivonat a vállalati alkalmazások az Azure Active Directory által kiadott szerepkör jogcím konfigurálása

Ez a funkció lehetővé teszi, hogy a felhasználók testreszabása az Azure AD alkalmazás engedélyezése után kapott válasz jogkivonatba "szerepkörök" jogcímet a jogcím típusa.

## <a name="prerequisites"></a>Előfeltételek
- Az Azure AD szolgáltatásra directory telepítés
- Egyszeri bejelentkezés engedélyezve van az előfizetés
- Az alkalmazás az SSO kell konfigurálni

## <a name="when-to-use-this-feature"></a>Ha a szolgáltatás használatához

Ha az alkalmazás SAML-válasz átadni egyéni szerepkörök vár, akkor használja ezt a szolgáltatást. Ez a funkció lehetővé teszi átadását az Azure AD vissza az alkalmazás az igények szerinti annyi szerepköröket hozhat létre.

## <a name="steps-to-use-this-feature"></a>Ez a szolgáltatás használatának lépéseit

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. A keresőmezőbe írja be az alkalmazás nevét, az eredmény panelen válassza ki az alkalmazását, majd kattintson az **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában alkalmazás](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Az alkalmazás hozzáadása után Ugrás **tulajdonságok** lapjáról, és másolja a **objektum azonosítója**

    ![Tulajdonságok lap](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.

    b. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Az alkalmazás most új szerepkörök létrehozásához szüksége. Az Azure AD szerepkör generátor letöltheti a [Itt](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Nyissa meg az Azure AD-készítő, és hajtsa végre a következő lépéseket-

    ![Az Azure AD-készítő](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Adja meg **szerepkörnév**, **szerepkör leírása**, és **szerepkör értéke**. Kattintson a **Hozzáadás** a szerepkör hozzáadása
    
    Miután hozzáadta a szükséges szerepkörök, kattintson a **létrehozása**
    
    Másolja a tartalmat kattintva **tartalom másolása**

    > [!NOTE] 
    > Győződjön meg arról, hogy rendelkezik **msiam_access** létrehozott szerepkör felhasználói szerepkör, és az azonosító van megfelelő.

    g. Lépjen vissza a diagramon Explorer. A módszer váltása **beolvasása** való **javítás**. A szolgáltatás egyszerű objektumot a másolt értékekkel appRoles tulajdonság frissítésével appRoles rendelkezik szükséges javítás. Kattintson a **lekérdezés**.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Az alábbiakban látható egy példa appRoles objektum. 
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
        "description": "teacher",
        "displayName": "teacher",
        "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
        "isEnabled": ,
        "origin": "ServicePrincipal",
        "value": "teacher"
    }
    ] 
    }   
    ```
7. Miután a szolgáltatás egyszerű a további szerepkörök telepítve, azt felhasználók szerepét lehet társítani. Ezt megteheti Portal fog, és navigáljon a megfelelő alkalmazás. Kattintson a a **felhasználók és csoportok** lapon látható. Ez a folyamat felsorolja azokat a felhasználókat és csoportokat.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. A szerepkör hozzárendelése minden olyan felhasználó, jelölje ki az adott felhasználói csoporthoz, majd kattintson a **hozzárendelése** a lap alsó részén gombjára.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Egy előugró ablak választási szerepkör más szerepkörök a megfelelő egyszerű szolgáltatásnév definiált kattint, amely megteremti.

    c. Válassza ki a szükséges szerepkör, majd kattintson a küldje el.

8. Szerepkörök hozzárendelése a felhasználókhoz, után frissíteni kell **attribútumok** táblázat segítségével határozza meg a testreszabott leképezését **szerepkör** jogcímek.

9. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | -------------- | ----------------|    
    | Szerepkör neve      | user.assignedrole |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

10. Az alkalmazás teszteléséhez a kiállító terjesztési hely által kezdeményezett egyszeri bejelentkezéshez a napló a hozzáférési panelre (https://myapps.microsoft.com) , majd kattintson az alkalmazás csempe a. A SAML-jogkivonat meg kell jelennie a hozzárendelt szerepköröket a felhasználó az adott jogcím neve.

## <a name="update-existing-role"></a>Meglévő szerepkör frissítése

1. Egy meglévő szerepkör frissítése, hajtsa végre a következő lépéseket-

    a. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    b. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.
    
    c. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból.
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. A meglévő szerepkör frissítéséhez hajtsa végre következő lépések:

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * A módszer váltása **beolvasása** való **javítás**.

    * A meglévő szerepkörök másolása az alkalmazást, majd illessze be a számára a **kérelem törzse**.
    
    * Frissítse az értéket, a szerepkör tartományvezérlőkkel történő lecserélésével a **szerepkör leírása**, **szerepkör értéke**, és **szerepkör displayname** a szervezeti követelmények szerint.
    
    * Miután frissítette a szükséges szerepkörök, kattintson a **lekérdezés futtatása**.
        
## <a name="delete-existing-role"></a>Meglévő szerepkör törlése

Egy meglévő szerepkör törléséhez hajtsa végre a következő lépéseket:

a. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

b. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.

c. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
`https://graph.microsoft.com/beta/servicePrincipals`
    
Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
d. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
     
`https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
e. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból.
    
![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

f. Törölje a meglévő szerepkört, adjon kövesse az alábbi lépéseket:

![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

A módszer váltása **beolvasása** való **javítás**.

A meglévő szerepkörök másolása az alkalmazást, majd illessze be a a **kérelem törzse**.
    
Állítsa be a **IsEnabled** egy érték **hamis** a szerepkör, amely a törölni kívánt

Kattintson a **lekérdezés**.
    
> [!NOTE] 
> Győződjön meg arról, hogy rendelkezik **msiam_access** létrehozott szerepkör felhasználói szerepkör, és az azonosító van megfelelő.
    
g. Ezután a fenti folyamatot, tartsa a módszert az **javítás** , majd illessze be a többi szerepkört a **kérelem törzse** kattintson **lekérdezés futtatása**.
    
![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

h. A lekérdezés futtatása után a szerepkör törlődni fog.
    
> [!NOTE]
> A szerepkör kell előtt le kell tiltani először el kell távolítani. 

## <a name="next-steps"></a>További lépések

Tekintse meg a [alkalmazás dokumentációjának ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) a további lépéseket.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
