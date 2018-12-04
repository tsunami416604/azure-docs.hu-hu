---
title: A felhőalkalmazások korlátozása révén a bérlők – Azure-hozzáférés kezelése |} A Microsoft Docs
description: Bérlői korlátozások használata kezelheti, hogy mely felhasználók érhetik el az alkalmazásokat az Azure AD-bérlő alapján.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: yossib
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: barbkess
ms.reviewer: richagi
ms.openlocfilehash: ca3396024fdab31217b00ef7cdd39e4e61504d27
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52851094"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>A felhőalapú alkalmazások használati Bérlőkorlátozások SaaS való hozzáférés kezelése

Nagy szervezeteknek, amelyek biztonsági hangsúlyozzák át szeretné helyezni a felhőalapú szolgáltatások, például az Office 365-höz, de kell tudni, hogy a felhasználók csak hozzáférhetnek a jóváhagyott erőforrásokhoz. Hagyományosan vállalatok korlátozza a tartomány neve vagy IP-hozzáférés kezelése szeretne. Ez a megközelítés egy olyan világban, SaaS-alkalmazások a nyilvános felhőben futó megosztott tartományneveket, mint outlook.office.com és login.microsoftonline.com üzemeltető sikertelen lesz. Ezek a címek blokkolása szeretné, hogy a felhasználók hozzáférjenek a webes Outlookban teljesen, helyett csupán jóváhagyott identitások és erőforrásokhoz való korlátozása.

Az Azure Active Directory-megoldás mutatták Bérlőkorlátozások nevű funkciója. Bérlői korlátozások lehetővé teszi a szervezetek számára, hogy ki férhet hozzá a felhőalapú SaaS-alkalmazásai, az egyszeri bejelentkezés használata az alkalmazások Azure AD-bérlő alapján. Például érdemes férhessenek hozzá a szervezet Office 365-alkalmazásokhoz, és megakadályozza, hogy más szervezetek számára példányok ugyanazt az alkalmazásokhoz való hozzáférést.  

Bérlői korlátozások révén a szervezetek a bérlők, amely hozzáférhető a felhasználók listáját adja meg. Az Azure AD majd csak ezeket a bérlők számára engedélyezett hozzáférést biztosít.

Ez a cikk foglalkozik a bérlői korlátozások az Office 365-höz, de a szolgáltatás bármely felhőalapú SaaS-alkalmazással, az egyszeri bejelentkezés az Azure AD-vel modern hitelesítési protokollok használó működnie kell. Ha SaaS-alkalmazások egy másik Azure AD-bérlői Office 365 által használt bérlőtől használ, ellenőrizze, hogy az összes szükséges bérlők számára engedélyezett. További információ a felhőalapú SaaS-alkalmazások: a [Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Működés

A teljes megoldás az alábbi összetevőkből áll: 

1. **Az Azure AD** – Ha a `Restrict-Access-To-Tenants: <permitted tenant list>` van adva, az Azure AD csak problémák biztonsági jogkivonatokat az engedélyezett bérlők számára. 

2. **A helyszíni proxy server infrastruktúra** – SSL-forgalmat, úgy konfigurálva, hogy a fejléc listáját tartalmazó beszúrása képes proxy eszköz engedélyezett bérlők be az Azure ad felé irányuló forgalom. 

3. **Ügyfélszoftver** – bérlői korlátozása érdekében, az ügyfélszoftvert kell újraindítást igényelnie jogkivonatok közvetlenül az Azure AD-ben, hogy a forgalom a-proxy infrastruktúra elfoghatják. Bérlői korlátozások jelenleg támogatott Office-ügyfelek és a böngészőalapú Office 365-alkalmazások (például az OAuth 2.0-s) a modern hitelesítés használatakor. 

4. **Modern hitelesítés nélküli** – a cloud services kell használnia a modern hitelesítés Bérlőkorlátozások használhatja, és nem engedélyezett az összes bérlőre való hozzáférés letiltása. Office 365 cloud services alapértelmezés szerint kell állítani a modern hitelesítési protokollok használatára. Az Office 365 modern hitelesítés támogatása a legfrissebb információkért olvassa el a [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

A következő ábra szemlélteti a magas szintű adatforgalmat. SSL-ellenőrzés csak a forgalom az Azure ad-hez, az Office 365 cloud services, nem szükséges. Ezt a különbséget fontos, mert a forgalom mennyisége az Azure AD-hitelesítés általában sokkal alacsonyabb, mint a SaaS-alkalmazásokban, mint például az Exchange online-hoz és a SharePoint Online adatforgalma.

![Bérlői korlátozások forgalom áramlását – ábra](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

Ismerkedés a bérlői korlátozások két lépésből áll. Győződjön meg arról, hogy az ügyfelek csatlakozhatnak-e a megfelelő címeket az első lépéseként. A második, hogy a proxy infrastruktúra beállítása.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

Bérlői korlátozások használata, az ügyfelek csatlakozni a következő Azure AD URL-címek hitelesítéséhez képesnek kell lennie: login.microsoftonline.com login.microsoft.com és login.windows.net. Emellett való hozzáférést az Office 365, az ügyfelek is kell lennie csatlakozni a teljes tartománynevek/URL-címek és IP-címek meghatározott [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxy konfigurálása és követelmények

Bérlői korlátozások engedélyezése a proxy-infrastruktúrán keresztül a következő konfiguráció szükséges. Ez az útmutató az általános, így a proxy gyártója által biztosított dokumentációt megvalósítási lépések hivatkozzon.

#### <a name="prerequisites"></a>Előfeltételek

- A proxy SSL hozzáférés, HTTP-fejléc Beszúrás után hajtsa végre, és teljes tartománynevek/URL-címekkel destinations szűrése képesnek kell lennie. 

- Az ügyfelek meg kell bíznia az SSL-kommunikációhoz a proxy által bemutatott tanúsítványlánc. Például ha a belső PKI-tanúsítványok használata esetén a belső vállalati tanúsítvány tanúsítvány megbízhatónak kell lennie.

- Ez a funkció az Office 365-előfizetés szerepel, de ha más SaaS-alkalmazásokhoz való hozzáférés szabályozásához Bérlőkorlátozások használni kívánt majd az Azure AD Premium 1 licencekre szükség.

#### <a name="configuration"></a>Konfiguráció

Az egyes bejövő kérések login.microsoftonline.com, login.microsoft.com és login.windows.net két HTTP-fejlécek beszúrása: *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet*.

A fejlécek a következő elemeket kell tartalmaznia: 
- A *korlátozása-Access-az-bérlők*, érték \<engedélyezett bérlő listájának\>, azaz engedélyezheti a felhasználók hozzáférhessenek a bérlők vesszővel tagolt listája. Bármely tartomány, amely regisztrálva van a bérlő segítségével azonosíthatja a bérlő ebben a listában. Például hogy engedélyezze a hozzáférést a Contoso és Fabrikam is bérlők számára, a név-érték pár hasonlóan néz ki:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- A *korlátozása-Access-környezet*, egy egyetlen címtár-azonosító, kijelenti, hogy melyik bérlőhöz beállítása a bérlői korlátozások érték. Például Contoso nyilvánítja, a bérlő, amely a bérlői korlátozások házirend beállítása, a név-érték pár hasonlóan néz ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A címtár-azonosító, az annak a [az Azure portal](https://portal.azure.com). Jelentkezzen be rendszergazdaként, válassza ki **Azure Active Directory**, majd **tulajdonságok**.

Meg, hogy a felhasználók saját HTTP-fejléc beszúrása a nem jóváhagyott bérlők, a proxy cserélje le a korlátozása-Access-az-bérlők fejléc, ha már a bejövő kérelemben szereplő kell. 

Az ügyfelek kell kényszeríthető login.microsoftonline.com login.microsoft.com és login.windows.net érkező összes kérés a proxy használatára. Például ha a PAC-fájlt használ a proxy használata az ügyfelek, a végfelhasználók nem lehet tudni szerkesztése vagy letiltása a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz bemutatja a élmény a végfelhasználók és rendszergazdák is.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Példa felhasználóként a Contoso hálózatán lévő, de próbál meg hozzáférni egy megosztott SaaS-alkalmazás például az Outlookból online Fabrikam példányát. Ha a Contoso az adott példány számára nem engedélyezett bérlők, a felhasználónál a következő oldalt:

![Hozzáférés megtagadva lapot a bérlők számára nem engedélyezett a felhasználók számára](./media/tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>A rendszergazda teendői

Bérlői korlátozások konfigurálása történik a vállalati proxy infrastruktúra, míg a rendszergazdák hozzáférhetnek a bérlői korlátozások jelentések az Azure Portalon közvetlenül. A jelentések megtekintéséhez lépjen az Azure Active Directory – áttekintés lapra, majd keresse meg a más képességet.

A rendszergazda a bérlő, a Tiltott helyek-Access-környezet bérlői használható ez a jelentés megtekintéséhez a bejelentkezések Bérlőkorlátozások házirend, beleértve az identitásnak miatt blokkolva a megadott és a céloldali könyvtár-azonosító. Bejelentkezések szerepelnek, ha a bérlői beállítások a korlátozás a felhasználó bérlői vagy az erőforrás-bérlő számára a bejelentkezés.

![Korlátozott bejelentkezési kísérletek megtekintéséhez az Azure portal használatával](./media/tenant-restrictions/portal-report.png)

Más jelentések az Azure Portalon, például a szűrők segítségével határozhatja meg a jelentés hatókörét. Egy adott felhasználó, alkalmazás, ügyfél vagy időintervallum szűrésével.

## <a name="office-365-support"></a>Az Office 365-támogatás

Office 365-alkalmazások bérlői korlátozások teljes támogatása érdekében két feltételeknek kell megfelelnie:

1. A használt ügyfél támogatja a modern hitelesítés
2. Modern hitelesítés engedélyezve van, mint az alapértelmezett hitelesítési protokoll, a felhőszolgáltatás számára.

Tekintse meg [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) a legfrissebb információkat a Microsoft Office mely ügyfelek jelenleg támogatja a modern hitelesítést. Az oldal adott Exchange Online és Skype vállalati Online bérlők modern hitelesítés engedélyezése az utasításokra mutató hivatkozásokat is tartalmaz. Modern hitelesítés nélküli már engedélyezve van a SharePoint online-ban alapértelmezés szerint.

Bérlői korlátozások Office 365 böngészőalapú alkalmazások által jelenleg támogatott (az Office-portálon, a Yammeren, SharePoint helyekkel, Outlook, a webes, stb.). Vastag ügyfelek (Outlook, a Skype for Business, a Word, Excel, PowerPoint, stb.) Bérlői korlátozások csak kényszeríti, amikor a modern hitelesítést használja.  

Az Outlook verzió és az üzleti ügyfelek számára a modern hitelesítést támogató előfordulhat, hogy továbbra is tudja használni a bérlőre, régebbi protokollokra, ahol nincs engedélyezve a modern hitelesítést, hatékonyan Bérlőkorlátozások kihagyásával. Alkalmazásokat, amelyek használják az örökölt protokollok Bérlőkorlátozások előfordulhat, hogy blokkolja, ha azok kapcsolatba login.microsoftonline.com, login.microsoft.com vagy login.windows.net hitelesítés során.

Az Outlook, a Windows-ügyfelek dönthetnek úgy, hogy meggátolja, hogy a végfelhasználók számára nem engedélyezett e-mail-fiókokat ad hozzá a profilok korlátozásokat. Ha például a [nem alapértelmezett Exchange-fiókok hozzáadásának letiltása](https://gpsearch.azurewebsites.net/default.aspx?ref=1) csoportházirend-beállítás.

## <a name="testing"></a>Tesztelés

Ha azt szeretné, kipróbálhatja a bérlői korlátozások megvalósítása, az egész cég számára előtt, két lehetőség van: a gazdagép-alapú megközelítést vizsgálata a Fiddlerrel vagy a proxybeállítások egy előkészített bevezetésének hasonló eszköz használatával.

### <a name="fiddler-for-a-host-based-approach"></a>A gazdagép-alapú megközelítést a fiddler

A fiddler egy ingyenes webes hibaelhárító proxy, amely segítségével rögzítése, és módosítsa a HTTP/HTTPS-forgalmat, többek között a HTTP-fejlécek beszúrását. Bérlői korlátozások tesztelése a Fiddler konfigurálásához hajtsa végre az alábbi lépéseket:

1.  [Töltse le és telepítse a Fiddler](https://www.telerik.com/fiddler).
2.  Fiddler-HTTPS-forgalom visszafejtése konfigurálása [Fiddler a Súgó dokumentációját](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurálja a Fiddler beszúrása a *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet* fejlécek egyéni szabályok használatával:
  1. A Fiddler webes hibakereső eszközben válassza ki a **szabályok** menüre, majd válassza **szabályok testreszabása...** a customrules fájl egynél fájl megnyitásához.
  2. Adja hozzá a következő sorokat elején a *OnBeforeRequest* függvény. Cserélje le \<bérlői tartomány\> egy tartomány regisztrálva a bérlővel, például contoso.onmicrosoft.com. Cserélje le \<címtár-azonosító\> a bérlő Azure AD GUID azonosítóval.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Ha több bérlő engedélyeznie kell, a bérlő neveket használjon vesszőt. Példa:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Mentse és zárja be a customrules fájl egynél fájlt.

Miután konfigurálta a Fiddler, a forgalom rögzítése a **fájl** menüt, és kiválasztja **forgalom rögzítése**.

### <a name="staged-rollout-of-proxy-settings"></a>A proxybeállítások szakaszos bevezetés

A proxy infrastruktúra képességeit, függően beállításait a felhasználók számára a bevezetésének előkészítéséhez. Az alábbiakban a megfontolásra néhány magas szintű lehetőségek:

1.  Tesztfelhasználók átirányítása egy test-proxy infrastruktúra, míg a normál felhasználók továbbra is használhatja a termelési-proxy infrastruktúra a PAC-fájl használatával.
2.  Proxykiszolgálók, amelyeken előfordulhat, hogy a csoportok különböző konfigurációkat támogatják.

Tekintse meg a proxy server dokumentációjában talál részleteket.

## <a name="next-steps"></a>További lépések

- További információ [frissített Office 365 modern hitelesítését](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Tekintse át a [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
