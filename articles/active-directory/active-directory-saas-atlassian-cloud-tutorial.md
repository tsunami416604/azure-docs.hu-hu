---
title: "Oktatóanyag: Azure Active Directory-integráció a Atlassian felhőalapú |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Atlassian felhő között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: 98623b6734726d43136703cbfee0b7ed0d82f815
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Oktatóanyag: Azure Active Directoryval integrált Atlassian felhő

Ebben az oktatóanyagban elsajátíthatja Atlassian felhő integrálása az Azure Active Directory (Azure AD).

Atlassian felhő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik Atlassian felhőbe szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Atlassian felhőbe (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Atlassian felhőalapú, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- SAML-alapú egyszeri bejelentkezés Atlassian felhő termékek beállítva fel identitáskezelő kell engedélyezni. További információ [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Atlassian felhő hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-atlassian-cloud-from-the-gallery"></a>A gyűjteményből Atlassian felhő hozzáadása
Az Azure AD integrálása a Atlassian felhő konfigurálásához kell hozzáadnia Atlassian felhőalapú a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Atlassian felhő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Atlassian felhő**, jelölje be **Atlassian felhő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Atlassian felhő](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálhatja, és a Atlassian felhőalapú Azure AD az egyszeri bejelentkezés teszt "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Atlassian felhőben Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Atlassian felhőben közötti kapcsolat kapcsolatot kell létrehozni.

Atlassian felhőben, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Atlassian felhőalapú tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Atlassian felhő tesztfelhasználó](#create-an-atlassian-cloud-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely csatolva van a felhasználó az Azure AD-ábrázolását Atlassian felhőben.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Atlassian felhőalapú alkalmazásokhoz.

**Atlassian a felhő konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Atlassian felhő** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Az a **Atlassian felhőalapú tartományt és URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Atlassian felhőalapú tartományt és URL-címek](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Az a **azonosító** szövegmező, írja be az URL-cím:`https://auth.atlassian.com/saml/<unique ID>`
    
    b. Az a **válasz URL-CÍMEN** szövegmező, írja be az URL-cím:`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Az a **továbbítási állapotot** szövegmező, adja meg a következő minta használatával URL-címe:`https://<instancename>.atlassian.net`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Atlassian felhőalapú tartományt és URL-címek](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    Az a **bejelentkezési URL-cím beviteli mező**, írja be egy URL-CÍMÉT a következő mintát:`https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Ezek az értékek frissíti a tényleges azonosítója, válasz és bejelentkezési URL-címe. Ezek az értékek lekérése az oktatóanyag a későbbi lépésekben ismertetett Atlassian felhő SAML konfigurálására szolgáló képernyőn.

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. A Atlassian felhőalapú alkalmazásnál a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Felhasználói azonosító az user.userprincipalname alapértelmezés szerint le van képezve. Módosítsa a leképezéséhez **user.mail**. Bármely más megfelelő érték a szervezet telepítő / is beállíthatja, de az esetek többségében, e-mailt kell működnie.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. A a **Atlassian Felhőkonfiguráció** kattintson **Atlassian felhő konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Atlassian Felhőkonfiguráció](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

9. A beolvasandó SSO konfigurálva az alkalmazáshoz, a bejelentkezés a Atlassian portálra a rendszergazdai jogosultságokkal.

10. Navigáljon a **Atlassian helyfelügyelet** > **szervezetek & biztonsági**. Ha még nem tette, hozzon létre, és a szervezet neve. Majd kattintson a bal oldali navigációs **tartományok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

11. Adja meg, hogy szeretné-e ellenőrizni a tartomány - hogyan **DNS** vagy **HTTPS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

12. DNS-ellenőrzési, válasszon **DNS** lapján a **tartományok** lapon, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Kattintson a **másolási** másolása a TXT-rekord értékét.

    b. A DNS-ből a beállítások lapon keresése felvesz egy új bejegyzést.

    c. Válassza ki az új rekordok beállítását, majd illessze be az érték, amelyből másolta a **tartományok** lap a **érték** mező. A DNS-kiszolgáló is hivatkozhat rá **válasz** vagy **leírás**.

    d. A DNS-rekordot is tartalmazhatják a következő mezőket:
    
    * **Rekordtípus**: Adjon meg **TXT**
    * **/ Host/aliasa**: hagyja meg az alapértelmezett (@ vagy üres)
    * **Az élettartam (TTL) idő**: Adjon meg **86400**
    
    e.  Mentse a bejegyzést.

13. Lépjen vissza a **tartományok lapon** szervezet felügyelete, és kattintson a **tartomány hitelesítése** gombra. Írja be a tartomány nevét az előugró ablak, és kattintson a **tartomány hitelesítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > A TXT rekord módosítások érvénybe lépéséhez akár 72 órát is igénybe vehet, akkor nem fogja tudni azonnal a tartomány ellenőrzése sikeres volt-e. Ellenőrizze a **tartományok** lapon, miután az ellenőrzés állapot elvégzi ezeket a lépéseket. Lásd a következő képernyő, frissített állapotú **ELLENŐRIZVE**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

14. HTTPS-ellenőrzésre, válassza ki a **HTTPS** lapján a **tartományok** lapon, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Kattintson a **letöltendő fájl** a HTML-fájl letöltésére.

    b.  A HTML-fájl feltöltése a tartomány a gyökérkönyvtárba.

15. Lépjen vissza a **tartományok** a szervezet felügyeleti lapon, majd kattintson a **tartomány hitelesítése** gombra. Adja meg a **tartománynév** az előugró ablak, és kattintson a **tartomány hitelesítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

16. Ha az ellenőrzési folyamat megtalálja a gyökérkönyvtárban feltöltött fájl, a tartomány állapotának frissítései **ellenőrizve**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > A tartományok ellenőrzésének további információkért tekintse meg a [Atlassian tartozó ellenőrzési dokumentációja](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

17. A bal oldali navigációs sávon kattintson **SAML-alapú egyszeri bejelentkezést**. Ha még nem tette meg, az előfizetés Atlassian tartozó Identity Manager.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

18. A **hozzáadása SAML-alapú konfigurációs** párbeszéd mezőben, adja hozzá az identitás-szolgáltató beállításait az alábbi módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Az a **identitásszolgáltató Entitásazonosító** szöveg mezőbe illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    b. Az a **identitásszolgáltató egyszeri bejelentkezési URL-cím** szöveg mezőbe illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. Nyissa meg a letöltött tanúsítvány egy Jegyzettömb Azure portálról, a tanúsítvány megkezdéséhez és End Certificate sorok nélkül másolja és illessze be a **nyilvános X509 tanúsítvány** mezőbe.
    
    d. Kattintson a **konfigurációjának mentéséhez**.
     
19. Az Azure AD-beállításokat, győződjön meg arról, hogy a telepítő a helyes URL-címének frissítése.
  
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Másolás a **SP identitás azonosító** a SAML a képernyőn, majd illessze be az értéket a **azonosító** mezőben az Azure portál, Atlassian felhő **tartomány és az URL-címek** szakasz.
    
    b. Másolás a **SP helyességi feltétel ügyfél szolgáltatás URL-címe** a SAML a képernyőn, majd illessze be az értéket a **válasz URL-CÍMEN** mezőben az Azure portál, Atlassian felhő **tartomány és az URL-címek** a szakasz.
    
    c. Bejelentkezés az URL-címe a Atlassian felhő bérlői URL-CÍMÉT. 

    > [!NOTE]
    > A meglévő ügyfeleknek kell választania **Igen, a konfiguráció frissítése** frissítése után a **SP identitás azonosító** és **SP helyességi feltétel ügyfél szolgáltatás URL-címe** értékek az Azure portálon. Új ügyfelek nem kell végrehajtania ezt a lépést. 
    
20. Az Azure portálon kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Hozzon létre egy Atlassian felhő tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók Atlassian felhő bejelentkezni, akkor ki kell építenie Atlassian felhőbe. Atlassian felhő esetén kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. A hely felügyeleti területen kattintson a **felhasználók** gomb

    ![Atlassian felhő felhasználó létrehozása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Kattintson a **a meghívás felhasználói** Atlassian felhőalapú felhasználó létrehozása gomb.

    ![Atlassian felhő felhasználó létrehozása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Adja meg a felhasználó **E-mail cím** , és rendelje hozzá az alkalmazás-hozzáférés. 

    ![Atlassian felhő felhasználó létrehozása](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Kattintson a **meghívott felhasználóknak** gomb, elküldi az e-mailek meghívót a felhasználónak, és a meghívót elfogadása után a felhasználó a rendszerben aktív lesz. 

>[!NOTE] 
>A tömeges felhasználók kattintással is létrehozhat a **tömeges létrehozása** gombra a felhasználók szakaszban.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Atlassian felhőalapú Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Atlassian felhő, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Atlassian felhő**.

    ![Az alkalmazások listáját a Atlassian felhő hivatkozás](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Atlassian felhő csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Atlassian felhő alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

