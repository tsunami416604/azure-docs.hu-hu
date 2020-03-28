---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Qlik Sense Enterprise szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Qlik Sense Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9349d8ed330a00a64922a44f99910f9eeeb0df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136448"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Oktatóanyag: A Qlik Sense Enterprise integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Qlik Sense Enterprise szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Qlik Sense Enterprise vállalatot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, amely hozzáfér a Qlik Sense Enterprise szolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezzenek a Qlik Sense Enterprise-ra az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Qlik Sense Enterprise egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 
* A Qlik Sense Enterprise támogatja az **SP** által kezdeményezett SSO-t.
* A Qlik Sense Enterprise támogatja **a just-in-time kiépítést**

* A Qlik Sense Enterprise konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Qlik Sense Enterprise hozzáadása a galériából

A Qlik Sense Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Qlik Sense Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Qlik Sense Enterprise** kifejezést a keresőmezőbe.
1. Válassza a **Qlik Sense Enterprise** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Qlik Sense Enterprise segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a qlik sense enterprise kapcsolódó felhasználó javunkra.

Az Azure AD SSO qlik sense enterprise-szal való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Qlik Sense Enterprise SSO-t](#configure-qlik-sense-enterprise-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Qlik Sense Enterprise tesztfelhasználót](#create-qlik-sense-enterprise-test-user)** – ha a Qlik Sense Enterprise-ban britta Simon megfelelője is részt szeretne, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Qlik Sense Enterprise** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet az alábbi minta egyikével:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosító és válasz URL-cím, Amely később az oktatóanyag, vagy lépjen kapcsolatba [Qlik Sense Enterprise Client támogatási csapat,](https://www.qlik.com/us/services/support) hogy ezeket az értékeket. Az URL-címek alapértelmezett portja 443, de testre szabhatja a szervezet igényeinek.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML-t** a megadott beállítások közül a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Qlik Sense Enterprise-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Qlik Sense Enterprise lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-qlik-sense-enterprise-sso"></a>A Qlik Sense vállalati sso konfigurálása

1. Készítse elő az összevonási metaadat-XML-fájlt, hogy feltölthesse azt a Qlik Sense kiszolgálóra.

    > [!NOTE]
    > Az IdP-metaadatok Qlik Sense-kiszolgálóra való feltöltése előtt a fájlt kell szerkesztetten eltávolítani az adatokat az Azure AD és a Qlik Sense kiszolgáló közötti megfelelő működés biztosítása érdekében.

    ![QlikSense között][qs24]

    a. Nyissa meg a FederationMetaData.xml fájlt, amelyet az Azure Portalról töltött le egy szövegszerkesztőben.

    b. Keresse meg a **RoleDescriptor**értéket.  Négy bejegyzés van (két pár nyitó és záró elemcímke).

    c. Törölje a RoleDescriptor címkéket és a köztük lévő összes információt a fájlból.

    d. Mentse a fájlt, és tartsa a közelben későbbi használatra a dokumentumban.

2. Keresse meg a Qlik Sense Qlik Management Console (QMC) felhasználóként, aki virtuális proxy konfigurációkat hozhat létre.

3. A QMC-ben kattintson a **Virtuális proxyk** menüelemre.

    ![QlikSense között][qs6]

4. A képernyő alján kattintson az **Új létrehozása** gombra.

    ![QlikSense között][qs7]

5. Megjelenik a Virtuális proxy szerkesztése képernyő.  A képernyő jobb oldalán található egy menü, amely láthatóvá teszi a konfigurációs beállításokat.

    ![QlikSense között][qs9]

6. Az Azonosítás menü bevan jelölve, adja meg az Azure virtuális proxykonfigurációazonosító adatait.

    ![QlikSense között][qs8]  

    a. A **Leírás** mező a virtuális proxykonfiguráció rövid neve.  Adjon meg egy leírást.

    b. Az **előtag** mező azonosítja a virtuális proxy végpont az Azure AD egyszeri bejelentkezéssel a Qlik Sense-hez való csatlakozáshoz.  Adja meg a virtuális proxy egyedi előtagnevét.

    c. **A munkamenet inaktivitásának időtúlszáma (perc)** a virtuális proxyn keresztüli kapcsolatok időtúlszáma.

    d. A **munkamenet cookie fejlécének neve** az a cookie-név, amely a sikeres hitelesítés után a felhasználó által kapott Qlik Sense munkamenet munkamenet-azonosítóját tárolja.  A névnek egyedinek kell lennie.

7. A hitelesítés menügombra kattintva láthatóvá teheti azt.  Megjelenik a Hitelesítés képernyő.

    ![QlikSense között][qs10]

    a. A **Névtelen hozzáférési mód** legördülő menü határozza meg, hogy a névtelen felhasználók hozzáférhetnek-e a Qlik Sense-hez a virtuális proxyn keresztül.  Az alapértelmezett beállítás: Nincs névtelen felhasználó.

    b. A **Hitelesítési módszer** legördülő menü határozza meg a virtuális proxy által használni fog.  Válassza az SAML elemet a legördülő listából.  Ennek eredményeképpen további beállítások jelennek meg.

    c. Az **SAML host URI mezőben**adja meg a felhasználók által a Qlik Sense eléréséhez megadott állomásnevet ezen az SAML virtuális proxyn keresztül.  A hostname a Qlik Sense szerver uri-ja.

    d. Az **SAML entitásazonosítóban**adja meg ugyanazt az értéket, amelyet az SAML állomás URI-mezője megadott.

    e. Az **SAML IdP metaadatok** a fájl szerkesztése korábban az **Azure AD-konfiguráció** szakaszban szerkesztett fájl.  **Az IdP-metaadatok feltöltése előtt a fájlt kell szerkesztett,** hogy távolítsa el az információkat az Azure AD és a Qlik Sense kiszolgáló közötti megfelelő működés biztosítása érdekében.  **Kérjük, olvassa el a fenti utasításokat, ha a fájl még nem szerkesztett.**  Ha a fájlt szerkesztették, kattintson a Tallózás gombra, és jelölje ki a szerkesztett metaadatfájlt a virtuális proxy konfigurációjába való feltöltéshez.

    f. Adja meg az Azure **AD-t** a Qlik Sense-kiszolgálónak küldött UserID-attribútum attribútumnevét vagy sémahivatkozását.  Séma referenciainformáció érhető el az Azure-alkalmazás képernyői konfiguráció után.  A névattribútum használatához írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`be a be írt értéket.

    g. Adja meg a **felhasználói könyvtár** értékét, amely et a felhasználókcsatolják, amikor hitelesítik magukat a Qlik Sense-kiszolgálón az Azure AD-n keresztül.  A kódolt értékeket **szögletes zárójelek veszik körül []**.  Az Azure AD SAML-ben küldött attribútum használatához adja meg az attribútum nevét ebben a szövegmezőben szögletes zárójelek **nélkül.**

    h. Az **SAML aláíró algoritmus** beállítja a szolgáltató (ebben az esetben Qlik Sense szerver) tanúsítvány aláírása a virtuális proxy konfiguráció.  Ha a Qlik Sense kiszolgáló a Microsoft Enhanced RSA és AES kriptográfiai szolgáltató használatával létrehozott megbízható tanúsítványt használ, módosítsa az SAML aláíró algoritmust **SHA-256-ra.**

    i. Az SAML attribútumleképezési szakasz lehetővé teszi, hogy további attribútumokat, például csoportokat kell küldeni a Qlik Sense biztonsági szabályokban való használatra.

8. A beállításhoz kattintson a **TERHELÉSELOSZTÁS** menüre.  Megjelenik a Terheléselosztás képernyő.

    ![QlikSense között][qs11]

9. Kattintson az **Új kiszolgálócsomópont hozzáadása** gombra, válassza ki a motorcsomópontokat vagy csomópontokat, ahová a Qlik Sense munkameneteket küld terheléselosztási célokra, majd kattintson a **Hozzáadás** gombra.

    ![QlikSense között][qs12]

10. Kattintson a Speciális menüre, hogy láthatóvá tegye. Megjelenik a Speciális képernyő.

    ![QlikSense között][qs13]

    A Host allow list azonosítja a Qlik Sense kiszolgálóhoz való csatlakozáskor elfogadott állomásneveket.  **Adja meg a hostname felhasználók meg, amikor csatlakozik Qlik Sense szerver.** A állomásnév megegyezik az HTTPS:// nélküli SAML host uri értékkel.

11. Kattintson az **Alkalmaz** gombra.

    ![QlikSense között][qs14]

12. Kattintson az OK gombra a virtuális proxyhoz kapcsolódó proxykat tartalmazó figyelmeztető üzenet elfogadásához.

    ![QlikSense között][qs15]

13. A képernyő jobb oldalán megjelenik a Társított elemek menü.  Kattintson a **Proxyk** menüre.

    ![QlikSense között][qs16]

14. Megjelenik a proxyképernyő.  Kattintson az alsó **Hivatkozás** gombra, ha proxyt szeretne csatolni a virtuális proxyhoz.

    ![QlikSense között][qs17]

15. Jelölje ki azt a proxycsomópontot, amely támogatja ezt a virtuális proxykapcsolatot, és kattintson a **Hivatkozás** gombra.  Csatolása után, a proxy lesz listázva a kapcsolódó proxyk.

    ![QlikSense között][qs18]
  
    ![QlikSense között][qs19]

16. Körülbelül 5–10 másodperc elteltével megjelenik a QMC frissítése üzenet.  Kattintson a **QMC frissítése** gombra.

    ![QlikSense között][qs20]

17. Amikor a QMC frissül, kattintson a **Virtuális proxyk** menüelemre. Az új SAML virtuális proxybejegyzés a képernyőn látható táblázatban található.  Egyetlen kattintás a virtuális proxy bejegyzést.

    ![QlikSense között][qs51]

18. A képernyő alján aktiválódik az SP metaadatának letöltése gomb.  Az **SP metaadatainak letöltése gombra** kattintva mentse a metaadatokat egy fájlba.

    ![QlikSense között][qs52]

19. Nyissa meg az sp metaadatfájlt.  Figyelje meg az **entityID** bejegyzést és a **AssertionConsumerService** bejegyzést.  Ezek az értékek egyenértékűek az **azonosítóval,** a **Bejelentkezési URL-címés** a **válasz URL-címével** az Azure AD alkalmazás konfigurációjában. Illessze be ezeket az értékeket a **Qlik Sense enterprise tartomány és URL-címek** szakaszban az Azure AD-alkalmazás konfigurációja, ha azok nem egyeznek, majd le kell cserélni őket az Azure AD App konfigurációs varázsló.

    ![QlikSense között][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise tesztfelhasználó létrehozása

A Qlik Sense Enterprise támogatja **a just-in-time kiépítést**, a felhasználók automatikusan hozzáadva a Qlik Sense Enterprise "USERS" tárházához, ahogy az SSO funkciót használják. Ezenkívül az ügyfelek használhatják a QMC-t, és létrehozhatnak egy UDC-t (User Directory-összekötő) a Qlik Sense Enterprise felhasználóinak előzetes feltöltéséhez a választott LDAP-ból, például az Active Directoryból és másokból.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Qlik Sense Enterprise csempét, automatikusan be kell jelentkeznie a Qlik Sense Enterprise-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

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
