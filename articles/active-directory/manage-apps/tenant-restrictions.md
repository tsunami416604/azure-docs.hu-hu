---
title: Használja bérlői korlátozások saas-hozzáférés kezelése a felhőalapú alkalmazások – Azure |} A Microsoft Docs
description: Hogyan használható a bérlői korlátozások kezelése mely felhasználók férhetnek hozzá az alkalmazások az Azure AD-bérlő szerint.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78897e2e03085a20f07ce8724226f0e0171861e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545865"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Bérlői korlátozások használatával felhőalapú SaaS-alkalmazásokhoz való hozzáférés kezelése

Nagy szervezeteknek, amelyek biztonsági hangsúlyozzák át szeretné helyezni a felhőalapú szolgáltatások, például az Office 365-höz, de kell tudni, hogy a felhasználók csak hozzáférhetnek a jóváhagyott erőforrásokhoz. Hagyományosan vállalatok korlátozza a tartomány neve vagy IP-hozzáférés kezelése szeretne. Ezzel a módszerrel nem sikerül, egy olyan világban, ahol futnak, nyilvános felhő, például a megosztott tartománynevek futó szoftverfrissítési szolgáltatásként (vagy SaaS) alkalmazások [outlook.office.com](https://outlook.office.com/) és [login.microsoftonline.com](https://login.microsoftonline.com/). Ezek a címek blokkolása szeretné, hogy a felhasználók hozzáférjenek a webes Outlookban teljesen, helyett csupán jóváhagyott identitások és erőforrásokhoz való korlátozása.

Az Azure Active Directory (Azure AD-) megoldás mutatták bérlőkorlátozások nevű funkciója. Bérlői korlátozások, a szervezetek szabályozhatja a hozzáférést az SaaS-felhőalapú alkalmazásokhoz, az egyszeri bejelentkezés használata az alkalmazások Azure AD-bérlő alapján. Például érdemes férhessenek hozzá a szervezet Office 365-alkalmazásokhoz, és megakadályozza, hogy más szervezetek számára példányok ugyanazt az alkalmazásokhoz való hozzáférést.  

Bérlői korlátozások a szervezetek megadhatják azokat a bérlők, amely hozzáférhető a felhasználók listáját. Az Azure AD majd csak ezeket a bérlők számára engedélyezett hozzáférést biztosít.

Ez a cikk az Office 365 bérlői korlátozások összpontosít, de a szolgáltatás bármely felhőalapú SaaS-alkalmazással, az egyszeri bejelentkezés az Azure AD-vel modern hitelesítési protokollok használó működnie kell. Ha SaaS-alkalmazások egy másik Azure AD-bérlői Office 365 által használt bérlőtől használ, ellenőrizze, hogy az összes szükséges bérlők számára engedélyezett. További információ a felhőalapú SaaS-alkalmazások: a [Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Működés

A teljes megoldás az alábbi összetevőkből áll:

1. **Az Azure AD**: Ha a `Restrict-Access-To-Tenants: <permitted tenant list>` van adva, az Azure AD csak problémák biztonsági jogkivonatokat az engedélyezett bérlők számára.

2. **A helyszíni proxy server infrastruktúra**: Ez az infrastruktúra képes a Secure Sockets Layer (SSL)-ellenőrzési proxy eszközről. A fejléc engedélyezett bérlők listáját tartalmazó be az Azure ad felé irányuló forgalom beszúrása a proxyt kell konfigurálnia.

3. **Ügyfélszoftver**: Bérlői korlátozások támogatása érdekében ügyfélszoftvert kell újraindítást igényelnie jogkivonatok közvetlenül az Azure AD-ben, hogy a proxy infrastruktúra nem figyelheti a forgalmat. Office 365-alkalmazások böngészőalapú jelenleg használható bérlőkorlátozások, (például az OAuth 2.0-s) a modern hitelesítést használó Office-ügyfelekhez.

4. **Modern hitelesítés nélküli**: A cloud services kell használnia a modern hitelesítés bérlőkorlátozások használhatja, és nem engedélyezett az összes bérlőre való hozzáférés letiltása. Office 365 felhőszolgáltatások alapértelmezés szerint a modern hitelesítési protokollok használatára kell konfigurálni. Az Office 365 modern hitelesítés támogatása a legfrissebb információkért olvassa el a [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

A következő ábra szemlélteti a magas szintű adatforgalmat. Bérlői korlátozások csak a forgalmat az Azure ad-hez, az Office 365 cloud services, a nem SSL-ellenőrzés szükséges. Ezt a különbséget fontos, mert a forgalom mennyisége az Azure AD-hitelesítés általában sokkal alacsonyabb, mint a SaaS-alkalmazásokban, mint például az Exchange online-hoz és a SharePoint Online adatforgalma.

![Bérlői korlátozások forgalom áramlását – ábra](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

Ismerkedés a bérlői korlátozások két lépésből áll. Első lépésként ellenőrizze, hogy az ügyfelek csatlakozhatnak-e a megfelelő címeket. A második a-proxy infrastruktúra beállítása.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

Bérlői korlátozások használata, az ügyfelek csatlakozni a következő Azure AD URL-címek hitelesítéséhez képesnek kell lennie: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), és [ login.Windows.NET](https://login.windows.net/). Emellett való hozzáférést az Office 365, az ügyfelek is kell lennie tud csatlakozni a teljes tartománynevek (FQDN) URL-címek és IP-címek meghatározott [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxy konfigurálása és követelmények

A következő konfiguráció szükséges ahhoz, hogy a proxy-infrastruktúrán keresztül bérlői korlátozások. Ez az útmutató az általános, így a proxy gyártója által biztosított dokumentációt megvalósítási lépések hivatkozzon.

#### <a name="prerequisites"></a>Előfeltételek

- A proxy SSL hozzáférés, HTTP-fejléc Beszúrás után hajtsa végre, és teljes tartománynevek/URL-címekkel destinations szűrése képesnek kell lennie.

- Az ügyfelek meg kell bíznia az SSL-kommunikációhoz a proxy által bemutatott tanúsítványlánc. Például ha a belső tanúsítványainak [nyilvános kulcsokra épülő infrastruktúrájú (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) is használta, a belső vállalati legfelső szintű tanúsítvány hatóság tanúsítványnak megbízhatónak kell lennie.

- Ez a funkció az Office 365-előfizetés tartalmazza, azonban ha azt szeretné, ki férhet hozzá más SaaS-alkalmazások bérlői korlátozások használatával, majd az Azure AD Premium 1 licencekre szükség.

#### <a name="configuration"></a>Konfiguráció

Az egyes bejövő kérések login.microsoftonline.com, login.microsoft.com és login.windows.net szúrjon be két HTTP-fejlécek: *Korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet*.

A fejlécek a következő elemeket kell tartalmaznia:

- A *korlátozása-Access-az-bérlők*, az érték \<engedélyezett bérlő listájának\>, azaz engedélyezheti a felhasználók hozzáférhessenek a bérlők vesszővel tagolt listája. Bármely tartomány, amely regisztrálva van a bérlő segítségével azonosíthatja a bérlő ebben a listában. Például hogy engedélyezze a hozzáférést a Contoso és Fabrikam is bérlők számára, a név-érték pár hasonlóan néz ki: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- A *korlátozása-Access-környezet*, az érték egy egyetlen címtár-azonosító, melyik bérlőhöz deklaráló beállítása a bérlői korlátozások. Például Contoso nyilvánítja, a bérlő, amely a bérlői korlátozások házirend beállítása, a név-érték pár hasonlóan néz ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A címtár-azonosító, az annak a [Azure Active Directory portálon](https://aad.portal.azure.com/). Jelentkezzen be rendszergazdaként, válassza ki **Azure Active Directory**, majd **tulajdonságok**.

Meg, hogy a felhasználók saját HTTP-fejléc beszúrása a nem jóváhagyott bérlők, a proxy felül kell írni a *korlátozása-Access-az-bérlők* fejléc, ha már a bejövő kérelemben szereplő.

Az ügyfelek kell kényszeríthető login.microsoftonline.com login.microsoft.com és login.windows.net érkező összes kérés a proxy használatára. Például ha PAC-fájlt használ a proxy használata az ügyfelek, a végfelhasználók nem fogják tudni szerkesztése vagy letiltása a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz ismerteti a élmény a végfelhasználók és rendszergazdák is.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Példa felhasználóként a Contoso hálózatán lévő, de próbál meg hozzáférni egy megosztott SaaS-alkalmazás például az Outlookból online Fabrikam példányát. Ha a Fabrikam egy Contoso-példány nem engedélyezett bérlő, a felhasználó látja hozzáférés megtagadása jelenik, mely szerint a próbál hozzáférni egy erőforrás, amely egy az informatikai osztálya által jóvá nem hagyott szervezethez tartozik.

### <a name="admin-experience"></a>A rendszergazda teendői

Bérlői korlátozások konfigurálása történik a vállalati proxy infrastruktúra, míg a rendszergazdák hozzáférhetnek a bérlői korlátozások jelentések az Azure Portalon közvetlenül. A jelentések megtekintéséhez:

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/). A **Azure Active Directory felügyeleti központ** irányítópult jelenik meg.

2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. Az Azure Active Directory – Áttekintés lap jelenik meg.

3. Az a **egyéb képességek** szakaszban kattintson **bérlői korlátozások**.

A rendszergazda a bérlő, a Tiltott helyek-Access-környezet bérlői használható ez a jelentés megtekintéséhez bejelentkezésekhez használt, beleértve a bérlői korlátozások házirend miatt blokkolva a megadott és a céloldali könyvtár-azonosító. Bejelentkezések szerepelnek, ha a bérlői beállítások a korlátozás a felhasználó bérlői vagy az erőforrás-bérlő számára a bejelentkezés.

Más jelentések az Azure Portalon, például a szűrők segítségével határozhatja meg a jelentés hatókörét. Egy adott időtartam alatt, felhasználó, alkalmazás, ügyfél vagy állapota szűrésével. Ha a **oszlopok** gomb, választhat a következő mezőket tetszőleges kombinációival adatainak megjelenítéséhez:

- **Felhasználó**
- **Alkalmazás**
- **Állapot**
- **Dátum**
- **Dátum (UTC)** (ahol a UTC az egyezményes világidő)
- **MFA-hitelesítési módszer** (többtényezős hitelesítési módszer)
- **MFA-hitelesítés részletei** (többtényezős hitelesítés részletei)
- **MFA eredménye**
- **IP-cím**
- **Ügyfél**
- **Felhasználónév**
- **Hely**
- **Céloldali Bérlőazonosító**

## <a name="office-365-support"></a>Az Office 365-támogatás

Office 365-alkalmazások bérlői korlátozások teljes támogatása érdekében két feltételeknek kell megfelelnie:

1. A használt ügyfél támogatja a modern hitelesítést.
2. Modern hitelesítés engedélyezve van, mint az alapértelmezett hitelesítési protokoll, a felhőszolgáltatás számára.

Tekintse meg [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) a legfrissebb információkat a Microsoft Office mely ügyfelek jelenleg támogatja a modern hitelesítést. Az oldal adott Exchange Online és Skype vállalati Online bérlők modern hitelesítés engedélyezése az utasításokra mutató hivatkozásokat is tartalmaz. SharePoint online-hoz már Modern hitelesítést alapértelmezés szerint engedélyezi.

Az Office 365 böngészőalapú alkalmazások (az Office-portálon, a Yammeren, SharePoint helyek, a webes és egyéb az Outlook) jelenleg támogatja a bérlői korlátozások. Vastag ügyfelek (Outlook, a Skype for Business, a Word, Excel, PowerPoint és további) kényszeríthetik bérlői korlátozások csak a modern hitelesítés használata esetén.  

Az Outlook verzió és az üzleti ügyfelek számára a modern hitelesítést támogató előfordulhat, hogy továbbra is képes bérlőre örökölt protokollt használják, ahol a modern hitelesítés nincs engedélyezve, hatékonyan bérlőkorlátozások kihagyásával. Bérlői korlátozások blokkolhatják a alkalmazásokat, amelyek használják az örökölt protokollok, ha azok kapcsolatba login.microsoftonline.com, login.microsoft.com vagy login.windows.net hitelesítés során.

Az Outlook, a Windows-ügyfelek dönthetnek úgy, hogy meggátolja, hogy a végfelhasználók számára nem engedélyezett e-mail-fiókokat ad hozzá a profilok korlátozásokat. Ha például a [nem alapértelmezett Exchange-fiókok hozzáadásának letiltása](https://gpsearch.azurewebsites.net/default.aspx?ref=1) csoportházirend-beállítás.

## <a name="testing"></a>Tesztelés

Ha azt szeretné, kipróbálhatja a bérlői korlátozások megvalósítása, az egész cég számára előtt, két lehetősége van: a gazdagép-alapú megközelítést vizsgálata a Fiddlerrel vagy a proxybeállítások egy előkészített bevezetésének hasonló eszköz használatával.

### <a name="fiddler-for-a-host-based-approach"></a>A gazdagép-alapú megközelítést a fiddler

A fiddler egy ingyenes webes hibaelhárító proxy, amely segítségével rögzítése, és módosítsa a HTTP/HTTPS-forgalmat, többek között a HTTP-fejlécek beszúrását. Bérlői korlátozások tesztelése a Fiddler konfigurálásához hajtsa végre az alábbi lépéseket:

1. [Töltse le és telepítse a Fiddler](https://www.telerik.com/fiddler).

2. Fiddler-HTTPS-forgalom visszafejtése konfigurálása [Fiddler a Súgó dokumentációját](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurálja a Fiddler beszúrása a *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet* fejlécek egyéni szabályok használatával:

   1. A Fiddler webes hibakereső eszközben válassza ki a **szabályok** menüre, majd válassza **szabályok testreszabása...** a customrules fájl egynél fájl megnyitásához.

   2. Adja hozzá a következő sorokat elején a `OnBeforeRequest` függvény. Cserélje le \<bérlői tartomány\> a bérlő regisztrált tartomány (például `contoso.onmicrosoft.com`). Cserélje le \<címtár-azonosító\> a bérlő Azure AD GUID azonosítóval.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Ha több bérlő engedélyeznie kell, a bérlő neveket használjon vesszőt. Példa:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárja be a customrules fájl egynél fájlt.

Miután konfigurálta a Fiddler, a forgalom rögzítése a **fájl** menüt, és kiválasztja **forgalom rögzítése**.

### <a name="staged-rollout-of-proxy-settings"></a>A proxybeállítások szakaszos bevezetés

A proxy infrastruktúra képességeit, függően beállításait a felhasználók számára a bevezetésének előkészítéséhez. Az alábbiakban a megfontolásra néhány magas szintű lehetőségek:

1. Tesztfelhasználók átirányítása egy test-proxy infrastruktúra, míg a normál felhasználók továbbra is használhatja a termelési-proxy infrastruktúra a PAC-fájl használatával.
2. Proxykiszolgálók, amelyeken előfordulhat, hogy a csoportok különböző konfigurációkat támogatják.

Részletes tekintse meg a proxy server dokumentációjában talál.

## <a name="next-steps"></a>További lépések

- További információ [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)
- Tekintse át a [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
