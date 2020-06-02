---
title: A Azure Security Center kibocsátási megjegyzései
description: A Azure Security Center újdonságait és változásait ismertető leírás.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 3fa9de1057160340fdf10d72809a104cae985162
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248158"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonságai

Az Azure Security aktív fejlesztés alatt áll, és folyamatosan fejleszti a fejlesztéseket. Ha naprakészen szeretne maradni a legújabb fejleményekkel, az oldal a következő információkat tartalmazza:

- Új funkciók
- Hibajavítások
- Elavult funkciók

Ez az oldal rendszeresen frissül, ezért gyakran újra felkeresik. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Security Center újdonságait](release-notes-archive.md).


## <a name="may-2020"></a>2020. május


### <a name="alert-suppression-rules-preview"></a>Riasztás-elnyomási szabályok (előzetes verzió)

Ez az új funkció (jelenleg előzetes verzióban érhető el) segít csökkenteni a riasztások fáradtságát. Szabályok használatával automatikusan elrejtheti azokat a riasztásokat, amelyek ismerten ártalmatlanok vagy a szervezeten belüli szokásos tevékenységekkel kapcsolatosak. Ez lehetővé teszi, hogy a leginkább releváns fenyegetésekre koncentráljon. 

Az engedélyezett letiltási szabályoknak megfelelő riasztások továbbra is létrejönnek, de az állapotukat a rendszer elutasítja. Megtekintheti az állapotot a Azure Portalban, vagy hozzáférhet a Security Center biztonsági riasztásokhoz.

A mellőzési szabályok határozzák meg azokat a feltételeket, amelyeknek a riasztásait automatikusan el kell utasítani. Általában letiltási szabályt kell használnia a következőkhöz:

- hamis pozitívként azonosított riasztások letiltása

- a túl gyakran kiváltott riasztások mellőzése

További információ a [riasztások Azure Security Center fenyegetés elleni védelemből való letiltásáról](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A virtuális gépek sebezhetőségi felmérése mostantól általánosan elérhető

A Security Center standard szintű csomagja mostantól egy integrált sebezhetőségi felmérést is tartalmaz a virtuális gépekhez, felár nélkül. Ezt a bővítményt a Qualys működteti, de az eredményeket közvetlenül visszaküldi Security Centerra. Nincs szüksége Qualys-licencre, vagy akár Qualys-fiókra is – minden a Security Centeron belül zökkenőmentesen kezelhető.

Az új megoldás segítségével folyamatosan ellenőrizheti a virtuális gépeket, és megtalálhatja a biztonsági réseket, és bemutathatja az eredményeket a Security Centerban. 

A megoldás üzembe helyezéséhez használja az új biztonsági javaslatot:

"A beépített sebezhetőségi felmérési megoldás engedélyezése a virtuális gépeken (Qualys-alapú)"

További információ a [Security Center a virtuális gépek integrált sebezhetőségi felméréséről](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Az igény szerinti (JIT) virtuális gépekhez való hozzáférés módosításai

Security Center tartalmaz egy opcionális szolgáltatást a virtuális gépek felügyeleti portjainak a megóvásához. Ez védelmet nyújt a találgatásos támadás leggyakoribb formája ellen.

Ez a frissítés a következő módosításokat hajtja végre a szolgáltatásban:

- A rendszer azt ajánlja, hogy a JIT engedélyezése egy virtuális gépen legyen átnevezve. Korábban "igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken." most már: "a virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni."

- A javaslat úgy lett beállítva, hogy csak akkor legyen aktiválva, ha vannak nyitott felügyeleti portok.

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Egyéni javaslatok lettek áthelyezve egy külön biztonsági vezérlőbe

A fokozott biztonságú pontszámmal bevezetett biztonsági vezérlők egyike az "ajánlott biztonsági eljárások megvalósítása". Az előfizetésekhez létrehozott egyéni javaslatok automatikusan ebbe a vezérlőbe kerülnek. 

Ahhoz, hogy könnyebben megtalálja az egyéni javaslatokat, áthelyezte őket egy dedikált biztonsági vezérlőbe, "egyéni javaslatok". Ez a vezérlő nem befolyásolja a biztonságos pontszámot.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>A bekapcsolva beállítással megtekintheti a vezérlőkre vagy a lapos listára vonatkozó javaslatokat

A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai. A sebezhető támadási felületeket tükrözik. A vezérlők a biztonsági javaslatok összessége, amelyek útmutatást nyújtanak a javaslatok megvalósításához.

Ha szeretné azonnal megtekinteni, hogy a szervezet milyen jól védi az egyes támadási felületet, tekintse át az egyes biztonsági vezérlők pontszámait.

Alapértelmezés szerint a javaslatok a biztonsági vezérlőkben jelennek meg. Ebből a frissítésből listát is megjeleníthet. Ha az érintett erőforrások állapotának megfelelően rendezi az egyszerű listát, használja az új "Group By Controls" (csoportosítás a vezérlők szerint) lehetőséget. A váltógomb a portálon megjelenő lista felett van.

A biztonsági vezérlők – és ez a váltás – az új biztonsági pontszám részét képezik. Ne feledje, hogy küldje el nekünk a visszajelzéseit a portálon belülről.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.


### <a name="expanded-security-control-implement-security-best-practices"></a>Kibővített biztonsági szabályozás – az ajánlott biztonsági eljárások megvalósítása 

A fokozott biztonságú pontszámmal bevezetett biztonsági vezérlők egyike az "ajánlott biztonsági eljárások megvalósítása". Ha egy javaslat ebben a vezérlőben van, az nem érinti a biztonságos pontszámot. 

Ezzel a frissítéssel három javaslatot helyeztek el azokról a vezérlőkről, amelyekben eredetileg elhelyezték őket, és az ajánlott eljárások szabályozásával. Azért tettük ezt a lépést, mert azt állapítottuk meg, hogy a három javaslat kockázata alacsonyabb, mint eredetileg gondolták.

Emellett két új javaslat is be lett vezetve, és hozzá lett adva a vezérlőhöz.

Az áthelyezett három javaslat a következőket tartalmazza:

- Az **MFA-t engedélyezni kell az előfizetés olvasási engedéllyel rendelkező fiókjain** (eredetileg az "MFA engedélyezése" vezérlőben)
- Az **olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)
- Az **előfizetéshez legfeljebb 3 tulajdonost kell kijelölni** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)

A vezérlőhöz hozzáadott két új javaslat a következő:

- **[Előzetes verzió] a vendég konfigurációs ügynöknek telepítve kell lennie** – [Azure Policy vendég konfigurációjának](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) használatával a virtuális gépeken belül láthatóságot biztosít a kiszolgáló és az alkalmazás beállításai számára (csak Windows).

- **[Előzetes verzió] a Windows kiaknázási őr engedélyezve kell legyen** – a Windows Defender Exploit Guard kihasználja a Azure Policy vendég konfigurációs ügynököt. A kiaknázási Gárda négy összetevőből áll, amelyek célja, hogy zárolják az eszközöket a különböző támadási vektorok és a kártevők elleni támadásokban leggyakrabban használt viselkedések blokkolásával, miközben lehetővé teszik a vállalatok számára a biztonsági kockázatok és a termelékenységi követelmények kiegyensúlyozását (csak Windows).

További információ a Windows Defender Exploit Guard-védelemről a biztonsági [rés kiaknázására szolgáló szabályzat létrehozása és üzembe helyezése című](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)témakörben.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Az egyéni metaadatokkal rendelkező egyéni szabályzatok már általánosan elérhetők

Az egyéni szabályzatok mostantól a Security Center javaslatok, a biztonságos pontszám és a szabályozási megfelelőségi szabványok irányítópultjának részét képezik. Ez a funkció mostantól általánosan elérhető, és lehetővé teszi a szervezet biztonsági értékelésének kiterjesztését Security Center. 

Hozzon létre egy egyéni kezdeményezést az Azure Policy-ben, adja hozzá a szabályzatokat, és helyezze be Azure Security Centerba, és jelenítse meg javaslatként.

Most hozzáadjuk az egyéni ajánlási metaadatok szerkesztésének lehetőségét is. A metaadat-beállítások közé tartozik a súlyosság, a Szervizelési lépések, a fenyegetésekkel kapcsolatos információk és egyebek.  

További információk az [Egyéni javaslatok részletes információkkal történő növeléséről](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Összeomlási memóriakép-elemzési képességek migrálása a fájlok közötti támadás észlelése érdekében 

Integráljuk a Windows Crash dump Analysis (CDA) észlelési funkcióit a [fájlok közötti támadás észlelésére](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). A fájlok közötti támadás észlelésének elemzése a következő biztonsági riasztások továbbfejlesztett verzióit biztosítja a Windows rendszerű gépekhez: kód befecskendezése felderített, álcázott Windows-modul, észlelt Héjkód, és gyanús kódrészletet észlelt.

Az áttérés néhány előnye:

- **Proaktív és kellő időben kártevő szoftverek észlelése** – a CDA megközelítése arra vár, hogy összeomlik, majd futtatja az elemzést a kártékony összetevők kereséséhez. A fájlok közötti támadás észlelésével a memóriában tárolt fenyegetések proaktív módon azonosíthatók a futás közben. 

- Bővített **riasztások** – a fájlokkal nem rendelkező támadások észlelésével kapcsolatos biztonsági riasztások közé tartoznak a CDA-ból nem elérhető bővítések, például az aktív hálózati kapcsolatok adatai. 

- **Riasztások összesítése** – ha a CDA több támadási mintát észlelt egyetlen összeomlási memóriaképen belül, több biztonsági riasztást váltott ki. A fájl nélkül történő támadás észlelése az összes azonosított támadási mintát egyetlen riasztásba ötvözi, így nem kell több riasztást összekapcsolni.

- A **log Analytics munkaterületre vonatkozó kisebb követelmények** – a potenciálisan bizalmas adatokat tartalmazó összeomlási memóriaképek többé nem lesznek feltöltve a log Analytics-munkaterületre.



## <a name="april-2020"></a>2020. április

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>A dinamikus megfelelőségi csomagok már általánosan elérhetők

A Azure Security Center szabályozási megfelelőségi irányítópult mostantól tartalmazza a **dinamikus megfelelőségi csomagokat** (mostantól általánosan elérhető) a további iparági és szabályozási szabványok nyomon követéséhez.

A dinamikus megfelelőségi csomagok hozzáadhatók az előfizetéshez vagy a felügyeleti csoportjához a Security Center biztonsági házirend lapról. Ha standard vagy teljesítménytesztet készített elő, a szabvány a szabályzatoknak megfelelő megfelelőségi irányítópulton jelenik meg, amely az értékelésként leképezett összes megfelelőségi adattal együtt szerepel. A rendszer letölthetővé teszi az előkészített szabványok bármelyikének összegző jelentését.

Most hozzáadhat olyan szabványokat, mint például a:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF – v2020**
- **Egyesült Királyság hivatalos és egyesült királysági NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (új)** (az Azure CIS 1.1.0 teljes körű képviselete)

Emellett a közelmúltban hozzáadta az **Azure biztonsági teljesítménytesztet**, a Microsoft által készített Azure-specifikus iránymutatásokat a biztonsági és megfelelőségi ajánlott eljárásokhoz a közös megfelelőségi keretrendszerek alapján. Az irányítópulton további szabványok is támogatottak lesznek, amint azok elérhetővé válnak.  
 
További információ [a szabványok készletének testreszabásáról a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>A Azure Security Center ingyenes szinten már szerepelnek a személyazonossággal kapcsolatos javaslatok

A Azure Security Center ingyenes szinten már általánosan elérhetők az identitásra és a hozzáférésre vonatkozó biztonsági javaslatok. Ez annak a erőfeszítésnek a részét képezi, hogy a Cloud Security testtartás-felügyeleti (CSPM) funkciók ingyenesek legyenek. Eddig ezek a javaslatok csak a standard díjszabási szinten voltak elérhetők.

Az identitásra és a hozzáférésre vonatkozó javaslatok például a következők:

- "A többtényezős hitelesítést engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon."
- "Az előfizetéshez legfeljebb három tulajdonost kell kijelölni."
- "Az elavult fiókokat el kell távolítani az előfizetésből."

Ha előfizetése van az ingyenes díjszabási szinten, a biztonsági pontszámok hatással lesznek a változásokra, mivel soha nem értékelték a személyazonosságát és a hozzáférés biztonságát.

További információ az [identitással és a hozzáférési javaslatokkal](recommendations-reference.md#recs-identity)kapcsolatban.

További információ az [identitás és a hozzáférés figyeléséről](security-center-identity-access.md).


## <a name="march-2020"></a>2020. március

### <a name="workflow-automation-is-now-generally-available"></a>A Munkafolyamat-automatizálás mostantól általánosan elérhető

A Azure Security Center munkafolyamat-automatizálási funkciója már általánosan elérhető. Ezzel a megoldással automatikusan aktiválhatja Logic Apps biztonsági riasztásokra és javaslatokra. Emellett manuális eseményindítók is elérhetők a riasztásokhoz és az összes olyan javaslathoz, amelynél elérhető a gyors javítás lehetőség.

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek megválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érintett felek értesítését, a módosítási felügyeleti folyamat indítását és az adott szervizelési lépések alkalmazását. A biztonsági szakértők azt ajánlják, hogy az eljárások több lépésének automatizálására legyen lehetőség. Az Automation csökkenti a terhelést, és javíthatja a biztonságot azáltal, hogy a folyamat lépéseinek gyors, konzisztens és az előre meghatározott követelmények szerint kell történnie.

A munkafolyamatok futtatásának automatikus és manuális Security Center funkcióival kapcsolatos további információkért lásd: [munkafolyamat-automatizálás](workflow-automation.md).

További információ a [Logic apps létrehozásáról](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center integrációja a Windows felügyeleti központtal

Mostantól közvetlenül a Azure Security Center helyezheti át a helyszíni Windows-kiszolgálókat a Windows felügyeleti központból. A Security Center ezután az egyetlen üvegtábla lesz a Windows felügyeleti központ összes erőforrásának (beleértve a helyszíni kiszolgálókat, a virtuális gépeket és a további Pásti munkaterheléseket) biztonsági információinak megtekintésére.

Miután áthelyezett egy kiszolgálót a Windows felügyeleti központból a Azure Security Centerba, a következőket teheti:

- Tekintse meg a biztonsági riasztásokat és javaslatokat a Windows felügyeleti központ Security Center bővítményében.
- Tekintse meg a biztonsági állapotot, és kérje le a Windows felügyeleti központ felügyelt kiszolgálóinak további részletes információit a Azure Portalon (vagy egy API-n keresztül) található Security Centerban.

További információ a [Azure Security Center integrálásáról a Windows felügyeleti központba](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Az Azure Kubernetes szolgáltatás védelme

Azure Security Center kibővíti a tárolók biztonsági funkcióit az Azure Kubernetes szolgáltatás (ak) védelmére.

A népszerű, nyílt forráskódú platform Kubernetes olyan széles körben vezették be, hogy most már iparági szabványnak számít a tárolók összehangolása. Ez a széleskörű megvalósítás ellenére még mindig hiányzik a Kubernetes-környezet biztonságossá tételével kapcsolatos megértés. Egy tároló alkalmazás támadási felületének védelme szaktudást igényel annak biztosításához, hogy az infrastruktúra biztonságosan legyen konfigurálva, és folyamatosan figyelje a potenciális fenyegetéseket.

A Security Center védelmi szolgáltatás a következőket tartalmazza:

- **Felderítés és láthatóság** – a felügyelt AK-példányok folyamatos felderítése a Security Center regisztrált előfizetéseken belül.
- **Biztonsági javaslatok** – gyakorlati ajánlások az AK-ra vonatkozó biztonsági eljárások betartása érdekében. Ezek a javaslatok a biztonságos pontszám részét képezik, így biztosítva, hogy a szervezete biztonsági helyzete része legyen. Egy példa arra, hogy a "szerepköralapú hozzáférés-vezérlést kell használni a Kubernetes Service-fürthöz való hozzáférés korlátozásához".
- **Veszélyforrások elleni védelem** – az AK üzembe helyezésének folyamatos elemzése révén Security Center riasztást küld, amely a gazdagép és az AK-fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységgel kapcsolatos.

További információ az [Azure Kubernetes Services és a Security Center integrálásáról](azure-kubernetes-service-integration.md).

További információ [a Security Center tároló biztonsági funkcióival](container-security.md)kapcsolatban.


### <a name="improved-just-in-time-experience"></a>Továbbfejlesztett, igény szerinti élmény

A felügyeleti portok védelmét biztosító, a Azure Security Center funkcióit, műveleteit és felhasználói felületét a következőképpen fejlesztettük ki: 

- **Indoklási mező** – ha egy virtuális GÉPHEZ (VM) való hozzáférést kér a Azure Portal igény szerinti oldalán, egy új opcionális mező is elérhető, amely a kérelem indoklását adja meg. A mezőben megadott információ nyomon követhető a tevékenység naplójában. 
- A **redundáns igény szerinti (JIT) szabályok automatikus karbantartása** – amikor egy JIT-házirendet frissít, a rendszer automatikusan futtat egy karbantartó eszközt a teljes szabályrendszert érvényességének ellenőrzéséhez. Az eszköz eltéréseket keres a szabályzat szabályai és a NSG lévő szabályok között. Ha a tisztítási eszköz nem megfelelőnek találja, akkor meghatározza az okot, és ha ez biztonságos, eltávolítja a már nem szükséges beépített szabályokat. A tisztább soha nem törli a létrehozott szabályokat. 

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Két biztonsági javaslat elavult webalkalmazásokhoz

A webalkalmazásokkal kapcsolatos két biztonsági javaslat elavult: 

- A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni.
    (Kapcsolódó szabályzat: a IaaS webalkalmazásaihoz tartozó NSG-szabályokat meg kell erősíteni)

- A App Serviceshoz való hozzáférést korlátozni kell.
    (Kapcsolódó házirend: a App Services elérését korlátozni kell [előzetes verzió])

Ezek a javaslatok többé nem jelennek meg a javaslatok Security Center listájában. A kapcsolódó házirendeket a rendszer nem fogja tartalmazni a "Security Center default" nevű kezdeményezésben.

További információ a [biztonsági javaslatokról](recommendations-reference.md).



## <a name="february-2020"></a>2020. február

### <a name="fileless-attack-detection-for-linux-preview"></a>Fájl alapú támadás észlelése Linux rendszerhez (előzetes verzió)

Mivel a támadók egyre nagyobb mértékben alkalmaznak legrejtettebb módszereket az észlelés elkerülésére, Azure Security Center a Windows rendszereken kívül kiterjeszti a fájl-alapú támadási észlelést Linux rendszeren. A szoftveres biztonsági réseket rosszindulatú támadások okozják, a kártékony hasznos adatokat a jóindulatú rendszerfolyamatok szúrják be, és elrejtik a memóriában. Ezek a technikák:

- a kártevők nyomkövetésének csökkentése vagy megszüntetése lemezen
- nagy mértékben csökkentheti a lemezes kártevő-ellenőrzési megoldások általi észlelés esélyét

Ennek a fenyegetésnek a ellensúlyozására a 2018-es, a Windows rendszerhez készült, a fájloktól mentesen észlelhető támadás észlelését Azure Security Center, és a Linuxon is kiterjesztette a fájlokkal való támadás észlelését. 


## <a name="january-2020"></a>2020. január

### <a name="enhanced-secure-score-preview"></a>Bővített biztonságos pontszám (előzetes verzió)

A Azure Security Center biztonságos pontszám funkciójának továbbfejlesztett verziója már előzetes verzióban érhető el. Ebben a verzióban a több javaslat olyan biztonsági vezérlőkbe van csoportosítva, amelyek jobban tükrözik a sebezhető támadási felületet (például a felügyeleti portokhoz való hozzáférés korlátozása).

Ismerkedjen meg a biztonsági pontszám változásaival az előzetes verzió fázisában, és határozza meg azokat a további szervizeléseket, amelyek segítséget nyújtanak a környezet további védelméhez.

További információ a [Azure Security Center továbbfejlesztett biztonságos pontszámáról (előzetes verzió)](secure-score-security-controls.md).