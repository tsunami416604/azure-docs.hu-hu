<properties
   pageTitle="Útmutató a Security Center tervezéséhez és működtetéséhez | Microsoft Azure "
   description="Ez a dokumentum segít az Azure Security Center bevezetése előtti tervezésben, valamint megismerteti a napi műveletekhez tartozó szempontokkal."
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
   ms.date="04/25/2016"
   ms.author="yurid"/>
 
# Útmutató az Azure Security Center tervezéséhez és működtetéséhez
Ez az útmutató olyan informatikusok, megoldástervezők, adatbiztonsági elemzők és felhőszolgáltatás-rendszergazdák számára készült, akik az Azure Security Center egész vállalatra kiterjedő bevezetését tervezik.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.

## Az Azure Security Center áttekintése
Az Azure Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket. 

Az [Azure Security Center frequently asked questions (FAQ)](security-center-faq.md) (Az Azure Security Centerhez kapcsolódó gyakori kérdések) témakörben számos általánosan előforduló kérdést és választ talál, amelyek szintén hasznosak lehetnek a tervezési fázisban.

## Tervezési útmutató
Az útmutatóban ismertetett lépések és feladatok segítségével vállalata biztonsági igényeinek és felhőfelügyeleti modelljének megfelelően optimalizálhatja az Azure Security Center használatát. Annak érdekében, hogy a lehető legnagyobb mértékben kihasználhassa az Azure Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan fogják használni a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos fejlesztésre, működésre, ellenőrzésre, irányításra és incidensmegoldásra vonatkozó követelményeket. Az Azure Security Center használatának tervezésekor fordítson különös figyelmet a következő területekre:

- Biztonsági szerepkörök és hozzáférés-vezérlés
- Biztonsági szabályzatok és javaslatok
- Adatgyűjtés és -tárolás
- A biztonság folyamatos ellenőrzése 
- Incidensmegoldás

A következő részekben bemutatjuk, hogyan alkothatja meg a fenti területekre vonatkozó tervet, és hogyan alkalmazhatja a javasolt lépéseket saját környezetének megfelelően. 

## Biztonsági szerepkörök és hozzáférés-vezérlés 

A vállalat méretétől és felépítésétől függően előfordulhat, hogy sok ember és csoport is használni fogja a Security Centert a különböző biztonsági feladatok elvégzésére. Az alábbi képen néhány képzeletbeli személyt mutatunk be, és ismertetjük a szerepköreiket és biztonsági feladataikat:

![Szerepkörök](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01.png)

Ezek a személyek az Azure Security Center segítségével teljesítik feladataikat. Példa:

**Bálint (felhőbeli számítási feladatok felelőse)**

- Megtekinti, illetve teljesíti a Security Center javaslatait az Azure portálon 
- Esetleg jegyellenőrzési rendszert használ a változások követésére (az API segítségével feltölti a javaslatokat)

**Gábor (adatvédelmi felelős/számítástechnikai felelős)**

- A Power BI-ben vagy az Excelben megtekinti a Security Center jelentéseit

**András (számítástechnikai biztonsági felelős)**

- Meghatározza a biztonsági szabályzatokat, és megtekinti a biztonsági állapotot az Azure portálon
- A Power BI-ben elemzi az adatokat és létrehozza a jelentéseket 

**Csaba (biztonsági műveletek felelőse)**

- Megtekinti és osztályozza a Security Center riasztásait az Azure portálon 
- Elképzelhető, hogy egy meglévő irányítópultot használ (az API segítségével feltölti a riasztásokat)

**Mihály (biztonsági elemző)**

- Megtekinti a Security Center riasztásait az Azure portálon 
- Elképzelhető, hogy egy meglévő irányítópultot használ (az API segítségével feltölti a riasztásokat)
- Elemzi a riasztási trendeket a Power BI-ben 
- Ellenőrzi a Storage eseménynaplóit

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) használ, amelynek [beépített szerepköreit](../active-directory/role-based-access-built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti. Amikor egy felhasználó megnyitja az Azure Security Centert, kizárólag azokat az információkat fogja látni, amelyek arra az erőforrásra vonatkoznak, amelyhez hozzáféréssel rendelkezik. Ez azt jelenti, hogy a felhasználó ahhoz az előfizetéshez vagy erőforráscsoporthoz kapja meg a Tulajdonos, Közreműködő vagy Olvasó szerepkört, amelyhez az erőforrás tartozik. A fenti személyeket alapul véve a következő szerepköralapú hozzáférés-vezérlés lenne szükséges:

**Bálint (felhőbeli számítási feladatok felelőse)**

- Erőforráscsoport: tulajdonos/közreműködő

**András (számítástechnikai biztonsági felelős)**

- Előfizetés: tulajdonos/közreműködő

**Csaba (biztonsági műveletek felelőse)**

- Előfizetés: olvasó a riasztások megtekintése érdekében
- Előfizetés: tulajdonos/közreműködő a riasztások elvetése érdekében

**Mihály (biztonsági elemző)**

- Előfizetés: olvasó a riasztások megtekintése érdekében
- Előfizetés: tulajdonos/közreműködő a riasztások kezelése/elvetése érdekében
- Előfordulhat, hogy hozzáférést kell kapnia a Storage-hoz

Egyéb megfontolandó szempontok:

- A biztonsági szabályzatot kizárólag az előfizetésnél Tulajdonos és Közreműködő szerepkörrel rendelkező személyek módosíthatják
- Az erőforrásra kizárólag az előfizetésnél és az erőforráscsoportnál Tulajdonos és Közreműködő szerepkörrel rendelkező személyek alkalmazhatják a biztonsági javaslatokat

Az Azure Security Centerre vonatkozó szerepköralapú hozzáférés-vezérlés kialakítása során fontos, hogy tisztában legyen vele, hogy a vállalatnál ki és milyen típusú feladatok elvégzésére fogja használni az Azure Security Centert. A szerepköralapú hozzáférés-vezérlést ennek megfelelően kell beállítani. 

> [AZURE.NOTE] Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Érdemes például az Olvasó szerepkört rendelni azokhoz a felhasználókhoz, akik csupán megtekintik az erőforrások biztonsági állapotát, de nem tesznek lépéseket azzal kapcsolatban (tehát nem alkalmazhatnak javaslatokat, és nem módosíthatják a szabályzatokat).

## Biztonsági szabályzatok és javaslatok
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. Az Azure Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat. 

Az előfizetési csomagnak megfelelő szabályzatokat a rendszer automatikusan feltölti az előfizetésen belüli összes erőforráscsoport számára, ahogy az az alábbi ábrán is látható: 

![Biztonsági szabályzatok](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2.png) 

Ahogy az a fenti ábrán is látható, az erőforráscsoportokra vonatkozó biztonsági szabályzatok az előfizetési szintről is örökölhetők. 

Előfordulhat azonban, hogy az egy adott erőforráscsoporthoz tartozó erőforrásokhoz ettől eltérő szabályzatkészletet szeretne használni. Ilyenkor letilthatja az öröklést, és egyedi szabályzatokat alkalmazhat a kívánt erőforráscsoportra. 

Ha egy adott erőforráscsoportban egyedi szabályzatokat kíván alkalmazni, tiltsa le az erőforráscsoportnál az öröklés funkciót, és módosítsa a biztonsági szabályzatokat. Ha például bizonyos számítási feladatokhoz nincs szükség az SQL transzparens adattitkosítási szabályzatának használatára, kapcsolja ki a szabályzatot az előfizetési szinten, majd kapcsolja be csak azoknál az erőforráscsoportoknál, amelyeknél szükség van a használatára.
 
A különböző erőforráscsoportokra vonatkozó egyedi szabályzatok létrehozásakor úgy tervezze meg a szabályzatok üzembe helyezését, hogy az előfizetés és az erőforráscsoport beállításainak ütközése esetén a rendszer az erőforráscsoportra vonatkozó szabályzatot helyezi előtérbe. 

> [AZURE.NOTE] A módosított szabályzatok ellenőrzéséhez használja az [Azure naplóit](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). Az Azure naplói minden esetben rögzítik a szabályzatok változásait.

### Biztonsági javaslatok

A biztonsági szabályzatok konfigurálása előtt mindig olvassa el a [biztonsági javaslatokat](security-center-recommendations.md), és döntse el, hogy megfelelőek-e az Ön által használt előfizetésekhez és erőforráscsoportokhoz. Ezenfelül fontos, hogy tisztában legyen vele, milyen lépéseket kell tennie a biztonsági javaslatok kezelése érdekében.

**Végpontok védelme**: ha a virtuális gépen nincs végpontvédelmi megoldás, az Azure Security Center javasolni fogja, hogy telepítsen egy ilyen típusú szoftvert. Ha van olyan végpontvédelmi megoldás, amelyet Ön előnyben részesít, és esetleg már használja is a helyszíni számítógépeken, el kell döntenie, hogy ugyanezt a kártevőirtót szeretné-e alkalmazni az Azure virtuális gépeknél is. Az Azure Security Center többféle végpontvédelmi lehetőséget kínál fel.  Használhatja az ingyenes Microsoft Antimalware szoftvert, de választhat integrált partnereink által kínált végpontvédelmi megoldások listájából is. A kártevőirtó megoldások Azure Security Centerrel történő üzembe helyezésével kapcsolatos további információk: [Enable antimalware in Azure Security Center](security-center-enable-antimalware.md) (Kártevőirtó bekapcsolása az Azure Security Centerben).

**Rendszerfrissítések**: az Azure Security Center képes azonosítani azokat a virtuális gépeket, amelyekről biztonsági vagy fontos operációsrendszer-frissítések hiányoznak. Fontolja meg, hogy ki legyen a felelős a szükséges frissítések telepítéséért, illetve határozza meg alkalmazásuk módját. Számos vállalat a WSUS, a Windows Update vagy más hasonló eszközzel kezeli ezt a területet.

**Alapkonfiguráció**: ha a virtuális gép operációs rendszerének konfigurációja nem egyezik a javasolt alapkonfigurációkkal, a rendszer javaslatot jelenít meg. Érdemes megismerni az alapkonfigurációkat [itt](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335), és megfontolni, hogy hogyan szeretné alkalmazni az operációs rendszer konfigurációját. 

**Lemeztitkosítás**: ha nem titkosított lemezek vannak a virtuális gépben, az Azure Security Center javasolni fogja az Azure Disk Encryption használatát, amely Windows esetén a BitLocker, Linux esetén pedig a DM-Crypt program segítségével kötettitkosítást biztosít az operációsrendszer- és adatlemezek számára. Ez a javaslat átirányítja Önt egy [részletes lépéseket tartalmazó útmutatóra](security-center-disk-encryption.md), amely leírja, hogy hogyan kell elvégezni a titkosítást. 

Vegye figyelembe, hogy több titkosítási feladatot is végre kell hajtania. Fontos, hogy felkészüljön az ezekhez a feladatokhoz tartozó egyedi követelmények teljesítésére:

- Új Azure virtuális gépek titkosítása saját titkosítási kulccsal titkosított virtuális merevlemezekről
- Az Azure katalógusából létrehozott Azure virtuális gépek titkosítása
- Az Azure-ban már futó Azure virtuális gépek titkosítása

A fenti helyzetek mind eltérő tervezést igényelnek. A fenti helyzetekkel kapcsolatos részletes információkért olvassa el az [Azure Disk Encryption tanulmányt](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

**Webalkalmazási tűzfal**: az Azure Security Center azonosítja azokat a virtuális gépeket, amelyeken webalkalmazások futnak, és javasolja, hogy telepítsen webalkalmazási tűzfalat (WAF-ot). Döntse el, hogy melyik megoldást fogja licencbe venni. Ehhez értékelje ki partnereink ajánlatait, és határozza meg, hogy melyik illik legjobban a vállalathoz (egyes partnerek a saját licenc használatát és/vagy használatalapú fizetést is lehetővé tesznek). Arról, hogy miképpen helyezhet üzembe webalkalmazási tűzfalakat Azure virtuális gépeken, az Azure Security Center segítségével, az [Add a web application firewall in Azure Security Center](security-center-add-web-application-firewall.md) (Webalkalmazási tűzfal beállítása az Azure Security Centerben) című cikkben talál további tájékoztatást.

**Virtuális hálózatok**: az Azure Security Center kiértékeli az [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) infrastruktúráját és konfigurációját, és ellenőrzi, hogy alkalmazta-e a [hálózati biztonsági csoportot](../virtual-network/virtual-networks-nsg.md), és megfelelően beállította-e a bejövő forgalomra vonatkozó szabályokat. Határozza meg, hogy milyen forgalmi szabályokat kíván alkalmazni, és közölje ezt a kapcsolódó biztonsági javaslatok alkalmazásáért felelős személyekkel.

## Adatgyűjtés és -tárolás

Határozottan javasoljuk, hogy minden egyes előfizetésnél kapcsolja be az adatgyűjtést, mivel így az összes virtuális gép esetében elérhető a biztonsági ellenőrzés. Az adatgyűjtést az Azure figyelőügynök (ASMAgentLauncher.exe) és az Azure biztonsági figyelőbővítmény (ASMMonitoringAgent.exe) teszi lehetővé. 

Az Azure biztonsági figyelőbővítmény ellenőrzi a különböző biztonsági konfigurációkat, és biztonsági naplókat gyűjt a virtuális gépről. Ezeket az adatokat a rendszer elküldi az Ön által megadott tárfiókba. A vizsgálókezelőt (ASMSoftwareScanner.exe) a rendszer a virtuális gépre is telepíti, itt javításkeresőként funkcionál.

Miután a biztonsági szabályzatban engedélyezte az adatgyűjtést, a rendszer automatikusan telepíti a figyelőügynököt és a bővítményeket az Azure-ban kiépített összes meglévő és új támogatott virtuális gépre.  Az ügynök folyamata nem zavarja a munkában, és nincs hatással a virtuális gép teljesítményére sem.
 
Ha bármikor ki szeretné kapcsolni az adatgyűjtést, ezt a biztonsági szabályzatban teheti meg. A korábban beállított figyelőügynökök törléséhez válassza a menü Delete Agents (Ügynökök törlése) lehetőségét.

> [AZURE.NOTE] A támogatott virtuális gépek listáját az [Azure Security Center frequently asked questions (FAQ)](security-center-faq.md) (Az Azure Security Centerhez kapcsolódó gyakori kérdések) című témakörben találja meg.

Minden olyan régió esetében, ahol virtuális gépei futnak, kiválaszthatja, hogy melyik tárfiók tárolja a virtuális gépekről összegyűjtött adatokat. Ha nem választ tárfiókot az egyes régiókhoz, a rendszer automatikusan létrehoz egy-egy tárfiókot minden régióhoz. Régiónként eltérő tárolási helyet is megadhat, de akár egyetlen központi helyet is használhat az összes adat tárolására. Míg a biztonsági szabályzatokat az Azure-előfizetés szintjén és az erőforráscsoport szintjén is beállíthatja, a tárfiók régióját kizárólag az előfizetési szinten lehet beállítani.

Ha több Azure-erőforrás között megosztott tárfiókot használ, a mérethatárokra és egyéb korlátozásokra vonatkozó információkért mindenképp olvassa el az [Azure Storage Scalability and Performance Targets](../storage/storage-scalability-targets.md) (Az Azure Storage skálázhatósága és a teljesítménycélok) című cikket. Az előfizetésre is vonatkoznak tárfiók-korlátozások, ezek részletesebb megismeréséhez olvassa el az [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits) (Az Azure-előfizetésre vonatkozó szolgáltatási korlátok, kvóták és korlátozások) című cikket.

> [AZURE.NOTE] Az Azure Security Center szolgáltatás díja nem foglalja magában a tárhellyel kapcsolatos költségeket, ezeket az [Azure Storage díjszabásának](https://azure.microsoft.com/pricing/details/storage/) megfelelően külön kell megfizetnie. 

A teljesítményt és a skálázhatóságot mindig az adott Azure-környezet mérete, illetve a tárfiók által felhasznált erőforrások mértéke szerint tervezze meg. További információk: [Microsoft Azure Storage Performance and Scalability Checklist](../storage/storage-performance-checklist.md) (A Microsoft Azure Storage teljesítményére és skálázhatóságára vonatkozó ellenőrzőlista).

## A biztonság folyamatos ellenőrzése

A kezdeti konfigurációt, illetve az Azure Security Center javaslatainak alkalmazását követően a következő lépés az Azure Security Center működési folyamatainak megtervezése. 

Az Azure Security Centernek az Azure portálról történő eléréséhez kattintson a **Tallózás** elemre, majd a **Szűrő** mezőbe írja be a következőt: **Security Center**. A felhasználó számára megnyíló ablak az alkalmazott szűrőknek megfelelő elemeket tartalmazza.

Az Azure Security Center nem akadályozza Önt a megszokott eljárások elvégzésében, csupán passzívan ellenőrzi az üzemelő példányokat, és javaslatokat biztosít a beállított biztonsági szabályzatok alapján.

Az Azure Security Center irányítópultja két nagyobb részre oszlik: 

- Megelőzés 
- Észlelés 

Amikor első alkalommal kapcsolja be az aktuálisan használt Azure-környezetre vonatkozó adatgyűjtést az Azure Security Centerben, ellenőrizze, hogy elolvasta-e az összes javaslatot. Ezt a **Javaslatok** panelen, illetve erőforrásonként (**virtuális gép**, **hálózat**, **SQL** és **alkalmazás**) teheti meg. 

Ha már az összes javaslattal foglalkozott, az összes érintett erőforráshoz tartozó **Megelőzés** szakasznak zölden kell megjelennie. Innentől kezdve egyszerűbbé válik a folyamatos ellenőrzés, hiszen kizárólag akkor kell lépéseket tennie, ha az erőforrások biztonsági állapotában változás következik be, vagy javaslat jelenik meg a megfelelő csempén.

Az **Észlelés** szakasz gyakrabban változik. Itt azok az aktuálisan zajló, illetve a múltban lezajlott és az Azure Security Center és a külső fejlesztőktől származó rendszerek vezérlőelemei által észlelt problémákkal kapcsolatos riasztások láthatók. A Biztonsági riasztások csempén az egyes napokon jelzett fenyegetésészlelési figyelmeztetések számát, illetve ezek kategóriák (alacsony, közepes, magas) szerinti eloszlását mutató oszlopdiagramok láthatók. A biztonsági riasztásokkal kapcsolatos további információkért lásd: [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben). 

> [AZURE.NOTE] Az Azure Security Center által szolgáltatott adatokat a Microsoft Power BI segítségével ábrázolhatja is. Lásd: [Get insights from Azure Security Center data with Power BI](security-center-powerbi.md) (Mélyebb betekintés az Azure Security Center adataiba a Power BI segítségével).

### Új vagy módosult erőforrások keresése

Az Azure-környezetek általában dinamikusan változnak: új erőforrások jönnek létre és szűnnek meg, módosulnak a konfigurációk és így tovább. Az Azure Security Center segítségével könnyedén átláthatja az új erőforrások biztonsági állapotát.

Az Azure Security Center automatikusan felfedezi az Azure-környezethez adott új erőforrásokat (virtuális gépeket, SQL-adatbázisokat stb.), és elkezdi a biztonsági állapotuk figyelését. Ha a biztonsági szabályzatban bekapcsolja az adatgyűjtést, a rendszer automatikusan további figyelési funkciókat kapcsol be a virtuális gépeken. 

![Fontos területek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3.png) 

1.  A virtuális gépek esetében nyissa meg a **Resource security health** (Erőforrás biztonsági állapota) csempét, és kattintson a **Virtual Machines** (Virtuális gépek) elemre. Az adatgyűjtéshez vagy az azzal kapcsolatos javaslatokhoz tartozó problémák a **Monitoring Recommendations** (Figyelési javaslatok) részben jelennek meg.
2.  A **Javaslatok** áttekintésével tudhatja meg, hogy a rendszer azonosított-e biztonsági kockázatokat az új erőforrással kapcsolatban, és ha igen, miket.
3.  Gyakran előfordul, hogy a környezethez adott új virtuális gépekre csak az operációs rendszer van telepítve. Az erőforrás tulajdonosának több időre lehet szüksége a virtuális gépeken használt további alkalmazások telepítéséhez.  Az az ideális, ha tisztában van a számítási feladat végső céljával. Alkalmazáskiszolgálóként fogja használni? Az új számítási feladat típusától függően beállíthatja a megfelelő **biztonsági szabályzatot**, amely a jelen munkafolyamat harmadik lépése.
4.  Amikor új erőforrásokat ad az Azure-környezethez, elképzelhető, hogy új riasztások jelennek meg a **Biztonsági riasztások** csempén. Mindig figyelje, hogy nem jelentek-e meg új riasztások a csempén, és tegye meg az Azure Security Center javaslatainak megfelelő lépéseket.

Ezenfelül érdemes figyelemmel kísérni a meglévő erőforrások állapotát is, mivel így tudomást szerezhet róla, ha egy konfigurációmódosítás biztonsági kockázat kialakulásához, a javasolt alapkonfigurációktól való eltéréshez, illetve biztonsági riasztásokhoz vezetett. Kezdje az Azure Security Center irányítópultjánál. Itt három főbb területet kell rendszeresen áttekintenie.

![Műveletek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png) 

1.  A **Resource security health** (Erőforrás biztonsági állapota) panelen gyorsan eléri a legfontosabb erőforrásokat. Ezzel a funkcióval ellenőrizheti virtuális gépeit, hálózatait, az SQL-t és az alkalmazásokat. 
2.  A **Javaslatok** panelen megtekintheti az Azure Security Center javaslatait. Az ellenőrzés során azt tapasztalhatja, hogy nem minden nap jelennek meg javaslatok. Ez nem jelent problémát, hiszen az Azure Security Center kezdeti beállításakor minden javaslattal foglalkozott. Ezért fordulhat elő, hogy ebben a szakaszban nem jelennek meg minden nap új információk, így csak olyankor kell megnyitnia, ha szükséges.
3.  Az **Észlelés** panel vagy rendkívül gyakran, vagy rendkívül ritkán jelez változásokat. Mindig tekintse meg a biztonsági riasztásokat, és tegye meg az Azure Security Center javaslatai szerinti lépéseket. 

## Incidensmegoldás

Az Azure Security Center észleli az előforduló fenyegetéseket, és riasztást küld róluk. Javasoljuk, hogy mindig kövesse figyelemmel az új biztonsági riasztásokat, és tegye meg a szükséges lépéseket a támadás alaposabb kivizsgálása vagy következményeinek elhárítása érdekében.

> [AZURE.NOTE] E cikknek nem célja, hogy segítséget nyújtson az incidensmegoldási terv kialakításában. A terv létrehozásában segítséget nyújthat az amerikai National Institute of Standards and Technology (Országos Szabványügyi és Technológiai Intézet, NIST) által kidolgozott [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Útmutató a számítógépes biztonsági incidensek kezeléséhez) című dokumentum.

A biztonsági riasztásokban mindig talál olyan információkat, amelyek segítségével jobban megismerheti a támadás természetét. A riasztás ezenfelül lehetséges kockázatcsökkentési megoldásokat is tartalmaz.  Egyes riasztásokban további információkra, vagy az Azure-ban található más forrásokra mutató hivatkozások is helyet kaptak. Az így kapott információk alapján további vizsgálatokat indíthat, vagy megkezdheti a kockázatcsökkentést.

Az alábbi képen gyanús RDP-tevékenységre figyelmeztető üzenetre láthat példát:

![Gyanús tevékenység](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5.png)

Ahogy látja, ezen a panelen a támadás idejére, a forrás állomásnevére és a megcélzott virtuális gépre vonatkozó adatokat, illetve a javasolt következő lépésre vonatkozó információkat talál. Előfordulhat, hogy a támadás forrására vonatkozó információk nem szerepelnek a panelen. Ezzel kapcsolatban további információkat talál a [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Hiányzó forrásadatok az Azure Security Center riasztásaiban) című cikkben.

| **Szint**        | **Észlelt támadás**                     | **Javasolt megoldás**                                                                       |
|------------------|------------------------------------------|-----------------------------------------------------------------------------------------------|
| Hálózat          | Találgatásos típusú RDP-támadás észlelése                | A támadási felület csökkentése a nem szükséges nyitott végpontok eltávolításával, ACL-ek konfigurálása, erős jelszó használata |
| Virtuális gépek | Nem megfelelő könyvtárból futó SVCHOST | Virtuális gép áthelyezése egy másik (elszigetelt) alhálózatba, majd átvizsgálása és ismételt létrehozása                        |
| Alkalmazás      | SQL-injektálás az Azure Database-ben          | Adatbázis-konfigurációk megerősítése                                                                |



## Következő lépések
Ebből a dokumentumból megtanulta a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – A szolgáltatás használatára vonatkozó gyakran ismételt kérdések.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Jun16_HO2-->


