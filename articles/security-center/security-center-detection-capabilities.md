<properties
   pageTitle="Észlelési funkciók az Azure Security Centerben | Microsoft Azure"
   description="Ebből a dokumentumból megismerheti, hogyan működnek az Azure Security Center észlelési funkciót."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>


# Az Azure Security Center észlelési funkciói
Ez a dokumentum az Azure Security Center speciális észlelési funkciót ismerteti, amelyek segítségével azonosíthatja a Microsoft Azure-erőforrásokra irányuló aktív fenyegetéseket, és betekintést nyújt a gyors reagálás lehetőségeibe.

> [AZURE.NOTE] A speciális észlelési funkciók az Azure Security Center Standard rétegben érhetők el. Igénybe veheti a 90 napos ingyenes próbaverziót. A választott tarifacsomagról a [Biztonsági szabályzat](security-center-policies.md) beállításainál frissíthet. A díjszabással kapcsolatos további tudnivalókért keresse fel a [Security Center oldalát](https://azure.microsoft.com/pricing/details/security-center/). 


## Reagálás a napjainkban felmerülő fenyegetésekre
Az elmúlt 20 évben jelentős változásokat figyelhettünk meg a fenyegetések területén. Korábban a vállalatoknak általában csak a webhelyük arculatának lerombolása miatt kellett aggódniuk, mert a támadókat főleg az érdekelte, hogy kipróbálják, „mit is lehetne tenni”. A mai támadók sokkal bonyolultabban és szervezettebben cselekszenek. Gyakran konkrét pénzügyi és stratégiai célokat követnek. Ezenkívül több erőforrás áll a rendelkezésükre, mert akár az is előfordulhat, hogy egy nemzetállam vagy a szervezett bűnözés biztosítja számukra az anyagi hátteret.

Ezzel a megközelítéssel a támadók eddig példátlan professzionális szintet értek el. Már nem a webhelyek megrongálása érdekli őket. Információt, pénzügyi adatokat és személyes adatokat akarnak ellopni, mert mindezekkel pénzt tudnak szerezni a nyitott piacon, vagy meghatározott üzleti, politikai és katonai pozíciókat tudnak kihasználni. Még nagyobb aggodalomra ad okot, hogy a pénzügyi célokat követő támadókon kívül olyanokkal is számolni kell, akik azért törnek be a hálózatokba, hogy kárt okozzanak az infrastruktúrában és a felhasználóknak.

Erre reagálva a szervezetek gyakran olyan adott pontokon működő megoldásokat alkalmaznak, amelyek a vállalat peremsávját vagy végpontjait védelmezik a támadások ismert jelei után kutatva. Ezek a megoldások általában nagy mennyiségű, alacsony megbízhatósági szintű riasztást eredményeznek, és ezeket a biztonsági elemzőknek szét kell válogatniuk és ki kell vizsgálniuk. A legtöbb szervezetnek nincs ideje és megfelelő szaktudása ahhoz, hogy reagáljon ezekre a riasztásokra, ezért sok probléma megoldatlan marad.  Időközben a támadók továbbfejlesztették a módszereiket, hogy ki tudják játszani az aláírás-alapú védelem egyes megoldásait, és hogy [alkalmazkodjanak a felhőbeli környezetekhez](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Az új módszerek a fenyegetések egyre bővülő körének gyorsabb felismerését, azonnali észlelését és kiiktatását igénylik. 

## Hogyan észleli az Azure Security Center a fenyegetéseket és hogyan reagál rájuk?

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. Rendelkezésükre áll a telemetriai adatok kiterjedt halmaza, amelyet a Microsoft a globális felhőbeli és helyszíni jelenléte során gyűjtött össze. Ezen adatkészletek széles körű és változatos gyűjteményére alapozva a Microsoft fel tudja fedezni az új támadási mintákat és trendeket a helyszíni fogyasztói és a vállalati termékeiben, valamint az online szolgáltatásaiban. Ennek eredményeképpen a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, igazodva a támadók újabb és egyre összetettebb biztonságirés-kihasználási megoldásaihoz. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben növekvő körével. 

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

![A Security Center adatgyűjtése és adatábrázolása](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttörést jelentő eredményeinek felhasználásával értékelhetők ki az események a teljes felhőalapú hálóban, és így olyan fenyegetések is észlelhetők, amelyeket manuális módszerekkel lehetetlen volna azonosítani, és lehetőség van a támadások fejlődésének előrejelzésére. Ezek a biztonsági elemzések a következők: 

- **Integrált fenyegetésészlelési intelligencia**: megkeresi az ismert kártékony elemeket a Microsoft-termékek és -szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC), valamint külső források globális fenyegetésészlelési intelligenciájának alkalmazásával.
- **Működés elemzése**: az ismert minták alapján deríti fel a rosszindulatú működést. 
- **Rendellenességek észlelése**: statisztikai adatokon alapuló profilok használatával összeállít egy kiinduló alapkonfigurációt. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.


### Fenyegetésészlelési intelligencia
A Microsoft rendkívül nagy mennyiségű adattal rendelkezik a globális fenyegetésészlelési intelligencia keretein belül. Telemetriai adatok gyűjt több forrásból, ilyen például az Azure, az Office 365, a Microsoft CRM online, a Microsoft Dynamics AX, az outlook.com, az MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC). A kutatók ezenkívül olyan adatokat is kapnak a fenyegetésészleléshez, amelyeket a fő felhőszolgáltatók osztanak meg, és előfizetnek a harmadik felek fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra is. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről. Néhány példa:

- **Kimenő kommunikáció kártékony IP-címre**: az ismert botnet vagy darknet felé irányuló kimenő forgalom arra utal, hogy az erőforrást valószínűleg feltörték, és egy támadó parancsokat próbál végrehajtani a rendszerben vagy adatokat próbál kiszűrni a rendszerből. Az Azure Security Center összehasonlítja a hálózati forgalmat a Microsoft fenyegetéseket tartalmazó globális adatbázisával, és figyelmezteti Önt, ha rosszindulatú IP-címre irányuló kommunikációt észlel.

## Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt. Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. Az Azure Security Center a működés elemzésével tudja azonosítani a feltört erőforrásokat a virtuális gépek naplóinak, a virtuális hálózati eszközök naplóinak, a háló naplóinak, az összeomlási memóriaképeknek és az egyéb forrásoknak az elemzésével. 

Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket. Néhány példa:

- **Gyanús folyamat-végrehajtás**: a támadók különböző módszereket vetnek be a kártékony szoftverek észrevétlen végrehajtásához. A támadó például a megbízható rendszerfájlok nevével megegyező nevet ad a kártevőnek, de más helyen tárolja azt, olyan nevet választ, amely nagyon hasonlít egy ártalmatlan fájl nevére, vagy álcázza a fájl valódi kiterjesztését. A Security Center modelljei feldolgozzák a működéseket, és megfigyelik a folyamat-végrehajtást az ezekhez hasonló kakukktojások felismeréséhez.  
- **Rejtett károkozó és veszélyeztető kísérletek**: a speciálisan kifejlesztett kártevők ki tudják játszani a hagyományos kártevőirtó termékeket azzal, hogy soha nem írnak a lemezre, és nem titkosítják a lemezen tárolt szoftverösszetevőket.  Az ilyen kártevők memóriaelemzéssel ismerhetők fel, mivel a kártevőknek nyomot kell hagyniuk maguk után a memóriában ahhoz, hogy működni tudjanak. Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában.  Az összeomlási memóriaképben található memóriarész elemzésével az Azure Security Center észlelni tudja az olyan technikákat, amelyekkel a kártevők kihasználják a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz, és a feltört gépen maradnak elrejtőzve, anélkül, hogy befolyásolnák a számítógép teljesítményét.
- **Oldalirányú mozgás és belső felderítés**: ahhoz, hogy bennmaradjanak a feltört hálózatban, és megkereshessék és összegyűjthessék az értékes adatokat, a támadók gyakran oldalirányban próbálnak továbbhaladni a feltört gépről a többi gépre az adott hálózaton belül. A Security Center megfigyeli a feldolgozási és a bejelentkezési tevékenységeket, hogy felfedezhesse a támadók hálózaton belüli terjeszkedési kísérleteit, például a távoli parancsvégrehajtással megkísérelt hálózatszondázást és a fiókok számbavételét.
- **Rosszindulatú PowerShell-parancsfájlok**: a támadók a PowerShell felhasználásával hajtják végre a különféle szándékú rosszindulatú kódokat a célba vett virtuális gépeken. A Security Center megvizsgálja a PowerShell tevékenységeit, hogy megtalálja a gyanús tevékenységek nyomait. 
- **Kimenő támadások**: a támadók gyakran felhőbeli erőforrásokat céloznak meg azzal a szándékkal, hogy ezeket az erőforrásokat használják fel a további támadások kibontakoztatásához. A feltört virtuális gépek például felhasználhatók más virtuális gépek elleni találgatásos támadásokra, levélszemét küldésére, vagy nyitott portok és más eszközök keresésére az interneten. A hálózati forgalomra irányított gépi tanulás alkalmazásával a Security Center észlelni tudja, ha a szokásosnál nagyobb mértékű a kimenő hálózati kommunikáció. A levélszemét esetében a Security Center ezenkívül összehasonlítja a szokatlan e-mail-forgalmat az Office 365 vonatkozó adatgyűjteményével annak megállapításához, hogy az e-mailek kártékonyak-e vagy egy normál e-mail-kampányhoz tartoznak-e.  

### Rendellenességek észlelése

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak. Például:

- **Bejövő RDP/SSH találgatásos támadások**: az üzemelő példányokon lehetnek nagy forgalmú virtuális gépek, amelyeken nagy mennyiségű bejelentkezést hajtanak végre mindennap, és olyan virtuális gépek is, amelyeken nagyon kevés bejelentkezés történik, vagy egyáltalán nincs ilyen tevékenység. Az Azure Security Center megállapítja a bejelentkezési alaptevékenységet ezeknél a virtuális gépeknél, és gépi tanulás alkalmazásával meghatározza, hogy mi az, ami kívül esik a normál bejelentkezési tevékenységen. Ha a bejelentkezések száma, időpontja, helye vagy más jellemzője szignifikánsan különbözik az alapkonfigurációtól, akkor a Security Center riasztást jeleníthet meg. Ebben az esetben is gépi tanulás alapján határozza meg, hogy mi számít szignifikáns eltérésnek.

## A fenyegetésekre vonatkozó intelligencia folyamatos figyelése

Az Azure Security Center biztonsági kutatással és adattudománnyal foglalkozó csapatai folyamatosan figyelik a fenyegetések területén bekövetkező változásokat. Ide tartoznak a következők:

- **Fenyegetésekre vonatkozó intelligencia figyelése**: a fenyegetésekre vonatkozó intelligencia a meglévő és felmerülő fenyegetésekkel kapcsolatos mechanizmusokat, jelzőket, következtetéseket és műveleti tanácsokat foglal magában. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.
- **Jelmegosztás**: megosztjuk és elemezzük a Microsoft felhőbeli és helyszíni szolgáltatásokat, kiszolgálókat és végponti eszközök tartalmazó kiterjedt portfóliójához kapcsolódó biztonsági csoportok megfigyeléseit. 
- **A Microsoft biztonsági szakértői**: folyamatos kapcsolatot tartunk a Microsoft csapataival, amelyek tagjai meghatározott biztonsági szakterületekkel foglalkoznak, például igazságügyi szakértői tevékenységekkel és a webes támadások észlelésével.
- **Az észlelés finomhangolása**: a biztonsági szakértők az ügyfelek valós adatkészletein futtatják az algoritmusokat, és az ügyfelekkel együttműködve ellenőrzik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az egyesített erőfeszítések új és hatékonyabb észleléseket eredményeznek, amelyeknek azonnal hasznát veheti anélkül, hogy bármit is tennie kellene.

## Lásd még:
Ebben a dokumentumban megismerkedhetett az Azure Security Center észlelési funkcióinak működésével. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
- [Biztonsági riasztások típus szerint az Azure Security Centerben](security-center-alerts-type.md)
- [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
- [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Sep16_HO4-->


