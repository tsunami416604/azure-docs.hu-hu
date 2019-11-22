---
title: A bérlői korlátozások használata az SaaS-alkalmazásokhoz való hozzáférés kezelésére – Azure AD
description: A bérlői korlátozások használata annak kezeléséhez, hogy mely felhasználók férhetnek hozzá az alkalmazásokhoz az Azure AD-bérlőn alapuló módon.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f73dd8dbef3f08cd4ea5841e4ec21bac2f55bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276508"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>A bérlői korlátozások használata a SaaS-Felhőbeli alkalmazásokhoz való hozzáférés kezelésére

Nagy szervezeteknek, amelyek biztonsági hangsúlyozzák át szeretné helyezni a felhőalapú szolgáltatások, például az Office 365-höz, de kell tudni, hogy a felhasználók csak hozzáférhetnek a jóváhagyott erőforrásokhoz. Hagyományosan vállalatok korlátozza a tartomány neve vagy IP-hozzáférés kezelése szeretne. Ez a megközelítés egy olyan világban meghiúsul, ahol a szolgáltatott szoftverek (vagy SaaS-alkalmazások) nyilvános felhőben futnak, és megosztott tartományneveket futtatnak, például a [Outlook.Office.com](https://outlook.office.com/) és a [login.microsoftonline.com](https://login.microsoftonline.com/). Ezek a címek blokkolása szeretné, hogy a felhasználók hozzáférjenek a webes Outlookban teljesen, helyett csupán jóváhagyott identitások és erőforrásokhoz való korlátozása.

A kihíváshoz tartozó Azure Active Directory (Azure AD) megoldás a bérlői korlátozások nevű szolgáltatás. A bérlői korlátozásokkal a szervezetek az Azure AD-bérlő alapján vezérelhetik a SaaS-Felhőbeli alkalmazásokhoz való hozzáférést az egyszeri bejelentkezéshez használt alkalmazások esetében. Például érdemes férhessenek hozzá a szervezet Office 365-alkalmazásokhoz, és megakadályozza, hogy más szervezetek számára példányok ugyanazt az alkalmazásokhoz való hozzáférést.  

A bérlői korlátozásokkal a szervezetek meghatározhatják azon bérlők listáját, amelyekhez a felhasználók hozzáférhetnek. Az Azure AD majd csak ezeket a bérlők számára engedélyezett hozzáférést biztosít.

Ez a cikk az Office 365 bérlői korlátozásait ismerteti, de a szolgáltatásnak működnie kell minden olyan SaaS Cloud-alkalmazással, amely modern hitelesítési protokollokat használ az Azure AD-vel az egyszeri bejelentkezéshez. Ha SaaS-alkalmazások egy másik Azure AD-bérlői Office 365 által használt bérlőtől használ, ellenőrizze, hogy az összes szükséges bérlők számára engedélyezett. További információ a felhőalapú SaaS-alkalmazások: a [Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Működési elv

A teljes megoldás az alábbi összetevőkből áll:

1. **Azure ad**: ha a `Restrict-Access-To-Tenants: <permitted tenant list>` jelen van, az Azure ad csak az engedélyezett bérlők biztonsági jogkivonatait bocsátja ki.

2. Helyszíni **proxykiszolgáló-infrastruktúra**: ez az infrastruktúra egy SSL (SSL) vizsgálatra alkalmas proxy eszköz. A proxyt úgy kell konfigurálni, hogy beszúrja az engedélyezett bérlők listáját tartalmazó fejlécet az Azure AD-ba irányuló forgalomba.

3. **Ügyfélszoftver**: a bérlői korlátozások támogatásához az ügyfélszoftvernek közvetlenül az Azure ad-ből kell kérnie a jogkivonatokat, hogy a proxy-infrastruktúra képes legyen a forgalom elfogására. A böngészőalapú Office 365-alkalmazások jelenleg támogatják a bérlői korlátozásokat, mint a modern hitelesítést használó Office-ügyfeleket (például OAuth 2,0).

4. **Modern hitelesítés**: a Cloud servicesnek modern hitelesítést kell használnia a bérlői korlátozások használatához, és le kell tiltania az összes nem engedélyezett bérlő hozzáférését. Az Office 365 Cloud Servicest úgy kell konfigurálni, hogy alapértelmezés szerint modern hitelesítési protokollokat használjanak. Az Office 365 modern hitelesítés támogatása a legfrissebb információkért olvassa el a [frissített Office 365 modern hitelesítését](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

A következő ábra szemlélteti a magas szintű adatforgalmat. A bérlői korlátozások csak az Azure AD-ra irányuló adatforgalomra, az Office 365 Cloud Servicesre nem vonatkoznak. Ez a különbség azért fontos, mert az Azure AD-hitelesítéshez használt forgalom mennyisége általában sokkal alacsonyabb, mint a forgalom mennyisége olyan SaaS-alkalmazásokhoz, mint az Exchange Online és a SharePoint Online.

![Bérlői korlátozások – forgalom folyamatábrája](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

A bérlői korlátozások megkezdésének két lépése van. Először is győződjön meg arról, hogy az ügyfelek csatlakozhatnak a megfelelő címekhez. Másodszor konfigurálja a proxy-infrastruktúrát.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

A bérlői korlátozások használatához az ügyfeleknek képesnek kell lenniük a következő Azure AD URL-címekhez való kapcsolódásra a hitelesítéshez: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)és [login.Windows.net](https://login.windows.net/). Emellett az Office 365 eléréséhez az ügyfeleknek képesnek kell lenniük az [office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)által definiált teljes tartománynevek, URL-címek és IP-címek összekapcsolására is. 

### <a name="proxy-configuration-and-requirements"></a>Proxy konfigurálása és követelmények

A következő konfiguráció szükséges a bérlői korlátozások a proxy-infrastruktúrán keresztüli engedélyezéséhez. Ez az útmutató az általános, így a proxy gyártója által biztosított dokumentációt megvalósítási lépések hivatkozzon.

#### <a name="prerequisites"></a>Előfeltételek

- A proxy SSL hozzáférés, HTTP-fejléc Beszúrás után hajtsa végre, és teljes tartománynevek/URL-címekkel destinations szűrése képesnek kell lennie.

- Az ügyfelek meg kell bíznia az SSL-kommunikációhoz a proxy által bemutatott tanúsítványlánc. Ha például egy belső [nyilvános kulcsokra épülő infrastruktúra (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) tanúsítványait használja, a belső kiállító főtanúsítvány-hitelesítésszolgáltató tanúsítványának megbízhatónak kell lennie.

- Ez a funkció az Office 365-előfizetések részét képezi, de ha bérlői korlátozásokkal szeretné szabályozni a más SaaS-alkalmazásokhoz való hozzáférést, akkor prémium szintű Azure AD 1 licenc szükséges.

#### <a name="configuration"></a>Konfiguráció

Az egyes bejövő kérések login.microsoftonline.com, login.microsoft.com és login.windows.net két HTTP-fejlécek beszúrása: *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet*.

A fejlécek a következő elemeket kell tartalmaznia:

- A *hozzáférés korlátozása a bérlők*számára értékkel \<engedélyezett bérlői lista\>, amely a bérlők vesszővel tagolt listája, amely számára engedélyezni kívánja a felhasználók számára a hozzáférést. Bármely tartomány, amely regisztrálva van a bérlő segítségével azonosíthatja a bérlő ebben a listában. Például hogy engedélyezze a hozzáférést a Contoso és Fabrikam is bérlők számára, a név-érték pár hasonlóan néz ki: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- A *korlátozás-hozzáférés-kontextushoz*használjon egy egyedi CÍMTÁR-azonosító értékét, amely deklarálja, hogy melyik bérlő állítja be a bérlői korlátozásokat. Ha például a contoso-t a bérlői korlátozási szabályzatot beállító bérlőként szeretné deklarálni, a név/érték párok a következőképpen néznek ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A címtár-azonosítót a [Azure Active Directory-portálon](https://aad.portal.azure.com/)találhatja meg. Jelentkezzen be rendszergazdaként, válassza ki **Azure Active Directory**, majd **tulajdonságok**.

Annak megakadályozása érdekében, hogy a felhasználók beillesszenak saját HTTP-fejlécet nem jóváhagyott bérlők számára, a proxynak le kell cserélnie a *korlátozás-hozzáférés – bérlői* fejlécet, ha az már szerepel a bejövő kérelemben.

Az ügyfelek kell kényszeríthető login.microsoftonline.com login.microsoft.com és login.windows.net érkező összes kérés a proxy használatára. Ha például a PAC-fájlok használatával irányítja az ügyfeleket a proxy használatára, a végfelhasználók nem tudják szerkeszteni vagy letiltani a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz a végfelhasználók és a rendszergazdák felhasználói élményét ismerteti.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Példa felhasználóként a Contoso hálózatán lévő, de próbál meg hozzáférni egy megosztott SaaS-alkalmazás például az Outlookból online Fabrikam példányát. Ha a fabrikam nem engedélyezett bérlő a contoso-példányhoz, a felhasználó egy hozzáférés-megtagadási üzenetet kap, amely azt mondja, hogy olyan erőforráshoz próbál hozzáférni, amely az IT-részleg által nem jóváhagyott szervezethez tartozik.

### <a name="admin-experience"></a>A rendszergazda teendői

Míg a bérlői korlátozások konfigurálása a vállalati proxy infrastruktúráján történik, a rendszergazdák közvetlenül is hozzáférhetnek a bérlői korlátozások jelentéseihez a Azure Portal. A jelentések megtekintése:

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/). Megjelenik a **Azure Active Directory felügyeleti központ** irányítópultja.

2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. Megjelenik a Azure Active Directory áttekintés oldal.

3. A **többi funkció** fejlécében válassza a **bérlői korlátozások**elemet.

A korlátozott hozzáférésű bérlőként megadott bérlő rendszergazdája a jelentés használatával megtekintheti a bérlői korlátozási szabályzat miatt letiltott bejelentkezéseket, beleértve a használt identitást és a cél könyvtár-azonosítót is. Bejelentkezések szerepelnek, ha a bérlői beállítások a korlátozás a felhasználó bérlői vagy az erőforrás-bérlő számára a bejelentkezés.

Más jelentések az Azure Portalon, például a szűrők segítségével határozhatja meg a jelentés hatókörét. Szűrheti egy adott időintervallumot, felhasználót, alkalmazást, ügyfelet vagy állapotot. Ha az **oszlopok** gombot választja, a következő mezők tetszőleges kombinációjával választhat:

- **Felhasználói**
- **Alkalmazás**
- **Állapot**
- **Dátum**
- **Dátum (UTC)** (az UTC egyezményes világidő)
- **MFA hitelesítési módszer** (többtényezős hitelesítési módszer)
- **MFA** -hitelesítés részletei (többtényezős hitelesítés részletei)
- **MFA-eredmény**
- **IP-cím**
- **Ügyfél**
- **Felhasználónév**
- **Hely**
- **Cél bérlő azonosítója**

## <a name="office-365-support"></a>Az Office 365-támogatás

Az Office 365-alkalmazásoknak két feltételnek kell megfelelniük a bérlői korlátozások teljes körű támogatásához:

1. A használt ügyfél támogatja a modern hitelesítést.
2. Modern hitelesítés engedélyezve van, mint az alapértelmezett hitelesítési protokoll, a felhőszolgáltatás számára.

Tekintse meg [frissített Office 365 modern hitelesítését](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) a legfrissebb információkat a Microsoft Office mely ügyfelek jelenleg támogatja a modern hitelesítést. Az oldal adott Exchange Online és Skype vállalati Online bérlők modern hitelesítés engedélyezése az utasításokra mutató hivatkozásokat is tartalmaz. A SharePoint Online alapértelmezés szerint már lehetővé teszi a modern hitelesítés használatát.

Az Office 365 böngésző-alapú alkalmazások (az Office-portál, a Yammer, a SharePoint-webhelyek, az Outlook a weben és egyebek) jelenleg is támogatja a bérlői korlátozásokat. A vastag ügyfelek (az Outlook, a Skype vállalati verzió, a Word, az Excel, a PowerPoint stb.) csak a modern hitelesítés használata esetén tudják kényszeríteni a bérlői korlátozásokat.  

A modern hitelesítést támogató Outlook és Skype vállalati ügyfelek továbbra is használhatnak örökölt protokollokat a bérlők esetében, ahol nincs engedélyezve a modern hitelesítés, ami gyakorlatilag megkerüli a bérlői korlátozásokat. A bérlői korlátozások az örökölt protokollokat használó alkalmazásokat letilthatják, ha a hitelesítés során kapcsolatba kerülnek a login.microsoftonline.com, a login.microsoft.com vagy a login.windows.net.

Az Outlook, a Windows-ügyfelek dönthetnek úgy, hogy meggátolja, hogy a végfelhasználók számára nem engedélyezett e-mail-fiókokat ad hozzá a profilok korlátozásokat. Ha például a [nem alapértelmezett Exchange-fiókok hozzáadásának letiltása](https://gpsearch.azurewebsites.net/default.aspx?ref=1) csoportházirend-beállítás.

## <a name="testing"></a>Tesztelés

Ha a teljes szervezete előtt szeretné kipróbálni a bérlői korlátozásokat, két lehetőség közül választhat: egy gazdagépen alapuló megközelítés, például a Hegedűs használata, vagy a proxybeállítások fokozatos bevezetése.

### <a name="fiddler-for-a-host-based-approach"></a>A gazdagép-alapú megközelítést a fiddler

A fiddler egy ingyenes webes hibaelhárító proxy, amely segítségével rögzítése, és módosítsa a HTTP/HTTPS-forgalmat, többek között a HTTP-fejlécek beszúrását. A Hegedűs a bérlői korlátozások teszteléséhez való konfigurálásához hajtsa végre a következő lépéseket:

1. [Töltse le és telepítse a Fiddler](https://www.telerik.com/fiddler).

2. Fiddler-HTTPS-forgalom visszafejtése konfigurálása [Fiddler a Súgó dokumentációját](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurálja a Fiddler beszúrása a *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet* fejlécek egyéni szabályok használatával:

   1. A Fiddler webes hibakereső eszközben válassza ki a **szabályok** menüre, majd válassza **szabályok testreszabása...** a customrules fájl egynél fájl megnyitásához.

   2. Adja hozzá a következő sorokat a `OnBeforeRequest` függvény elejéhez. Cserélje le \<bérlői tartományt\> a Bérlővel regisztrált tartománnyal (például `contoso.onmicrosoft.com`). Cserélje le \<címtár-azonosító\> a bérlő Azure AD GUID azonosítóval.

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

      Ha több bérlő engedélyeznie kell, a bérlő neveket használjon vesszőt. Például:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárja be a customrules fájl egynél fájlt.

Miután konfigurálta a Fiddler, a forgalom rögzítése a **fájl** menüt, és kiválasztja **forgalom rögzítése**.

### <a name="staged-rollout-of-proxy-settings"></a>A proxybeállítások szakaszos bevezetés

A proxy infrastruktúra képességeit, függően beállításait a felhasználók számára a bevezetésének előkészítéséhez. Az alábbiakban a megfontolásra néhány magas szintű lehetőségek:

1. Tesztfelhasználók átirányítása egy test-proxy infrastruktúra, míg a normál felhasználók továbbra is használhatja a termelési-proxy infrastruktúra a PAC-fájl használatával.
2. Proxykiszolgálók, amelyeken előfordulhat, hogy a csoportok különböző konfigurációkat támogatják.

További részletekért tekintse meg a proxykiszolgáló dokumentációját.

## <a name="next-steps"></a>Következő lépések

- További információ [frissített Office 365 modern hitelesítését](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Tekintse át a [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
