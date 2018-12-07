---
title: 'Oktatóanyag: Azure Active Directory-integráció a Qlik Sense vállalati |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Qlik Sense vállalati között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2018
ms.author: jeedes
ms.openlocfilehash: 24353f89f7c833a16675841f741a4a81ac34597c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015220"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a Qlik Sense Enterprise-zal

Ebben az oktatóanyagban elsajátíthatja, hogyan Qlik Sense vállalati integrálható az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Qlik Sense vállalati nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Qlik Sense vállalati Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Qlik Sense Enterprise (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Qlik Sense vállalati, a következőkre van szükség:

- Azure AD-előfizetés
- A Qlik Sense vállalati egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Qlik Sense vállalati hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Qlik Sense vállalati hozzáadása a katalógusból
Az Azure AD integrálása a Qlik Sense vállalati konfigurálásához hozzá kell Qlik Sense vállalati a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Qlik Sense vállalati a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Qlik Sense vállalati**, jelölje be **Qlik Sense vállalati** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Qlik Sense Enterprise](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a Qlik Sense vállalati a teszt "Britta Simon" nevű felhasználó az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Qlik Sense vállalati tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Qlik Sense vállalati hivatkozás kapcsolata kell létrehozni.

Qlik Sense Enterprise, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Qlik Sense vállalati tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Qlik Sense vállalati tesztfelhasználót](#create-a-qlik-sense-enterprise-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a Qlik Sense vállalat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Qlik Sense vállalati alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a Qlik Sense vállalati, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Qlik Sense vállalati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Az a **Qlik Sense vállalati tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Qlik Sense vállalati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-azonosító és válasz URL-cím, amelyeket később ebben az oktatóanyagban, vagy forduljon a [Qlik Sense vállalati ügyfél-támogatási csapatának](https://www.qlik.com/us/services/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Készítse elő az összevonási metaadatainak XML-fájlt, így feltöltheti, amely a Qlik Sense-kiszolgálóhoz.

    > [!NOTE]
    > Mielőtt feltöltené a IdP-metaadatok a Qlik Sense-kiszolgálóhoz, eltávolítani a megfelelő működéshez az Azure AD között adatokat kell szerkeszteni kell a fájlt és a Qlik Sense kiszolgáló.

    ![QlikSense][qs24]

    a. Nyissa meg a federationmetadata.XML mintát követi fájlt, amely már letöltötte az Azure Portalról egy szövegszerkesztőben.

    b. Keresse meg az értéket **található Securitytokenservicetype**.  Nincsenek négy bejegyzést (nyitó és záró elem címkék két pár).

    c. Törölje a található Securitytokenservicetype címkék és minden információt a kettő között a fájlból.

    d. Mentse a fájlt, és a közeli tartani a dokumentum későbbi használatra.

7. Keresse meg, a Qlik Sense Qlik felügyeleti konzol (QMC) hozhat létre virtuális proxybeállításokkal felhasználóként.

8. A QMC, kattintson a **virtuális proxyk** menüpontot.

    ![QlikSense][qs6]

9. A képernyő alján kattintson a **új létrehozása** gombra.

    ![QlikSense][qs7]

10. A virtuális proxy szerkesztési képernyő jelenik meg.  Az a képernyő jobb oldalán, a konfigurációs beállítások láthatóvá tétele menüje.

    ![QlikSense][qs9]

11. Az azonosító menü beállítás be van jelölve és írja be a azonosító adatokat az Azure virtuális proxy konfigurálása.

    ![QlikSense][qs8]  

    a. A **leírás** mező egy rövid nevet a virtuális proxykonfigurációt.  Adjon meg egy értéket egy leírást.

    b. A **előtag** mező azonosítja a virtuális proxy végpont az Azure AD egyszeri bejelentkezés Qlik Sense való kapcsolódáshoz.  Adja meg a virtuális proxy előtag egyedi nevét.

    c. **Inaktivitás időkorlátja (perc)** van az időtúllépési érték a virtuális proxyn keresztül.

    d. A **munkamenet cookie-k fejlécnév** a Qlik Sense munkamenet a felhasználó sikeres hitelesítés után kap a munkamenet-azonosító tárolására cookie nevét.  A névnek egyedinek kell lennie.

12. Kattintson a hitelesítési menüelem látható.  A hitelesítési képernyő jelenik meg.

    ![QlikSense][qs10]

    a. A **névtelen hozzáférés mód** legördülő lista határozza meg, ha a névtelen felhasználók elérhessék-e a Qlik Sense a virtuális proxyn keresztül.  Az alapértelmezett beállítás nem nincs névtelen felhasználó.

    b. A **hitelesítési módszer** legördülő meghatározza, hogy a hitelesítési séma a virtuális proxy fogja használni.  A legördülő listából válassza ki a SAML.  További beállítások eredményeképpen jelennek meg.

    c. Az a **SAML gazdagép URI mező**, adjon meg a hostname felhasználókat adja meg a Qlik Sense ezen virtuális SAML-proxyn keresztül történő eléréséhez.  Az állomásnév az URI-t, a Qlik Sense-kiszolgáló.

    d. Az a **SAML Entitásazonosító**, adja meg ugyanazt az értéket a SAML gazdagép URI mezőben megadott.

    e. A **identitásszolgáltató SAML-metaadatok** a korábban szerkesztett fájl a **összevonási metaadatainak szerkesztése az Azure AD-konfigurációból** szakaszban.  **Mielőtt feltöltené a identitásszolgáltató metaadatok, a fájl szerkeszthető kell** között az Azure AD megfelelő működés érdekében adatok eltávolítása és a Qlik Sense kiszolgáló.  **Tekintse meg a fenti utasításokat, ha a fájl még nem szerkeszthető.**  Ha a fájl kattintson a Tallózás gombra, és válassza ki a szerkesztett metaadatok fájlt feltölteni a virtuális proxykonfigurációt.

    f. Adja meg a nevét vagy a séma attribútumhivatkozás jelölő SAML-attribútumot a **UserID** az Azure AD elküldi a Qlik Sense-kiszolgálóhoz.  Az Azure-alkalmazás képernyők post konfigurációs séma referenciainformációk érhető el.  A name attribútum használatához adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Adja meg az értéket a **felhasználói címtárba** , amely csatolva lesz a felhasználók hitelesítéskor Qlik Sense-kiszolgálóhoz az Azure AD-n keresztül.  Nem változtatható értékek kell lennie.%n **szögletes szögletes zárójeleket []**.  Egy attribútum küldi el az Azure AD SAML helyességi feltétel használatához adja meg a nevét, a szövegmezőbe **nélkül** szögletes zárójelek között.

    h. A **SAML aláíró algoritmus** állítja be a service provider (a ez megkülönbözteti a kis Qlik Sense-kiszolgáló) tanúsítvány-aláírási a virtuális proxy konfigurálása.  Ha Qlik Sense-kiszolgáló Microsoft Enhanced RSA és az AES titkosítási szolgáltató használatával létrehozott egy megbízható tanúsítványt használ, módosítsa az SAML aláírási algoritmus **SHA-256**.

    i. A SAML-attribútum leképezés szakaszában további attribútumok, például a csoportok a Qlik Sense kell elküldeni a biztonsági szabályok használata lehetővé teszi.

13. Kattintson a **TERHELÉSELOSZTÁS** menüelem látható.  A terheléselosztás képernyő jelenik meg.

    ![QlikSense][qs11]

14. Kattintson a a **új csomópont hozzáadása a kiszolgáló** gomb, válassza motor csomópontot vagy csomópontokat a Qlik Sense küld-e a munkamenetek a betöltés terheléselosztás céljából, majd kattintson a **Hozzáadás** gombra.

    ![QlikSense][qs12]

15. Kattintson a speciális menüelem látható. A Speciális képernyő jelenik meg.

    ![QlikSense][qs13]

    A gazdagép-engedélyezési lista, amely fogadja a Qlik Sense-kiszolgálóhoz való kapcsolódás során állomásnevek azonosítja.  **Adja meg a felhasználóknak meg kell adnia Qlik Sense-kiszolgálóhoz való csatlakozáshoz állomásnév.** Az állomásnév értéke megegyezik az SAML gazdagép URI-t a https:// nélkül.

16. Kattintson a **alkalmaz** gombra.

    ![QlikSense][qs14]

17. Kattintson az OK gombra, fogadja el a figyelmeztető üzenet arról, hogy a virtuális proxy kapcsolódó proxyk újra kell indítani.

    ![QlikSense][qs15]

18. A képernyő jobb oldalán megjelenik a társított elem menü.  Kattintson a **proxyk** menüpont.

    ![QlikSense][qs16]

19. A proxy képernyő jelenik meg.  Kattintson a **hivatkozás** proxy csatolása a virtuális proxy alsó gomb.

    ![QlikSense][qs17]

20. Jelölje ki a proxy csomópontot, amely támogatja a virtuális proxykapcsolatot kezdeményezni, és kattintson a **hivatkozás** gombra.  Összekapcsolás, után a proxy társított proxyk alatt helyezkednek el.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Körülbelül öt-tíz számítógépből álló másodperc elteltével a frissítés QMC üzenet jelenik meg.  Kattintson a **frissítése QMC** gombra.

    ![QlikSense][qs20]

22. Amikor frissíti a QMC, kattintson a a **virtuális proxyk** menüpontot. Az SAML virtuális proxy új bejegyzést a képernyőn a táblázatban szerepel.  A virtuális proxy tétel egyetlen kattintással.

    ![QlikSense][qs51]

23. A képernyő alján aktiválja a letöltés SP metaadatok gombra.  Kattintson a **letöltése SP metaadatok** gombra kattintva a metaadatok mentése fájlba.

    ![QlikSense][qs52]

24. Nyissa meg az sp metaadatait tartalmazó fájl.  Figyelje meg a **entityID** bejegyzést, és a **AssertionConsumerService** bejegyzés.  Ezek az értékek egyenértékűek a **azonosító**, **bejelentkezési URL-cím** és a **válasz URL-cím** az Azure AD alkalmazás konfigurációjában. Illessze be ezeket az értékeket a **Qlik Sense vállalati tartomány és URL-címek** szakaszban az Azure AD alkalmazás konfigurációjában, ha azok nem egyeznek, akkor kell cserélni őket az Azure AD-alkalmazás konfigurációs varázslóban.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

   ![Az Azure Active Directory gomb](./media/qliksense-enterprise-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

   ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

   ![A Hozzáadás gombra.](./media/qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![A felhasználó párbeszédpanel](./media/qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. Az a **neve** mezőbe írja be **BrittaSimon**.

   b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

   c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

   d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-qlik-sense-enterprise-test-user"></a>Qlik Sense vállalati tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Qlik Sense vállalati felhasználó hoz létre. Együttműködve [Qlik Sense vállalati ügyfél-támogatási csapatának](https://www.qlik.com/us/services/support) a felhasználók hozzáadása a Qlik Sense nagyvállalati platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Qlik Sense vállalati Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a Qlik Sense Enterprise, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Qlik Sense vállalati**.

    ![Az alkalmazások listáját a Qlik Sense vállalati hivatkozás](./media/qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Qlik Sense vállalati csempére kattint, meg kell lekérése automatikusan bejelentkezett a Qlik Sense vállalati alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png