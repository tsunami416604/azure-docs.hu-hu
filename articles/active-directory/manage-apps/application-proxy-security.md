---
title: Biztonsági szempontok az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Azure AD Application Proxy használatára vonatkozó biztonsági szempontokat ismerteti.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: f74dc441016a0064aca388ce4881eeae9e7bc6d0
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657858"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Távolról az Azure AD-alkalmazásproxy-alkalmazások eléréséhez szükséges biztonsági szempontok

Ez a cikk azt ismerteti, hogy az összetevők, amelyek azt biztonságban a felhasználók és az alkalmazások Azure Active Directory Application Proxy használatakor.

Az alábbi ábrán látható, hogy az Azure AD lehetővé teszi, hogy az a helyszíni alkalmazások biztonságos távoli hozzáférést.

 ![Biztonságos távoli hozzáférést az Azure AD-alkalmazásproxyn keresztül ábrája](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Biztonsági előnyeit

Az Azure AD-alkalmazásproxy a következő biztonsági előnyöket nyújtja:

### <a name="authenticated-access"></a>Hitelesített hozzáférés 

Ha az Azure Active Directory előhitelesítési használatát választja, csak a hitelesített kapcsolatokhoz a hálózat hozzáférhet.

Azure AD Application proxyt az Azure AD biztonsági jogkivonat-szolgáltatás (STS) összes hitelesítési támaszkodik.  Általi előhitelesítés rendkívül tárhelyigényes, letiltja a jelentős számú névtelen támadások, mivel csak hitelesített identitást is hozzáférni a háttéralkalmazás.

Ha csatlakoztatott legyen a előhitelesítési módszer, ezzel az értékelemmel nem kap. 

### <a name="conditional-access"></a>Feltételes hozzáférés

A hálózati kapcsolatot létesít alkalmaznia gazdagabb szabályzati vezérlőket.

A [feltételes hozzáférési](../conditional-access/overview.md), korlátozásokat definiálhat a forgalmat a háttér-alkalmazások hozzáférhetnek. Amelyek korlátozzák a bejelentkezések hely, a hitelesítés és felhasználói kockázatú profil erőssége alapján szabályzatokat hozhat létre.

Feltételes hozzáférés használatával újabb biztonsági réteget ad hozzá a felhasználói hitelesítések multi-factor Authentication hitelesítési házirendek konfigurálása. Ezenkívül az alkalmazások is továbbíthatók a Microsoft Cloud App Security keresztül az Azure AD feltételes hozzáférés használatával valós idejű figyelés és a vezérlők keresztül [hozzáférés](https://docs.microsoft.com/cloud-app-security/access-policy-aad) és [munkamenet](https://docs.microsoft.com/cloud-app-security/session-policy-aad) házirendek

### <a name="traffic-termination"></a>Forgalom megszüntetése

Az összes forgalom megszakad a felhőben.

Mivel az Azure AD-alkalmazásproxy egy fordított proxyt, a háttér-alkalmazások felé irányuló összes forgalma a szolgáltatás megszakad. A munkamenet csak a háttér-kiszolgáló, ami azt jelenti, hogy a háttér-kiszolgálók nem érhetők a közvetlen HTTP-forgalmat a is első helyre. Ez a konfiguráció azt jelenti, hogy jobban védve célzott támadásoktól.

### <a name="all-access-is-outbound"></a>Minden hozzáférés kimenő 

Nem kell megnyitni a bejövő kapcsolatok a vállalati hálózathoz.

Alkalmazásproxy-összekötők csak akkor használja az Azure AD-alkalmazásproxy szolgáltatás, ami azt jelenti, hogy hiba esetén nem kell tűzfalportokat a bejövő kapcsolatok nyissa meg a kimenő kapcsolatokat. Hagyományos proxyk szükséges a szegélyhálózaton (más néven *DMZ*, *demilitarizált zóna*, vagy *alapos szűrésen átesett alhálózati*) férhetnek hozzá a nem hitelesített kapcsolatokhoz és a hálózat szélén. Ebben a forgatókönyvben a tűzfal termékek webes forgalom elemzése és a környezetvédelem befektetéseket szükséges. Az alkalmazásproxy nem szükséges a szegélyhálózaton, mivel az összes kapcsolat kimenő, és egy biztonságos csatornán kerül a sor.

Összekötők kapcsolatos további információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>A felhőméretű elemzés és gépi tanulás 

A legmodernebb biztonsági védelmet kap.

Mivel az Azure Active Directory részét, kihasználhatják a Application Proxy [Azure AD Identity Protection](../active-directory-identityprotection.md), a Microsoft Security Response Center és a digitális bűnözés elleni Egységünkkel származó adatokkal. Együtt és amelyet a proaktív módon azonosíthatja a feltört fiókoktól nyújtanak védelmet a magas kockázatú bejelentkezések. Hogy figyelembe venniük annak meghatározására, hogy melyik bejelentkezési megkísérel magas kockázatú számos tényező. Ezek a tényezők közé tartozik a megjelölés fertőzött eszközök, a hálózatok névtelenítését és a szokatlan vagy nem valószínű, helyek.

Ezeket a jelentéseket és események számos már elérhető a biztonságiadat-és eseménykezelő (SIEM) rendszerekbe-integráció az API-n keresztül.

### <a name="remote-access-as-a-service"></a>A távelérés szolgáltatás

Nem kell aggódnia karbantartásával és a helyszíni kiszolgálók javítása.

Veszéllyel szoftver továbbra is nagyszámú támadások számlák. Az Azure AD-alkalmazásproxy egy internetes léptékű szolgáltatás, amely a Microsoft tulajdonában lévő,, így mindig a legújabb biztonsági javításokat és frissítéseket.

Az Azure AD-alkalmazásproxy által közzétett alkalmazások a biztonság növelése érdekében azt webes webbejáró robotokat indexelése és archiválása az alkalmazások letiltása. Minden alkalommal, amikor a webes webbejáró robot megpróbálja beolvasni a robot beállításait a közzétett alkalmazás Application Proxy válaszol a robots.txt fájlt, amely tartalmazza a `User-agent: * Disallow: /`.

### <a name="ddos-prevention"></a>A DDOS-megelőzés

Elosztott szolgáltatásmegtagadás-szolgáltatás (DDOS) támadások ellen védett alkalmazásproxyn keresztül közzétett alkalmazásokról.

Az alkalmazásproxy-szolgáltatás figyelése elérni az alkalmazások és a hálózati forgalom mennyisége. Ha az alkalmazások távoli hozzáférést igénylő eszközök száma hirtelen megugró kihasználtság, a Microsoft szabályozza a hálózathoz. 

A Microsoft az egyes alkalmazások és az előfizetés teljes forgalmat figyeli. Ha egy alkalmazás nagyobb, mint a normál kérés érkezik, majd kérelmeket, hogy az alkalmazáshoz való hozzáférés megtagadása egy rövid idő alatt. Ha magasabb, mint a normál kérés az egész előfizetésében, majd bármely, az alkalmazások elérésére kérelmek elutasítva. A megelőző intézkedés biztosítja, hogy az alkalmazáskiszolgálók a távoli hozzáférési kérelmeket, amelyet éppen túlterhelt, hogy a helyszíni felhasználók is tartsa az alkalmazások elérése. 

## <a name="under-the-hood"></a>Technikai részletek

Az Azure AD-alkalmazásproxy két részből áll:

* A felhő alapú szolgáltatás: Ez a szolgáltatás az Azure-ban fut, és, ahol a külső ügyfelek és a felhasználók kapcsolatok jönnek létre.
* [A helyszíni összekötő](application-proxy-connectors.md): Egy helyszíni összetevő, az összekötő figyeli, a belső alkalmazásokat az Azure AD-alkalmazásproxy szolgáltatás és a leírók kapcsolatokról érkező kérésekre. 

Létrejön az összekötő és az alkalmazásproxy szolgáltatás közötti folyamat során:

* Az összekötő először van beállítva.
* A connector lekéri az alkalmazásproxy-szolgáltatás konfigurációs adatait.
* Egy felhasználó a közzétett alkalmazáshoz fér hozzá.

>[!NOTE]
>Minden kommunikáció SSL-en keresztül történik, és mindig, az az alkalmazásproxy-összekötő származnak. A szolgáltatás csak akkor kimenő forgalomról beszélünk.

Az összekötő egy ügyfél-tanúsítványt használ az alkalmazásproxy-szolgáltatás szinte összes híváshoz a hitelesítéséhez. Az egyetlen kivétel ez alól lépése a kezdeti beállítás, ahol az ügyféltanúsítvány létrejött.

### <a name="installing-the-connector"></a>Az összekötő telepítése

Ha az összekötő először be van állítva, a következő folyamat-eseményekre kerül sor:

1. Az összekötő regisztrációját, hogy a szolgáltatás-összekötő a telepítés részeként történik. Adja meg az Azure AD rendszergazdai hitelesítő adatait a rendszer kéri. A hitelesítésszolgáltatótól származik ez a hitelesítési jogkivonat ezután megjelenik az Azure AD-alkalmazásproxy-szolgáltatás.
2. Az alkalmazásproxy-szolgáltatás kiértékeli a jogkivonatot. Azt ellenőrzi, hogy a felhasználó egy vállalati rendszergazda, a bérlőben. Ha a felhasználó nem rendszergazda, a folyamat megszakadt.
3. Az összekötő egy ügyfél tanúsítványkérelmet állít elő, és átadja azokat, a tokent, az alkalmazásproxy-szolgáltatás együtt. A szolgáltatás viszont a token ellenőrzi és aláírja az ügyfél-tanúsítvány kérése.
4. Az összekötőt használ az ügyféltanúsítvány jövőbeli kommunikál az alkalmazásproxy-szolgáltatás.
5. Az összekötő hajt végre egy kezdeti lekéréses, a rendszer-konfigurációs adatokat a szolgáltatásból az ügyféltanúsítványt használja, és már készen áll a kérelmek igénybe vehet.

### <a name="updating-the-configuration-settings"></a>Konfigurációs beállításainak frissítése

Minden alkalommal, amikor az alkalmazásproxy-szolgáltatás konfigurációs beállításait frissíti, a következő folyamat-eseményekre kerül sor:

1. Az összekötő az ügyféltanúsítvány használatával csatlakozik az alkalmazásproxy-szolgáltatás konfigurációs végpontjába.
2. Az ügyféltanúsítvány érvényesítését követően az alkalmazásproxy-szolgáltatás konfigurációs adatok és az összekötő (például az összekötőcsoportot, hogy az összekötő részének kell lennie) adja vissza.
3. Ha az aktuális tanúsítvány több mint 180 napos, az összekötő egy új tanúsítvány kérelmet, amely hatékonyan 180 naponta frissíti az ügyfél-tanúsítványt állít elő.

### <a name="accessing-published-applications"></a>A közzétett alkalmazások elérése

Amikor a felhasználó a közzétett alkalmazás hozzáfér, a a következő eseményekre kerül sor az alkalmazásproxy-szolgáltatás és az alkalmazásproxy-összekötő között:

1. [A szolgáltatás hitelesíti a felhasználót az alkalmazás a](#the-service-checks-the-configuration-settings-for-the-app)
2. [A szolgáltatás egy kérelem helyezi az összekötő-üzenetsorba](#The-service-places-a-request-in-the-connector-queue)
3. [Egy összekötő feldolgozza a kérést, az üzenetsorból](#the-connector-receives-the-request-from-the-queue)
4. [Az összekötő a válaszra vár](#the-connector-waits-for-a-response)
5. [A szolgáltatás a felhasználói adatok elemzésének lehetőségeit.](#the-service-streams-data-to-the-user)

Többet szeretne megtudni, mi történik az egyes lépéseket, tartsa meg az olvasó.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. A szolgáltatás hitelesíti a felhasználót az alkalmazás a

Ha konfigurálta a csatlakoztatott az előhitelesítési módszerként használhatják az alkalmazást, a rendszer kihagyja a jelen szakaszban ismertetett lépéseket.

Ha konfigurálta az alkalmazást, hogy az Azure ad-vel preauthenticate, az Azure AD-STS hitelesítéséhez a rendszer átirányítja a felhasználókat, és elvégezze a következő lépéseket:

1. Az alkalmazásproxy ellenőrzi, hogy bármely feltételes hozzáférési szabályzat követelményei szerint az adott alkalmazáshoz. Ez a lépés biztosítja, hogy az a felhasználó az alkalmazáshoz hozzárendelt-e. Ha a kétlépéses ellenőrzés szükség, a hitelesítési folyamat bekéri a felhasználótól egy második hitelesítési módszerrel.

2. Összes ellenőrzés rendelkezik követően az Azure AD STS az alkalmazás egy aláírt jogkivonatot, és átirányítja a felhasználót vissza az alkalmazásproxy-szolgáltatás.

3. Alkalmazásproxy ellenőrzi, hogy az alkalmazás javítása a jogkivonatot adta-e. Azt ellenőrzi más is, például annak biztosítása, hogy a jogkivonat aláírása az Azure AD, és hogy továbbra is a érvényes időszakon belül.

4. Application Proxy beállítja annak jelzésére, hogy a hitelesítés, az alkalmazás-titkosított hitelesítési cookie történt. A cookie-t tartalmaz egy lejárati időbélyeg, amely az Azure AD-ből és más adatok, például a felhasználó nevét, a hitelesítés alapján alapul. A cookie-k és csak az alkalmazásproxy-szolgáltatás által ismert titkos kulccsal van titkosítva.

5. Alkalmazásproxy átirányítja a felhasználót az eredetileg kérelmezett URL-címet.

Ha az előhitelesítés lépéseket bármely részét nem sikerül, a rendszer megtagadja a felhasználó kérelmet, és a felhasználó számára megjelenik egy üzenet jelzi, a probléma forrása.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. A szolgáltatás egy kérelem helyezi az összekötő-üzenetsorba

Összekötők ne zárja be egy kimenő kapcsolatot, az alkalmazásproxy-szolgáltatás. Amikor kérelem érkezik, a szolgáltatás az összekötő csomópontmetrikák az egyik, nyissa meg a kérelem várólistára helyezi.

A kérelem olyan adatokat tartalmaz, az alkalmazásból, például a kérelem fejlécében adatait a titkosított cookie-t, a felhasználó a kérelmet, és a kérés azonosítóját. A titkosított cookie-k adatait a rendszer elküldi a kérelemmel, de nem áll a hitelesítési cookie magát.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. Az összekötő feldolgozza a kérést az üzenetsorból. 

A kérés alapján az alkalmazásproxy hajtja végre az alábbi műveletek egyikét:

* Ha a kérés egy egyszerű feladat (például nem szerepel megjeleníthető adat a szervezet, mert az egy RESTful belül *LEKÉRÉSE* kérelem), az összekötő a célként megadott belső erőforrás kapcsolatot hoz létre, és választ vár.

* Ha a kérelem törzsében a hozzá kapcsolódó adatokat rendelkezik-e (például egy RESTful *POST* művelet), az összekötő egy kimenő kapcsolatot létesít az alkalmazásproxy-példányhoz az ügyféltanúsítvány használatával. A kapcsolat lekérheti az adatokat, és kapcsolatot létesíteni a belső erőforrás teszi. Az összekötő a kérést kap, miután az alkalmazásproxy-szolgáltatás megkezdi a tartalom a felhasználó fogadja, és továbbítja az adatokat az összekötő. Az összekötő viszont továbbítja az adatokat a belső erőforrás.

#### <a name="4-the-connector-waits-for-a-response"></a>4. Az összekötő a válaszra vár.

A kérés és a háttéralkalmazás minden tartalom továbbításának befejezése után az összekötő a válaszra vár.

Miután választ kap, az összekötő teszi az alkalmazásproxy-szolgáltatás a fejléc részleteit adja vissza, és elkezdeni a streamelést a visszaadott adatok egy kimenő kapcsolatot.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. A szolgáltatás a felhasználói adatok elemzésének lehetőségeit. 

Az alkalmazás egyes feldolgozási ide fordulhat elő. Fejlécek lefordítani Proxy vagy URL-címek konfigurálva az alkalmazásban, ha a feldolgozás ennél a lépésnél szükség szerint történik.


## <a name="next-steps"></a>További lépések

[Hálózati topológia szempontjai az Azure AD-alkalmazásproxy használatával](application-proxy-network-topology.md)

[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
