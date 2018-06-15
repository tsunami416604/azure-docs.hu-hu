---
title: Az alkalmazások Azure AD egyszeri bejelentkezés konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan önkiszolgáló csatlakozzon az Azure Active Directoryhoz, SAML és a jelszó-alapú egyszeri Bejelentkezést használó alkalmazások
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
ms.openlocfilehash: 3c115414fc6ae599342192196e256597c28e5aa6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293194"
---
# <a name="configure-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Egyszeri bejelentkezés alkalmazásokhoz, amelyek nincsenek rajta az Azure Active Directory alkalmazáskatalógusában konfigurálása

Ez a cikk egy szolgáltatás, amely lehetővé teszi a rendszergazdák egyszeri bejelentkezés alkalmazásokhoz nem szerepel az Azure Active Directory-alkalmazásgyűjtemény konfigurálása tárgya *kód írása nélkül*. Ez a funkció a 2015. November 18 technical Preview-ban jelent meg, és szerepel a [Azure Active Directory Premium](active-directory-whatis.md). Ha ehelyett fejlesztői útmutató az egyéni alkalmazások integrálása az Azure AD kód keres, tekintse meg [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).

Az Azure Active Directory alkalmazáskatalógusában felsorolja alkalmazásokat, amelyek kat közismerten támogató egy formája, amelyet az egyszeri bejelentkezés az Azure Active Directoryval, a [Ez a cikk](manage-apps/what-is-single-sign-on.md). (A egy informatikai szakember vagy a rendszer integráló a szervezet) megkeresése az alkalmazáshoz, amelyhez csatlakozni, elkezdheti az egyszeri bejelentkezés engedélyezése az Azure-portálon jelenik meg a részletes utasításokat követve.

Az ügyfelek [Azure Active Directory Premium](active-directory-whatis.md) licenc is ezekhez a kiegészítő lehetőségekhez beolvasása:

* Önkiszolgáló integrációs bármely alkalmazás, amely támogatja az SAML 2.0 identitás-szolgáltatóktól (a Szolgáltató által kezdeményezett vagy a kiállító terjesztési hely által kezdeményezett)
* A webes alkalmazás, amelynek használatával egy bejelentkezési lap HTML-alapú önkiszolgáló integrációs [jelszó-alapú egyszeri bejelentkezés](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* Kapcsolat az önkiszolgáló felhasználó történő üzembe helyezéséhez a SCIM protokollt használó alkalmazások ([itt leírt](manage-apps/use-scim-to-provision-users-and-groups.md))
* Hivatkozások a bármely alkalmazás hozzáadása a [Office 365 alkalmazás indító](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)

Ez magában foglalhatja nem csak a SaaS-alkalmazásokhoz, amelyek használják, de rendelkezik nem még nincs előre telepített az Azure AD alkalmazás gyűjteményébe, de külső webes alkalmazásokhoz, amelyek azt szabályozzák, vagy a felhőben, vagy a helyszíni kiszolgálók alkalmazva van a szervezet.

Ezek a képességek, más néven *app integrációs sablonok*, adja meg a szabvány alapú csatlakozási pontok SAML, SCIM vagy űrlapalapú hitelesítést támogató alkalmazások esetében, és rugalmas lehetőségeket és a beállításokat széles körű több alkalmazáshoz is kompatibilisek. 

## <a name="adding-an-unlisted-application"></a>Egy fel nem sorolt alkalmazás hozzáadása
Alkalmazás-integráció sablonnal alkalmazás csatlakoztatása az Azure portálra az Azure Active Directory rendszergazdai fiókkal jelentkezzen be. Keresse meg a **Active Directory > Vállalati alkalmazások > új alkalmazás > nem-gyűjtemény alkalmazás** szakaszban jelölje be **Hozzáadás**, majd **hozzáadhat egy alkalmazást a katalógusból** .

  ![](./media/active-directory-saas-custom-apps/customapp1.png)

A gyűjtemény, adhat hozzá egy fel nem sorolt alkalmazás kiválasztásával a **nem-gyűjtemény alkalmazás** csempe, ha a kívánt alkalmazás nem található a keresési eredmények látható. Után írja be az alkalmazás nevét, az egyszeri bejelentkezésre vonatkozó beállításokat és viselkedése is konfigurálhatja. 

**Egyszerű ötlet**: ajánlott eljárásként, a keresési funkció segítségével ellenőrizze, hogy az alkalmazás már létezik-e az alkalmazás gyűjteménye. Ha az alkalmazás megtalálható-e, és annak leírását az egyszeri bejelentkezés említi, majd az alkalmazás már támogatja az összevont egyszeri bejelentkezést.

  ![](./media/active-directory-saas-custom-apps/customapp2.png)

Az alkalmazás így hozzáadása – ebben a rendelkezésre álló előre integrált alkalmazások hasonló élményt nyújt. Elindításához válassza ki a **konfigurálása egyszeri bejelentkezéshez** vagy kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében. A következő képernyőn megadja a beállítások konfigurálása egyszeri bejelentkezéshez. A beállítások a következő szakaszok a cikk ismerteti.
  
![](./media/active-directory-saas-custom-apps/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés
Válassza ezt a beállítást, az alkalmazás SAML-alapú hitelesítés beállítása. Ehhez szükséges, hogy az alkalmazás támogatja-e a SAML 2.0-s. Információkat gyűjtenek a SAML képességeit, a folytatás előtt az alkalmazás használatáról. Hajtsa végre az alábbi szakaszok konfigurálása egyszeri bejelentkezéshez az alkalmazások és az Azure AD között.

### <a name="enter-basic-saml-configuration"></a>Adja meg az alapszintű SAML konfigurálása

Az Azure AD beállítása, írja be a következőt a SAML-alapkonfiguráció. Manuálisan adja meg az értékeket, vagy a mezők értékének kinyerése a metaadat-fájl feltöltése.

  ![Litware tartomány és az URL-címek](./media/active-directory-saas-custom-apps/customapp4.png)

- **Bejelentkezés az URL-címe (Szolgáltató által kezdeményezett csak)** – Ha a felhasználó megnyitja a jelentkezik be az alkalmazás számára. Ha az alkalmazás szolgáltatás szolgáltató által kezdeményezett egyszeri bejelentkezést, végrehajtására van konfigurálva, majd amikor a felhasználók megnyitják az URL-címet, a szolgáltató a szükséges átirányítást tegye fel az Azure ad Szolgáltatásba, és jelentkezzen be a felhasználó hitelesítéséhez. Ha ez a mező nem üres, majd az Azure AD fogja használni az Office 365 és az Azure AD hozzáférési Panel az alkalmazás elindításához az URL-cím. Ha ez a mező nincs megadva, akkor az Azure AD ehelyett hajtsa végre az identitásszolgáltató-bejelentkezés által kezdeményezett, az alkalmazás indításakor az Office 365, az Azure AD hozzáférési Panel vagy az Azure AD egyetlen bejelentkezési URL-címről (az irányítópult fület a copyable).
- **Azonosító** -érdemes egyedi módon azonosítja az alkalmazást, amelynek egyszeri bejelentkezésre van konfigurálva. Ezt az értéket a kibocsátó elemet a AuthRequest (SAML-kérelmet) az alkalmazás által küldött található. Ez az érték is frissült a **Entitásazonosító** bármely, az alkalmazás által biztosított SAML metaadatokban. Ellenőrizze az alkalmazás SAML dokumentációjában talál részletes információt Entitásazonosító vagy a célközönség értéke pedig. 

    A következő egy példa hogyan a azonosítót vagy a kibocsátó megjelennek-e az alkalmazás az Azure AD által küldött SAML-kérelmet:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Válasz URL-cím** -válasz URL-címe, ahol az alkalmazás fogadni az SAML-jogkivonat vár. Ez más néven a helyességi feltétel fogyasztói Service (ACS) URL-CÍMÉT. Ellenőrizze az alkalmazás SAML dokumentációjában talál részletes információt a SAML token válasz és ACS URL-címe van. 

    Több replyURLs konfigurálása a következő PowerShell-parancsfájlt is használhat.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

További információkért lásd: [SAML 2.0 hitelesítési kérelmeit és válaszait, amely támogatja az Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Tekintse át, vagy állítsa be a SAML-jogkivonat kiadott jogcímeket

Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD fog kiadni egy SAML-jogkivonat az adatokat (vagy jogcímeket) tartalmazó alkalmazás, amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználónevét, e-mail cím, Utónév és Vezetéknév. 

Megtekintheti vagy szerkesztheti a jogcímek küldése az alkalmazásnak a SAML-jogkivonat a **attribútumok** fülre.

  ![](./media/active-directory-saas-custom-apps/customapp7.png)

Két oka lehet, hogy miért szerkesztése a SAML-jogkivonat kiadott jogcímeket kell:

- Az alkalmazás eltérő szabályzatkészletet jogcím URI-azonosítók, vagy a jogcímértékek van írva.
- Az alkalmazás telepítve lett a NameIdentifier jogcímet, mint az Azure Active Directoryban tárolt felhasználónév (AKA egyszerű felhasználónév) igényel. 

További információkért lásd: [a SAML-jogkivonat a vállalati alkalmazások kiállított jogcímek testreszabása](./develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Felülvizsgálati tanúsítvány lejárati dátum, állapot és e-mail-értesítések

Egy gyűjtemény vagy egy nem-gyűjtemény alkalmazás létrehozásakor az Azure AD egy alkalmazás-specifikus tanúsítványt hoz létre a létrehozási 3 évig lejárati dátumot. Ez a tanúsítvány az Azure AD közötti megbízhatóság kialakításához van szüksége, és az alkalmazás. A tanúsítvány-formátumban a részletek a az alkalmazás SAML dokumentációjában találhatók. 

Az Azure AD töltse le a tanúsítvány Base64 vagy nyers formátumban. Emellett a tanúsítvány beszerzéséhez, az alkalmazás metaadatainak XML-fájl letöltése vagy az alkalmazás összevonási metaadatainak URL-cím használatával.

  ![Tanúsítvány](./media/active-directory-saas-custom-apps/certificate.png)

Ellenőrizze a tanúsítvány rendelkezik-e:

- A kívánt lejárati dátuma. A lejárati dátum legfeljebb 3 évig is konfigurálhat.
- Aktív állapotának. Ha az állapot inaktív, állapotúra, aktív. Ellenőrizze az állapot módosításához **aktív** , és mentse a konfigurációt. 
- A megfelelő értesítő e-mailt. Ha az aktív tanúsítvány lejárati dátuma, az Azure AD az ebben a mezőben beállított e-mail cím egy értesítést küld.  

További információkért lásd: [összevont egyszeri bejelentkezés az Azure Active Directoryban tanúsítványainak kezelése](manage-apps/manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Célalkalmazás beállítása

Állítsa be az alkalmazását az egyszeri bejelentkezés, keresse meg az alkalmazás dokumentációját. A dokumentációjában található, az SAML-alapú konfigurációs bejelentkezési oldal végére, és kattintson a **konfigurálása <application name>** . 

A szükséges értékeket az alkalmazás függvényében. További információkért tekintse meg az alkalmazás SAML dokumentációját. A bejelentkezési és kijelentkezési URL-címe is oldja fel a azonos végpont, amely az SAML-kéréskezeléshez végpont az az Azure AD példányával. Az SAML-entitás azonosítója az érték, amely a az alkalmazás számára kiállított SAML-jogkivonat-kiállítóként jelenik meg.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Felhasználók és csoportok hozzárendelése az SAML-alkalmazás

Miután az alkalmazás konfigurációja használhatja az Azure Active Directory SAML-alapú identitás-szolgáltatóként, majd azt már majdnem kész teszteléséhez. Biztonsági ellenőrzés, mint az Azure AD egy felhasználó bejelentkezni az alkalmazásba, kivéve, ha az Azure AD hozzáférést kap a felhasználó jogkivonat nem állítanak ki. Felhasználók adható hozzáférés közvetlenül, vagy a csoporttagság. 

Az alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, kattintson a **felhasználók hozzárendelése** gombra. Válassza ki a felhasználó vagy csoport szeretne rendelni, majd válassza ki a **hozzárendelése** gombra.

  ![](./media/active-directory-saas-custom-apps/customapp6.png)

A felhasználó hozzárendelése lehetővé teszi a felhasználó jogkivonatok kiállítása az Azure AD. Ez az alkalmazás a felhasználó hozzáférési panelen jelennek meg a mozaikokra is okoz. Ha a felhasználó által használt Office 365 egy alkalmazás csempe is megjelennek az Office 365 alkalmazásindító. 

> [!NOTE] 
> Feltöltheti a csempe emblémát használó alkalmazás a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz. 


### <a name="test-the-saml-application"></a>Az SAML-alkalmazás tesztelése

A SAML-alapú alkalmazás tesztelése előtt meg kell állította be az alkalmazás az Azure ad-val, és felhasználókhoz vagy csoportokhoz rendelt az alkalmazás.

  ![Tesztelés](./media/active-directory-saas-custom-apps/testing.png)

Az egyszeri bejelentkezés lapon kattintson a **teszt SAML beállítások** a tartomány és az URL-címek szakaszban. Ekkor megnyílik a tartalom ablaktáblában a utasításokkal kell tesztelni az alkalmazást.

1. Jelentkezzen be az alkalmazást. Ha az alkalmazás, szolgáltatás szolgáltató által kezdeményezett egyszeri bejelentkezést van konfigurálva, a rendszer visszairányítja az egyetlen bejelentkezési URL-címhez kezdeményezheti, ahol a bejelentkezés. Ha az alkalmazás van konfigurálva identitás szolgáltató által kezdeményezett egyszeri bejelentkezést, majd Ön van bejelentkezve az alkalmazáshoz.
2.  Ha valamilyen hiba a vállalati bejelentkezési oldal jelenik meg, másolja át a hiba, és térjen vissza az Azure AD-egyszeri bejelentkezés a tartalom ablaktáblában tesztelése. Illessze be a hibát, majd kattintson a **megoldási lépések beolvasása**. Ha a hiba: az alkalmazás lapján, az alkalmazás gyártójától, és az Azure AD-be az érték érvényesítése a konfiguráció megosztása szüksége. 
3.  A hibaüzenet alapján, az Azure AD lépéseit adott meg a probléma megoldásához.

További információkért lásd: [SAML-alapú egyszeri bejelentkezés az Azure Active Directoryban alkalmazások hibakeresése](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-saml-debugging/?WT.mc_id=DMC_AAD_?WT.mc_id=UI_AAD_Configure_NonGalleryApps)


## <a name="password-single-sign-on"></a>Jelszó egyszeri bejelentkezést.

Válassza ezt a lehetőséget választva [jelszó-alapú egyszeri bejelentkezést](manage-apps/what-is-single-sign-on.md) , amely rendelkezik egy bejelentkezési lap HTML webalkalmazás. Egyszeri jelszó alapú is hívják jelszó vaulting, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezeléséhez. Akkor célszerű is forgatókönyvek, ahol több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

Miután kiválasztott **következő**, kérni fogja az alkalmazás webes bejelentkezési lap URL-címet. Vegye figyelembe, hogy a felhasználónév és jelszó bemeneti mezőket tartalmazó oldalon kell lennie. Ha meg van adva, az Azure AD elindítja elemzése a bejelentkezési lapon adjon meg egy felhasználónevet és jelszót adjon meg egy folyamatot. Ha a folyamat nem sikeres, majd azt végigvezeti Önt egy másik folyamat telepíthet egy bővítmény (igényli az Internet Explorer, a Chrome vagy a Firefox), amely lehetővé teszi a mezők manuálisan rögzítéséhez.

Amennyiben a bejelentkezési oldal rögzítése, felhasználók és csoportok rendelt és credential házirendeket állíthat be hasonlóan rendszeres [jelszó SSO alkalmazások](manage-apps/what-is-single-sign-on.md).

> [!NOTE] 
> Feltöltheti a csempe emblémát használó alkalmazás a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz. 
>

## <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezést.
Válassza ezt a beállítást, a szervezet Azure AD hozzáférési Panel vagy az Office 365 portál alkalmazásokra mutató hivatkozás hozzáadása. Ezzel hivatkozások felvétele egyéni webes alkalmazásokat, amelyek jelenleg használják az Azure Active Directory összevonási szolgáltatások (vagy más összevonási szolgáltatásból) helyett az Azure AD használatára a hitelesítéshez. Vagy mélyhivatkozással hozzáadása adott SharePoint-lapokat vagy más szeretné jelennek meg a felhasználói hozzáférés panelek weblapokat. 

Miután kiválasztott **következő**, kérni fogja a csatolni kívánt alkalmazás URL-címet. Ezt követően felhasználókhoz és csoportokhoz rendelt az alkalmazásról, így az alkalmazás megjelenik a [Office 365 alkalmazás indító](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) azoknak a felhasználóknak.

> [!NOTE] 
> Feltöltheti a csempe emblémát használó alkalmazás a **embléma feltöltéséhez** gombra a **konfigurálása** fülre az alkalmazáshoz. 
>

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
- [A SAML-jogkivonat előzetesen beépített alkalmazások számára kiállított jogcímek testreszabása](active-directory-saml-claims-customization.md)
- [Hibaelhárítási SAML-alapú egyszeri bejelentkezést.](active-directory-saml-debugging.md)

