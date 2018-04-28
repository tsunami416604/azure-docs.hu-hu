---
title: Korlátozza, hogy a bérlők - Azure felhőalapú alkalmazásokba való hozzáférés kezelése |} Microsoft Docs
description: Bérlői korlátozások segítségével kezelheti a felhasználók hogyan férhetnek hozzá az Azure AD-bérlő alapuló alkalmazásokat.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: kgremban
ms.openlocfilehash: dae4599db5127ac8fd266d5e0f299e1284fc9b9c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>A felhőalapú alkalmazások esetében használja bérlői korlátozások a Szolgáltatottszoftver-hozzáférés kezelése

Nagy szervezeteknek, emelje ki biztonsági át szeretne helyezni a felhőalapú szolgáltatások Office 365-höz hasonló, de tudniuk kell, hogy, hogy a felhasználók csak hozzá jóváhagyott erőforrásokhoz. Hagyományosan a vállalatok korlátozása tartománynév vagy IP-címek hozzáférésének kezelése olyan szeretne. Ez a megközelítés a Szolgáltatottszoftver-alkalmazásoknál a rendszer hol tárolja a nyilvános felhőben, például outlook.office.com és login.microsoftonline.com megosztott tartománynevek futó világ sikertelen lesz. Ezeknél a címeknél blokkolja szeretné, hogy a felhasználók hozzáférését az Outlook, a weben teljesen, csupán jóváhagyott identitások és az erőforrásokhoz való korlátozása helyett.

Ez a kérdés megoldást az Azure Active Directory bérlői korlátozások nevezett szolgáltatással. Bérlői korlátozások lehetővé teszi a szervezetek SaaS felhőalapú alkalmazásokhoz, az alkalmazások használnak, az egyszeri bejelentkezés az Azure AD-bérlő alapján való hozzáférés vezérlése érdekében. Érdemes lehet például, hogy férjen hozzá a szervezet Office 365-alkalmazásokhoz, közben megakadályozza más szervezetek példányok az azonos alkalmazások elérésére.  

Bérlői korlátozások révén a szervezetek adja meg, hogy a felhasználók jogosultak hozzáférni bérlők listáját. Az Azure AD majd csak ezeket a bérlők számára engedélyezett hozzáférést biztosít.

Ez a cikk az Office 365 bérlői korlátozásokkal foglalkozik, de a szolgáltatás bármely modern hitelesítési protokollok megvalósítását végzi az Azure ad-val egyszeri bejelentkezést használó SaaS felhőalkalmazás együtt kell működnie. SaaS-alkalmazásokat egy másik Azure AD-val a bérlőtől használják az Office 365 bérlői használatakor győződjön meg arról, hogy minden szükséges bérlők engedélyezettek. A cloud SaaS-alkalmazások kapcsolatos további információkért lásd: a [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Működés

A teljes megoldás a következő összetevőkből áll: 

1. **Az Azure AD** – Ha a `Restrict-Access-To-Tenants: <permitted tenant list>` van jelen, az Azure AD csak problémák biztonsági jogkivonatokat engedélyezett tenantok. 

2. **A helyszíni proxy kiszolgálói infrastruktúra** – képes SSL-ellenőrzést, listáját tartalmazó fejléc insert konfigurálva proxy eszköz engedélyezett bérlők adatforgalmat az Azure AD-be. 

3. **Ügyfélszoftver** – bérlői korlátozása érdekében, az ügyfélszoftver kell igényelnie az Azure AD-ről jogkivonatokat, úgy, hogy a forgalom megszerezhetik a proxy infrastruktúra. Bérlői korlátozások jelenleg támogatott webböngésző-alapú Office 365-alkalmazásokat és az Office-ügyfelek (például az OAuth 2.0-s) a modern hitelesítés használatakor. 

4. **A modern hitelesítés** – felhőszolgáltatások modern hitelesítést kell használnia bérlői korlátozások használja, és letiltja a hozzáférést minden nem engedélyezett bérlők számára. Office 365 felhőalapú szolgáltatások alapértelmezés szerint a modern hitelesítési protokollok használatára kell állítani. Az Office 365 modern hitelesítés támogatása legfrissebb információkért olvassa el a [frissített Office 365 modern hitelesítést](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

A következő ábra szemlélteti a magas szintű adatforgalmat. Az Azure AD, és nem az Office 365 felhőszolgáltatások forgalom csak szükséges SSL-ellenőrzést. Ezt a különbséget fontos, mert az Azure ad hitelesítési forgalom kötet általában sokkal alacsonyabb, mint adatforgalma SaaS-alkalmazásokhoz, mint például az Exchange Online és SharePoint online-hoz való.

![A bérlői forgalom áramlását korlátozások – ábra](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

Ismerkedés a bérlő korlátozások két lépésből áll. Az első lépés, győződjön meg arról, hogy az ügyfelek csatlakozhatnak-e a megfelelő címet. A második pedig a proxy-infrastruktúra konfigurálására.

### <a name="urls-and-ip-addresses"></a>URL-címei és IP-címek

Bérlői korlátozások használja, az ügyfelek kell kapcsolódnia kell a következő Azure AD URL-címek hitelesítéséhez: login.microsoftonline.com login.microsoft.com és login.windows.net. Ezenkívül hozzáférést az Office 365, az ügyfelek is kell kapcsolódnia kell az FQDN vagy URL-címek és IP-címek meghatározott [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Proxy konfigurációs és követelményei

A következő konfiguráció szükséges bérlői korlátozásának engedélyezése a proxy-infrastruktúrán keresztül. Ez az útmutató nem általános, ezért a szabott megvalósítási lépéseket a proxy gyártójának dokumentációjában tájékozódhat.

#### <a name="prerequisites"></a>Előfeltételek

- A proxykiszolgáló SSL-hozzáférés, HTTP-fejléc beszúrási, hajtsa végre, és FQDN vagy URL-címekkel célok szűréséhez képesnek kell lennie. 

- Az ügyfelek által a proxy SSL-kommunikációhoz a tanúsítványlánc megbízhatónak kell lennie. Például ha egy belső használatú PKI-tanúsítványokat használ, a belső vállalati tanúsítvány Főhitelesítésszolgáltató-tanúsítvány megbízhatónak kell lennie.

- Az Office 365-előfizetés tartalmazza ezt a szolgáltatást, de ha más SaaS-alkalmazásokhoz való hozzáférés-vezérlésének bérlői korlátozások használni kívánt szükség-e majd Azure AD Premium 1 licenceket.

#### <a name="configuration"></a>Konfiguráció

Az egyes bejövő kérelmek login.microsoftonline.com, login.microsoft.com és login.windows.net, két HTTP-fejléc insert: *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet*.

A fejléc a következő elemeket kell tartalmaznia: 
- A *korlátozása-Access-az-bérlők*, érték \<bérlői lista megengedett\>, amely, engedélyezi a felhasználók hozzáférhessenek a bérlők vesszővel tagolt listája. Bármely tartomány, amely regisztrálva van egy bérlő segítségével azonosíthatja a bérlő ezen a listán. Például hogy hozzáférést a Contoso és Fabrikam mind a bérlők számára, a név-érték pár néz ki:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- A *korlátozása-Access-környezet*, egy egyetlen Azonosítót, mely bérlői beállítja a bérlő korlátozások deklaráló érték. Például Contoso deklarálnia eljárva, amely a bérlői korlátozások házirend beállítása, a név-érték pár néz ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A könyvtár-azonosítója a található a [Azure-portálon](https://portal.azure.com). Jelentkezzen be rendszergazdaként, válassza **Azure Active Directory**, majd jelölje be **tulajdonságok**.

A felhasználók megakadályozása a saját HTTP-fejléc beszúrni a bérlők számára nem engedélyezett, a proxy kell cserélje le a korlátozása-Access-az-bérlők fejléc, ha már szerepel a bejövő kérelemben. 

Ügyfelek kell kényszeríthető, a proxy használata login.microsoftonline.com, login.microsoft.com és login.windows.net minden olyan kérelem esetében. Például ha a PAC-fájlok használatával irányíthatják az ügyfeleket a proxyt használ, a végfelhasználók nem kell tudni szerkesztéséhez, vagy tiltsa le a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz ismerteti a végfelhasználók és rendszergazdák élményének.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Egy példa felhasználó a Contoso hálózatán lévő, de megpróbál hozzáférni egy megosztott SaaS-alkalmazáshoz például az Outlookhoz online Fabrikam példányát. Ha a Contoso egy bérlőt nem engedélyezett ezen példányát, a felhasználónál a következő lapon:

![Oldalt a bérlők számára nem engedélyezett a felhasználók a hozzáférés megtagadva](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Rendszergazdai feladatok

Amíg a bérlő korlátozások konfigurálást vállalati proxy az infrastruktúrán, rendszergazdák férhetnek hozzá az Azure-portálon a bérlő korlátozások jelentéseket közvetlenül. A jelentések megtekintéséhez nyissa meg az Azure Active Directory – áttekintés oldalra, majd keresse meg a többi képességeket.

A megadott a korlátozott-Access-környezet bérlői a Ez a jelentés segítségével láthatja az összes bejelentkezések blokkolva miatt a bérlő korlátozások házirend, a használt, beleértve a bérlői rendszergazda és a célkönyvtárat azonosítóját.

![Korlátozott bejelentkezési kísérletek megtekintéséhez az Azure-portál használatával](./media/active-directory-tenant-restrictions/portal-report.png)

Az Azure portálon más jelentések, például a szűrők segítségével adja meg a jelentés hatókörében. Egy adott felhasználó, alkalmazás, ügyfél vagy alatt az időtartam alatt jeleníthetők meg.

## <a name="office-365-support"></a>Az Office 365-támogatás

Office 365-alkalmazások teljes körű támogatásához a bérlő korlátozások két feltételeknek kell megfelelnie:

1. A használt ügyfél támogatja a modern hitelesítés
2. A felhőszolgáltatás alapértelmezett hitelesítési protokollnak engedélyezve van a modern hitelesítést.

Tekintse meg [frissített Office 365 modern hitelesítést](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) mely Office ügyfelek jelenleg támogatja a modern hitelesítést a legfrissebb információkat. Ez az oldal az adott Exchange Online és Skype vállalati Online bérlőkhöz modern hitelesítést engedélyezésének utasításokra mutató hivatkozásokat is tartalmaz. A modern hitelesítés alapértelmezés szerint a SharePoint online-hoz már engedélyezve van.

Bérlői korlátozások jelenleg az Office 365 webböngésző-alapú alkalmazások támogatják (az Office-portál Yammer, SharePoint webhelyek, az Outlook Web, stb.). Vastag ügyfelek (Outlook, a Skype vállalati, a Word, Excel, PowerPoint, stb.) Bérlői korlátozások csak kényszerítheti a modern hitelesítés használatakor.  

Outlook és Skype a modern hitelesítést támogató üzleti ügyfelek előfordulhat, hogy továbbra is tudni használni a bérlők elleni régebbi protokollokra, ahol nincs engedélyezve a modern hitelesítést, hatékonyan kihagyásával bérlői korlátozások. Alkalmazásokat, amelyek használják az örökölt protokollok bérlői korlátozások előfordulhat, hogy blokkolja, ha azok kapcsolatba login.microsoftonline.com, login.microsoft.com vagy login.windows.net hitelesítés során.

Az Outlook, a Windows-ügyfelek dönthetnek úgy, hogy meggátolja, hogy a végfelhasználók számára nem engedélyezett levelezési fiók hozzáadása a profilok korlátozások megvalósítása. Például tekintse meg a [tiltása nem alapértelmezett Exchange-fiókok hozzáadása](http://gpsearch.azurewebsites.net/default.aspx?ref=1) csoportházirend-beállítás. Az Outlook, a nem Windows platformokon, és a Skype vállalati minden platformon bérlő korlátozások teljes körű támogatása jelenleg nem áll rendelkezésre.

## <a name="testing"></a>Tesztelés

Ha azt szeretné, bérlő korlátozások kipróbálásához előtt bevezetné, a teljes szervezet számára, vannak-e két lehetőség közül választhat: egy állomásalapú megközelítés Fiddler, vagy a proxybeállítások egy előkészített bevezetésének hasonló eszköz használatával.

### <a name="fiddler-for-a-host-based-approach"></a>A fiddler állomásalapú megközelítésre

Fiddler egy ingyenes webes proxy rögzítése, és a HTTP/HTTPS-forgalmat, beleértve a HTTP-fejlécek beszúrása módosításához használható hibakeresés. Bérlői korlátozások tesztelése a Fiddler konfigurálásához hajtsa végre az alábbi lépéseket:

1.  [Töltse le és telepítse a Fiddler](http://www.telerik.com/fiddler).
2.  Konfigurálja a Fiddler HTTPS-forgalmat, visszafejtéséhez [Fiddler tartozó súgót](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurálja a Fiddler beszúrására a *korlátozása-Access-az-bérlők* és *korlátozása-Access-környezet* fejlécek egyéni szabályok használatával:
  1. A Fiddler webes Debugger eszköz, válassza ki a **szabályok** menüre, majd válassza **testreszabása szabályok...** a CustomRules fájl megnyitásához.
  2. Adja hozzá a következő sorokat elején a *OnBeforeRequest* függvény. Cserélje le \<bérlői tartomány\> tartománnyal rendelkező regisztrálva a bérlő például contoso.onmicrosoft.com. Cserélje le \<könyvtár-azonosítója\> a bérlő Azure AD GUID azonosítóval.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Ha szeretné, hogy a bérlők több, a bérlő neveket használjon vesszőt. Példa:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Mentse és zárja be a CustomRules fájlt.

Miután konfigurálta a Fiddler, rögzítheti a forgalmat a **fájl** menüben, majd válassza **forgalom rögzítése**.

### <a name="staged-rollout-of-proxy-settings"></a>A proxybeállítások előkészített bevezetésének

Attól függően, hogy a proxy infrastruktúra szolgáltatásait akkor esetleg előkészítése a Bevezetés a beállítások a felhasználók számára. Az alábbiakban a megfontolásra néhány általános beállítások:

1.  PAC-fájlok segítségével tesztfelhasználók mutasson a teszt-proxy infrastruktúra, amíg a normál felhasználók továbbra is használhatják a termelési-proxy infrastruktúra.
2.  Néhány proxykiszolgáló a csoportok különböző konfigurációt is támogatja.

Tekintse meg a proxy server dokumentációjában talál információt.

## <a name="next-steps"></a>További lépések

- További információ a [frissített Office 365 modern hitelesítést](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Tekintse át a [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
