---
title: Egyszeri bejelentkezés – katalógusban nem szereplő alkalmazások – Azure Active Directory |} A Microsoft Docs
description: Egyszeri bejelentkezés (SSO) a katalógusban nem szereplő alkalmazásokhoz az Azure Active Directoryban (Azure AD) konfigurálása
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: barbkess
ms.reviewer: asmalser,luleon
ms.openlocfilehash: 1c364b3be0c2203c64e64df1bd0e4ab5ec2078a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153879"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Egyszeri bejelentkezés nem katalógusból származó alkalmazások az Azure Active Directory konfigurálása

Ez a cikk egy szolgáltatás, amely lehetővé teszi a rendszergazdák egyszeri bejelentkezés alkalmazásokhoz nincs jelen az Azure Active Directory alkalmazásgyűjtemény tárgya *kódírás nélkül*. Ha inkább az egyéni alkalmazások integrálása az Azure AD-kódon keresztül: Fejlesztői útmutató keres, tekintse meg [hitelesítési forgatókönyvek az Azure ad-ben](../develop/authentication-scenarios.md).

Az Azure Active Directory alkalmazáskatalógusában felsorolja a alkalmazások ismert támogatja az egyszeri bejelentkezés az Azure Active Directoryval, az űrlap leírtak szerint [Ez a cikk](what-is-single-sign-on.md). Ha (a egy informatikai specialistája vagy támaszkodva a szervezetben) megtalálta az alkalmazást, amelyhez csatlakozni, az egyszeri bejelentkezés engedélyezése az Azure Portalon megjelenő részletes utasításokat követve kezdheti.

Ezek a képességek is elérhetők, a licencszerződésben. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory/) olvasható. 

- Önkiszolgáló integrációja bármilyen alkalmazás, amely támogatja az SAML 2.0 identitásszolgáltató (SP által kezdeményezett vagy identitásszolgáltató által kezdeményezett)
- Bármely, amely rendelkezik egy a bejelentkezési oldal HTML-alapú webes alkalmazás önkiszolgáló integrációja [jelszóalapú egyszeri bejelentkezés](what-is-single-sign-on.md#password-based-sso)
* A felhasználókiépítés az SCIM-protokollt használó alkalmazások önkiszolgáló kapcsolat ([az itt leírtak szerint](use-scim-to-provision-users-and-groups.md))
* Lehetővé teszi, hogy minden olyan alkalmazáshoz, a hivatkozások a [Office 365 appindítóban](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](what-is-single-sign-on.md#linked-sso)

Ez nem csupán a SaaS-alkalmazásokhoz, amelyek használják, de rendelkezik nem még lett bevezetve, az Azure AD alkalmazáskatalógusában, de a szervezet azt szabályozza, a felhőben vagy a helyszíni kiszolgálókra telepített külső webes alkalmazásokat tartalmazza.

Ezek a képességek, más néven *integrációs alkalmazássablonok*, szabványokon alapuló kapcsolódási pontok SAML, SCIM vagy űrlapalapú hitelesítést támogató alkalmazások esetében, és foglalják rugalmas lehetőségek és beállítások számára kompatibilitás az alkalmazások széles körű száma. 

## <a name="adding-an-unlisted-application"></a>Egy listán nem szereplő alkalmazás hozzáadása
Egy alkalmazás egy alkalmazás-integráció sablon használatával csatlakozhat, jelentkezzen be az Azure Portalon az Azure Active Directory-rendszergazdai fiók használatával. Keresse meg a **Active Directory > Vállalati alkalmazások > új alkalmazás > katalógusban nem szereplő alkalmazás** szakaszban jelölje be **Hozzáadás**, majd **alkalmazás hozzáadása a katalógusból** .

  ![Alkalmazás hozzáadása](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

Az alkalmazáskatalógusban, kiválasztásával adhat hozzá egy listán nem szereplő alkalmazást a **katalógusban nem szereplő alkalmazás** csempét, amely a keresési eredmények között látható, ha a kívánt alkalmazás nem található. Miután megadta az alkalmazás nevét, konfigurálhatja az egyszeri bejelentkezésre vonatkozó beállításokat és viselkedését. 

**Gyors tipp**:  Ajánlott eljárásként a keresési funkció segítségével ellenőrizze, hogy ha az alkalmazás már létezik-e az alkalmazás-katalógusában. Ha az alkalmazás megtalálható, és egyszeri bejelentkezés említi a leírását, majd az alkalmazás már támogatja az összevont egyszeri bejelentkezést.

  ![Keresés](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

Ezzel a módszerrel egy alkalmazás hozzáadása egy előre integrált alkalmazások számára elérhető hasonló élményt nyújt. Válassza **konfigurálása egyszeri bejelentkezéshez** , vagy kattintson az **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében. A következő képernyőn kapcsolódhatnak az egyszeri bejelentkezés konfigurálásához. A beállítások a következő szakaszban Ez a cikk ismerteti.
  
![Beállítási lehetőségek](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML-alapú egyszeri bejelentkezés
Válassza ki ezt a beállítást, az alkalmazás SAML-alapú hitelesítés konfigurálása. Ehhez az szükséges, hogy az alkalmazás támogatja-e a SAML 2.0. Információkat gyűjtenek a folytatás előtt az alkalmazás SAML funkcióinak használatát. Hajtsa végre a következő szakaszok konfigurálása egyszeri bejelentkezéshez az alkalmazások és az Azure AD között.

### <a name="enter-basic-saml-configuration"></a>Adja meg az alapszintű SAML-konfigurációja

Az Azure AD beállításához, adja meg az alapszintű SAML-konfigurációja. Manuálisan adja meg az értékeket, vagy a mező értékének kinyerése-metaadatfájl feltöltésére.

  ![Litware tartomány és URL-címek](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **Bejelentkezési URL-(SP által kezdeményezett csak)** – Ha a felhasználó megnyitja a jelentkezzen be az alkalmazás. Ha az alkalmazás szolgáltatás szolgáltató által kezdeményezett egyszeri bejelentkezést, végrehajtására van konfigurálva, majd amikor a felhasználók az URL-címet, a szolgáltató a szükséges átirányítást tegye fel az Azure AD-hitelesítést, és jelentkezzen be a felhasználó. Ha ez a mező fel van töltve, majd Azure AD fog használni az Office 365 és az Azure AD hozzáférési Panel az alkalmazás elindításához az URL-címet. Ha kihagyja ezt a mezőt, majd az Azure AD ehelyett hajtsa végre az identitásszolgáltató-kezdeményezett bejelentkezési, amikor az alkalmazás elindítása az Azure AD egyszeri bejelentkezési URL-cím (másolható, az irányítópult lapon) vagy az Office 365, az Azure AD hozzáférési Panel.
- **Azonosító** -egyedileg kell azonosítania az alkalmazást, amelyhez egyszeri bejelentkezés konfigurálása történik. Ezt az értéket a AuthRequest (SAML-kérelem) kibocsátói elemében az alkalmazás által küldött találja. Ez az érték is frissült: a **Entitásazonosító** a az alkalmazás által biztosított bármely SAML-metaadatokat. Ellenőrizze az alkalmazás SAML dokumentációjában az Entitásazonosító vagy célközönség értékét is. 

    Az alábbiakban látható egy példát, hogyan az azonosító vagy a kiállító szerepel-e az alkalmazás az Azure AD által küldött SAML-kérelem:

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **Válasz URL-cím** – a válasz URL-címe, ahol az alkalmazás megjelenik az SAML-jogkivonat vár. Ez is nevezzük a helyességi feltétel fogyasztói Service (ACS) URL-CÍMÉT. Ellenőrizze az alkalmazás SAML dokumentációjában, a SAML token válasz URL-cím vagy ACS URL-CÍMÉT. 

    Több replyURLs konfigurálásához használhatja a következő PowerShell-parancsfájlt.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

További információkért lásd: [SAML 2.0-s hitelesítési kérelmek és válaszok, amely támogatja az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Tekintse át, vagy a SAML-jogkivonatban kiadott jogcímek testreszabása

Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD fog kiadni egy SAML-jogkivonat az alkalmazást, amely tartalmazza az adatokat (vagy jogcímek), amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználónevét, e-mail címét, Utónév és Vezetéknév. 

Megtekintheti és szerkesztheti a jogcímek, az alkalmazásnak a SAML-jogkivonatban elküldött a **attribútumok** fülre.

  ![Attribútumok](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

Előfordulhat, hogy miért a SAML-jogkivonatban kiadott jogcímek szerkesztése kell két oka van:

- Az alkalmazás eltérő szabályzatkészletet jogcím URI-k vagy a jogcímértékek lett írva.
- Az alkalmazás telepítése úgy, hogy a NameIdentifier jogcím verziójáétól eltérő a felhasználónevet (AVAGY egyszerű felhasználónév), az Azure Active Directoryban tárolt igényel. 

További információkért lásd: [vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](./../develop/../develop/active-directory-saml-claims-customization.md). 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Felülvizsgálat tanúsítvány lejárati dátum, állapot és e-mail-értesítés

Egy katalógusban vagy egy katalógusban nem szereplő alkalmazást hoz létre, amikor az Azure AD egy alkalmazás-specifikus tanúsítványt hoz létre a létrehozás dátumától számítva 3 éves lejárati dátumot. Ez a tanúsítvány beállítása az Azure AD közötti megbízhatósági kapcsolat van szüksége, és az alkalmazás. A tanúsítvány formátuma részletes ismertetéséért tekintse meg az alkalmazás SAML dokumentációját. 

Az Azure ad-ből a tanúsítvány Base64 vagy nyers formátumban töltheti le. Emellett a tanúsítvány beszerzéséhez, az alkalmazás metaadatainak XML-fájl letöltése vagy az alkalmazás összevonási metaadatainak URL-cím használatával.

  ![Tanúsítvány](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

Ellenőrizze a tanúsítvány rendelkezik-e:

- A kívánt lejárati dátuma. A lejárati dátum legfeljebb 3 évig is beállíthatja.
- Aktív állapotú. Ha az állapot inaktív, állapotúra, aktív. Ha módosítani szeretné az állapot, ellenőrizze **aktív** , és mentse a konfigurációt. 
- A megfelelő értesítési e-mailt. Ha az aktív tanúsítvány lejárati dátuma közel, akkor az Azure AD értesítést küld az ebben a mezőben beállított e-mail-címre.  

További információkért lásd: [összevont egyszeri bejelentkezés az Azure Active Directory-tanúsítványok kezelése](manage-certificates-for-federated-single-sign-on.md).

### <a name="set-up-target-application"></a>Cél-alkalmazás beállítása

Az alkalmazás az egyszeri bejelentkezés konfigurálásához, keresse meg az alkalmazás dokumentációját. A dokumentációban talál, az SAML-alapú bejelentkezés beállításainak oldal végére, és kattintson a **konfigurálása <application name>** . 

A szükséges értékeket az alkalmazás függően változnak. Részletekért tekintse meg az alkalmazás SAML dokumentációját. A bejelentkezési és kijelentkezési szolgáltatás URL-címe, mindkettő egyazon végpont, amely a SAML-kérelem-kezelési végpont esetében az Azure AD példányával feloldani. Az SAML-entitás azonosítója az érték, amely az alkalmazás számára kibocsátott SAML-jogkivonat a kiállító elemként jelenik meg.


### <a name="assign-users-and-groups-to-your-saml-application"></a>Felhasználók és csoportok hozzárendelése az SAML-alkalmazáshoz

Miután az alkalmazás SAML-alapú identitás-szolgáltatóként az Azure AD használatára van konfigurálva, majd csaknem készen áll teszteléséhez. Biztonsági ellenőrzés, mint az Azure AD egy jogkivonatot, lehetővé téve a felhasználót, hogy jelentkezzen be az alkalmazást, kivéve, ha az Azure AD hozzáférést a felhasználó nem ad ki. Felhasználók adható hozzáférés közvetlenül, vagy egy csoport tagságát. 

Az alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, kattintson a **felhasználók hozzárendelése** gombra. Válassza ki a felhasználót vagy csoportot szeretne hozzárendelni, és válassza ki a **hozzárendelése** gombra.

  ![Felhasználók hozzárendelése](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

Egy felhasználó számára a felhasználói jogkivonatok kiállítása az Azure AD lehetővé teszi. Ez az alkalmazás a felhasználó hozzáférési panelen jelennek meg a csempe is okoz. Egy alkalmazás-csempét is megjelennek az Office 365 alkalmazásindítójában, ha a felhasználó Office 365-höz használ. 

> [!NOTE] 
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon. 


### <a name="test-the-saml-application"></a>Az SAML-alkalmazás tesztelése

A SAML-kérelem-beli tesztelése előtt, az alkalmazás az Azure AD beállítása, és a felhasználók vagy csoportok hozzárendelve az alkalmazáshoz. Tekintse meg az SAML-alkalmazás teszteléséhez [SAML-alapú egyszeri bejelentkezés az Azure Active Directory-alkalmazások hibakeresése](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Jelszavas egyszeri bejelentkezés

Válassza ezt a beállítást, konfigurálhat [jelszóalapú egyszeri bejelentkezés](what-is-single-sign-on.md) egy webalkalmazás, amely rendelkezik egy HTML formátumú bejelentkezési oldala. Jelszavas egyszeri Bejelentkezést, jelszó vaulting, engedélyezi a kezelheti a felhasználói hozzáférés és a jelszavakat a webes alkalmazások, amelyek nem támogatják az identitás-összevonást is hivatkoznak. Emellett akkor is hasznos, ahol több felhasználó kell megosztani egyetlen fiókban, mint például a szervezet közösségi alkalmazás fiókok forgatókönyvekhez. 

Miután **tovább**, meg kell adnia az alkalmazás webes bejelentkezési oldal URL-címet. Vegye figyelembe, hogy ez az oldal, amely tartalmazza a felhasználónév és jelszó beviteli mezőket kell lennie. Ha meg van adva, az Azure AD megkezdi egy elemezni a bejelentkezési lapon adjon meg egy felhasználónevet és jelszót adjon meg. Ha a folyamat nem sikeres, majd azt végigvezeti egy másik folyamat telepíti egy bővítmény (az Internet Explorer, a Chrome és a Firefox szükséges), amely lehetővé teszi, hogy manuálisan a mezők rögzítése.

A bejelentkezési oldal rögzített, felhasználók és csoportok rendelhetők, és a hitelesítő adatok szabályzatokat is létrehozhatnak, csakúgy, mint a normál [jelszó SSO-alkalmazások](what-is-single-sign-on.md).

> [!NOTE] 
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon. 
>

## <a name="existing-single-sign-on"></a>Meglévő egyszeri bejelentkezés
Válassza ki ezt a beállítást, hivatkozás hozzáadása egy alkalmazáshoz a szervezet Azure AD hozzáférési Panel vagy az Office 365 portálra. Ezzel a hivatkozásokat adhat hozzá egyéni webes alkalmazások, amelyek jelenleg használják az Active Directory összevonási szolgáltatások (vagy más összevonási szolgáltatásból) az Azure AD-hitelesítés helyett. Vagy mélyhivatkozással hozzáadása adott SharePoint-oldalakba vagy más weblapokat csak át szeretné megjeleníteni a felhasználók hozzáférési Paneljein. 

Kiválasztása után **tovább**, meg kell adnia összekapcsolása az alkalmazás URL-címet. Miután végeztünk ezzel, felhasználók és csoportok rendelhetők hozzá az alkalmazásról, így az alkalmazás megjelenik a [Office 365 appindítóban](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy a [Azure AD hozzáférési panel](end-user-experiences.md) azoknak a felhasználóknak.

> [!NOTE] 
> Feltöltheti az alkalmazást a csempe emblémájának a **embléma feltöltése** gombot a **konfigurálása** az alkalmazás lapon. 
>

## <a name="related-articles"></a>Kapcsolódó cikkek
- [Az előre integrált alkalmazások SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md)
- [SAML-alapú egyszeri bejelentkezés hibaelhárítása](../develop/howto-v1-debug-saml-sso-issues.md)

