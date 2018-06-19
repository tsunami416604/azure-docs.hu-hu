---
title: 'Oktatóanyag: Azure Active Directoryval integrált titkos Server (helyszíni) |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a titkos kulcs Server (helyszíni) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 552ba0ac9591f6c753e79ab5a1b406f2035457c0
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923806"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Oktatóanyag: Azure Active Directoryval integrált titkos Server (helyszíni)

Ebben az oktatóanyagban elsajátíthatja titkos Server (helyszíni) integrálása az Azure Active Directory (Azure AD).

Titkos kulcs Server (helyszíni) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a titkos kulcs Server (helyszíni) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a titkos kulcs Server (helyszíni) (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a titkos kulcs Server (helyszíni), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A titkos kulcs Server (helyszíni) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Titkos kulcs Server (helyszíni) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Titkos kulcs Server (helyszíni) hozzáadása a gyűjteményből
Titkos kulcs Server (helyszíni) integrálása az Azure AD-be konfigurálásához kell hozzáadnia titkos Server (helyszíni) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a titkos kulcs Server (helyszíni) a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **titkos Server (helyszíni)**, jelölje be **titkos Server (helyszíni)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában titkos Server (helyszíni)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés titkos Server (helyszíni) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó, a titkos kulcs Server (helyszíni) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a titkos kulcs Server (helyszíni) közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a titkos kulcs Server (helyszíni) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Titkos kulcs Server (helyszíni) tesztfelhasználó létrehozása](#create-a-secret-server-on-premises-test-user)**  – egy megfelelője a Britta Simon rendelkezik a titkos kulcs Server (helyszíni), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a titkos kulcs Server (helyszíni) alkalmazás egyszeri bejelentkezés konfigurálása.

**Titkos kulcs Server (helyszíni) konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **titkos Server (helyszíni)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Az a **titkos Server (helyszíni) tartományhoz és URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk titkos Server (helyszíni) tartományhoz és URL-címek](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Az a **azonosító** szövegmező, adja meg az érték, mint például a kiválasztott felhasználói: `https://secretserveronpremises.azure`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > A fenti Entitásazonosító csak egy példa, és szabadon bármely egyedi érték, amely azonosítja a titkos kulcs kiszolgáló példányát az Azure ad-ben válassza. Az entitás azonosítója a kell [titkos Server (helyszíni) ügyfél-támogatási csoport](https://thycotic.force.com/support/s/) , és konfigurálja az oldalon. További részletekért olvassa el [Ez a cikk](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk titkos Server (helyszíni) tartományhoz és URL-címek](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [titkos Server (helyszíni) ügyfél-támogatási csoport](https://thycotic.force.com/support/s/) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Ellenőrizze **megjelenítése speciális tanúsítvány-aláírási beállítások** válassza **aláíró beállítás** , **bejelentkezési SAML-válasz és helyességi feltétel**.

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/signing.png)

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Az a **titkos kiszolgálókonfiguráció (helyszíni)** területen kattintson **titkos kiszolgáló konfigurálása (helyszíni)** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Titkos kiszolgálókonfiguráció (helyszíni)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Egyszeri bejelentkezés konfigurálása **titkos Server (helyszíni)** oldalon kell küldeniük a letöltött **Certificate(Base64), Sign-Out URL-címe, SAML egyetlen bejelentkezési URL-címe**, és **SAML entitás Azonosító** való [titkos Server (helyszíni) támogatási csoport](https://thycotic.force.com/support/s/). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Titkos kulcs Server (helyszíni) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű titkos Server (helyszíni) felhasználói hoz létre. Együttműködve [titkos Server (helyszíni) támogatási csoport](https://thycotic.force.com/support/s/) a felhasználók hozzáadása a titkos kulcs Server (helyszíni) platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés titkos Server (helyszíni) használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Titkos kulcs Server (helyszíni) Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **titkos Server (helyszíni)**.

    ![A titkos kulcs Server (helyszíni) hivatkozásra az alkalmazások listáját](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a titkos kulcs Server (helyszíni) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a titkos kulcs Server (helyszíni) alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

