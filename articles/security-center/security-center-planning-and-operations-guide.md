---
title: Útmutató a Security Center tervezéséhez és működtetéséhez | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center bevezetése előtti tervezésben, valamint megismerteti a napi műveletekhez tartozó szempontokkal.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid

---
# Útmutató az Azure Security Center tervezéséhez és működtetéséhez
Ez az útmutató olyan informatikusok, megoldástervezők, adatbiztonsági elemzők és felhőszolgáltatás-rendszergazdák számára készült, akik az Azure Security Center egész vállalatra kiterjedő bevezetését tervezik.

## Tervezési útmutató
Az útmutatóban ismertetett lépések és feladatok segítségével vállalata biztonsági igényeinek és felhőfelügyeleti modelljének megfelelően optimalizálhatja a Security Center használatát. Ahhoz, hogy a lehető legnagyobb mértékben kihasználhassa a Security Center által nyújtott előnyöket, fontos tisztában lennie azzal, hogy a vállalat különböző osztályai és dolgozói hogyan használják a szolgáltatást, mivel így könnyebben teljesítheti a biztonságos fejlesztésre, működésre, ellenőrzésre, irányításra és incidensmegoldásra vonatkozó követelményeket. A Security Center használatának tervezésekor fordítson különös figyelmet a következő területekre:

* Biztonsági szerepkörök és hozzáférés-vezérlés
* Biztonsági szabályzatok és javaslatok
* Adatgyűjtés és -tárolás
* A biztonság folyamatos ellenőrzése
* Incidensmegoldás

A következő részekben bemutatjuk, hogyan alkothatja meg a fenti területekre vonatkozó tervet, és hogyan alkalmazhatja a javasolt lépéseket saját környezetének megfelelően.

> [!NOTE]
> Az [Azure Security Center frequently asked questions (FAQ)](security-center-faq.md) (Az Azure Security Centerhez kapcsolódó gyakori kérdések) témakörben számos általánosan előforduló kérdést és választ talál, amelyek szintén hasznosak lehetnek a tervezési fázisban.
> 
> 

## Biztonsági szerepkörök és hozzáférés-vezérlés
A vállalat méretétől és felépítésétől függően előfordulhat, hogy sok ember és csoport is használni fogja a Security Centert a különböző biztonsági feladatok elvégzésére. A következő ábrán néhány képzeletbeli személyt mutatunk be, és ismertetjük a szerepköreiket és biztonsági feladataikat:

![Szerepkörök](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-ga.png)

Ezek a személyek a Security Center segítségével teljesítik feladataikat. Példa:

**Bálint (felhőbeli számítási feladatok felelőse)**

* A felhő munkaterhelését és kapcsolódó erőforrásait kezeli.
* Feladata a védelmi megoldások megvalósítása és kezelése a vállalat biztonsági szabályzatának megfelelően.

**Eszter (adatvédelmi felelős/számítástechnikai felelős)**

* A vállalat általános biztonságáért felelős, ennek szempontjaival foglalkozik.
* Ismernie kell a vállalat biztonsági irányelveit a felhőbeli munkaterhelésekben.
* Tájékoztatást kell kapnia a komolyabb támadásokról és kockázatokról.

**András (számítástechnikai biztonsági felelős)**

* Kidolgozza a vállalat biztonsági szabályzatát a megfelelő védelmi megoldások alkalmazása érdekében.
* Ellenőrzi a szabályzatok betartását.
* Jelentéseket készít a vezetőség és az auditorok számára.

**Judit (biztonsági műveletek felelőse)**

* Figyeli a biztonsági riasztásokat a hét 7 napján, 24 órában, és megfelelően reagál azokra.
* Továbbítja a problémákat a felhőbeli munkaterhelés tulajdonosának vagy a számítástechnikai biztonsági elemzőnek.

**Sándor (biztonsági elemző)**

* Kivizsgálja a támadásokat.
* Megoldja a riasztásokhoz kapcsolódó problémákat, vagy együttműködik a felhőbeli munkaterhelés tulajdonosával a problémák megoldásában. 

A Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../active-directory/role-based-access-control-configure.md) használ, amelynek [beépített szerepköreit](../active-directory/role-based-access-built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti. A Security Center megnyitásakor a felhasználók csak azon erőforrások információit látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználó az előfizetés vagy az erőforrást tartalmazó erőforráscsoport tulajdonosi, közreműködői vagy olvasói szerepköréhez van hozzárendelve. Az előző ábrán felsorolt személyek esetében a következő szerepköralapú hozzáférés-vezérlés szükséges:

**Bálint (felhőbeli számítási feladatok felelőse)**

* Erőforráscsoport: tulajdonos/közreműködő

**András (számítástechnikai biztonsági felelős)**

* Előfizetés: tulajdonos/közreműködő

**Judit (biztonsági műveletek felelőse)**

* Előfizetés: olvasó a riasztások megtekintése érdekében
* Előfizetés: tulajdonos/közreműködő a riasztások elvetése érdekében

**Sándor (biztonsági elemző)**

* Előfizetés: olvasó a riasztások megtekintése érdekében
* Előfizetés: tulajdonos/közreműködő a riasztások kezelése/elvetése érdekében
* Előfordulhat, hogy hozzáférést kell kapnia a Storage-hoz

Egyéb megfontolandó szempontok:

* A biztonsági szabályzatot kizárólag az előfizetésnél Tulajdonos és Közreműködő szerepkörrel rendelkező személyek módosíthatják
* Az erőforrásra kizárólag az előfizetésnél és az erőforráscsoportnál Tulajdonos és Közreműködő szerepkörrel rendelkező személyek alkalmazhatják a biztonsági javaslatokat

A Security Centerre vonatkozó szerepköralapú hozzáférés-vezérlés kialakítása során fontos, hogy tisztában legyen azzal, hogy a vállalatnál ki fogja használni a Security Centert. Ezenkívül tudnia kell azt, hogy ezek a személyek milyen típusú feladatokat fognak végrehajtani, és ennek megfelelően kell beállítania a szerepköralapú hozzáférés-vezérlést.

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Érdemes például az Olvasó szerepkört rendelni azokhoz a felhasználókhoz, akik csupán megtekintik az erőforrások biztonsági állapotát, de nem tesznek lépéseket azzal kapcsolatban (tehát nem alkalmazhatnak javaslatokat, és nem módosíthatják a szabályzatokat).
> 
> 

## Biztonsági szabályzatok és javaslatok
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. A Security Centerben a vállalat biztonsági elvárásainak és az alkalmazások típusának vagy az adatok érzékenységének megfelelően állíthatja be a szabályzatokat.

Az előfizetési csomagnak megfelelő szabályzatokat a rendszer automatikusan feltölti az előfizetésen belüli összes erőforráscsoport számára, ahogy a következő ábrán is látható:

![Biztonsági szabályzatok](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-ga.png)

Ahogy az előző ábrán is látható, az erőforráscsoportokra vonatkozó biztonsági szabályzatok az előfizetési szintről is örökölhetők.

Előfordulhat azonban, hogy az egy adott erőforráscsoporthoz tartozó erőforrásokhoz ettől eltérő szabályzatkészletet szeretne használni. Ilyenkor letilthatja az öröklést, és egyedi szabályzatokat alkalmazhat a kívánt erőforráscsoportra.

Ha egy adott erőforráscsoportban egyedi szabályzatokat kíván alkalmazni, tiltsa le az erőforráscsoportnál az öröklés funkciót, és módosítsa a biztonsági szabályzatokat. Ha például bizonyos számítási feladatokhoz nincs szükség az SQL transzparens adattitkosítási szabályzatának használatára, kapcsolja ki a szabályzatot az előfizetési szinten, majd kapcsolja be csak azoknál az erőforráscsoportoknál, amelyeknél szükség van a használatára.

A különböző erőforráscsoportokra vonatkozó egyedi szabályzatok létrehozásakor úgy tervezze meg a szabályzatok üzembe helyezését, hogy az előfizetés és az erőforráscsoport beállításainak ütközése esetén a rendszer az erőforráscsoportra vonatkozó szabályzatot helyezi előtérbe.

> [!NOTE]
> A módosított szabályzatok ellenőrzéséhez használja az [Azure naplóit](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). Az Azure naplói minden esetben rögzítik a szabályzatok változásait.
> 
> 

### Biztonsági javaslatok
A biztonsági szabályzatok konfigurálása előtt mindig olvassa el a [biztonsági javaslatokat](security-center-recommendations.md), és döntse el, hogy ezek a szabályzatok megfelelőek-e az Ön által használt előfizetésekhez és erőforráscsoportokhoz. Ezenkívül fontos, hogy tisztában legyen azzal, milyen lépéseket kell tennie a biztonsági javaslatok kezelése érdekében.

**Végpontok védelme**: ha a virtuális gépen nincs végpontvédelmi megoldás, a Security Center javasolja, hogy telepítsen egy ilyen típusú szoftvert. Ha van olyan végpontvédelmi megoldás, amelyet Ön előnyben részesít, és esetleg már használja is a helyszíni számítógépeken, el kell döntenie, hogy ugyanezt a kártevőirtót szeretné-e alkalmazni az Azure virtuális gépeknél is. A Security Center többféle végpontvédelmi lehetőséget kínál.  Használhatja az ingyenes Microsoft Antimalware szoftvert, de választhat integrált partnereink által kínált végpontvédelmi megoldások listájából is. A kártevőirtó megoldások Security Centerrel történő üzembe helyezésével kapcsolatos további információk: [Install Endpoint Protection in Azure Security Center](security-center-install-endpoint-protection.md) (Az Endpoint Protection telepítése a Security Centerben).

**Rendszerfrissítések**: a Security Center felismeri azokat a virtuális gépeket, amelyekről hiányoznak az IaaS és a Cloud Services (PaaS) fontos biztonsági vagy operációsrendszer-frissítései. Fontolja meg, hogy ki legyen a felelős a szükséges frissítések telepítéséért, illetve határozza meg az alkalmazásuk módját. Számos vállalat a WSUS, a Windows Update vagy más hasonló eszközzel kezeli ezt a területet.

**Alapkonfiguráció**: ha a virtuális gép operációs rendszerének konfigurációja nem egyezik a javasolt alapkonfigurációkkal, a rendszer javaslatot jelenít meg. Tekintse át az alapkonfigurációkat [itt](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335), és fontolja meg, hogyan szeretné alkalmazni az operációs rendszer konfigurációját.

**Lemeztitkosítás**: ha van olyan virtuális gépe, amely titkosítatlan lemezen található, a Security Center javasolja az Azure Disk Encryption alkalmazását. Ez a szolgáltatás a Windows BitLocker funkcióját és a Linux DM-Crypt funkcióját alkalmazva biztosítja az operációs rendszer lemezének és az adatlemezek kötettitkosítását. Ez a javaslat átirányítja Önt egy [részletes lépéseket tartalmazó útmutatóra](security-center-disk-encryption.md), amely leírja, hogyan kell elvégezni a titkosítást.

Vegye figyelembe, hogy több titkosítási feladatot is végre kell hajtania. Fontos, hogy felkészüljön az ezekhez a feladatokhoz tartozó egyedi követelmények teljesítésére:

* Új Azure virtuális gépek titkosítása saját titkosítási kulccsal titkosított virtuális merevlemezekről
* Az Azure katalógusából létrehozott Azure virtuális gépek titkosítása
* Az Azure-ban már futó Azure virtuális gépek titkosítása

A fenti helyzetek mind eltérő tervezést igényelnek. A fenti helyzetekkel kapcsolatos részletes információkért olvassa el az [Azure Disk Encryption tanulmányt](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

**Webalkalmazási tűzfal**: a Security Center azonosítja azokat a virtuális gépeket, amelyeken webalkalmazások futnak, és javasolja, hogy telepítsen webalkalmazási tűzfalat (WAF). Döntse el, hogy melyik megoldást fogja licencbe venni. Ehhez értékelje ki partnereink ajánlatait, és határozza meg, hogy melyik illik legjobban a vállalathoz (egyes partnerek a saját licenc használatát és/vagy használatalapú fizetést is lehetővé tesznek). Az [Add a web application firewall in Azure Security Center](security-center-add-web-application-firewall.md) (Webalkalmazási tűzfal beállítása az Azure Security Centerben) című cikkben talál további tájékoztatást arról, hogy miképpen helyezhet üzembe webalkalmazási tűzfalakat Azure virtuális gépeken a Security Center segítségével.

**Következő generációs tűzfal**: lehetővé teszi, hogy a vezető szállítóktól származó virtuális készülékeket építsen ki. Ilyen például a Check Point, és hamarosan a Cisco, valamint a Fortinet. Ezzel az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felfedezi az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését.

**Virtuális hálózatok**: a Security Center kiértékeli az [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) infrastruktúráját és konfigurációját, és ellenőrzi, hogy alkalmazta-e a [hálózati biztonsági csoportot](../virtual-network/virtual-networks-nsg.md), és megfelelően beállította-e a bejövő forgalomra vonatkozó szabályokat. Határozza meg, hogy milyen forgalmi szabályokat kíván alkalmazni, és közölje ezt a kapcsolódó biztonsági javaslatok alkalmazásáért felelős személyekkel.

A Security Center javasolni fogja, hogy adja meg a biztonsági kapcsolattartó adatait az Azure-előfizetéséhez. A Microsoft arra használja ezt az információt, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön ügyféladataihoz. Olvassa el a [Provide security contact details in Azure Security Center](security-center-provide-security-contact-details.md) (A biztonsági kapcsolattartó adatainak megadása az Azure Security Centerben) részt, amelyből megtudhatja, hogyan engedélyezheti ezt a javaslatot.

## Adatgyűjtés és -tárolás
Határozottan javasoljuk, hogy minden egyes előfizetésnél kapcsolja be az adatgyűjtést, mivel így az összes virtuális gép esetében elérhető a biztonsági ellenőrzés. Az adatgyűjtést az Azure figyelőügynök (ASMAgentLauncher.exe) és az Azure biztonsági figyelőbővítmény (ASMMonitoringAgent.exe) teszi lehetővé.

Az Azure biztonsági figyelőbővítmény ellenőrzi a különböző biztonsági konfigurációkat, és biztonsági naplókat gyűjt a virtuális gépről. Ezeket az adatokat a rendszer elküldi az Ön által megadott tárfiókba. A vizsgálókezelőt (ASMSoftwareScanner.exe) a rendszer a virtuális gépre is telepíti, itt javításkeresőként funkcionál.

Miután a biztonsági szabályzatban engedélyezte az adatgyűjtést, a rendszer automatikusan telepíti a figyelőügynököt és a bővítményeket az Azure-ban kiépített összes meglévő és új támogatott virtuális gépre.  Az ügynök folyamata nem zavarja a munkában, és nincs hatással a virtuális gép teljesítményére sem.

> [!NOTE]
> Az Azure Security Monitoring Agent ügynöknél előforduló problémák megoldásáról az [Azure Security Center hibaelhárítási útmutatójában](security-center-troubleshooting-guide.md) olvashat.
> 
> 

Ha bármikor ki szeretné kapcsolni az adatgyűjtést, ezt a biztonsági szabályzatban teheti meg. A korábban beállított figyelőügynökök törléséhez válassza a menü Delete Agents (Ügynökök törlése) lehetőségét.

> [!NOTE]
> A támogatott virtuális gépek listáját az [Azure Security Center frequently asked questions (FAQ)](security-center-faq.md) (Az Azure Security Centerhez kapcsolódó gyakori kérdések) című témakörben találja meg.
> 
> 

Minden olyan régió esetében, ahol virtuális gépei futnak, kiválaszthatja, hogy melyik tárfiók tárolja a virtuális gépekről összegyűjtött adatokat. Ha nem választ tárfiókot az egyes régiókhoz, a rendszer automatikusan létrehoz egy-egy tárfiókot minden régióhoz. Régiónként eltérő tárolási helyet is megadhat, de akár egyetlen központi helyet is használhat az összes adat tárolására. Míg a biztonsági szabályzatokat az Azure-előfizetés szintjén és az erőforráscsoport szintjén is beállíthatja, a tárfiók régióját kizárólag az előfizetési szinten lehet beállítani.

Ha több Azure-erőforrás között megosztott tárfiókot használ, a mérethatárokra és egyéb korlátozásokra vonatkozó információkért mindenképp olvassa el az [Azure Storage Scalability and Performance Targets](../storage/storage-scalability-targets.md) (Az Azure Storage skálázhatósága és a teljesítménycélok) című cikket. Az előfizetésre is vonatkoznak tárfiók-korlátozások, ezek részletesebb megismeréséhez olvassa el az [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Az Azure-előfizetésre vonatkozó szolgáltatási korlátok, kvóták és korlátozások) című cikket.

> [!NOTE]
> A Security Center szolgáltatás díja nem foglalja magában a tárhellyel kapcsolatos költségeket, ezeket az [Azure Storage díjszabásának](https://azure.microsoft.com/pricing/details/storage/) megfelelően külön kell megfizetnie.
> 
> 

A teljesítményt és a skálázhatóságot mindig az adott Azure-környezet mérete, illetve a tárfiók által felhasznált erőforrások mértéke szerint tervezze meg. További információk: [Microsoft Azure Storage Performance and Scalability Checklist](../storage/storage-performance-checklist.md) (A Microsoft Azure Storage teljesítményére és skálázhatóságára vonatkozó ellenőrzőlista).

## A biztonság folyamatos ellenőrzése
A kezdeti konfigurációt, illetve a Security Center javaslatainak alkalmazását követően a következő lépés a Security Center működési folyamatainak megtervezése.

A Security Centernek az Azure Portalról történő eléréséhez kattintson a **Tallózás** elemre, majd a **Szűrő** mezőbe írja be a **Security Center** nevet. A felhasználó számára megnyíló ablak az alkalmazott szűrőknek megfelelő elemeket tartalmazza.

A Security Center nem akadályozza Önt a megszokott eljárások elvégzésében, csupán passzívan ellenőrzi az üzemelő példányokat, és javaslatokat biztosít a beállított biztonsági szabályzatok alapján.

A Security Center irányítópultja két nagyobb részre oszlik:

* Megelőzés
* Észlelés

Amikor első alkalommal kapcsolja be az aktuálisan használt Azure-környezetre vonatkozó adatgyűjtést a Security Centerben, ellenőrizze, hogy elolvasta-e az összes javaslatot. Ezt a **Javaslatok** panelen, illetve erőforrásonként (**Virtuális gép**, **Hálózat**, **SQL** és **Alkalmazás**) teheti meg.

Ha már az összes javaslattal foglalkozott, az összes érintett erőforráshoz tartozó **Megelőzés** szakasznak zölden kell megjelennie. Innentől kezdve egyszerűbbé válik a folyamatos ellenőrzés, hiszen kizárólag akkor kell lépéseket tennie, ha az erőforrások biztonsági állapotában változás következik be, vagy javaslat jelenik meg a megfelelő csempén.

Az **Észlelés** szakasz gyakrabban változik. Itt azok az aktuálisan zajló, illetve a múltban lezajlott és a Security Center és a külső fejlesztőktől származó rendszerek vezérlőelemei által észlelt problémákkal kapcsolatos riasztások láthatók. A Biztonsági riasztások csempén az egyes napokon jelzett fenyegetésészlelési figyelmeztetések számát, illetve ezek kategóriák (alacsony, közepes, magas) szerinti eloszlását mutató oszlopdiagramok láthatók. A biztonsági riasztásokkal kapcsolatos további információkért lásd: [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben).

> [!NOTE]
> A Security Center által szolgáltatott adatokat a Microsoft Power BI segítségével ábrázolhatja is. Lásd: [Get insights from Azure Security Center data with Power BI](security-center-powerbi.md) (Mélyebb betekintés az Azure Security Center adataiba a Power BI segítségével).
> 
> 

### Új vagy módosult erőforrások keresése
Az Azure-környezetek általában dinamikusan változnak: új erőforrások jönnek létre és szűnnek meg, módosulnak a konfigurációk és így tovább. A Security Center segítségével könnyen átláthatja az új erőforrások biztonsági állapotát.

A Security Center automatikusan felfedezi az Azure-környezethez adott új erőforrásokat (virtuális gépeket, SQL-adatbázisokat stb.), és elkezdi a biztonsági állapotuk figyelését. Ide tartoznak a PaaS webes és feldolgozói szerepkörei is. Ha a [Biztonsági szabályzat](security-center-policies.md) beállításainál bekapcsolja az adatgyűjtést, a rendszer automatikusan további figyelési funkciókat kapcsol be a virtuális gépeken.

![Fontos területek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-ga.png)

1. A virtuális gépek esetében nyissa meg a **Resource security health** (Erőforrás biztonsági állapota) csempét, és kattintson a **Virtual Machines** (Virtuális gépek) elemre. Az adatgyűjtéshez vagy az azzal kapcsolatos javaslatokhoz tartozó problémák a **Monitoring Recommendations** (Figyelési javaslatok) részben jelennek meg.
2. A **Javaslatok** áttekintésével tudhatja meg, hogy a rendszer azonosított-e biztonsági kockázatokat az új erőforrással kapcsolatban, és ha igen, miket.
3. Gyakran előfordul, hogy a környezethez adott új virtuális gépekre csak az operációs rendszer van telepítve. Az erőforrás tulajdonosának több időre lehet szüksége a virtuális gépeken használt további alkalmazások telepítéséhez.  Az az ideális, ha tisztában van a számítási feladat végső céljával. Alkalmazáskiszolgálóként fogja használni? Az új számítási feladat típusától függően beállíthatja a megfelelő **biztonsági szabályzatot**, amely a jelen munkafolyamat harmadik lépése.
4. Amikor új erőforrásokat ad az Azure-környezethez, elképzelhető, hogy új riasztások jelennek meg a **Biztonsági riasztások** csempén. Mindig figyelje, hogy nem jelentek-e meg új riasztások a csempén, és tegye meg a Security Center javaslatainak megfelelő lépéseket.

Ezenfelül érdemes figyelemmel kísérni a meglévő erőforrások állapotát is, mivel így tudomást szerezhet róla, ha egy konfigurációmódosítás biztonsági kockázat kialakulásához, a javasolt alapkonfigurációktól való eltéréshez, illetve biztonsági riasztásokhoz vezetett. Kezdje a Security Center irányítópultjánál. Itt három főbb területet kell rendszeresen áttekintenie.

![Műveletek](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4.png)

1. A **Resource security health** (Erőforrás biztonsági állapota) panelen gyorsan eléri a legfontosabb erőforrásokat. Ezzel a funkcióval ellenőrizheti virtuális gépeit, hálózatait, az SQL-t és az alkalmazásokat.
2. A **Javaslatok** panelen megtekintheti a Security Center javaslatait. Az ellenőrzés során azt tapasztalhatja, hogy nem minden nap jelennek meg javaslatok. Ez nem jelent problémát, hiszen a Security Center kezdeti beállításakor minden javaslattal foglalkozott. Ezért fordulhat elő, hogy ebben a szakaszban nem jelennek meg minden nap új információk, így csak olyankor kell megnyitnia, ha szükséges.
3. Az **Észlelés** panel vagy rendkívül gyakran, vagy rendkívül ritkán jelez változásokat. Mindig tekintse meg a biztonsági riasztásokat, és tegye meg a Security Center javaslatai szerinti lépéseket.

## Incidensmegoldás
A Security Center észleli az előforduló fenyegetéseket, és riasztást küld róluk. Javasoljuk, hogy mindig kövesse figyelemmel az új biztonsági riasztásokat, és tegye meg a szükséges lépéseket a támadás alaposabb kivizsgálása vagy következményeinek elhárítása érdekében. Ha részletes tájékoztatást szeretne kapni a Security Center fenyegetésészlelési funkciójának működéséről, olvassa el az [Azure Security Center detection capabilities](security-center-detection-capabilities.md) (Az Azure Security Center észlelési funkciói) című cikket.

Bár ez a cikk nem nyújt segítséget a saját incidensmegoldási tervének kidolgozásához, a Microsoft Azure Security Response szolgáltatást fogjuk használni a felhő életciklusában az incidensmegoldási szakaszok alapjaként. Ezek a szakaszok a következő ábrán láthatók:

![Gyanús tevékenység](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> A terv létrehozásában segítséget nyújthat az amerikai National Institute of Standards and Technology (Országos Szabványügyi és Technológiai Intézet, NIST) által kidolgozott [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Útmutató a számítógépes biztonsági incidensek kezeléséhez) című dokumentum.
> 
> 

A Security Center riasztásait a következő szakaszokban használhatja:

* **Észlelés**: a gyanús tevékenységek azonosítása egy vagy több erőforrásban. 
* **Felmérés**: az első vizsgálat végrehajtása a gyanús tevékenységgel kapcsolatos további információk összegyűjtéséhez.
* **Diagnosztizálás**: a hibaelhárítási lépések végrehajtása a probléma megoldásához szükséges technikai eljárás lefolytatásához.

A biztonsági riasztásokban mindig talál olyan információkat, amelyek segítségével jobban megismerheti a támadás természetét. A riasztás ezenfelül lehetséges kockázatcsökkentési megoldásokat is tartalmaz. Egyes riasztásokban további információkra, vagy az Azure-ban található más forrásokra mutató hivatkozások is helyet kaptak. Az így kapott információk alapján további vizsgálatokat indíthat, és megkezdheti a kockázatcsökkentést.

A következő példában gyanús RDP-tevékenységre figyelmeztető üzenetet láthat:

![Gyanús tevékenység](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Ahogy látja, ezen a panelen a támadás idejére, a forrás állomásnevére és a megcélzott virtuális gépre vonatkozó adatokat, illetve a javasolt következő lépésre vonatkozó információkat talál. Előfordulhat, hogy a támadás forrására vonatkozó információk nem szerepelnek a panelen. Ezzel kapcsolatban további információkat talál a [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Hiányzó forrásadatok az Azure Security Center riasztásaiban) című cikkben.

A [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Az Azure Security Center és a Microsoft Operations Management Suite használata az incidensmegoldáshoz) videóban megtekinthet néhány példát, amelynek alapján pontosabb képet kaphat arról, hogyan használhatja a Security Centert az egyes szakaszokban.

> [!NOTE]
> Ha további részletek szeretne megtudni arról, hogy miként lehetnek a segítségére a Security Center képességei az incidensmegoldási folyamat során, olvassa el [Az Azure Security Center használata incidensek megoldásához](security-center-incident-response.md) című cikket. 
> 
> 

## Lásd még:
Ebben a dokumentumban megismerkedhetett a Security Center bevezetésével. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--HONumber=Sep16_HO4-->


