---
title: "Az alkalmazások Azure AD egyszeri bejelentkezés konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan önkiszolgáló csatlakozzon az Azure Active Directoryhoz, SAML és a jelszó-alapú egyszeri Bejelentkezést használó alkalmazások"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2018
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e161bb308f08e2a7c137c696e77bf1dfb86e8d31
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz
Ez a cikk egy szolgáltatás, amely lehetővé teszi a rendszergazdák egyszeri bejelentkezés alkalmazásokhoz nem szerepel az Azure Active Directory-alkalmazásgyűjtemény konfigurálása tárgya *kód írása nélkül*. Ez a funkció a 2015. November 18 technical Preview-ban jelent meg, és szerepel a [Azure Active Directory Premium](active-directory-editions.md). Ha ehelyett fejlesztői útmutató az egyéni alkalmazások integrálása az Azure AD kód keres, tekintse meg [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).

Az Azure Active Directory alkalmazáskatalógusában felsorolja alkalmazásokat, amelyek kat közismerten támogató egy formája, amelyet az egyszeri bejelentkezés az Azure Active Directoryval, a [Ez a cikk](active-directory-appssoaccess-whatis.md). (A egy informatikai szakember vagy a rendszer integráló a szervezet) megkeresése az alkalmazáshoz, amelyhez csatlakozni, elkezdheti az egyszeri bejelentkezés engedélyezése az Azure-portálon jelenik meg a részletes utasításokat követve.

Az ügyfelek [Azure Active Directory Premium](active-directory-editions.md) licenc is ezekhez a kiegészítő lehetőségekhez beolvasása:

* Önkiszolgáló integrációs bármely alkalmazás, amely támogatja az SAML 2.0 identitás-szolgáltatóktól (a Szolgáltató által kezdeményezett vagy a kiállító terjesztési hely által kezdeményezett)
* A webes alkalmazás, amelynek használatával egy bejelentkezési lap HTML-alapú önkiszolgáló integrációs [jelszó-alapú egyszeri bejelentkezés](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Kapcsolat az önkiszolgáló felhasználó történő üzembe helyezéséhez a SCIM protokollt használó alkalmazások ([itt leírt](active-directory-scim-provisioning.md))
* Hivatkozások a bármely alkalmazás hozzáadása a [Office 365 alkalmazás indító](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Ez magában foglalhatja nem csak a SaaS-alkalmazásokhoz, amelyek használják, de rendelkezik nem még nincs előre telepített az Azure AD alkalmazás gyűjteményébe, de külső webes alkalmazásokhoz, amelyek azt szabályozzák, vagy a felhőben, vagy a helyszíni kiszolgálók alkalmazva van a szervezet.

Ezek a képességek, más néven *app integrációs sablonok*, adja meg a szabvány alapú csatlakozási pontok SAML, SCIM vagy űrlapalapú hitelesítést támogató alkalmazások esetében, és rugalmas lehetőségeket és a beállításokat széles körű több alkalmazáshoz is kompatibilisek. 

## <a name="adding-an-unlisted-application"></a>Egy fel nem sorolt alkalmazás hozzáadása
Alkalmazás-integráció sablonnal alkalmazás csatlakoztatása az Azure-portálon az Azure Active Directory rendszergazdai fiókkal bejelentkezni, és keresse meg a a **Active Directory > Vállalati alkalmazások > új alkalmazás > Nem-gyűjtemény alkalmazás** szakaszban jelölje be **hozzáadása**, majd **alkalmazás hozzáadása a gyűjteményből**.

  ![][1]

A gyűjtemény, adhat hozzá egy fel nem sorolt alkalmazás kiválasztásával a **nem-gyűjtemény alkalmazás** csempe, ha a kívánt alkalmazás nem található a keresési eredmények látható. Után írja be az alkalmazás nevét, az egyszeri bejelentkezésre vonatkozó beállításokat és viselkedése is konfigurálhatja. 

**Egyszerű ötlet**: ajánlott eljárásként, a keresési funkció segítségével ellenőrizze, hogy az alkalmazás már létezik-e az alkalmazás gyűjteménye. Ha az alkalmazás megtalálható-e, és annak leírását az "egyszeri bejelentkezéshez" említi, majd az alkalmazás már támogatja az összevont egyszeri bejelentkezést.

  ![][2]

Az alkalmazás így hozzáadása – ebben a rendelkezésre álló előre integrált alkalmazások nagyon hasonló élményt nyújt. Elindításához válassza ki a **konfigurálása egyszeri bejelentkezéshez**. A következő képernyő mutatja be a következő három módon konfigurálásához egyszeri bejelentkezést, amelyek a következő szakaszok ismertetik.

  ![][3]

## <a name="saml-based-sign-on"></a>SAML-alapú bejelentkezés
Válassza ezt a beállítást, az alkalmazás SAML-alapú hitelesítés beállítása. Ehhez szükséges, hogy az alkalmazás támogatási SAML 2.0, és információkat gyűjtenek a SAML képességeit, a folytatás előtt az alkalmazás használatáról. Miután kiválasztott **következő**, három különböző URL-eket a SAML-végpontok az alkalmazás megfelelő megadását kéri.

  ![][4]

Ezek a következők:

* **Bejelentkezés az URL-címe (Szolgáltató által kezdeményezett csak)** – Ha a felhasználó megnyitja a jelentkezik be az alkalmazás számára. Ha az alkalmazás szolgáltatás szolgáltató által kezdeményezett egyszeri bejelentkezést, végrehajtására van konfigurálva, majd amikor a felhasználók megnyitják az URL-címet, a szolgáltató a szükséges átirányítást tegye fel az Azure ad Szolgáltatásba, és jelentkezzen be a felhasználó hitelesítéséhez. Ha ez a mező nem üres, majd az Azure AD fogja használni az Office 365 és az Azure AD hozzáférési Panel az alkalmazás elindításához az URL-cím. Ha ez a mező nincs megadva, akkor az Azure AD ehelyett hajtsa végre az identitásszolgáltató-bejelentkezés által kezdeményezett, az alkalmazás indításakor az Office 365, az Azure AD hozzáférési Panel vagy az Azure AD egyetlen bejelentkezési URL-címről (az irányítópult fület a copyable).
* **Kiállítói URL-címe** -a kibocsátó URL-cím egyedileg kell azonosítania az alkalmazás, amelynek egyszeri bejelentkezésre van konfigurálva. Ez az érték, amely az Azure AD küld vissza az alkalmazásra, amely a **célközönség** a SAML-jogkivonat és az alkalmazás lemezszámmal érvényesíti. Ez az érték is frissült a **Entitásazonosító** bármely, az alkalmazás által biztosított SAML metaadatokban. Ellenőrizze az alkalmazás SAML dokumentációjában talál részletes információt Entitásazonosító vagy a célközönség értéke pedig. Alább látható egy példa a célközönség URL-cím hogyan jelenik meg az adott vissza az alkalmazásba SAML-jogkivonat:
  
  ```
  <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
    ```

* **Válasz URL-cím** -válasz URL-címe, ahol az alkalmazás fogadni az SAML-jogkivonat vár. Ez más néven a a **helyességi feltétel fogyasztói Service (ACS) URL-cím**. Ellenőrizze az alkalmazás SAML dokumentációjában talál részletes információt a SAML token válasz és ACS URL-címe van.
  Miután ezek megadott, kattintson a **következő** folytassa a következő képernyő. Ez a képernyő mit kell megadni az alkalmazás oldalon engedélyezze, hogy fogadja el az Azure AD SAML-jogkivonatból információkat biztosít.

  * Kattintson a **Litware konfigurálása**.
  
    ![][5]

Mely értékei szükségesek az alkalmazástól függően változnak, ezért ellenőrizze az alkalmazás SAML dokumentációjában találhatók. A **bejelentkezés** és **kijelentkezési** URL-címét egyaránt oldja fel a azonos végpont, amely az SAML-kéréskezeléshez végpont az az Azure AD példányával. A kiállítói URL-címe az érték, amely akkor jelenik meg, mint a "kiállító" belül a SAML-jogkivonat ki az alkalmazáshoz. 

Az alkalmazás konfigurálása után kattintson **következő** gombra, majd a **Complete** bezárhatja a párbeszédpanelt. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>Felhasználók és csoportok hozzárendelése az SAML-alkalmazás
Miután az alkalmazás konfigurációja használhatja az Azure Active Directory SAML-alapú identitás-szolgáltatóként, majd azt már majdnem kész teszteléséhez. Biztonsági ellenőrzés, mint az Azure AD lehetővé teheti, hogy jelentkezzen be az alkalmazáshoz, kivéve, ha azok nem biztosít hozzáférést az Azure AD jogkivonat nem állítanak ki. Felhasználók adható hozzáférés közvetlenül, vagy egy csoportot, amely tagja. 

Az alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, kattintson a **felhasználók hozzárendelése** gombra. Válassza ki a felhasználó vagy csoport szeretne rendelni, majd válassza ki a **hozzárendelése** gombra.

  ![][6]

A felhasználó hozzárendelése lehetővé teszi a felhasználó, valamint az alkalmazás megjelenik a felhasználói hozzáférési Panel egy csempe, amely a jogkivonatok kiállítása az Azure AD. Ha a felhasználó által használt Office 365 egy alkalmazás csempe is megjelennek az Office 365 alkalmazásindító. 

Feltöltheti a csempe emblémát használó alkalmazás a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>A SAML-jogkivonat kiállított jogcímek testreszabása
Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD fog kiadni egy SAML-jogkivonat az adatokat (vagy jogcímeket) tartalmazó alkalmazás, amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználónevét, e-mail cím, Utónév és Vezetéknév. 

Megtekintheti vagy szerkesztheti a jogcímek küldése az alkalmazásnak a SAML-jogkivonat a **attribútumok** fülre.

  ![][7]

Előfordulhat, hogy miért szerkesztése a SAML-jogkivonat kiadott jogcímeket kell két lehetséges oka lehet: •a alkalmazás írt eltérő szabályzatkészletet jogcím URI-azonosítók, vagy a jogcím-értékek •Your alkalmazás telepítve lett az NameIdentifier jogcím igényel az Azure Active Directoryban tárolja (AKA egyszerű felhasználónév) felhasználónév nem lehet. 

Hozzáadása és szerkesztése a jogcímek forgatókönyvek esetén információkért tekintse meg a [jogcímek testreszabása foglalkozó](active-directory-saml-claims-customization.md). 

### <a name="testing-the-saml-application"></a>A SAML-alkalmazás tesztelése
A SAML-alapú URL-címek és a tanúsítvány konfigurálása az Azure AD és az alkalmazás Azure, az alkalmazáshoz hozzárendelt felhasználók vagy csoportok és jogcímek felülvizsgálata és szerkeszthető, ha szükséges, majd jelentkezzen be az alkalmazás készen áll a felhasználó. 

Egyszerűen jelentkezzen be az Azure AD hozzáférési panel https://myapps.microsoft.com az alkalmazáshoz hozzárendelt felhasználói fiókkal, teszteléséhez, és kattintson a csempére a bejelentkezési folyamat egyetlen indítsa az alkalmazásra vonatkozóan. Alternatív megoldásként tallózással is kikeresheti közvetlenül azt a bejelentkezési URL-címet az alkalmazás és a bejelentkezési onnan. 

Hibakeresési tippeket, megjelenik ez [cikk az SAML-alapú egyszeri bejelentkezést alkalmazások hibakeresése](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>Jelszó egyszeri bejelentkezést.
Válassza ezt a lehetőséget választva [jelszó-alapú egyszeri bejelentkezést](active-directory-appssoaccess-whatis.md) , amely rendelkezik egy bejelentkezési lap HTML webalkalmazás. Egyszeri jelszó alapú is hívják jelszó vaulting, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezeléséhez. Akkor célszerű is forgatókönyvek, ahol több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

Miután kiválasztott **következő**, kérni fogja az alkalmazás webes bejelentkezési lap URL-címet. Vegye figyelembe, hogy a felhasználónév és jelszó bemeneti mezőket tartalmazó oldalon kell lennie. Ha meg van adva, az Azure AD elindítja elemzése a bejelentkezési lapon adjon meg egy felhasználónevet és jelszót adjon meg egy folyamatot. Ha a folyamat nem sikeres, majd azt végigvezeti Önt egy másik folyamat telepíthet egy bővítmény (igényli az Internet Explorer, a Chrome vagy a Firefox), amely lehetővé teszi a mezők manuálisan rögzítéséhez.

Amennyiben a bejelentkezési oldal rögzítése, felhasználók és csoportok rendelt és credential házirendeket állíthat be hasonlóan rendszeres [jelszó SSO alkalmazások](active-directory-appssoaccess-whatis.md).

Megjegyzés: Az alkalmazás használatára vonatkozó egy csempeembléma feltöltheti a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz. 

## <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezést.
Válassza ezt a beállítást, a szervezet Azure AD hozzáférési Panel vagy az Office 365 portál alkalmazásokra mutató hivatkozás hozzáadása. Ezzel hivatkozások felvétele egyéni webes alkalmazásokat, amelyek jelenleg használják az Azure Active Directory összevonási szolgáltatások (vagy más összevonási szolgáltatásból) helyett az Azure AD használatára a hitelesítéshez. Vagy mélyhivatkozással hozzáadása adott SharePoint-lapokat vagy más szeretné jelennek meg a felhasználói hozzáférés panelek weblapokat. 

Miután kiválasztott **következő**, kérni fogja a csatolni kívánt alkalmazás URL-címet. Ezt követően felhasználókhoz és csoportokhoz rendelt az alkalmazásról, így az alkalmazás megjelenik a [Office 365 alkalmazás indító](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) azoknak a felhasználóknak.

Megjegyzés: Az alkalmazás használatára vonatkozó egy csempeembléma feltöltheti a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [A SAML-jogkivonat előzetesen beépített alkalmazások számára kiállított jogcímek testreszabása](active-directory-saml-claims-customization.md)
* [Hibaelhárítási SAML-alapú egyszeri bejelentkezést.](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png