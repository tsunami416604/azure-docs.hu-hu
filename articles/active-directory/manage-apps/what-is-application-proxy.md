---
title: Helyszíni alkalmazások közzététele az Azure AD alkalmazásproxyval
description: Ismerje meg, hogy miért használja az Alkalmazásproxy t a helyszíni webalkalmazások távoli felhasználók számára történő közzétételéhez. Ismerje meg az alkalmazásproxy architektúráját, az összekötőket, a hitelesítési módszereket és a biztonsági előnyöket.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7047dfd0f02ffe95dcacfdf4ddc014047a338513
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79481194"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Az Azure AD alkalmazásproxy használata a helyszíni alkalmazások távoli felhasználók számára történő közzétételéhez

Az Azure Active Directory (Azure AD) számos lehetőséget kínál a felhasználók, alkalmazások és adatok védelmére a felhőben és a helyszínen. Az Azure AD alkalmazásproxy-funkciót különösen olyan informatikai szakemberek valósíthatják meg, akik külsőleg szeretnék közzétenni a helyszíni webalkalmazásokat. Azok a távoli felhasználók, akiknek belső alkalmazásokhoz kell hozzáférniük, biztonságos módon érhetik el őket.

A belső alkalmazások hálózaton kívülről történő biztonságos elérése még kritikusabbá válik a modern munkahelyen. Az olyan forgatókönyvek, mint a BYOD (Bring Your Own Device) és a mobileszközök esetében az informatikai szakemberek két cél elérésére hívják fel a kihívást:

* Képessé tenni a végfelhasználókat arra, hogy bárhol és bármikor termelékenyek legyenek
* A vállalati eszközök védelme mindenkor

Sok szervezet úgy gondolja, hogy ők irányítanak és védve vannak, ha az erőforrások a vállalati hálózatok határain belül vannak. De a mai digitális munkahelyen ez a határ kibővült a felügyelt mobileszközökkel, erőforrásokkal és szolgáltatásokkal a felhőben. Most már kezelnie kell a felhasználók identitásának és az eszközökön és alkalmazásokon tárolt adatoknak a bonyolultságát.

Lehet, hogy már használja az Azure AD-t a felhőben lévő felhasználók kezelésére, akiknek szükségük van az Office 365 és más SaaS-alkalmazások, valamint a helyszíni üzemeltetett webalkalmazások eléréséhez. Ha már rendelkezik Az Azure AD-vel, egyetlen vezérlősíkként használhatja ki, hogy zökkenőmentes és biztonságos hozzáférést biztosítson a helyszíni alkalmazásokhoz. Vagy talán még mindig a felhőre való költözést fontolgatja. Ha igen, akkor megkezdheti az utazást a felhőbe alkalmazásproxy megvalósításával, és az első lépés egy erős identitásalap létrehozása felé.

Bár nem átfogó, az alábbi lista bemutatja néhány dolgot, amit engedélyezhet az Alkalmazásproxy hibrid együttélési forgatókönyvben történő megvalósításával:

* Helyszíni webalkalmazások közzététele külső legkülönfélébben, Egyszerűsített módon, DMZ nélkül
* Egyszeri bejelentkezés (SSO) támogatása a felhőben és a helyszínen lévő eszközök, erőforrások és alkalmazások között
* Többtényezős hitelesítés támogatása a felhőben és a helyszíni alkalmazásokban
* A Microsoft Cloud biztonságával gyorsan kihasználhatja a felhőfunkciókat
* Felhasználói fiók kezelésének központosítása
* Az identitás és a biztonság ellenőrzésének központosítása
* Felhasználói hozzáférés automatikus hozzáadása vagy eltávolítása az alkalmazásokhoz csoporttagság alapján

Ez a cikk bemutatja, hogy az Azure AD és az Alkalmazásproxy hogyan biztosít a távoli felhasználók számára egyetlen bejelentkezési (SSO) élményt. A felhasználók biztonságosan csatlakozhatnak a helyszíni alkalmazásokhoz VPN- vagy kétcímes kiszolgálók és tűzfalszabályok nélkül. Ez a cikk segít megérteni, hogy az Application Proxy hogyan hozza a felhő képességeit és biztonsági előnyeit a helyszíni webalkalmazásokba. Azt is leírja, az architektúra és a topológiák, amelyek lehetségesek.

## <a name="remote-access-in-the-past"></a>Távelérés a múltban

Korábban a belső erőforrások támadókkal szembeni védelmére szolgáló vezérlősík a Távoli felhasználók hozzáférésének megkönnyítése mellett mind a DMZ-ben vagy a peremhálózaton volt. A külső ügyfelek által a vállalati erőforrások eléréséhez használt DMZ-ben üzembe helyezett VPN- és fordított proxymegoldások azonban nem felelnek meg a felhővilágnak. Jellemzően a következő hátrányokkal szenvednek:

* Hardverköltségek
* A biztonság fenntartása (javítás, portfigyelés stb.)
* Felhasználók hitelesítése a peremhálózaton
* Felhasználók hitelesítése a peremhálózat webkiszolgálóira
* VPN-hozzáférés karbantartása a távoli felhasználók számára a VPN-ügyfélszoftver terjesztésével és konfigurálásával. Emellett a tartományhoz csatlakozó kiszolgálók karbantartása a DMZ-ben, amely ki van téve a külső támadásoknak.

A mai felhőalapú világában az Azure AD a legalkalmasabb arra, hogy szabályozhatja, hogy ki és mi jut be a hálózatba. Az Azure AD alkalmazásproxy modern hitelesítéssel és felhőalapú technológiákkal, például SaaS-alkalmazásokkal és identitásszolgáltatókkal integrálható. Ez az integráció lehetővé teszi a felhasználók számára, hogy bárhonnan hozzáférjenek az alkalmazásokhoz. Nem csak az App Proxy alkalmasabb a mai digitális munkahely, ez biztonságosabb, mint a VPN és fordított proxy megoldások és könnyebben megvalósítható. A távoli felhasználók ugyanúgy érhetik el a helyszíni alkalmazásokat, mint az O365-öt és az Azure AD-vel integrált más SaaS-alkalmazásokat. Nem szükséges módosítani vagy frissíteni az alkalmazásokat ahhoz, hogy használhatók legyenek az alkalmazásproxyval. Továbbá az alkalmazásproxy nem követeli meg a bejövő kapcsolatok megnyitását a tűzfalon keresztül. Az App Proxy, egyszerűen állítsa be, és felejtsd el.

## <a name="the-future-of-remote-access"></a>A távelérés jövője

Napjaink digitális munkahelyén a felhasználók bárhol dolgozhatnak több eszközzel és alkalmazással. Az egyetlen állandó a felhasználói identitás. Ezért az első lépés a biztonságos hálózat ma az [Azure AD identitáskezelési](https://docs.microsoft.com/azure/security/security-identity-management-overview) képességek használata, mint a biztonsági vezérlő síkon. A vezérlősíkként identitást használó modell általában a következő összetevőkből áll:

* Identitásszolgáltató a felhasználók és a felhasználókkal kapcsolatos információk nyomon követésére.
* Eszközkönyvtár a vállalati erőforrásokhoz hozzáférő eszközök listájának karbantartásához. Ez a könyvtár tartalmazza a megfelelő eszközadatokat (például az eszköz típusát, az integritást stb.).
* Házirend-értékelési szolgáltatás annak megállapítására, hogy a felhasználó és az eszköz megfelel-e a biztonsági rendszergazdák által meghatározott házirendnek.
* A szervezeti erőforrásokhoz való hozzáférés megadásának vagy megtagadásának lehetősége.

Az Alkalmazásproxy val az Azure AD nyomon követi azokat a felhasználókat, akiknek a helyszínen és a felhőben közzétett webalkalmazások eléréséhez kell hozzáférni. Központi felügyeleti pontot biztosít ezekhez az alkalmazásokhoz. Bár nem szükséges, ajánlott az Azure AD feltételes hozzáférés engedélyezése is. A felhasználók hitelesítési és hozzáférési feltételeinek meghatározásával biztosíthatja továbbá, hogy a megfelelő személyek hozzáférjenek az alkalmazásokhoz.

**Megjegyzés:** Fontos megérteni, hogy az Azure AD alkalmazásproxy vpn- vagy fordított proxycsereként szolgál a belső erőforrásokhoz hozzáféréssel rendelkező barangolásos (vagy távoli) felhasználók számára. Nem a vállalati hálózat belső felhasználóinak készült. Az alkalmazásproxyt szükségtelenül használó belső felhasználók váratlan és nemkívánatos teljesítményproblémákat okozhatnak.

![Az Azure Active Directory és az összes alkalmazás](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Az alkalmazásproxy működésének áttekintése

Az alkalmazásproxy egy Azure AD-szolgáltatás, amelyet az Azure Portalon konfigurál. Lehetővé teszi egy külső nyilvános HTTP/HTTPS URL-végpont közzétételét az Azure-felhőben, amely a szervezet belső alkalmazáskiszolgálójának URL-címéhez csatlakozik. Ezek a helyszíni webalkalmazások integrálhatók az Azure AD-vel az egyszeri bejelentkezés támogatásához. A végfelhasználók ezután ugyanúgy érhetik el a helyszíni webalkalmazásokat, mint az Office 365-öt és más SaaS-alkalmazásokat.

A szolgáltatás összetevői közé tartozik az alkalmazásproxy-szolgáltatás, amely a felhőben fut, az alkalmazásproxy-összekötő, amely egy könnyű ügynök, amely egy helyszíni kiszolgálón fut, és az Azure AD, amely az identitásszolgáltató. Mindhárom összetevő együttműködik, hogy a felhasználó egyetlen bejelentkezési élményt nyújtson a helyszíni webalkalmazások eléréséhez.

A bejelentkezés után a külső felhasználók egy ismerős URL-cím vagy a [MyApps hozzáférési panel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) használatával érhetik el a helyszíni webalkalmazásokat asztali vagy iOS/MAC-eszközeikről. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztalhoz, a SharePoint-webhelyekhez, a Tableau-hoz, a Qlik-hoz, a Webes Outlookhoz és az üzletági (LOB) alkalmazásokhoz.

![Azure AD alkalmazásproxy-architektúra](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Hitelesítés

Az alkalmazások egyszeri bejelentkezéshez való konfigurálásának számos módja van, és a kiválasztott módszer az alkalmazás által használt hitelesítéstől függ. Az alkalmazásproxy a következő típusú alkalmazásokat támogatja:

* Webalkalmazások
* Webes API-k, amelyeket különböző eszközökön szeretne elérhetővé tenni a gazdag alkalmazások számára
* Távoli asztali átjáró mögött üzemeltetett alkalmazások
* Az Active Directory hitelesítési könyvtárával (ADAL) integrált bővített ügyfélalkalmazások

Az alkalmazásproxy olyan alkalmazásokkal működik, amelyek a következő natív hitelesítési protokollt használják:

* **[Integrált Windows-hitelesítés (IWA).](application-proxy-configure-single-sign-on-with-kcd.md)** Az IWA esetében az alkalmazásproxy-összekötők Kerberos korlátozott delegálást (KCD) használnak a felhasználók Kerberos-alkalmazásba való hitelesítéséhez.

Az alkalmazásproxy a következő hitelesítési protokollokat is támogatja külső integrációval vagy adott konfigurációs forgatókönyvekben:

* [**Fejlécalapú hitelesítés**](application-proxy-configure-single-sign-on-with-ping-access.md). Ez a bejelentkezési módszer egy pingaccess nevű külső hitelesítési szolgáltatást használ, és akkor használatos, amikor az alkalmazás fejléceket használ a hitelesítéshez. Ebben az esetben a hitelesítést a PingAccess kezeli.
* [**Űrlap- vagy jelszóalapú hitelesítés.**](application-proxy-configure-single-sign-on-password-vaulting.md) Ezzel a hitelesítési módszerrel a felhasználók felhasználónévvel és jelszóval jelentkeznek be az alkalmazásba, amikor először férnek hozzá. Az első bejelentkezés után az Azure AD biztosítja a felhasználónevet és a jelszót az alkalmazáshoz. Ebben a forgatókönyvben a hitelesítést az Azure AD kezeli.
* [**SAML-hitelesítés**](application-proxy-configure-single-sign-on-on-premises-apps.md). Saml-alapú egyszeri bejelentkezés saml 2.0 vagy WS-Federation protokollokat használó alkalmazások esetében támogatott. Saml egyszeri bejelentkezéssel az Azure AD hitelesíti magát az alkalmazás a felhasználó Azure AD-fiókjával.

A támogatott módszerekről az [Egyszeri bejelentkezési módszer kiválasztása](what-is-single-sign-on.md#choosing-a-single-sign-on-method)című témakörben talál további információt.

### <a name="security-benefits"></a>Biztonsági előnyök

Az Application Proxy és az Azure AD által kínált távelérési megoldás számos biztonsági előnyt támogat az ügyfelek számára, többek között a következőket:

* **Hitelesített hozzáférés**. Az alkalmazásproxy a legalkalmasabb az [előzetes hitelesítéssel](application-proxy-security.md#authenticated-access) rendelkező alkalmazások közzétételére annak érdekében, hogy csak a hitelesített kapcsolatok érjék el a hálózatot. Az előhitelesítéssel közzétett alkalmazások esetében egyetlen forgalom sem haladhat át az alkalmazásproxy-szolgáltatáson keresztül a helyszíni környezetbe, érvényes jogkivonat nélkül. Az előhitelesítés természeténél fogva jelentős számú célzott támadást blokkol, mivel csak hitelesített identitások férhetnek hozzá a háttéralkalmazáshoz.
* **Feltételes hozzáférés**. A hálózathoz való csatlakozások létrehozása előtt gazdagabb házirend-vezérlők is alkalmazhatók. Feltételes hozzáférés, megadhatja a forgalom, amely lehetővé teszi, hogy elérje a háttéralkalmazás. Olyan házirendeket hozhat létre, amelyek a hely, a hitelesítés erőssége és a felhasználói kockázati profil alapján korlátozzák a bejelentkezéseket. A feltételes hozzáférés fejlődésével további vezérlőkkel egészül ki, hogy további biztonságot nyújtson, például a Microsoft Cloud App Security (MCAS) alkalmazásokkal való integrációt. Az MCAS-integráció lehetővé teszi egy helyszíni alkalmazás [valós idejű figyelésre](application-proxy-integrate-with-microsoft-cloud-application-security.md) való konfigurálását a feltételes hozzáférés beállításával a munkamenetek valós idejű figyeléséhez és vezérléséhez feltételes hozzáférési házirendek alapján.
* **Forgalom megszűnése**. A háttéralkalmazásminden forgalma leáll az alkalmazásproxy-szolgáltatás a felhőben, míg a munkamenet újra létre jön a háttérkiszolgálóval. Ez a kapcsolati stratégia azt jelenti, hogy a háttérkiszolgálók nincsenek kitéve a közvetlen HTTP-forgalom. Ezek jobban védett célzott DoS (denial-of-service) támadások, mert a tűzfal nincs támadás alatt.
* **Minden hozzáférés kimenő**. Az alkalmazásproxy-összekötők csak kimenő kapcsolatokat használnak az alkalmazásproxy-szolgáltatáshoz a felhőben a 80-as és 443-as portokon. Bejövő kapcsolatok nélkül nincs szükség tűzfalportok megnyitására a dmz bejövő kapcsolataihoz vagy összetevőihez. Minden kapcsolat kimenő és biztonságos csatornán keresztül.
* **Security Analytics és Machine Learning (ML) alapú intelligencia**. Mivel az Azure Active Directory része, az alkalmazásproxy kihasználhatja [az Azure AD Identity Protection (prémium](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) [szintű P2 licencelést](https://azure.microsoft.com/pricing/details/active-directory/)igényel). Az Azure AD Identity Protection a gépi tanulási biztonsági intelligenciát a Microsoft [digitális bűnügyekért felelős egységéből](https://news.microsoft.com/stories/cybercrime/index.html) és a [Microsoft Security Response Centerből](https://www.microsoft.com/msrc) származó adatcsatornákkal kombinálja a feltört fiókok proaktív azonosításához. Az Identity Protection valós idejű védelmet nyújt a magas kockázatú bejelentkezések elleni védelemmel szemben. Figyelembe veszi az olyan tényezőket, mint a fertőzött eszközökről, az anonimizáló hálózatokból való hozzáférés, vagy az atipikus és valószínűtlen helyekről, hogy növelje a munkamenet kockázati profilját. Ez a kockázati profil valós idejű védelemre szolgál. Ezek közül a jelentések és események közül sok már elérhető egy API-n keresztül a SIEM-rendszerekkel való integrációhoz.

* **Távelérés szolgáltatásként.** A távoli hozzáférés engedélyezéséhez nem kell aggódnia a helyszíni kiszolgálók karbantartása és javítása miatt. Az Alkalmazásproxy egy internetes méretű szolgáltatás, amely a Microsoft tulajdonában van, így mindig megkapja a legújabb biztonsági javításokat és frissítéseket. A nem javított szoftverek még mindig nagyszámú támadást jelentenek. Szerint a Department of Homeland Security, több mint [85 százaléka célzott támadások megelőzhető](https://www.us-cert.gov/ncas/alerts/TA15-119A). Ezzel a szolgáltatásmodellel már nem kell viselnie a peremhálózati kiszolgálók kezelésének nehéz terhét, és szükség szerint javítania kell őket.

* **Intune-integráció**. Az Intune-nal a vállalati forgalom a személyes forgalomtól elkülönítve kerül továbbításra. Az alkalmazásproxy biztosítja a vállalati forgalom hitelesítését. [Az alkalmazásproxy és az Intune felügyelt böngészőképessége](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) együttesen is használható, hogy a távoli felhasználók biztonságosan hozzáférhessenek az iOS és Android rendszerű eszközök belső webhelyeihez.

### <a name="roadmap-to-the-cloud"></a>Ütemterv a felhőhöz

Az alkalmazásproxy megvalósításának másik fő előnye az Azure AD kiterjesztése a helyszíni környezetre. Valójában az Alkalmazásproxy megvalósítása kulcsfontosságú lépés a szervezet és az alkalmazások felhőbe való áthelyezésében. A felhőbe való áthelyezéssel és a helyszíni hitelesítéstől távol csökkentheti a helyszíni lábnyomát, és az Azure AD identitáskezelési képességeit használhatja vezérlősíkként. A meglévő alkalmazások minimális vagy egyáltalán nem frissíti, hozzáférhet a felhőképességekhez, például az egyszeri bejelentkezéshez, a többtényezős hitelesítéshez és a központi felügyelethez. A szükséges összetevők telepítése az App Proxy szolgáltatásba egy egyszerű folyamat a távelérési keretrendszer létrehozásához. A felhőbe való áthelyezéssel pedig hozzáférhet az Azure AD legújabb funkcióihoz, frissítéseihez és funkcióihoz, például a magas rendelkezésre álláshoz és a vészhelyreállításhoz.

Ha többet szeretne megtudni az alkalmazások Azure AD-be való áttelepítéséről, olvassa el az [Alkalmazások áttelepítése az Azure Active Directoryba](https://aka.ms/migrateapps/whitepaper) című tanulmányt.

## <a name="architecture"></a>Architektúra

Az alábbi ábra általában bemutatja, hogy az Azure AD hitelesítési szolgáltatások és az alkalmazásproxy hogyan működnek együtt, hogy egyszeri bejelentkezést biztosítsanak a helyszíni alkalmazásoknak a végfelhasználók számára.

![Azure AD alkalmazásproxy hitelesítési folyamata](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Miután a felhasználó egy végponton keresztül hozzáfért az alkalmazáshoz, a felhasználó átirányítja az Azure AD bejelentkezési lapra. Ha feltételes hozzáférési házirendeket konfigurált, a rendszer jelenleg ellenőrzi a feltételeket, hogy megbizonyosodjon arról, hogy megfelel a szervezet biztonsági követelményeinek.
2. Sikeres bejelentkezés után az Azure AD egy jogkivonatot küld a felhasználó ügyféleszközére.
3. Az ügyfél elküldi a jogkivonatot az alkalmazásproxy szolgáltatásnak, amely lekéri az egyszerű felhasználónevet (UPN) és a rendszerbiztonsági rendszer egyszerű felhasználónevét (SPN) a jogkivonatból.
4. Az alkalmazásproxy továbbítja a kérelmet, amelyet az [alkalmazásproxy-összekötő](application-proxy-connectors.md)vesz fel.
5. Az összekötő minden további hitelesítést hajt végre a felhasználó nevében ( nem kötelező a*hitelesítési módszertől függően),* kéri az alkalmazáskiszolgáló belső végpontját, és elküldi a kérelmet a helyszíni alkalmazásnak.
6. Az alkalmazáskiszolgáló válasza az összekötőn keresztül érkezik az alkalmazásproxy-szolgáltatásba.
7. A válasz az alkalmazásproxy szolgáltatásból érkezik a felhasználóhoz.

|**Összetevő**|**Leírás**|
|:-|:-|
|Végpont|A végpont egy URL vagy egy [végfelhasználói portál.](end-user-experiences.md) A felhasználók a hálózaton kívül is elérhetik az alkalmazásokat egy külső URL-cím elérésével. A hálózat felhasználói URL-címen vagy végfelhasználói portálon keresztül érhetik el az alkalmazást. Amikor a felhasználók ezek a végpontok egyikét, az Azure AD-ben hitelesítik magukat, és majd az összekötőn keresztül a helyszíni alkalmazás.|
|Azure AD|Az Azure AD a hitelesítést a felhőben tárolt bérlői címtár használatával hajtja végre.|
|Alkalmazásproxy szolgáltatás|Ez az alkalmazásproxy-szolgáltatás az Azure AD részeként fut a felhőben. Továbbítja a bejelentkezési jogkivonatot a felhasználótól az alkalmazásproxy-összekötőnek. Az alkalmazásproxy továbbítja a kéréselérhető fejléceit, és a protokoll nak függően állítja be a fejléceket az ügyfél IP-címére. Ha a proxyhoz érkező kérelem már rendelkezik ezzel a fejléccel, az ügyfél IP-címe hozzáadódik a fejléc értékét tartalmazó, vesszővel tagolt lista végéhez.|
|Alkalmazásproxy-összekötő|Az összekötő egy könnyű ügynök, amely a hálózaton belüli Windows Server rendszeren fut. Az összekötő kezeli a felhőbeli alkalmazásproxy-szolgáltatás és a helyszíni alkalmazás közötti kommunikációt. Az összekötő csak kimenő kapcsolatokat használ, így nem kell megnyitnia a bejövő portokat, vagy semmit sem kell helyeznie a DMZ-ben. Az összekötők állapot nélküliek, és szükség esetén adatokat vonnak le a felhőből. Az összekötőkről, például a terheléselosztásról és a hitelesítésről az [Azure AD alkalmazásproxy-összekötők ismertetése című](application-proxy-connectors.md)témakörben talál további információt.|
|Active Directory (AD)|Az Active Directory a helyszíni futtatása a tartományi fiókok hitelesítésének végrehajtásához fut. Egyszeri bejelentkezés konfigurálásakor az összekötő kommunikál az AD-vel a szükséges további hitelesítés végrehajtásához.|
|Helyszíni alkalmazás|Végül a felhasználó hozzáférhet egy helyszíni alkalmazáshoz.|

Az Azure AD alkalmazásproxy a felhőalapú alkalmazásproxy-szolgáltatásból és egy helyszíni összekötőből áll. Az összekötő figyeli az alkalmazásproxy-szolgáltatás kéréseit, és kezeli a belső alkalmazásokkal létesített kapcsolatokat. Fontos megjegyezni, hogy minden kommunikáció TLS-en keresztül történik, és mindig az alkalmazásproxy-szolgáltatás összekötőjéről származik. Ez azt, hogy a kommunikáció csak kimenő. Az összekötő egy ügyféltanúsítványt használ az alkalmazásproxy-szolgáltatás hitelesítéséhez az összes híváshoz. A kapcsolatbiztonság alóli egyetlen kivétel az ügyféltanúsítvány létrehozásának kezdeti beállítási lépése. További részletekért lásd [az alkalmazásproxyt a motorháztető alatt.](application-proxy-security.md#under-the-hood)

### <a name="application-proxy-connectors"></a>Alkalmazásproxy-összekötők

[Az alkalmazásproxy-összekötők](application-proxy-connectors.md) a helyszínen telepített könnyű ügynökök, amelyek megkönnyítik a felhőbeli alkalmazásproxy-szolgáltatáshoz való kimenő kapcsolatot. Az összekötőket olyan Windows Server rendszerre kell telepíteni, amely hozzáfér a háttéralkalmazáshoz. A felhasználók csatlakoznak az App Proxy felhőszolgáltatáshoz, amely az összekötőkön keresztül irányítja a forgalmat az alkalmazásokhoz az alábbi ábrán látható módon.

![Azure AD alkalmazásproxy hálózati kapcsolatok](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Az összekötő és az alkalmazásproxy szolgáltatás közötti beállítás és regisztráció a következőképpen történik:

1. A rendszergazda megnyitja a 80-as és 443-as portokat a kimenő forgalom számára, és lehetővé teszi a hozzáférést az összekötő, az alkalmazásproxy-szolgáltatás és az Azure AD által szükséges több URL-címhez.
2. A rendszergazda bejelentkezik az Azure Portalon, és futtat egy végrehajtható egy helyszíni Windows-kiszolgálón az összekötő telepítéséhez.
3. Az összekötő elindul az app proxy szolgáltatás "figyelése".
4. A rendszergazda hozzáadja a helyszíni alkalmazást az Azure AD-hez, és konfigurálja a beállításokat, például az URL-címeket, amelyeket a felhasználóknak csatlakozniuk kell az alkalmazásaikhoz.

További információ: [Az Azure AD alkalmazásproxy telepítésének megtervezése.](application-proxy-deployment-plan.md)

Javasoljuk, hogy mindig több összekötőt telepítsen a redundancia és a méretezés érdekében. Az összekötők a szolgáltatással együtt gondoskodnak az összes magas rendelkezésre állási feladatról, és dinamikusan hozzáadhatók vagy eltávolíthatók. Minden alkalommal, amikor egy új kérelem érkezik, hogy a rendelkezésre álló összekötők egyikéhez. Amikor egy összekötő fut, aktív marad, ahogy csatlakozik a szolgáltatáshoz. Ha egy összekötő átmenetileg nem érhető el, nem válaszol erre a forgalomra. A nem használt összekötők inaktívként vannak megjelölve, és 10 nap inaktivitás után törlődnek.

Az összekötők leis kmegkérdezik a kiszolgálót, hogy megtudják, van-e újabb verzió az összekötőnek. Bár manuális frissítést is elvégezhet, az összekötők automatikusan frissülnek, amíg az Application Proxy Connector Updater szolgáltatás fut. A több összekötővel rendelkező bérlők esetében az automatikus frissítések minden csoportban egyszerre egy összekötőt céloznak meg, hogy megakadályozzák az állásidőt a környezetben.

**Megjegyzés:** Az RSS-hírcsatornára való feliratkozással figyelheti, hogy az Alkalmazásproxy [verzióelőzményeinek lapja](application-proxy-release-version-history.md) értesítést kapjon a frissítések kiadásáról.

Minden alkalmazásproxy-összekötő egy [összekötőcsoporthoz](application-proxy-connector-groups.md)van rendelve. Az azonos összekötők csoportban lévő összekötők egyetlen egységként működnek a magas rendelkezésre állás és a terheléselosztás érdekében. Új csoportokat hozhat létre, összekötőket rendelhet hozzájuk az Azure Portalon, majd hozzárendelhet bizonyos összekötőket adott alkalmazások kiszolgálásához. Javasoljuk, hogy a magas rendelkezésre állás érdekében minden egyes összekötőcsoportban legalább két összekötővel rendelkezik.

Az összekötőcsoportok akkor hasznosak, ha a következő eseteket kell támogatnia:

* Földrajzi alkalmazásközzététel
* Alkalmazás szegmentálása/elkülönítése
* A felhőben vagy a helyszínen futó webalkalmazások közzététele

Az összekötők telepítésének helyéről és a hálózat optimalizálásáról az [Azure Active Directory alkalmazásproxy használatakor a Hálózati topológia szempontjai című témakörben](application-proxy-network-topology.md)talál további információt.

## <a name="other-use-cases"></a>Egyéb használati esetek

A mai napig arra összpontosítottunk, hogy az Application Proxy használatával külsőleg közzétegyék a helyszíni alkalmazásokat, miközben lehetővé tesszük az egyszeri bejelentkezést az összes felhőbeli és helyszíni alkalmazásba. Vannak azonban más használati esetek app proxy, hogy érdemes megemlíteni. Ezek például az alábbi jelentések lehetnek:

* **Rest API-k biztonságos közzététele.** Ha a helyszíni vagy a felhőben lévő virtuális gépeken üzemeltetett üzleti logikával vagy API-kkal rendelkezik, az Alkalmazásproxy nyilvános végpontot biztosít az API-hozzáféréshez. Az API-végpont-hozzáférés lehetővé teszi a hitelesítés és az engedélyezés vezérlését bejövő portok nélkül. További biztonságot nyújt az Azure AD Premium-funkciókon keresztül, például a többtényezős hitelesítésen és az eszközalapú feltételes hozzáférésen keresztül asztali számítógépekhez, iOS-hez, MAC-hez és Android-eszközökhöz az Intune használatával. További információ: [A natív ügyfélalkalmazások proxyalkalmazások használata](application-proxy-configure-native-client-application.md) és AZ API védelme az [Azure Active Directory val és az API-kezeléssel](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)való engedélyezése.
* **Távoli asztali szolgáltatások** **(RDS).** A szabványos RDS-telepítések hez nyitott bejövő kapcsolatokra van szükség. Az [RDS alkalmazásproxyval történő központi telepítése](application-proxy-integrate-with-remote-desktop-services.md) azonban állandó kimenő kapcsolattal rendelkezik az összekötő szolgáltatást futtató kiszolgálóról. Így több alkalmazást kínálhat a végfelhasználóknak a helyszíni alkalmazások távoli asztali szolgáltatásokon keresztül történő közzétételével. A telepítés támadási felületét a kétlépéses ellenőrzés és a feltételes hozzáférés-vezérlők rds korlátozott készletével is csökkentheti.
* **WebSockets használatával csatlakozó alkalmazások közzététele**. A [Qlik Sense](application-proxy-qlik.md) támogatása nyilvános előzetes verzióban érhető el, és a jövőben más alkalmazásokra is kibővül.
* **A natív ügyfélalkalmazások proxyalkalmazásokkal való interakciójának engedélyezése.** Az Azure AD alkalmazásproxy segítségével közzéteheti a webalkalmazásokat, de az Azure AD hitelesítési könyvtárral (ADAL) konfigurált [natív ügyfélalkalmazások](application-proxy-configure-native-client-application.md) közzétételére is használható. A natív ügyfélalkalmazások különböznek a webalkalmazásoktól, mivel egy eszközre vannak telepítve, míg a webalkalmazások böngészőn keresztül érhetők el.

## <a name="conclusion"></a>Összegzés

Munkamódszerünk és az általunk használt eszközök gyorsan változnak. Mivel több alkalmazott hozza munkába saját eszközeit, és a Szoftver-szolgáltatásként (SaaS) alkalmazások használata, a szervezetek adatainak kezelésével és védelmével is fejlődnie kell. A vállalatok már nem kizárólag a saját falaikon belül működnek, amelyet a határukat körülvevő árok véd. Az adatok minden eddiginél több helyre utaznak – mind a helyszíni, mind a felhőalapú környezetekben. Ez a fejlődés hozzájárult a felhasználók termelékenységének és együttműködési képességének növeléséhez, de a bizalmas adatok védelmét is nagyobb kihívást jelent.

Függetlenül attól, hogy jelenleg az Azure AD-t használja-e a felhasználók kezelésére egy hibrid együttélési forgatókönyvben, vagy szeretné elindítani a felhőbe való utazást, az Azure AD alkalmazásproxy implementálása segíthet csökkenteni a helyszíni lábnyom méretét a távoli szolgáltatásként való hozzáférést.

Szervezetek kell kezdeni kihasználva App Proxy ma, hogy kihasználják a következő előnyöket:

* Helyszíni alkalmazások külső közzététele a hagyományos VPN- vagy más helyszíni webes közzétételi megoldások és A DMZ-megközelítés karbantartásával kapcsolatos többletterhelés nélkül
* Egyszeri bejelentkezés az összes alkalmazásba, legyen az Office 365 vagy más SaaS-alkalmazás, beleértve a helyszíni alkalmazásokat is
* Felhőszintű biztonság, ahol az Azure AD az Office 365 telemetriáját használja a jogosulatlan hozzáférés megakadályozása érdekében
* Intune-integráció a vállalati forgalom hitelesítésének biztosításához
* A felhasználói fiókok kezelésének központosítása
* Automatikus frissítések a legújabb biztonsági javítások biztosításához
* Új funkciók, mivel azok megjelentek; a legutóbbi az SAML egyszeri bejelentkezés és az alkalmazáscookie-k részletesebb kezelése

## <a name="next-steps"></a>További lépések

* Az Azure AD alkalmazásproxy tervezéséről, működtetéséről és kezeléséről az [Azure AD alkalmazásproxy telepítésének megtervezése](application-proxy-deployment-plan.md)című témakörben talál.
* Élő bemutató ütemezéséhez vagy egy ingyenes, 90 napos próbaverzió értékeléséhez az Első lépések az Enterprise Mobility + Security szolgáltatással című [témakörben látható.](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)
