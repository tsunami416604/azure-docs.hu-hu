---
title: Biztonságos pontszám Azure Security Center
description: Azure Security Center biztonságos pontszámának és biztonsági ellenőrzésének leírása
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 2ad817afd8f4e80e99055646dca34b9bb05d100f
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044311"
---
# <a name="enhanced-secure-score-in-azure-security-center"></a>Fokozottan biztonságos pontszám Azure Security Center

## <a name="introduction-to-secure-score"></a>A biztonságos pontszám bemutatása

Azure Security Center két fő célja van: az aktuális biztonsági helyzet megértéséhez, valamint a biztonság hatékony és hatékony fejlesztéséhez. A Security Center központi aspektusa, amely lehetővé teszi a célok elérését biztonságos pontszámként.

Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákra. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint.

A Security Center biztonságos pontszám lapja az alábbiakat tartalmazza:

- **A pontszám** – a biztonságos pontszám százalékos értékként jelenik meg, de a mögöttes értékek is egyértelműek:

    [![Az alapul szolgáló számok százalékos értékeként megjelenített biztonságos pontszám](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Biztonsági vezérlők** – az egyes vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai, és a sebezhető támadási felületeket tükrözik. A vezérlők a biztonsági javaslatok összessége, amelyek útmutatást nyújtanak a javaslatok megvalósításához. A pontszám csak akkor javul, ha egy vezérlőelemen belül egy adott erőforráshoz tartozó *összes* javaslatot szervizeli.

    Ha szeretné azonnal megtekinteni, hogy a szervezet milyen jól védi az egyes támadási felületet, tekintse át az egyes biztonsági vezérlők pontszámait.

    További információ: [a biztonságos pontszám kiszámítása](secure-score-security-controls.md#how-your-secure-score-is-calculated) alább. 


>[!TIP]
> Security Center odaítélt pontok korábbi verziói az ajánlási szinten: Ha egy adott erőforrásra vonatkozó javaslatot szervizelt, a biztonságos pontszám javult. Napjainkban a pontszám csak akkor javul, ha a vezérlőn belül egy adott erőforráshoz tartozó *összes* javaslatot szervizeli. Így a pontszám csak akkor javul, ha javította az erőforrás biztonságát.


## <a name="accessing-your-secure-score"></a>A biztonságos pontszám elérése

A teljes biztonsági pontszám, valamint a pontszám/előfizetés alapján a Azure Portal vagy a programozott módon a Azure Security Center REST API.

### <a name="getting-your-secure-score-from-the-portal"></a>Biztonságos pontszám beszerzése a portálról

Security Center a pontszám kiemelten jelenik meg a portálon: ez az első dolog, ami az Áttekintés oldalon látható. Ha a a dedikált biztonságos pontszám oldalra kattint, a pontszám az előfizetés alapján lebontva jelenik meg. Kattintson egy előfizetésre, és tekintse meg a rangsorolt javaslatok részletes listáját, valamint azt, hogy milyen hatással lehet a szervizelését az előfizetés pontszámára.

![Általános biztonsági pontszám a portálon látható módon](media/secure-score-security-controls/single-secure-score-via-ui.png)

### <a name="getting-your-secure-score-from-the-rest-api"></a>A REST API biztonságos pontszámának beolvasása

A pontszámot a [biztonságos pontszám API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) -n keresztül érheti el (jelenleg előzetes verzióban érhető el). Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Használhatja például a **Secure scores** API-t egy adott előfizetés pontszámának lekéréséhez. Emellett a **Secure score Controls** API használatával is listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát.

![Egyetlen biztonságos pontszám beolvasása az API-n keresztül](media/secure-score-security-controls/single-secure-score-via-api.png)

A biztonságos pontszám API-ra épülő eszközökre vonatkozó példákért tekintse meg [a GitHub-Közösség biztonságos pontszám területét](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="how-your-secure-score-is-calculated"></a>A biztonságos pontszám kiszámításának módja 

Az egyes biztonsági ellenőrzéseknek az általános biztonsági pontszámhoz való hozzájárulása egyértelműen a javaslatok oldalon látható.

[![A fokozott biztonságú pontszám bevezeti a biztonsági vezérlőket](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

A biztonsági ellenőrzés összes lehetséges pontjának lekéréséhez minden erőforrásnak meg kell felelnie a biztonsági ellenőrzésen belüli összes biztonsági javaslatnak. Security Center például több javaslattal is rendelkezik a felügyeleti portok biztonságossá tételével kapcsolatban. A múltban a kapcsolódó és az egymástól függő ajánlások némelyikét orvosolhatja, miközben mások megoldatlanul maradnak, és a biztonságos pontszám javulni fog. A tárgyilagosan megvizsgálva egyszerűen azt is megteheti, hogy a biztonság még nem javult, amíg meg nem oldotta őket. Most pedig az összeset ki kell javítania, hogy különbséget tegyen a biztonságos pontszámával.

Például a "rendszerfrissítések alkalmazása" nevű biztonsági vezérlő legfeljebb hat ponttal rendelkezik, amelyet az elemleírásban láthat a vezérlő lehetséges növelési értékeként:

[![A rendszerfrissítések alkalmazásának biztonsági vezérlése](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A vezérlő maximális pontszáma, rendszerfrissítések alkalmazása, mindig 6. Ebben a példában 50 erőforrás van. Így a maximális pontszámot 50-re osztjuk, és az eredmény az, hogy minden erőforrás a 0,12 pontot. 

* **Lehetséges növekedés** (0,12 x 8 nem megfelelő állapotú erőforrások = 0,96) – a vezérlőn belül elérhető további pontok. Ha kijavítja az ebben a vezérlőben található összes javaslatot, a pontszám 2%-kal nő (ebben az esetben az 0,96 pont 1 pontra kerekítve). 
* **Aktuális pontszám** (0,12 x 42 kifogástalan erőforrások = 5,04) – a vezérlő aktuális pontszáma. Az egyes vezérlőelemek a teljes pontszám irányába járulnak hozzá. Ebben a példában a vezérlő a 5,04-as számú aktuális biztonságos összegre mutat.
* Maximális **pontszám** – az összes, a vezérlőn belüli javaslat végrehajtásával elnyerhető pontok maximális száma. Egy vezérlőelem maximális pontszáma a vezérlőelem relatív jelentőségét jelzi. A maximális pontszám értékekkel osztályozhatja a problémákat, hogy először működjön. 


### <a name="calculations---understanding-your-score"></a>Számítások – a pontszám megismerése

|Metrika|Képlet és példa|
|-|-|
|**Biztonsági vezérlő aktuális pontszáma**|<br>![A biztonsági vezérlő aktuális pontszámának kiszámításához használt egyenlet](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Minden egyes biztonsági ellenőrzés hozzájárul a biztonsági pontszám eléréséhez. A vezérlőn belüli javaslat által érintett összes erőforrás a vezérlő aktuális pontszámának irányába járul hozzá. Az egyes vezérlők aktuális pontszáma *a vezérlőben lévő erőforrások* állapotának mértéke.<br>![A biztonsági vezérlő aktuális pontszámának kiszámításakor használt értékeket megjelenítő elemleírások](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Ebben a példában a 6 maximális pontszáma 78-re osztható, mert ez az egészséges és a nem kifogástalan erőforrások összege.<br>6/78 = 0,0769<br>A jelenlegi pontszám a (4) kifogástalan állapotú erőforrások számával való szorzását eredményezi:<br>0,0769 * 4 = **0,31**<br><br>|
|**Biztonsági pontszám**<br>Egyetlen előfizetés|<br>![A jelenlegi biztonságos pontszám kiszámításához használt egyenlet](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Egyszeri előfizetés biztonságos pontszáma minden engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Ebben a példában egyetlen előfizetés van az összes rendelkezésre álló biztonsági vezérlővel (a 60-pontok lehetséges maximális pontszáma). A pontszám 28 pontot mutat a lehetséges 60-ből, a fennmaradó 32 pont pedig a biztonsági vezérlők "lehetséges pontszám növelésének" számadatait tükrözi.<br>![A vezérlőelemek listája és a lehetséges pontszám növekedése](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Biztonsági pontszám**<br>Több előfizetés|<br>Az összes előfizetéshez tartozó összes erőforrás aktuális pontszáma hozzáadva, a számítás pedig azonos, mint egyetlen előfizetés esetén.<br><br>Több előfizetés megtekintésekor a biztonságos pontszám kiértékeli az összes engedélyezett szabályzaton belüli összes erőforrást, és a biztonsági vezérlők maximális pontszámára vonatkozó együttes hatásukat.<br>![Több előfizetés biztonságos pontszáma minden engedélyezett vezérlővel](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Az összesített pontszám **nem** átlag; Ehelyett az összes előfizetés összes erőforrása állapotának kiértékelt testtartása.<br>Itt is, ha a javaslatok lapra lép, és hozzáadja az elérhető lehetséges pontokat, akkor a jelenlegi pontszám (24) és a rendelkezésre álló maximális pontszám (60) közötti különbség jelenik meg.|
||||

## <a name="improving-your-secure-score"></a>A biztonságos pontszám javítása

A biztonságos pontszám javítása érdekében javítsa a javaslatok listáját a biztonsági javaslatok kijavításával. Az egyes javaslatokat minden erőforráshoz manuálisan, vagy a **gyors javítás** használatával javíthatja. lehetőség (ha elérhető) egy, az erőforrások egy csoportjára vonatkozó javaslatra vonatkozó szervizelés alkalmazásához. További információ: [javaslatok szervizelése](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Csak a beépített javaslatok befolyásolhatják a biztonságos pontszámot.


## <a name="security-controls-and-their-recommendations"></a>Biztonsági vezérlők és javaslataik

Az alábbi táblázat a Azure Security Center biztonsági vezérlőit sorolja fel. Az egyes vezérlőknél megtekintheti a biztonságos pontszámhoz adható pontok maximális számát, ha a vezérlőben felsorolt *összes* javaslatot kijavítja az *összes* erőforráshoz. 

<div class="foo">

<style type="text/css">. TG {Border-Collapse: Collapse; szegély – térköz: 0;}. TG TF {Border-Color: fekete; szegély stílusa: Solid; Border-width: 1px; betűkészlet-család: Arial, Sans-Serif; betűméret: 14px; túlcsordulás: rejtett; kitöltés: 10px 5px; Word-break: Normal;}. TG th {Border-Color: Black; Border-Style: Solid; Szegély szélessége: 1px; betűkészlet-család: Arial, Sans-Serif; betűkészlet-méret: 18px; font-Weight: Normal; túlcsordulás: rejtett; kitöltés: 10px 5px; Word-break: Normal;}. TG. TG-cly1 {Text-igazítás: balra; függőleges igazítás: középső}. TG. TG-lboi {Border-Color: öröklés; szöveg-igazítás: balra; függőleges igazítás: középső}</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Biztonsági ellenőrzés, pontszám és leírás</b><br></th>
    <th class="tg-cly1"><b>Javaslatok</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">MFA engedélyezése (legfeljebb 10. pontszám)</p></strong>Ha csak jelszó használatával hitelesíti a felhasználót, a támadási vektor nyitva marad. Ha a jelszó gyenge vagy máshol van kitéve, valóban a felhasználó bejelentkezik a felhasználónévvel és a jelszóval?<br>Ha az <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> engedélyezve van, a fiókok biztonságosabbak, és a felhasználók továbbra is elvégezhetik a hitelesítést az egyszeri bejelentkezéssel (SSO) rendelkező alkalmazásokkal.</td>
    <td class="tg-lboi"; width=55%>-Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon<br>-Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonságos felügyeleti portok (max. pontszám 8)</p></strong>A találgatásos támadás célja, hogy hozzáférjen a virtuális géphez. Mivel a portok nem mindig nyílnak meg, az egyik kockázatcsökkentő stratégia az igény szerinti hálózati hozzáférés-vezérléssel, a hálózati biztonsági csoportokkal és a virtuálisgép-portok felügyeletével való kitettség csökkentése.<br>Mivel számos informatikai szervezet nem blokkolja a hálózatról kimenő SSH-kommunikációt, a támadók olyan titkosított alagutakat hozhatnak létre, amelyek lehetővé teszik a fertőzött rendszereken lévő RDP-portok számára a támadók számára a kiszolgálók vezérlését. A támadók a Rendszerfelügyeleti webszolgáltatások alrendszerével később áthelyezhetik a környezetét, és ellopott hitelesítő adatokkal érhetik el a hálózat egyéb erőforrásait.</td>
    <td class="tg-lboi"; width=55%>-A virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni<br>-A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>-A felügyeleti portokat be kell zárni a virtuális gépeken</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Rendszerfrissítések alkalmazása (max. pontszám 6)</p></strong>A rendszerfrissítések biztosítják a szervezetek számára a működési hatékonyság fenntartását, csökkentik a biztonsági réseket, és stabilabb környezetet biztosítanak a végfelhasználók számára. A frissítések nem vonatkoznak a nem javított biztonsági rések és a támadásokra fogékony környezetek eredményeire. Ezek a sebezhetőségek felhasználhatók, és az adatvesztés, az adatkiszűrése, a ransomware és az erőforrás-visszaélések eléréséhez vezethetnek. A rendszerfrissítések központi telepítéséhez a <a href="https://docs.microsoft.com/azure/automation/automation-update-management">Update Management megoldás segítségével kezelheti a virtuális gépek javításait és frissítéseit</a> . Az Update Management a szoftverek kiadásainak központi telepítésének és karbantartásának szabályozása.</td>
    <td class="tg-lboi"; width=55%>-A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>-A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>-A figyelési ügynököt telepíteni kell a gépekre<br>-Az operációsrendszer-verziót frissíteni kell a Cloud Service-szerepkörökhöz<br>-A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell<br>-A számítógépekre telepíteni kell a rendszerfrissítéseket<br>-A számítógépeket újra kell indítani a rendszerfrissítések alkalmazásához<br>-A Kubernetes-szolgáltatásokat nem sebezhető Kubernetes-verzióra kell frissíteni<br>-A figyelési ügynököt telepíteni kell a virtuális gépekre<br>-Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>-Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonsági rések szervizelése (max. pontszám 6)</p></strong>A biztonsági rések olyan gyengeségek, amelyeket a veszélyforrások használhatnak, így veszélyeztethetik az erőforrások titkosságát, rendelkezésre állását vagy integritását. A <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">sebezhetőségek kezelése</a> csökkenti a szervezeti expozíciót, megerősíti a végpont felületét, növeli a szervezeti rugalmasságot, és csökkenti az erőforrások támadási felületét. A fenyegetések és a biztonsági rések kezelése a szoftveres és biztonsági konfigurációs hibák láthatóságát és a megoldásokra vonatkozó ajánlásokat biztosít.</td>
    <td class="tg-lboi"; width=55%>-A speciális adatbiztonságot engedélyezni kell SQL Database<br>-A Azure Container Registry lemezképek biztonsági réseit szervizelni kell<br>-Az SQL-adatbázisok biztonsági réseit szervizelni kell<br>-A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni<br>-A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon<br>-A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon<br>-A sebezhetőség-felmérési megoldást telepíteni kell a virtuális gépekre</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Titkosítás engedélyezése nyugalmi állapotban (max. pontszám 4)</p></strong>A inaktív adatok <a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">titkosítása</a> adatvédelmet biztosít a tárolt adatok számára. A REST-adatok elleni támadások közé tartozik az adatok tárolására szolgáló hardver fizikai hozzáférésének megszerzésére irányuló kísérlet. Az Azure szimmetrikus titkosítást használ a nagy mennyiségű inaktív adatok titkosításához és visszafejtéséhez. A szimmetrikus titkosítási kulcs használatával titkosíthatja az adattárakat a tárolóba való írás során. Ezt a titkosítási kulcsot is használja a rendszer, hogy visszafejtse az adott adatmennyiséget, mert az readied a memóriában való használathoz. A kulcsokat biztonságos helyen kell tárolni, az identitás-alapú hozzáférés-vezérléssel és a naplózási házirendekkel. Egy ilyen biztonságos hely Azure Key Vault. Ha egy támadó megszerzi a titkosított, de nem a titkosítási kulcsokat, a támadó nem fér hozzá az adatforráshoz a titkosítás megszakítása nélkül.</td>
    <td class="tg-lboi"; width=55%>-A lemezes titkosítást a virtuális gépeken kell alkalmazni<br>-Engedélyezve kell lennie a SQL Database transzparens adattitkosítás<br>-Az Automation-fiók változóit titkosítani kell<br>-Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani<br>-Az SQL Server TDE-védőt a saját kulccsal kell titkosítani</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Adatforgalom titkosítása (max. pontszám 4)</p></strong>Az adatok átvitele az összetevők, a helyszínek és a programok között történik. Azok a szervezetek, amelyek nem védik az adatátvitelt, a támadók, a lehallgatás és a munkamenet-eltérítések érzékenyek. Az adatcseréhez és a VPN-hez ajánlott SSL/TLS protokollt használni. Amikor titkosított adatokat küld egy Azure-beli virtuális gép és egy helyszíni hely között az interneten keresztül, egy virtuális hálózati átjárót (például az <a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">azure VPN Gateway</a> -t) használhat a titkosított forgalom elküldéséhez.</td>
    <td class="tg-lboi"; width=55%>-Az API-alkalmazás csak HTTPS protokollon keresztül érhető el<br>-függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el<br>-Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve<br>-A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell<br>-A webalkalmazás csak HTTPS protokollon keresztül érhető el</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Hozzáférés és engedélyek kezelése (max. pontszám 4)</p></strong>A biztonsági program alapvető része annak biztosítása, hogy a felhasználók a feladataik elvégzéséhez szükséges hozzáféréssel rendelkezzenek, de nem több mint: a <a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">legkevésbé privilegizált hozzáférési modell</a>.<br>A szerepköralapú <a href="https://docs.microsoft.com/azure/role-based-access-control/overview">hozzáférés-vezérléssel (RBAC)</a>rendelkező szerepkör-hozzárendelések létrehozásával szabályozhatja az erőforrásokhoz való hozzáférést. A szerepkör-hozzárendelés három elemet tartalmaz:<br>- <strong>Rendszerbiztonsági tag</strong>: a felhasználó által hozzáférést kérő objektum<br>- <strong>Szerepkör-definíció</strong>: az engedélyeik<br>- <strong>Hatókör</strong>: azon erőforrások készlete, amelyekre az engedélyek érvényesek</td>
    <td class="tg-lboi"; width=55%>-Az elavult fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>-A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>-A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>-Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből (előzetes verzió)<br>-Az előfizetéshez egynél több tulajdonos rendelhető hozzá<br>-Szerepköralapú Access Control (RBAC) használata szükséges a Kubernetes-szolgáltatásokban (előzetes verzió)<br>-Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Biztonsági konfigurációk szervizelése (max. pontszám 4)</p></strong>A helytelenül konfigurált informatikai eszközök nagyobb kockázatot jelentenek a támadásra. Az alapszintű megerősítési műveleteket gyakran elfelejtette az eszközök üzembe helyezése, és a határidőknek teljesülnie kell. A biztonsági beállítások az infrastruktúra bármely szintjén lehetnek: az operációs rendszertől és a hálózati berendezésektől a Felhőbeli erőforrásokhoz.<br>Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. Ha konfigurálta a szervezete számára fontos "megfelelőségi csomagokat" (szabványokat és alapkonfigurációkat), akkor a hiányosságok a CCEID tartalmazó biztonsági javaslatokat, valamint az esetleges biztonsági hatás magyarázatát eredményezik.<br>A gyakran használt csomagok az <a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">Azure Security benchmark</a> és a <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure founds benchmark Version 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>-A pod biztonsági szabályzatokat meg kell határozni a Kubernetes-szolgáltatásokban<br>-A tároló biztonsági konfigurációjában található biztonsági réseket szervizelni kell<br>-A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell<br>-A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell<br>-A figyelési ügynököt telepíteni kell a virtuális gépekre<br>-A figyelési ügynököt telepíteni kell a gépekre<br>-Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>-Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>-A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>-A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Nem engedélyezett hálózati hozzáférés korlátozása (max. pontszám 4)</p></strong>A szervezeten belüli végpontok közvetlen kapcsolódást biztosítanak a virtuális hálózatról a támogatott Azure-szolgáltatásokhoz. Az alhálózatban lévő virtuális gépek kommunikálhatnak az összes erőforrással. Ha korlátozni szeretné az alhálózaton belüli és az erőforrások közötti kommunikációt, hozzon létre egy hálózati biztonsági csoportot, és rendelje hozzá az alhálózathoz. A szervezetek a bejövő és kimenő szabályok létrehozásával korlátozhatják és védhetők a jogosulatlan forgalom ellen.</td>
    <td class="tg-lboi"; width=55%>-A virtuális gépen lévő IP-továbbítást le kell tiltani<br>-A Kubernetes-szolgáltatásokban (előzetes verzió) meg kell határozni a jóváhagyott IP-tartományokat.<br>-ELAVULT A App Services elérését korlátozni kell (előzetes verzió)<br>-ELAVULT A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni<br>-A virtuális gépeket hálózati biztonsági csoporttal kell társítani<br>-A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését<br>-A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését<br>-A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz<br>-A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz<br>-A távoli hibakeresést ki kell kapcsolni függvényalkalmazás<br>-A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz<br>-A hozzáférésnek korlátozva kell lennie az internetre irányuló virtuális gépekkel rendelkező, engedékeny hálózati biztonsági csoportoknak<br>-Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Adaptív alkalmazások vezérlésének alkalmazása (max. pontszám 3)</p></strong>Az adaptív alkalmazás-vezérlés (AAC) egy intelligens, automatizált és teljes körű megoldás, amellyel szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban és a nem Azure-beli gépeken. Emellett segít megerősíteni a gépeket a kártevők ellen.<br>A Security Center gépi tanulással hozza létre a számítógépek egy csoportjának ismert, biztonságos alkalmazásainak listáját.<br>A jóváhagyott alkalmazások listájának innovatív megközelítése a kezelési komplexitás nélkül biztosítja a biztonsági előnyöket.<br>Az AAC különösen fontos az olyan célra kiépített kiszolgálók esetében, amelyeknek adott alkalmazások futtatására van szükségük.</td>
    <td class="tg-lboi"; width=55%>-Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken<br>-A figyelési ügynököt telepíteni kell a virtuális gépekre<br>-A figyelési ügynököt telepíteni kell a gépekre<br>-Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>-Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>-A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Az adatbesorolás alkalmazása (max. pontszám 2)</p></strong>A szervezet adatai érzékenység és üzleti hatás alapján történő besorolásával meghatározhatja és hozzárendelheti az adatértékeket, valamint megadhatja a stratégia és az irányítás alapját.<br>Az <a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">Azure Information Protection</a> segíthet az adatbesorolásban. Titkosítási, identitás-és engedélyezési házirendeket használ az adatok védelme és az adatokhoz való hozzáférés korlátozása érdekében. A Microsoft által használt egyes besorolások nem üzleti, nyilvános, általános, bizalmas és szigorúan bizalmas jellegűek.</td>
    <td class="tg-lboi"; width=55%>-Az SQL-adatbázisokban lévő bizalmas adatokat osztályozni kell (előzetes verzió)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Alkalmazások elleni védelem a DDoS-támadások ellen (max. pontszám 2)</p></strong>Az elosztott szolgáltatásmegtagadási (DDoS) támadások elárasztják az erőforrásokat és az alkalmazások használhatatlanná teszik a szolgáltatást. A <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection standard</a> használatával védje szervezetét a DDOS-támadások három fő típusával:<br>A - <strong>térfogatmérő támadásokkal</strong> a hálózat legitim forgalommal elárasztható. DDoS Protection a standard csökkenti ezeket a támadásokat úgy, hogy automatikusan elnyeli vagy súrolja őket.<br>A - <strong>protokollok elleni támadások</strong> a 3. rétegbeli és a 4. rétegbeli protokollon alapuló gyengeségek kiaknázásával elérhetetlenné teszik a célt. DDoS Protection a standard csökkenti ezeket a támadásokat a kártékony forgalom blokkolásával.<br>Az - <strong>erőforrás (alkalmazás) réteg támadásokat</strong> céloz meg a webalkalmazások csomagjaiban. Védje ezt a típust egy webalkalmazási tűzfallal és DDoS Protection Szabványsal.</td>
    <td class="tg-lboi"; width=55%>-DDoS Protection a standardot engedélyezni kell</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Endpoint Protection engedélyezése (max. pontszám 2)</p></strong>Annak biztosítása érdekében, hogy a végpontok védve legyenek a kártevők ellen, a viselkedési érzékelők gyűjtik és dolgozzák fel az adatokat a végpontok operációs rendszereiből, és elküldik ezeket az adatokat a privát felhőbe elemzés céljából. A biztonsági elemzések nagy adatmennyiségeket, gépi tanulást és más forrásokat használhatnak a fenyegetésekre való reagálásra. A <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft DEFENDER ATP</a> például a fenyegetések felderítését használja a támadási módszerek azonosítására és a biztonsági riasztások előállítására.<br>Security Center a következő Endpoint Protection-megoldásokat támogatja: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, a Windowshoz készült McAfee v10, a Linux és a Sophos v9 for Linux. Ha Security Center észleli ezeket a megoldásokat, az Endpoint Protection telepítésére vonatkozó javaslat már nem fog megjelenni.</td>
    <td class="tg-lboi"; width=55%>-Az Endpoint Protection állapotával kapcsolatos hibákat a virtuálisgép-méretezési csoportokban kell szervizelni<br>-Az Endpoint Protection állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>-Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni<br>-Endpoint Protection-megoldás telepítése virtuális gépeken<br>-A figyelési ügynök állapotával kapcsolatos problémákat fel kell oldani a gépeken<br>-A figyelési ügynököt a virtuálisgép-méretezési csoportokra kell telepíteni<br>-A figyelési ügynököt telepíteni kell a gépekre<br>-A figyelési ügynököt telepíteni kell a virtuális gépekre<br>-Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)<br>-Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)<br>-Endpoint Protection-megoldás telepítése a számítógépekre</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Naplózás és naplózás engedélyezése (max. pontszám 1)</p></strong>A naplózási adat a múltbeli problémák elemzését biztosítja, megelőzheti a lehetséges problémákat, javíthatja az alkalmazások teljesítményét, és automatizálhatja azokat a műveleteket, amelyek egyébként manuálisan történnek.<br>A - <strong>vezérlési és felügyeleti naplók</strong> a <a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> műveletekkel kapcsolatos információkat biztosítanak.<br>Az - <strong>adatsíkok naplói</strong> az Azure-erőforrások használatának részeként kiváltott eseményekről nyújtanak információt.<br>A - <strong>feldolgozott események</strong> információt nyújtanak a feldolgozott elemzett eseményekről/riasztásokról.</td>
    <td class="tg-lboi"; width=55%>-Az SQL Server naplózását engedélyezni kell<br>-A App Services lévő diagnosztikai naplókat engedélyezni kell<br>-A Azure Data Lake Store lévő diagnosztikai naplókat engedélyezni kell<br>-A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell<br>-A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell<br>-A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell<br>-Az Event hub diagnosztikai naplóit engedélyezni kell<br>-A IoT Hub lévő diagnosztikai naplókat engedélyezni kell<br>-A Key Vault lévő diagnosztikai naplókat engedélyezni kell<br>-A Logic Apps lévő diagnosztikai naplókat engedélyezni kell<br>-A keresési szolgáltatásban engedélyezni kell a diagnosztikai naplókat.<br>-A Service Bus lévő diagnosztikai naplókat engedélyezni kell<br>-A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell<br>-A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni<br>-Az SQL naplózási beállításainak a kritikus tevékenységek rögzítéséhez konfigurált műveleti csoportokkal kell rendelkezniük<br>-Az SQL-kiszolgálókat az 90 napnál hosszabb naplózási megőrzési napokon kell konfigurálni.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ajánlott biztonsági eljárások implementálása (max. pontszám 0)</p></strong>Modern biztonsági eljárások "a hálózati peremhálózat megsértésének feltételezése". Emiatt a szabályozás számos ajánlott eljárása az identitások kezelésére koncentrál.<br>A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">Azure Key Vault</a> védi a kulcsokat és a titkos kulcsokat a kulcsok, a. pfx fájlok és a jelszavak titkosításával.<br>A virtuális magánhálózatok (VPN) biztonságos módon férhetnek hozzá a virtuális gépekhez. Ha a VPN-ek nem érhetők el, használjon összetett hozzáférési kódot és kétfaktoros hitelesítést, például az <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">Azure multi-Factor Authentication</a>-t. A kétfaktoros hitelesítés megakadályozza, hogy a rendszer csak a felhasználóneveket és a jelszavakat használja.<br>Az erős hitelesítési és engedélyezési platformok használata egy másik ajánlott eljárás. Az összevont identitások használata lehetővé teszi, hogy a szervezetek delegálják a meghatalmazott identitások kezelését. Ez akkor is fontos, ha az alkalmazottak le vannak zárva, és a hozzáférését vissza kell vonni.</td>
    <td class="tg-lboi"; width=55%>-Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni<br>-Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből<br>-Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon<br>-A Storage-fiókokhoz a tűzfal és a virtuális hálózati konfigurációk hozzáférését korlátozni kell<br>-A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből<br>-Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni<br>-Az Event hub-példány engedélyezési szabályait definiálni kell<br>-A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra<br>-A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra<br>-A SQL Database speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához<br>-A speciális adatbiztonságot engedélyezni kell a felügyelt példányokon<br>-Az összetett veszélyforrások elleni védelem összes típusát engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításainál<br>-A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban<br>-Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban<br>-Az alhálózatokat hálózati biztonsági csoporttal kell társítani<br>-Az összetett veszélyforrások elleni védelem összes típusát engedélyezni kell a kiszolgáló SQL Database speciális biztonsági beállításaiban<br>-Előnézet A Windows Exploit Guard-nek engedélyezve kell lennie <br>-Előnézet Telepíteni kell a vendég konfigurációs ügynököt<br>-A nem internetkapcsolattal rendelkező virtuális gépeket hálózati biztonsági csoportokkal kell védeni</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Biztonságos pontszám – gyakori kérdések

### <a name="why-has-my-secure-score-gone-down"></a>Miért esett le a biztonságos pontszám?
Security Center egy fokozottan biztonságos pontszámra váltott, amely a pontszám kiszámításának módját is magában foglalja. Most meg kell oldania az erőforrásra vonatkozó összes javaslatot a pontok fogadására. A pontszámok a 0-10-es méretre is módosultak.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Ha egy biztonsági ellenőrzésben csak három négy javaslat közül hármat találok, akkor a biztonságos pontszámom módosul?
Nem. Ez addig nem változik meg, amíg az egyetlen erőforráshoz tartozó összes ajánlást nem szervizeli. Egy vezérlőelem maximális pontszámának lekéréséhez az összes erőforrásra vonatkozóan az összes javaslatot szervizelni kell.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Továbbra is elérhető a biztonságos pontszám korábbi tapasztalata? 
Nem. Egy ideig, amikor egymás mellett szaladtak, hogy megkönnyítsék az átmenetet. Az előző modell már elavult. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Ha egy javaslat nem alkalmazható a számomra, és letiltom a szabályzatban, akkor a biztonsági ellenőrzésem teljesül, és a biztonságos pontszám frissítve lett?
Igen. Javasoljuk, hogy tiltsa le a javaslatokat, ha azok nem alkalmazhatók a környezetben. A konkrét javaslatok letiltásával kapcsolatos utasításokért lásd: [biztonsági szabályzatok letiltása](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Ha egy biztonsági vezérlő nulla pontot biztosít a biztonságos pontszám felé, figyelmen kívül hagyhatom?
Bizonyos esetekben a vezérlőelem maximális pontszáma nullánál nagyobb lesz, de a hatás nulla. Ha az erőforrások kijavításának növekményes pontszáma elhanyagolható, a rendszer nulla értékűre kerekíti. Ne hagyja figyelmen kívül ezeket az ajánlásokat, mivel azok továbbra is biztonsági javítást tesznek lehetővé. Az egyetlen kivétel a "további ajánlott eljárás" vezérlő. Szervizelését ezekkel az ajánlásokkal nem növeli a pontszámát, de a teljes biztonságot is növeli.

## <a name="next-steps"></a>További lépések

Ez a cikk a biztonságos pontszámot és az általa bevezetett biztonsági ellenőrzéseket ismerteti. A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket:

- [Tudnivalók a javaslatok különböző elemeiről](security-center-recommendations.md)
- [Útmutató a javaslatok megoldásához](security-center-remediate-recommendations.md)
