---
title: 'Oktatóanyag: Azure Active Directory-integráció a Qlik logika vállalati |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Qlik logika vállalati között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 8cca4fb3ee10d56481cefccb7ea869b6bf13109c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a Qlik logika vállalati

Ebben az oktatóanyagban elsajátíthatja Qlik logika vállalati integrálása az Azure Active Directory (Azure AD).

Qlik logika vállalati integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Qlik logika vállalati szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Qlik logika vállalati (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Qlik logika vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Qlik logika vállalati egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Qlik logika vállalati hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>A gyűjteményből Qlik logika vállalati hozzáadása
Az Azure AD integrálása a Qlik logika Enterprise konfigurálásához kell hozzáadnia Qlik logika vállalati a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Qlik logika vállalati hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Qlik logika vállalati**, jelölje be **Qlik logika vállalati** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Qlik logika Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Qlik logika vállalati "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Qlik logika vállalati a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Qlik logika vállalati közötti kapcsolat kapcsolatot kell létrehozni.

Qlik logika vállalat, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Qlik logika vállalati tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Qlik logika vállalati tesztfelhasználó létrehozása](#create-a-qlik-sense-enterprise-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon Qlik logika vállalat, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Qlik logika vállalati alkalmazásban.

**A Qlik logika vállalati konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Qlik logika vállalati** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Az a **Qlik logika vállalati tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Qlik logika vállalati tartomány és az URL-címek](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Jegyezze fel ezt az URI vége a lezáró perjelet. Szükség.
    
    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója, amelyeket ez az oktatóanyag, vagy forduljon a [Qlik logika vállalati ügyfél-támogatási csoport](https://www.qlik.com/us/services/support) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Készítse elő az összevonási metaadatok XML-fájl, így feltöltheti, amely Qlik logika kiszolgálóra.
   
    > [!NOTE]
    > A kiállító terjesztési hely metaadatok feltöltését a Qlik logika kiszolgálóra, mielőtt az futtatásával távolítsa el az Azure AD között megfelelő működés érdekében lehet szerkeszteni kell a fájlt és Qlik logika kiszolgáló.
    
    ![QlikSense][qs24]
   
    a. Nyissa meg az FederationMetaData.xml fájlt egy szövegszerkesztőben Azure portálról letöltött.
   
    b. Keresse meg az érték **RoleDescriptor**.  Négy bejegyzést (nyitó és záró elemcímkék két pár) is tartalmaz.
   
    c. Törölje a RoleDescriptor címkéket és az összes adatot a kettő között a fájlból.
   
    d. Mentse a fájlt, és a dokumentum későbbi használatra közelben tartani.

7. Keresse meg a a Qlik logika Qlik felügyeleti konzol (QMC) hozhat létre virtuális proxybeállításokkal felhasználóként.

8. A QMC, kattintson a a **virtuális proxyk** menüpont.
   
    ![QlikSense][qs6] 

9. A képernyő alján kattintson a **hozzon létre új** gombra.
   
    ![QlikSense][qs7]

10. A virtuális proxy Szerkesztés képernyő jelenik meg.  A képernyő jobb oldalán van egy menüben adja meg a megfelelő konfigurációs beállítások láthatók.
   
    ![QlikSense][qs9]

11. Az azonosító menü beállítás be van jelölve írja be az Azure virtuális proxykonfigurációt a azonosító adatokat.
    
    ![QlikSense][qs8]  
    
    a. A **leírás** mező értéke egy rövid nevet a virtuális proxybeállításait.  Adjon meg egy értéket leírását.
    
    b. A **előtag** mező azonosítja a virtuális proxy végpont Qlik logika az Azure AD egyszeri bejelentkezéshez történő kapcsolódáshoz.  Adja meg a virtuális proxy előtag egyedi nevét.
    
    c. **Munkamenet inaktivitás időkorlátja (perc)** az e virtuális proxyn keresztül történő kapcsolatok időkorlátot.
    
    d. A **munkamenet cookie-k fejlécnév** tárolja a munkamenet-azonosító a sikeres hitelesítést követően a felhasználó kap Qlik logika munkamenet cookie-név.  A névnek egyedinek kell lennie.

12. Kattintson a hitelesítési menüpont láthatóvá.  A hitelesítési képernyő jelenik meg.
    
    ![QlikSense][qs10]
    
    a. A **névtelen hozzáférési mód** legördülő lista határozza meg, ha a névtelen felhasználók férhetnek hozzá Qlik logika virtuális proxyn keresztül.  Az alapértelmezett beállítás nem névtelen felhasználó.
    
    b. A **hitelesítési módszer** legördülő lista határozza meg a hitelesítési séma a virtuális proxy fogja használni.  A legördülő listából válassza ki a SAML-alapú.  További beállítások következtében jelennek meg.
    
    c. Az a **SAML gazdagép URI mező**, adjon meg az állomásnév felhasználók Qlik logika a SAML-alapú virtuális proxyn keresztül történő eléréséhez adja meg.  Az állomásnév az uri a Qlik logika kiszolgáló.
    
    d. Az a **SAML Entitásazonosító**, adja meg a SAML állomás URI mezőben megadott azonos érték.
    
    e. A **SAML IdP metaadatok** a korábban a szerkesztett fájl a **összevonási metaadatok szerkesztése az Azure AD-konfigurációból** szakasz.  **Mielőtt a kiállító terjesztési hely metaadatok feltöltése, a fájl szerkeszthető kell** futtatásával távolítsa el az Azure AD között megfelelő működés érdekében és Qlik logika kiszolgáló.  **Tekintse meg a fenti utasításokat kell-e a fájl még szerkeszthető.**  Amennyiben a fájl kattintson a Tallózás gombra, és válassza ki a szerkesztett metaadatok fájlt feltölteni azt a virtuális proxykonfigurációt.
    
    f. Adja meg a nevet vagy séma attribútumhivatkozás a SAML attribútum képviselő a **UserID** Qlik logika kiszolgálónak küldi az Azure AD.  Az Azure alkalmazás képernyők feladás egy vagy több konfigurációs séma referencia jellegű információt érhető el.  A name attribútum használatához adja meg a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Adja meg az értéket a **felhasználói directory** , amely csatolva lesz a felhasználók Qlik logika kiszolgálóhoz az Azure AD-n keresztül való hitelesítéshez.  Szoftveresen kötött értékeket kell lennie.%n **szögletes szögletes zárójelbe []**.  Az Azure AD SAML-előfeltétel küldött attribútum használandó mezőben adja meg az attribútum neve a **nélkül** szögletes zárójelek között.
    
    h. A **SAML aláírási algoritmus** beállítja a szolgáltatás-szolgáltatója (a kis Qlik logika kiszolgáló) a tanúsítvány-aláírási virtuális proxy-konfigurációhoz.  Ha Qlik logika kiszolgáló Microsoft Enhanced RSA és az AES kriptográfiai szolgáltató használatával létrehozott megbízható tanúsítványt használ, módosítsa a SAML aláírási algoritmus **SHA-256**.
    
    i. A SAML attribútum leképezési szakasz lehetővé teszi további tulajdonságai, például a csoportok a biztonsági szabályok használható Qlik logika küldendő.

13. Kattintson a **TERHELÉSELOSZTÁS** láthatóvá menüjét.  A terheléselosztás képernyő jelenik meg.
    
    ![QlikSense][qs11]

14. Kattintson a a **új csomópont hozzáadása a kiszolgáló** gombra, jelölje be a vezérlő csomópont vagy csomópontok Qlik logika küldi a munkamenetek terheléselosztást terheléselosztási célra, és kattintson a **Hozzáadás** gombra.
    
    ![QlikSense][qs12]

15. Kattintson a speciális menüpont láthatóvá. A Speciális képernyő jelenik meg.
    
    ![QlikSense][qs13]
    
    A gazdagép fehér lista, amely a Qlik logika kiszolgálóhoz való csatlakozáskor az elfogadás állomásnevek azonosítja.  **Adja meg az állomásnevet kell megadnia a felhasználók Qlik logika kiszolgálóhoz való csatlakozáskor.** Az állomásnév megadása értéke megegyezik a SAML állomás uri a https:// nélkül.

16. Kattintson a **alkalmaz** gombra.
    
    ![QlikSense][qs14]

17. Kattintson az OK gombra, fogadja el a figyelmeztető üzenet arról, hogy a virtuális proxy kapcsolódó proxyk újraindul.
    
    ![QlikSense][qs15]

18. A képernyő jobb oldalán megjelenik a társított elem menü.  Kattintson a **proxyk** menüjét.
    
    ![QlikSense][qs16]

19. A proxy képernyő jelenik meg.  Kattintson a **hivatkozás** panel alján a proxy csatolása a virtuális proxy.
    
    ![QlikSense][qs17]

20. Jelölje ki a proxy csomópontot, amely támogatja a virtuális proxy kapcsolat, és kattintson a **hivatkozás** gombra.  Linking, miután a proxy a társított proxyk alatt jelennek meg.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Körülbelül öt-tíz számítógépből álló másodperc után a frissítési QMC üzenet jelenik meg.  Kattintson a **frissítése QMC** gombra.
    
    ![QlikSense][qs20]

22. Amikor frissíti a QMC, kattintson a a **virtuális proxyk** menüpont. Az új SAML-alapú virtuális proxy bejegyzést a képernyőn a táblázatban szerepel.  A virtuális proxy bejegyzésre egyetlen kattintással.
    
    ![QlikSense][qs51]

23. A képernyő alján a letöltés SP metaadatok gomb aktiválódik.  Kattintson a **letöltése SP metaadatok** gombra kattintva a metaadatok mentése fájlba.
    
    ![QlikSense][qs52]

24. Nyissa meg az sp metaadatait tartalmazó fájl.  Figyelje meg a **entityid beállítást** bejegyzést, és a **AssertionConsumerService** bejegyzés.  Ezek az értékek egyenértékűek a **azonosító** és a **bejelentkezési URL-cím** az Azure AD alkalmazás konfigurációjának. Ezek az értékek illessze be a **Qlik logika vállalati tartomány és az URL-címek** szakaszban az Azure AD alkalmazás konfigurációjának, ha azok nem egyeznek, akkor le kell cserélni őket az Azure AD alkalmazás-konfigurációs varázsló.
    
    ![QlikSense][qs53]

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

   ![Az Azure Active Directory gomb](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

   ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

   ![A Hozzáadás gombra.](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![A felhasználó párbeszédpanel](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. Az a **neve** mezőbe írja be **BrittaSimon**.

   b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

   c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

   d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Qlik logika vállalati tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon meghívta Qlik logika vállalati felhasználó létrehozása. Együttműködve [Qlik logika vállalati ügyfél-támogatási csoport](https://www.qlik.com/us/services/support) a felhasználók hozzáadása a Qlik logika vállalati platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Qlik logika vállalati Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Qlik logika vállalati, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Qlik logika vállalati**.

    ![Az alkalmazások listáját a Qlik logika vállalati hivatkozás](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Qlik logika vállalati csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Qlik logika vállalati alkalmazás. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

