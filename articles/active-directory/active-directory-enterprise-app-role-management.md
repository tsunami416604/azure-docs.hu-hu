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
ms.openlocfilehash: 43a4db9114cd47da5bef98ed634847b547589b47
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
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

5. Az alkalmazás hozzáadása után Ugrás **tulajdonságok** lapjáról, és másolja a **Objektumazonosító**.

<!-- ![Properties Page](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png) Note: Image is missing. -->

6. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

    a. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.

    b. Kell rendelkeznie a szerepkörök létrehozásához szükséges engedélyekkel. Kattintson a **módosítsa az engedélyeket** lekérni a szükséges engedélyekkel. 

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Jelölje be a következő engedélyeket a listáról (ha ezek már nem rendelkezik) "Engedélyek módosítása" gombra 

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Ez ekkor megkérdezi, hogy újból bejelentkezni, és fogadja el a hozzájárulásukat adják. A hozzájárulási elfogadása, miután bejelentkezett a rendszer újra.

    e. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból. 

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Ha az egyéni (nem galéria) alkalmazást használ, a két alapértelmezett szerepkör - felhasználói és msiam_access látható. Gyűjteményelem alkalmazás esetén msiam_access olyan csak alapértelmezett szerepkör. Nem kell módosítani az alapértelmezett szerepkör.

    h. Most szeretne létrehozni az alkalmazás új szerepköröket. 

    i. Alább JSON példája appRoles objektum. Hozzon létre egy hasonló objektumot a kívánt alkalmazás szerepkörök hozzáadásához. 

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
    > Csak akkor adhat hozzá új szerepkörök után a **msiam_access** a javítás művelet. Is ahányat csak szeretne, a szervezet kell egy tetszőleges számú szerepkörök is hozzáadhat. Az Azure AD fog küldeni a **érték** a fenti két szerepkör SAML-válasz a jogcím értéke.
    
    j. Lépjen vissza a diagramon Explorer és a módszer váltása **beolvasása** való **javítás**. A szolgáltatás egyszerű objektum rendelkezik szükségeskonfiguráció-szerepkörök által a fenti példában látható módon a hasonló appRoles tulajdonság frissítése javítás. Kattintson a **lekérdezés futtatása** a javítás művelet végrehajtásához. A sikerről szóló üzenetet megerősíti, hogy a szerepkör létrehozása.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Után az egyszerű szolgáltatás van telepítve a további szerepkörök, felhasználók rendelhet szerepét. Ezt megteheti portal fog, és navigáljon a megfelelő alkalmazás. Kattintson a **felhasználók és csoportok** lapon látható. A parancs megjeleníti az összes a felhasználókat és csoportokat, amelyek az alkalmazás már hozzá van rendelve. Új felhasználók hozzáadása az új szerepkör és is is válassza ki a meglévő felhasználót, és kattintson a **szerkesztése** a szerepkör módosítása.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     A szerepkör hozzárendelése minden felhasználóhoz, válassza ki az új szerepkör, és kattintson a **hozzárendelése** gombra az oldal alján.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Vegye figyelembe, hogy szeretné-e a munkamenet során az Azure portálhoz, és új szerepkörök frissítése.

8. Szerepkörök hozzárendelése a felhasználókhoz, után frissíteni kell **attribútumok** táblázat segítségével határozza meg a testreszabott leképezését **szerepkör** jogcímek.

9. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | -------------- | ----------------|    
    | Szerepkör neve      | User.assignedrole |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az attribútumnak a nevét, igény szerint. Ebben a példában a használtuk rendelkezik **szerepkörnév** módon a jogcím neve.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

10. Az alkalmazás teszteléséhez a kiállító terjesztési hely által kezdeményezett egyszeri bejelentkezéshez a napló a hozzáférési panelre (https://myapps.microsoft.com) , majd kattintson az alkalmazás csempe a. A SAML-jogkivonat meg kell jelennie a hozzárendelt szerepköröket a felhasználó az adott jogcím neve.

## <a name="update-existing-role"></a>Meglévő szerepkör frissítése

Egy meglévő szerepkör frissítése, hajtsa végre a következő lépéseket-

1. Nyissa meg [az Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.
    
3. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból.
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. A meglévő szerepkör frissítéséhez hajtsa végre következő lépések:

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * A módszer váltása **beolvasása** való **javítás**.

    * A meglévő szerepek másolja és illessze be őket a a **kérelem törzse**.

    * Frissítse az értéket, a szerepkör frissítésével a **szerepkör leírása**, **szerepkör értéke** vagy **szerepkör displayname** igény szerint.

    * Miután frissítette a szükséges szerepkörök, kattintson a **lekérdezés futtatása**.
        
## <a name="delete-existing-role"></a>Meglévő szerepkör törlése

Egy meglévő szerepkör törléséhez hajtsa végre a következő lépéseket:

1. Nyissa meg [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) egy másik ablakban.

2. Jelentkezzen be a grafikon Explorer webhelyre, a bérlő globális rendszergazdai vagy társadminisztrátori hitelesítő adataival.

3. Módosítsa a verzióra **beta** szolgáltatás résztvevők listájának beolvasása a következő lekérdezés a bérlőt és:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Ha több címtárral használ, majd kövesse ezt a mintát `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Lehívott szolgáltatás rendszerbiztonsági tagok közül módosítani kell egy beolvasása. A Ctrl + F segítségével is keresni a felsorolt ServicePrincipals az alkalmazást. Keresse meg a **objektumazonosító**, amely tulajdonságai lapon másolta, és használja a következő lekérdezés a megfelelő szolgáltatásnevet eléréséhez.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Bontsa ki a appRoles tulajdonság a szolgáltatás egyszerű objektumból.
    
    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Törölje a meglévő szerepkört, adjon kövesse az alábbi lépéseket:

    ![Graph explorer párbeszédpanel](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * A módszer váltása **beolvasása** való **javítás**.

    * A meglévő szerepkörök másolása az alkalmazást, majd illessze be a a **kérelem törzse**.
        
    * Állítsa be a **IsEnabled** egy érték **hamis** a szerepkör, amely a törölni kívánt

    * Kattintson a **lekérdezés**.
    
    > [!NOTE] 
    > Győződjön meg arról, hogy rendelkezik **msiam_access** létrehozott szerepkör felhasználói szerepkör, és az azonosító van megfelelő.
    
7. A szerepkör le van tiltva, ha a szerepkör blokk appRoles szakasz törlése, tartsa a módszert az **javítás** kattintson **lekérdezés futtatása**.
    
8. A lekérdezés futtatása után a szerepkör törlődni fog.
    
    > [!NOTE]
    > A szerepkör kell előtt le kell tiltani először el kell távolítani. 

## <a name="next-steps"></a>További lépések

Tekintse meg a [alkalmazás dokumentációjának ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) a további lépéseket.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
