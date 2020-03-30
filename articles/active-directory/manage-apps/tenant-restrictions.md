---
title: Bérlői korlátozások használata a SaaS-alkalmazásokhoz való hozzáférés kezeléséhez - Azure AD
description: A bérlői korlátozások használata annak kezeléséhez, hogy mely felhasználók férhetnek hozzá az Azure AD-bérlőn alapuló alkalmazásokhoz.
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
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481177"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Bérlői korlátozások használata a SaaS felhőalapú alkalmazásokhoz való hozzáférés kezeléséhez

A biztonságot hangsúlyozó nagy szervezetek felhőszolgáltatásokra, például az Office 365-re szeretnének átállni, de tudniuk kell, hogy a felhasználók csak jóváhagyott erőforrásokhoz férhetnek hozzá. A vállalatok hagyományosan korlátozzák a tartományneveket vagy AZ IP-címeket, ha kezelni szeretnék a hozzáférést. Ez a megközelítés sikertelen egy olyan világban, ahol a szoftver, mint szolgáltatás (vagy SaaS) alkalmazások üzemeltetik a nyilvános felhőben, futó megosztott tartománynevek, mint [a outlook.office.com](https://outlook.office.com/) és [login.microsoftonline.com](https://login.microsoftonline.com/). Ezeknek a címeknek a letiltása megakadályozná, hogy a felhasználók teljes mértékben hozzáférjenek a Webes Outlookhoz, ahelyett, hogy csupán a jóváhagyott identitásra és erőforrásokra korlátoznák őket.

Az Azure Active Directory (Azure AD) megoldás erre a kihívásra egy szolgáltatás, amelyet bérlői korlátozásoknak neveznek. A bérlői korlátozások, a szervezetek szabályozhatják a hozzáférést a SaaS felhőalapú alkalmazások, az Azure AD-bérlő alapján az alkalmazások használata egyszeri bejelentkezéshez. Előfordulhat például, hogy engedélyezni szeretné a hozzáférést a szervezet Office 365-alkalmazásaihoz, miközben meg szeretné akadályozni, hogy más szervezetek ugyanazoknak az alkalmazásoknak a példányai hozhassák meg a hozzáférést.  

Bérlői korlátozásokkal a szervezetek megadhatják a bérlők listáját, amelyhez a felhasználók hozzáférhetnek. Az Azure AD majd csak hozzáférést biztosít ezekhez az engedélyezett bérlőkhöz.

Ez a cikk az Office 365 bérlői korlátozásaira összpontosít, de a funkciónak együtt kell működnie minden Olyan SaaS-felhőalkalmazással, amely az Azure AD-vel modern hitelesítési protokollokat használ az egyszeri bejelentkezéshez. Ha saas-alkalmazásokat használ egy másik Azure AD-bérlővel az Office 365 által használt bérlőtől, győződjön meg arról, hogy minden szükséges bérlő engedélyezett. Az SaaS-felhőalapú alkalmazásokról az [Active Directory piactérben](https://azure.microsoft.com/marketplace/active-directory/)talál további információt.

## <a name="how-it-works"></a>Működés

A teljes megoldás a következő összetevőkből áll:

1. **Azure AD:** `Restrict-Access-To-Tenants: <permitted tenant list>` Ha a jelen van, az Azure AD csak az engedélyezett bérlők biztonsági jogkivonatokat ad ki.

2. **Helyszíni proxykiszolgáló-infrastruktúra**: Ez az infrastruktúra egy átviteliréteg-biztonsági (TLS) ellenőrzésre alkalmas proxyeszköz. Konfigurálnia kell a proxyt, hogy az engedélyezett bérlők listáját tartalmazó fejlécet helyezze be az Azure AD-re szánt forgalomba.

3. **Ügyfélszoftver:** A bérlői korlátozások támogatásához az ügyfélszoftvernek közvetlenül az Azure AD-től kell jogkivonatokat kérnie, hogy a proxyinfrastruktúra eltudja fogni a forgalmat. A böngészőalapú Office 365-alkalmazások jelenleg támogatják a bérlői korlátozásokat, csakúgy, mint a modern hitelesítést használó Office-ügyfelek (például az OAuth 2.0).

4. **Modern hitelesítés:** A felhőszolgáltatásoknak modern hitelesítést kell használniuk a bérlői korlátozások használatához, és le kell tiltaniuk a hozzáférést az összes nem engedélyezett bérlőhöz. Az Office 365 felhőszolgáltatásait úgy kell konfigurálnia, hogy alapértelmezés szerint modern hitelesítési protokollokat használjanak. A modern hitelesítéshez nyújtott Office 365-támogatásról a [Frissített Office 365 modern hitelesítés](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)című lapban olvashat.

Az alábbi ábra a magas szintű forgalmat mutatja be. A bérlői korlátozások csak az Azure AD-be irányuló forgalomban igényelNek TLS-ellenőrzést, az Office 365 felhőszolgáltatásaira vonatkozóan nem. Ez a megkülönböztetés azért fontos, mert az Azure AD-hitelesítéshez szükséges forgalom általában sokkal alacsonyabb, mint a SaaS-alkalmazások, például az Exchange Online és a SharePoint Online forgalma.

![Bérlő korlátozza a forgalom áramlását - diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Bérlői korlátozások beállítása

A bérlői korlátozások kal kérhető két lépésből áll. Először győződjön meg arról, hogy az ügyfelek a megfelelő címekhez tudnak csatlakozni. Másodszor konfigurálja a proxy infrastruktúrát.

### <a name="urls-and-ip-addresses"></a>URL-címek és IP-címek

A bérlői korlátozások használatához az ügyfeleknek a következő Azure AD URL-címekhez kell csatlakozniuk a hitelesítéshez: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)és [login.windows.net](https://login.windows.net/). Ezenkívül az Office 365 eléréséhez az ügyfeleknek csatlakozniuk kell az [Office 365 URL-címeiben és IP-címtartományokban](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)meghatározott teljesen minősített tartománynevekhez (FQDNs), URL-címekhez és IP-címekhez. 

### <a name="proxy-configuration-and-requirements"></a>Proxy konfigurációja és követelményei

A következő konfiguráció szükséges a bérlői korlátozások engedélyezéséhez a proxy infrastruktúrán keresztül. Ez az útmutató általános, ezért a proxy szállítójának dokumentációjában kell tájékoznia a konkrét megvalósítási lépéseket.

#### <a name="prerequisites"></a>Előfeltételek

- A proxynak képesnek kell lennie a TLS lehallgatás, a HTTP-fejlécbeillesztés és a célhelyek szűrésére teljes tartományn/URL-ek használatával.

- Az ügyfeleknek meg kell bízniuk a Proxy által a TLS-kommunikációhoz benyújtott tanúsítványláncban. Ha például egy belső [nyilvános kulcsú infrastruktúrából (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) származó tanúsítványokat használ, a belső kiállító legfelső szintű legfelső szintű hitelesítésszolgáltató tanúsítványának megbízhatónak kell lennie.

- Ez a funkció az Office 365-előfizetések részét képezi, de ha bérlői korlátozásokat szeretne használni más SaaS-alkalmazásokhoz való hozzáférés szabályozására, akkor Az Azure AD Premium 1 licencek szükségesek.

#### <a name="configuration"></a>Konfiguráció

A login.microsoftonline.com, login.microsoft.com és login.windows.net minden bejövő kérelemhez két HTTP-fejlécet szúrjon be: *Korlátozza a bérlőkhöz* való hozzáférést és a *Hozzáférés-környezet korlátozása.*

A fejléceknek a következő elemeket kell tartalmazniuk:

- A *bérlők hozzáférésének korlátozása*esetén használja \<az engedélyezett\>bérlői lista értékét, amely a felhasználók számára hozzáférést engedélyezni kívánt bérlők vesszővel tagolt listája. A bérlővel regisztrált tartományok a listában szereplő bérlő azonosítására használhatók. Ha például a Contoso és a Fabrikam-bérlőkhöz is hozzáférést szeretne biztosítani, a név-érték pár a következőképpen néz ki: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- A *Hozzáférés korlátozása-környezet ben*használja egyetlen könyvtárazonosító értékét, amely deklarálja, hogy melyik bérlő állítja be a bérlői korlátozásokat. Ha például a Contoso-t szeretné deklarálni a bérlői korlátozási házirendet beállító bérlőként, a név/érték pár így néz ki: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> A címtárazonosítót az [Azure Active Directory portálon](https://aad.portal.azure.com/)találja. Jelentkezzen be rendszergazdaként, válassza az **Azure Active Directory**lehetőséget, majd a **Tulajdonságok**lehetőséget.

Annak megakadályozása érdekében, hogy a felhasználók ne szúrják be saját HTTP-fejlécüket nem jóváhagyott bérlőkkel, a proxynak le kell cserélnie a *Bérlői korlátozás fejlécet,* ha az már szerepel a bejövő kérelemben.

Az ügyfeleket kötelezni kell arra, hogy a proxyt használják a login.microsoftonline.com, login.microsoft.com és login.windows.net minden kérelemhez. Ha például pac fájlokat használnak arra, hogy az ügyfeleket a proxy használatára irányítsák, a végfelhasználók nem szerkeszthetik vagy tilthatják le a PAC-fájlokat.

## <a name="the-user-experience"></a>A felhasználói élmény

Ez a szakasz a végfelhasználók és a rendszergazdák élményét ismerteti.

### <a name="end-user-experience"></a>Végfelhasználói élmény

Egy példa felhasználó a Contoso-hálózaton, de próbál hozzáférni a Fabrikam példányát egy megosztott SaaS-alkalmazás, például az Outlook online. Ha a Fabrikam a Contoso-példány nem engedélyezett bérlője, a felhasználó egy hozzáférésmegtagadási üzenetet lát, amely azt mondja, hogy olyan erőforráshoz próbál hozzáférni, amely az informatikai részleg által jóvá nem hagyott szervezethez tartozik.

### <a name="admin-experience"></a>Rendszergazdai élmény

Míg a bérlői korlátozások konfigurálása a vállalati proxy-infrastruktúrán történik, a rendszergazdák közvetlenül hozzáférhetnek a bérlői korlátozási jelentésekhez az Azure Portalon. A jelentések megtekintése:

1. Jelentkezzen be az [Azure Active Directory portálra.](https://aad.portal.azure.com/) Megjelenik **az Azure Active Directory felügyeleti központ** irányítópultja.

2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. Megjelenik az Azure Active Directory áttekintő lapja.

3. Az **Egyéb képességek** fejlécben válassza **a Bérlői korlátozások lehetőséget.**

A korlátozott hozzáférésű környezet-bérlőként megadott bérlő rendszergazdája használhatja ezt a jelentést a bérlői korlátozások házirendje miatt letiltott bejelentkezések megtekintéséhez, beleértve a használt identitást és a célkönyvtár-azonosítót. Bejelentkezések szerepelnek, ha a bérlő a korlátozás vagy a felhasználó bérlői vagy erőforrás-bérlő a bejelentkezéshez.

> [!NOTE]
> A jelentés korlátozott információkat tartalmazhat, például a célkönyvtár-azonosítót, ha egy felhasználó, aki nem a korlátozott hozzáférésű környezetben bérlő bejelentkezik. Ebben az esetben a felhasználó által azonosítható adatok, például a név és a felhasználó egyszerű neve, a más bérlők felhasználói adatainak védelme érdekében maszkolt.

Az Azure Portalon található többi jelentéshez hasonlóan szűrők használatával is megadhatja a jelentés hatókörét. Szűrhet egy adott időintervallumra, felhasználóra, alkalmazásra, ügyfélre vagy állapotra. Ha az Oszlopok gombot **választja,** az alábbi mezők tetszőleges kombinációjával jelenítheti meg az adatokat:

- **Felhasználó**
- **Alkalmazás**
- **Állapot**
- **Dátum**
- **Dátum (UTC)** (ahol az UTC egyezményes világidő)
- **MFA hitelesítési módszer** (többtényezős hitelesítési módszer)
- **MFA hitelesítésének részletei** (többtényezős hitelesítés részletei)
- **MFA-eredmény**
- **IP-cím**
- **Ügyfél**
- **Felhasználónév**
- **Helyen**
- **Célbérlő azonosítója**

## <a name="office-365-support"></a>Az Office 365 támogatása

Az Office 365-alkalmazásoknak két feltételnek kell megfelelniük a bérlői korlátozások teljes körű támogatásához:

1. A használt ügyfél támogatja a modern hitelesítést.
2. A felhőszolgáltatás alapértelmezett hitelesítési protokolljaként a modern hitelesítés engedélyezve van.

A legújabb információkat, amelyekkel kapcsolatban az Office-ügyfelek jelenleg támogatják a modern hitelesítést, olvassa el [az Office 365 modern hitelesítésének frissítése](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) című modern hitelesítéscímű legfrissebb információt. Ez a lap hivatkozásokat tartalmaz a modern hitelesítés engedélyezésére vonatkozó utasításokra adott Exchange Online és Skype Vállalati online verziós bérlőkön. A SharePoint Online alapértelmezés szerint már engedélyezi a modern hitelesítést.

Az Office 365 böngészőalapú alkalmazásai (az Office Portal, a Yammer, a SharePoint-webhelyek, a Webes Outlook stb.) jelenleg támogatják a bérlői korlátozásokat. A vastag ügyfelek (Outlook, Skype Vállalati verzió, Word, Excel, PowerPoint stb.) csak modern hitelesítés használata esetén kényszeríthetik ki a bérlői korlátozásokat.  

A modern hitelesítést támogató Outlook és Skype Vállalati verziós ügyfelek továbbra is használhatják az örökölt protokollokat azon bérlőkkel szemben, ahol nincs engedélyezve a modern hitelesítés, így gyakorlatilag megkerülve a bérlői korlátozásokat. A bérlői korlátozások blokkolhatják az örökölt protokollokat használó alkalmazásokat, ha a hitelesítés során kapcsolatba lépnek login.microsoftonline.com, login.microsoft.com vagy login.windows.net.

A Windows Outlook esetében az ügyfelek korlátozásokat alkalmazhatnak, amelyek megakadályozzák, hogy a végfelhasználók nem jóváhagyott e-mail fiókokat adjanak a profiljukhoz. Lásd például a [Nem alapértelmezett Exchange-fiókok hozzáadása csoportházirend-beállítás](https://gpsearch.azurewebsites.net/default.aspx?ref=1) című témakört.

## <a name="testing"></a>Tesztelés

Ha szeretné kipróbálni a bérlői korlátozásokat, mielőtt az egész szervezetre vonatkozóan megvalósítanád, két lehetősége van: egy gazdagép alapú megközelítés egy olyan eszközzel, mint a Fiddler, vagy a proxybeállítások szakaszos bevezetésével.

### <a name="fiddler-for-a-host-based-approach"></a>Hegedűs a gazdagép-alapú megközelítésért

A Fiddler egy ingyenes webes hibakeresési proxy, amely a HTTP/HTTPS-forgalom rögzítésére és módosítására használható, beleértve a HTTP-fejlécek beszúrását is. Ha a Fiddler-t bérlői korlátozások tesztelésére szeretné beállítani, hajtsa végre a következő lépéseket:

1. [Töltse le és telepítse Fiddler](https://www.telerik.com/fiddler).

2. Állítsa be a Fiddler-t a HTTPS-forgalom visszafejtésére [a Fiddler súgódokumentációja](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)szerint.

3. A Fiddler konfigurálásával egyéni szabályok használatával szúrja be a bérlők höz való *hozzáférés korlátozása* és *a hozzáférés-környezet korlátozása* fejléceket:

   1. A Fiddler Web Debugger eszközben válassza a **Szabályok** menüt, és válassza **a Szabályok testreszabása parancsot...** a CustomRules fájl megnyitásához.

   2. Adja hozzá a következő sorokat a `OnBeforeRequest` függvény elejéhez. Cserélje \<le\> a bérlői tartományt a `contoso.onmicrosoft.com`bérlőhöz regisztrált tartományra (például ). Lecserélheti \<\> a címtárazonosítót a bérlő Azure AD GUID azonosítójára.

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

      Ha több bérlőt kell engedélyeznie, használjon vesszőt a bérlőnevek elkülönítéséhez. Példa:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Mentse és zárja be a CustomRules fájlt.

A Fiddler konfigurálása után rögzítheti a forgalmat, ha a **Fájl** menüben a **Forgalom rögzítése**parancsot választja.

### <a name="staged-rollout-of-proxy-settings"></a>A proxybeállítások szakaszos bevezetése

A proxyinfrastruktúra képességeitől függően előfordulhat, hogy a beállítások bevezetését a felhasználók számára is meg tudja adni. Íme néhány magas szintű lehetőség megfontolásra:

1. A PAC-fájlok segítségével irányítsa a tesztfelhasználókat egy tesztproxy-infrastruktúrára, míg a normál felhasználók továbbra is az éles proxy infrastruktúrát használják.
2. Egyes proxykiszolgálók csoportok használatával különböző konfigurációkat támogathatnak.

További részletekért tekintse meg a proxykiszolgáló dokumentációját.

## <a name="next-steps"></a>További lépések

- További információ [a frissített Office 365 modern hitelesítéséről](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Az [Office 365 URL-cím- és IP-címtartományának áttekintése](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
