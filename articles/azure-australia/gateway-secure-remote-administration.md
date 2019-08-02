---
title: Az átjáró biztonságos távoli felügyelete az Azure Ausztráliában
description: Útmutató a biztonságos távfelügyelet konfigurálásához az ausztráliai régiókban az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok követelményeinek kielégítése érdekében.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 827dffc1c7544d9373b5f8d4426ea8c448fa25ab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571600"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Az átjáró biztonságos távoli felügyelete az Azure Ausztráliában

Kritikus fontosságú minden olyan rendszer rendelkezésre állása és integritása, amelyet a felügyeleti tevékenységek biztonságosan végeznek, és amelyek ellenőrzése alatt állnak. A felügyeleti tevékenységeket biztonságos eszközről kell elvégezni biztonságos kapcsolaton keresztül, és erős hitelesítési és engedélyezési folyamatokkal kell támogatni őket. A biztonságos távfelügyelet gondoskodik arról, hogy csak a jogosult műveletek legyenek végrehajtva, és csak az engedéllyel rendelkező rendszergazdák.

Ez a cikk részletesen ismerteti az Azure-ban üzemeltetett internetről elérhető rendszer biztonságos távfelügyeleti funkciójának megvalósítását, amely összhangban van az ausztrál Cyber Security Center (ASCS) fogyasztói útmutatóval és a ASCS információinak szándékával Biztonsági kézikönyv (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Az ausztrál Cyber Security Center (ASCS) követelményei

A nemzetközösségi rendszerek általános biztonsági követelményei az ISM-ben vannak meghatározva. Annak érdekében, hogy a Commonwealth-entitások segítséget nyújtsanak a [biztonságos felügyelet biztosításához, a ASCs közzétette a ASCs védelmét: Biztonságos felügyelet](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

Ez a dokumentum a biztonságos felügyelet fontosságát tárgyalja, és egy biztonságos felügyeleti környezet megvalósításának egyik módszerét javasolja. A dokumentum a következő módon ismerteti a biztonságos felügyeleti megoldás elemeit:

|Elem   |Leírás   |
|---|---|
|Emelt szintű hozzáférés-vezérlés   |A Kiemelt fiókok hozzáférésének szabályozása olyan alapvető biztonsági szabályozás, amely védelmet biztosít a rendszerjogosultságú fiókoknak a helytelen használattól. A hozzáférés-vezérlési módszer magában foglalja a "legalacsonyabb jogosultságok" és a "szükséges" fogalmait, valamint a szolgáltatásfiókok és a munkatársak forgalmának kezelésére szolgáló folyamatokat és eljárásokat.   |
|Többtényezős hitelesítés   |A felhasználónevek és a hozzáférési kódok (például a fizikai tokenek vagy az intelligens kártyák) a hitelesítés további tényezőit is lehetővé teszik a kritikus fontosságú eszközök védelmében. Ha egy támadó megsérti a jogosultsági szintű fiókok hitelesítő adatait, mivel az összes felügyeleti műveletnek először meg kell haladnia a többtényezős hitelesítés valamilyen formáját, a következmények nagy mértékben csökkenthetők.|
|Kiemelt munkaállomások|Egy ismert biztonságos környezet felügyeleti feladatokhoz való használata kisebb kockázatot jelenthet a hálózat számára a további biztonsági ellenőrzések végrehajtása miatt.|
|Naplózás és naplózás   |A biztonsági és adminisztratív kapcsolódó események automatizált létrehozása, gyűjtése és elemzése a munkaállomások, a kiszolgálók, a hálózati eszközök és a Jump Box mezők esetében lehetővé teszi a kompromisszumok észlelését és a támadási kísérleteket. Az Automation lehetővé teszi a szervezetek számára, hogy gyorsabban válaszoljanak, ami csökkenti a kompromisszum következményeit.|
|Hálózati szegmentálás és elkülönítés|A hálózat szegmentálása logikai zónákba, például a különböző biztonsági tartományokban, és a logikai hálózatok további elkülönítése az egyik zónából a másikba áramló adattípusok korlátozásával, korlátozza az oldalirányú mozgást. A szegmentálás megakadályozza, hogy egy támadó hozzáférjen a további erőforrásokhoz.|
|Jump boxok|A Jump Box egy megerősített távelérési kiszolgáló, amely általában a Microsoft Távoli asztali szolgáltatások vagy a Secure Shell-(SSH-) szoftvereket használja. A Jump Box a kritikus rendszerekhez és a dedikált gazdagépen végrehajtott összes felügyeleti művelethez hozzáférő rendszergazdák számára is kiugró pontként működik.|
|

Ez a cikk egy olyan hivatkozási architektúrát tartalmaz, amellyel a fenti elemek az Azure-ban üzembe helyezett rendszerek biztonságos felügyeletéhez használhatók.

## <a name="architecture"></a>Architektúra

A biztonságos felügyeleti képesség biztosításához több összetevőnek kell működnie, amelyek együttesen alkotnak egy összetartó megoldást. A megadott hivatkozási architektúrában az összetevők a [ASCs protectben leírt elemekhez vannak leképezve: Biztonságos felügyelet](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Azure biztonságos távfelügyeleti architektúra](media/remote-admin.png)

## <a name="components"></a>Összetevők

Az architektúra úgy van kialakítva, hogy a rendszerjogosultságú fiók csak a szükséges engedélyeket kapja, biztonságos módon azonosítható legyen, majd hozzáférést biztosítson a felügyeleti interfészekhez csak egy engedélyezett eszközről és biztonságos kommunikáción keresztül a felügyelt és auditált mechanizmusok.

|Megoldás| Összetevők|Elemek|
|---|---|---|
|Biztonságos eszközök |<ul><li>Kiemelt munkaállomás</li><li>Mobileszköz</li><li>Microsoft Intune</li><li>Csoportházirend</li><li>Jump Server/megerősített gazdagép</li><li>Igény szerinti (JIT) felügyelet</li></ul> |<ul><li>Kiemelt munkaállomások</li><li>Jump boxok</li></ul>|
|Biztonságos kommunikáció |<ul><li>Azure Portal</li><li>Azure VPN Gateway</li><li>Távoli asztal (RD) átjáró</li><li>Hálózati biztonsági csoportok (NSG)</li></ul> |<ul><li>Hálózati szegmentálás és elkülönítés</li></ul>|
|Erős hitelesítés |<ul><li>Tartományvezérlő (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Hálózati házirend-kiszolgáló (NPS)</li><li>Azure MFA</li></ul> |<ul><li>Többtényezős hitelesítés</li></ul> |
|Erős engedélyezés |<ul><li>Identitás-és hozzáférés-kezelés (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Feltételes hozzáférés</li></ul>|<ul><li>Emelt szintű hozzáférés-vezérlés</li></ul>|
|||

>[!NOTE]
>A naplózási és naplózási elemmel kapcsolatos további információkért tekintse meg az [átjáró naplózásával, naplózásával és láthatóságával](gateway-log-audit-visibility.md) foglalkozó cikket.

## <a name="administration-workflow"></a>Adminisztrációs munkafolyamat

Az Azure-ban üzembe helyezett rendszerek felügyelete két különböző kategóriába oszlik, az Azure konfigurációjának felügyeletével és az Azure-ban üzembe helyezett számítási feladatok felügyeletével. Az Azure-konfiguráció a Azure Portalon és a munkaterhelés-felügyeleten keresztül végezhető el olyan felügyeleti mechanizmusok használatával, mint például a RDP protokoll (RDP), a Secure Shell (SSH) vagy a Pásti-képességek olyan eszközökkel, mint például az SQL Management Studio.

Az adminisztrációhoz való hozzáférés több lépésből álló folyamat, amely az architektúrában felsorolt összetevőket foglalja magában, és hozzáférést igényel a Azure Portal és az Azure-konfigurációhoz, mielőtt elérhetővé tenné az Azure-munkaterheléseket.

>[!NOTE]
> Az itt ismertetett lépések az Azure grafikus felhasználói felületének (GUI) összetevőit használó általános folyamat. Ezek a lépések más felületek, például a PowerShell használatával is végezhetők el.

### <a name="azure-configuration-and-azure-portal-access"></a>Azure-konfiguráció és Azure Portal-hozzáférés

|Lépés |Leírás |
|---|---|
|Kiemelt munkaállomás-bejelentkezés |A rendszergazda rendszergazdai hitelesítő adatokkal jelentkezik be a Kiemelt munkaállomáson. Csoportházirend vezérlők megakadályozzák, hogy a nem rendszergazdai fiókok hitelesítsék magukat a Kiemelt munkaállomáson, és meggátolja a rendszergazdai fiókok hitelesítését a nem privilegizált munkaállomásokon. Microsoft Intune kezeli a Kiemelt munkaállomás megfelelőségét, hogy naprakész legyen a szoftveres javítások, a kártevő szoftverek és az egyéb megfelelőségi követelmények alapján. |
|Bejelentkezés Azure Portal |A rendszergazda megnyit egy webböngészőt a Azure Portal, amely Transport Layer Security (TLS) használatával van titkosítva, és rendszergazdai hitelesítő adatok használatával jelentkezik be. A hitelesítési kérést Azure Active Directory közvetlenül, vagy hitelesítési mechanizmusok (például Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy átmenő hitelesítés használatával dolgozzák fel. |
|Azure MFA |Az Azure MFA hitelesítési kérést küld a Kiemelt fiók regisztrált mobileszközének. A mobileszköz felügyeletét az Intune felügyeli a biztonsági követelmények teljesítése érdekében. A rendszergazdának először hitelesítenie kell a mobileszköz, majd a Microsoft Authenticator alkalmazás PIN-kód vagy biometrikus rendszer használatával, mielőtt a hitelesítési kísérlet engedélyezve lenne az Azure MFA-ra. |
|Feltételes hozzáférés |A feltételes hozzáférési szabályzatok ellenőrizhetik a hitelesítési kísérletet annak biztosítására, hogy az megfeleljen a szükséges követelményeknek, például a kapcsolathoz tartozó IP-címet, a csoport tagságát a Kiemelt fiókhoz, valamint a felügyeleti és megfelelőségi állapotot a következőben: az Intune által jelentett, Kiemelt jogosultságú munkaállomás. |
|Privileged Identity Management (PIM) |Az Azure Portal segítségével a rendszergazda aktiválhatja vagy kérheti az aktiválást azokhoz a Kiemelt szerepkörökhöz, amelyekhez a PIM-t engedélyezték. A PIM biztosítja, hogy a Kiemelt jogosultságú fiókok ne rendelkezzenek állandó rendszergazdai jogosultságokkal, és hogy az emelt szintű hozzáférésre irányuló kérelmek csak a rendszergazdai feladatok elvégzéséhez szükséges ideig legyenek. A PIM az összes kérelem és aktiválás naplózását is lehetővé teszi a naplózási célokra. |
|Identitáskezelés és hozzáférés-menedzsment (Identity and Access Management)|Miután a rendszerjogosultságú fiókot biztonságosan azonosították és aktiválta a szerepköröket, a rendszergazda hozzáférést kap azokhoz az Azure-előfizetésekhez és erőforrásokhoz, amelyeket az identitás-és hozzáférés-kezeléshez rendeltek.|
|

Miután a rendszerjogosultságú fiók végrehajtotta a rendszergazdai hozzáférés megszerzésének lépéseit a Azure Portalhoz, konfigurálhatja a munkaterhelések elérését, és megteheti a felügyeleti kapcsolatokat.

### <a name="azure-workload-administration"></a>Azure munkaterhelés-felügyelet

|Lépés |Leírás|
|---|---|
|Igény szerinti (JIT) hozzáférés|A virtuális gépekhez való hozzáféréshez a rendszergazda a JIT használatával kéri az RDP-hez való hozzáférést a Jump Server RD-átjáró IP-címről, RDP vagy SSH-ról a megfelelő munkaterhelés virtuális gépekre.|
|Azure VPN Gateway|A rendszergazda mostantól pont – hely típusú IPSec VPN-kapcsolatot hoz létre a Kiemelt munkaállomásról az Azure VPN Gatewayra, amely a kapcsolat létrehozásához tanúsítványalapú hitelesítést végez.|
|RD-átjáró|A rendszergazda most egy RDP-kapcsolattal próbálkozik a Jump Server kiszolgálóval a Távoli asztali kapcsolat konfigurációban megadott RD-átjáró. A RD-átjáró egy magánhálózati IP-címmel rendelkezik, amely az Azure VPN Gateway-kapcsolaton keresztül érhető el. A RD-átjáró szabályzatai határozzák meg, hogy a rendszerjogosultságú fiók jogosult-e a kért Jump Server elérésére. A RD-átjáró kéri a rendszergazdát a hitelesítő adatok megadására, és továbbítja a hitelesítési kérést a hálózati házirend-kiszolgálónak (NPS).|
|Hálózati házirend-kiszolgáló (NPS)|A hálózati házirend-kiszolgáló fogadja a hitelesítési kérést a RD-átjárótól, és érvényesíti a felhasználónevet és a jelszót Active Directory előtt, mielőtt elküld egy, az Azure MFA-hitelesítési kérelem elindítására Azure Active Directory irányuló kérelmet.|
|Azure MFA|Az Azure MFA hitelesítési kérést küld a Kiemelt fiók regisztrált mobileszközének. A mobileszköz felügyeletét az Intune felügyeli a biztonsági követelmények teljesítése érdekében. A rendszergazdának először hitelesítenie kell a mobileszköz, majd a Microsoft Authenticator alkalmazás PIN-kód vagy biometrikus rendszer használatával, mielőtt a hitelesítési kísérlet engedélyezve lenne az Azure MFA-ra.|
|Jump Server|A sikeres hitelesítés után az RDP-kapcsolat Transport Layer Security (TLS) protokollal van titkosítva, majd a titkosított IPSec-alagúton keresztül az Azure-VPN Gateway, a RD-átjáró és a Jump kiszolgálón A Jump Server-kiszolgálóról a rendszergazda mostantól RDP vagy SSH használatával is felhasználhatja a virtuális gépeket a JIT-kérelemben megadott módon.|
|

## <a name="general-guidance"></a>Általános útmutatás

A cikkben felsorolt összetevők megvalósításakor a következő általános útmutatás vonatkozik:

* A szolgáltatások rendelkezésre állásának ellenőrzése, annak biztosítása, hogy az összes adat a megengedett helyen belül maradjon, és üzembe helyezése az AU Central vagy az AU Central 2 alkalmazásban, a védett munkaterhelések első előnyben részesítése

* Tekintse meg az *Azure-ASCs minősítési jelentést – védett 2018-* es kiadvány az egyes szolgáltatások minősítési állapotáról, és végezze el az önértékelést a jelentésben nem szereplő bármely kapcsolódó összetevőn a *ASCs fogyasztói útmutatója szerint – VÉDETT Microsoft Azure*

* Győződjön meg arról, hogy a hálózati kapcsolat és a szükséges proxybeállítások a szükséges hitelesítési összetevőkhöz (például az Azure AD, az ADFS és a PTA) való hozzáféréshez.

* A követelmények megfelelőségének figyelése és érvényesítése Azure Policy használatával

* Győződjön meg arról, hogy a virtuális gépek, különösen Active Directory-tartomány vezérlők titkosított Storage-fiókokban vannak tárolva, és használják Azure Disk Encryption

* Robusztus identitás-és felügyeleti eljárásokat és irányítási folyamatokat hozhat létre és kezelhet, amelyek alátámasztják a jelen cikkben felsorolt műszaki ellenőrzéseket

|Resource|URL|
|---|---|
|Ausztrál szabályozási és szabályzatok megfelelőségi dokumentumai|[Ausztrál szabályozási és szabályzatok megfelelőségi dokumentumai](https://aka.ms/au-irap)|
|Azure-termékek – ausztráliai régiók és nem regionális|[Azure-termékek – ausztráliai régiók és nem regionális](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|A Cyber biztonsági incidensek enyhítésére szolgáló stratégiák|[A Cyber biztonsági incidensek enyhítésére szolgáló stratégiák](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ASCS-védelem: Biztonságos felügyelet|[ASCS-védelem: Biztonságos felügyelet](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|kézikönyv: A távoli asztali átjáró-infrastruktúra használata a hálózati házirend-kiszolgáló (NPS) bővítmény és az Azure AD integrálása|[RD-átjáró integrálása az NPS és az Azure AD szolgáltatással](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Összetevő-útmutatás

Ez a szakasz az egyes összetevők és a hozzá tartozó szerepkör a teljes biztonságos távfelügyeleti architektúrában történő felhasználására vonatkozó információkat tartalmazza. További hivatkozások érhetők el a hasznos erőforrások, például a dokumentáció, útmutatók és oktatóanyagok eléréséhez.

## <a name="secure-devices"></a>Biztonságos eszközök

A rendszerjogosultságú felhasználók által a felügyeleti funkciók elvégzéséhez használt fizikai eszközök értékes célokat szolgálnak a kártékony szereplők számára. A fizikai eszközök biztonságának és integritásának fenntartása, valamint annak biztosítása, hogy a kártevő szoftverektól mentesek legyenek, és a kompromisszumok elleni védelem kulcsfontosságú része a biztonságos távfelügyeleti képesség biztosításának. Ez magában foglalja a magas prioritású biztonsági konfigurációt, amely a ASCS alapvető nyolc stratégiájában van meghatározva a számítógépes biztonsági incidensek, például az alkalmazás-engedélyezési lista, az alkalmazások javítása, az alkalmazások megerősítése és a javítási operációs rendszerek enyhítése érdekében. Ezeket a képességeket telepíteni, konfigurálni, naplózni, érvényesíteni és jelenteni kell, hogy az eszköz állapota megfeleljen a szervezet követelményeinek.

### <a name="privileged-workstation"></a>Kiemelt munkaállomás

Az emelt szintű munkaállomás egy megerősített gép, amely rendszergazdai feladatok elvégzésére szolgál, és csak a rendszergazdai fiókok számára érhető el. A Kiemelt munkaállomásnak szabályzatokkal és konfigurációval kell rendelkeznie a futtatható szoftverek korlátozásához, a hálózati erőforrásokhoz való hozzáféréshez, valamint az Internet és a hitelesítő adatok védelméhez abban az esetben, ha az eszközt ellopják vagy feltörték. |

|További források|Összekapcsolás|
|---|---|
|Emelt szintű hozzáférési munkaállomások architektúrájának áttekintése|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|A Kiemelt hozzáférési segédanyagok biztonságossá tétele|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|
|

### <a name="mobile-device"></a>Mobileszköz

A mobileszköz hordozhatósága és mérete miatt nagyobb a véletlen adatvesztés vagy a lopás kockázata, és a megfelelő védelemmel kell rendelkeznie. A mobileszköz erős további tényezőt biztosít a hitelesítéshez, mivel lehetővé teszi az eszköz-hozzáférés hitelesítésének, a helymeghatározási szolgáltatások, a titkosítási függvények és a távoli törlés lehetőségének kikényszerítését. Ha egy mobileszköz további hitelesítési tényezőként való használata az Azure-ban, az eszközt úgy kell konfigurálni, hogy az Microsoft Authenticator alkalmazást PIN-kóddal vagy biometrikus hitelesítéssel használja, és ne a telefonhívások vagy szöveges üzenetek használatával.

|További források|Összekapcsolás|
|---|---|
|Azure AD-hitelesítési módszerek|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Az Microsoft Authenticator alkalmazás használata|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|
|

### <a name="microsoft-intune"></a>Microsoft Intune

Az Intune a mobileszközök és alkalmazások kezelését Enterprise Mobility + Security összetevője. Szorosan integrálható más összetevőkkel, például az identitás-és hozzáférés-vezérléshez, valamint az adatvédelem Azure Information Protectionéhez Azure Active Directory. Az Intune szabályzatokat biztosít a munkaállomások és a mobileszközök számára az erőforrások elérésére vonatkozó megfelelőségi követelmények megadásához, valamint jelentéskészítési és naplózási képességeket biztosít a felügyeleti eszközök állapotának betekintéséhez.

|További források|Összekapcsolás|
|---|---|
|Microsoft Intune dokumentáció|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Az eszközök megfelelőségének első lépései az Intune-ban|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|
|

### <a name="group-policy"></a>Csoportházirend

Csoportházirend az operációs rendszerek és alkalmazások konfigurációjának szabályozására szolgál. A biztonsági házirendek vezérlik a rendszer hitelesítési, engedélyezési és naplózási beállításait. Csoportházirend használatával megerősíthető a Kiemelt munkaállomás, megóvja a rendszergazdai hitelesítő adatokat, és korlátozhatja a nem Kiemelt fiókok hozzáférését a Kiemelt eszközökhöz.

|További források|Összekapcsolás|
|---|---|
|Helyi bejelentkezés engedélyezése Csoportházirend beállítás|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|
|

### <a name="jump-server--bastion-host"></a>Jump Server/megerősített gazdagép

A Jump Server/megerősített gazdagép egy központosított pont az adminisztrációhoz. A felügyeleti feladatok végrehajtásához szükséges eszközökkel rendelkezik, de a hálózati hozzáférés szükséges a felügyeleti portok erőforrásaihoz való kapcsolódáshoz is. A Jump Server a virtuális gépek számítási feladatainak a jelen cikkben való felügyeletének központi pontja, de konfigurálható úgy is, hogy a platform szolgáltatásként (például az SQL-ben) való felügyeletének engedélyezett pontként szolgáljon. A Pásti-funkciókhoz való hozzáférés szolgáltatás alapján, identitás-és hálózati vezérlők használatával korlátozható.

|További források|Összekapcsolás|
|---|---|
|Biztonságos felügyeleti gazdagépek implementálása|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|
|

### <a name="just-in-time-jit-access"></a>Igény szerinti (JIT) hozzáférés

A JIT egy Azure Security Center képesség, amely hálózati biztonsági csoportokat (NSG-ket) használ a felügyeleti protokollok, például az RDP és az SSH hozzáférésének letiltásához Virtual Machineson. A Virtual Machines üzemeltetett alkalmazások továbbra is normál módon működnek, de a beszerzéséhez rendszergazdai hozzáférésre csak meghatározott időtartamra lehet szükség. Minden kérés naplózási célból van naplózva.

|További források |Összekapcsolás |
|---|---|
|Igény szerinti (JIT) hozzáférés kezelése|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Azure-beli virtuális gépekhez való hozzáférés automatizálása|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|
|

## <a name="secure-communication"></a>Biztonságos kommunikáció

Az adminisztrációs tevékenységekhez kapcsolódó kommunikációs forgalom olyan bizalmas adatokat tartalmazhat, mint például a rendszergazdai hitelesítő adatok, és ennek megfelelően kell felügyelni és védeni. A biztonságos kommunikációhoz megbízható titkosítási funkciók szükségesek, amely megakadályozza a lehallgatást és a hálózati szegmentálást, valamint azokat a korlátozásokat, amelyek a jogosult végpontokra korlátozzák a felügyeleti forgalmat, és az oldalirányú mozgást vezérlik, ha a rendszer sérül

### <a name="azure-portal"></a>Azure Portal

A Azure Portal kommunikációját Transport Layer Security (TLS) használatával titkosítjuk, és a Azure Portal használatát a ASCS tanúsította. A nemzetközösségi entitásoknak követniük kell a *ASCs vásárlói útmutatóban* foglalt javaslatokat, és konfigurálniuk kell a webböngészőket annak biztosítására, hogy a TLS legújabb verzióját és a támogatott titkosítási algoritmusokat használják.

|További források |Összekapcsolás |
|---|---|
|Az Azure encryption áttekintése – az átvitel titkosítása|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|
|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Az Azure VPN Gateway biztonságos titkosított kapcsolódást biztosít a Kiemelt munkaállomásról az Azure-ba. Az Azure VPN Gatewayt a ASCS hitelesíti a biztonságos IPSec-kommunikáció biztosításához. A nemzetközösségi entitásoknak az Azure VPN Gateway a ASCS fogyasztói útmutató, a ASCS-tanúsítási jelentés és egyéb konkrét útmutatások alapján kell konfigurálniuk.

|További források |Összekapcsolás |
|---|---|
|Tudnivalók a pont – hely kapcsolatokról|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Az Azure VPN Gateway titkosítási adatai|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Azure VPN Gateway konfiguráció|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="remote-desktop-rd-gateway"></a>Távoli asztal (RD) átjáró

A RD-átjáró a rendszerek RDP-kapcsolatainak szabályozására és engedélyezésére szolgáló biztonságos mechanizmus. Úgy működik, hogy RDP-forgalmat ágyaz be a HyperText átviteli protokoll Secure (HTTPS) szolgáltatásba, és a TLS használatával titkosított. A TLS további biztonsági réteget biztosít a felügyeleti forgalom számára.

|További források |Összekapcsolás |
|---|---|
|Távoli asztali szolgáltatások architektúra|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|
|

### <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG)

A NSG az alhálózatok vagy virtuális gépek beléptetéséhez vagy elhagyásához használt hálózati forgalomhoz Access Control listákként (ACL) működnek. A NSG hálózati szegmentálást biztosítanak, és olyan mechanizmust biztosítanak, amely a rendszerek között engedélyezett kommunikációs folyamatok szabályozására és korlátozására szolgál. A NSG az igény szerinti felügyelet (JIT) alapvető összetevője, amely lehetővé teszi vagy megtagadja a hozzáférést a felügyeleti protokollokhoz.

|További források |Összekapcsolás |
|---|---|
|Azure biztonsági csoportok – áttekintés|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Útmutató: Virtuális hálózatok megtervezése|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
|

## <a name="strong-authentication"></a>Erős hitelesítés

A rendszerjogosultságú felhasználók biztonságos azonosítása a rendszerekhez való hozzáférés biztosítása előtt a biztonságos felügyelet alapvető összetevője. A rendszernek meg kell felelnie a rendszerjogosultságú fiókhoz társított hitelesítő adatok védelme érdekében, és meg kell akadályozni, hogy a rosszindulatú szereplők megszemélyesítés vagy hitelesítő adatok ellopása útján hozzáférjenek a rendszerekhez.

### <a name="domain-controller-dc"></a>Tartományvezérlő (DC)

A tartományvezérlő magas szinten tárolja a Active Directory adatbázis egy példányát, amely a tartományon belüli összes felhasználót, számítógépet és csoportot tartalmazza. A tartományvezérlők a felhasználók és számítógépek hitelesítését végzik. Az architektúrában található tartományvezérlők virtuális gépekként üzemelnek az Azure-ban, és hitelesítési szolgáltatásokat biztosítanak a Jump kiszolgálókhoz és a számítási feladatok virtuális gépeihoz csatlakozó, Kiemelt jogosultságú fiókokhoz.

|További források |Összekapcsolás |
|---|---|
|Active Directory tartományi szolgáltatások áttekintése|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|
|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Az Azure AD az Azure-hoz készült hitelesítési szolgáltatás. A felhőt tartalmazza

identitások és az Azure-környezet hitelesítésének és engedélyezésének engedélyezése. Az Azure AD szinkronizálható Active Directory segítségével Azure AD Connect és összevont hitelesítést biztosíthat Active Directory összevonási szolgáltatások (AD FS) (AD FS) és Azure AD Connect használatával. Az Azure AD a biztonságos felügyelet alapvető összetevője.

|További források |Összekapcsolás |
|---|---|
|Azure Active Directory-dokumentáció|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Hibrid identitás dokumentációja|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|
|

### <a name="network-policy-server-nps"></a>Hálózati házirend-kiszolgáló (NPS)

A hálózati házirend-kiszolgáló egy olyan hitelesítési és házirend-kiszolgáló, amely speciális hitelesítési és engedélyezési folyamatokat biztosít. Az ebben az architektúrában található NPS-kiszolgáló az Azure MFA-hitelesítés RD-átjáró hitelesítési kérelmekkel való integrálására szolgál. A hálózati házirend-kiszolgáló egy adott beépülő modullal támogatja az Azure MFA-nal való integrációt az Azure AD-ben.

|További források |Összekapcsolás |
|---|---|
|A hálózati házirend-kiszolgáló dokumentációja|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|
|

### <a name="azure-mfa"></a>Azure MFA

Az Azure MFA egy olyan hitelesítési szolgáltatás, amely a Azure Active Directoryon belül lehetővé teszi a hitelesítési kérések használatát a felhasználónévn és a jelszón túl a Felhőbeli erőforrások, például a Azure Portal eléréséhez. Az Azure MFA számos hitelesítési módszert támogat, és ez az architektúra a Microsoft Authenticator alkalmazást használja a fokozott biztonság és az NPS-integráció terén.

|További források |Összekapcsolás |
|---|---|
|Működés: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Útmutató: Felhőalapú Azure Multi-Factor Authentication üzembe helyezése|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|
|

## <a name="strong-authorisation"></a>Erős engedélyezés

Ha a Kiemelt fiók biztonságos azonosítása megtörtént, hozzáférést biztosíthat az erőforrásokhoz. Az engedélyek vezérlik és kezelik az adott fiókhoz rendelt jogosultságokat. Az erős engedélyezési folyamatok a ASCS alapvető nyolc stratégiájának megfelelően illeszkednek a felügyeleti jogosultságok korlátozására szolgáló Cyber-biztonsági incidensek enyhítéséhez.

### <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Az Azure-ban az adott fiókhoz rendelt szerepkörökön alapuló jogosultsági szintű műveletek végrehajtásához férhet hozzá. Az Azure-ban olyan szerepkörök széles választéka található, amelyek konkrét engedélyekkel rendelkeznek az adott feladatok elvégzéséhez. Ezek a szerepkörök több szinten is megadhatók, például egy előfizetés vagy egy erőforráscsoport. A szerepkör-hozzárendelés és az engedélyek kezelése a Azure Active Directory fiókjain és csoportjain alapul, és az Azure-on belül Access Control (IAM) kezeli.

|További források |Összekapcsolás |
|---|---|
|Azure-beli szerepköralapú Access Control|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|A szerepkör-definíciók ismertetése|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|
|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

A PIM egy Azure Active Directory-összetevő, amely a Kiemelt szerepkörökhöz való hozzáférést vezérli. A Kiemelt jogosultságú fiókoknak nincs szükségük állandó vagy állandó jogosultsági szintű hozzáférésre, de a jogosultsági szintű tevékenységek elvégzése érdekében lehetőség van arra, hogy az emelt szintű hozzáférési jogosultságot egy adott időszakra kérjék. A PIM további vezérlőket biztosít a privilegizált hozzáférés fenntartásához és korlátozásához, valamint a naplózáshoz és a naplózáshoz, hogy nyomon követhesse a jogosultságok felhasználásának példányait.

|További források |Összekapcsolás |
|---|---|
|Privileged Identity Management (PIM) dokumentációja|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Ismerkedés a PIM-mel|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|
|

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáférés Azure Active Directory olyan összetevője, amely lehetővé teszi vagy megtagadja az erőforrásokhoz való hozzáférést a feltételek alapján. Ezek a feltételek lehetnek a hálózati hely, az eszköz típusa, a megfelelőségi állapot, a csoporttagság és egyebek. A feltételes hozzáférés az MFA, az eszközkezelés és a megfelelőség kikényszeríthető az Intune-nal és a rendszergazdai fiókok csoporttagság használatával.

|További források |Összekapcsolás |
|---|---|
|A feltételes hozzáférés dokumentációja|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Útmutató: Felügyelt eszközök szükségesek a felhőalapú alkalmazásokhoz való hozzáféréshez feltételes hozzáféréssel|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|
|

## <a name="next-steps"></a>További lépések

Tekintse át az [átjáró bejövő forgalmának kezelésével és ellenőrzésével](gateway-ingress-traffic.md) foglalkozó cikket, amely részletesen ismerteti az Azure-beli átjáró-összetevők forgalmának szabályozását.
