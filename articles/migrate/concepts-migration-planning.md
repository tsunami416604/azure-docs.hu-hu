---
title: Áttelepítési terv létrehozása Azure Migrateokkal | Microsoft Docs
description: Útmutatást nyújt az áttelepítési terv létrehozásához Azure Migrate használatával.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: db1de363856fd560fea97f8f9cdf542717c4cca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090093"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Migrálási terv létrehozása az Azure Migrate-tel

Ez a cikk egy rövid útmutatót tartalmaz, amellyel átalakíthatja az Azure-ba történő áttelepítési tervet [Azure Migrate](migrate-services-overview.md)segítségével.Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [kiszolgálók áttelepítésével](common-questions-server-migration.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="define-the-goals-of-cloud-migration"></a>A Felhőbeli áttelepítés céljainak meghatározása

Az áttelepítési terv kiépítése előtt fontos megérteni és értékelni a [motivációt](/azure/cloud-adoption-framework/strategy/motivations) a felhőbe való áttéréshez, amely segíthet a sikeres üzleti eredmények létrehozásában. Az Azure-hoz készült [felhőalapú bevezetési keretrendszer](/azure/cloud-adoption-framework) elmagyarázza, hogy különböző triggerek és áttelepítési módszerek használhatók a vállalata számára:  

**Kritikus üzleti események** | **Áttelepítési eredmény**
--- | ---
Adatközpont bezárása | Költségmegtakarítás
Egyesítés, beszerzés vagy elidegenítés | A gyártó vagy a technikai komplexitás csökkentése
A tőke költségeinek csökkentése | Belső műveletek optimalizálása
A kritikus fontosságú technológiák támogatásának vége | Növekedés az üzleti agilitásban
Válasz a szabályozás megfelelőségi módosításaira | Új technikai képességek előkészítése
Új adatszuverenitási követelmények | Méretezés a piaci igények kielégítése érdekében
A fennakadások csökkentése és az informatikai stabilitás javítása | Méretezés a földrajzi igények kielégítése érdekében

Az áttelepítési motiváció segíthet az Azure-ba való Migrálás során felmerülő stratégiai célok és eredmények tükrözésében is. A következő lépés az Azure-ba történő áttelepítési útvonal azonosítása és megtervezése, amely a számítási feladatokra van szabva. Azure Migrate: a Server Assessment eszköz segítségével értékelheti a helyszíni számítási feladatokat, és útmutatást és eszközöket biztosít a Migrálás megkönnyítéséhez.

## <a name="understand-your-digital-estate"></a>A digitális ingatlan megismerése

A helyszíni infrastruktúrájának, alkalmazásainak és függőségeinek megismerésével megismerheti az Azure-ba migrálni kívánt munkaterheléseket, és optimalizálhatja az optimalizált költségeket. A kiszolgáló-Assessment eszköz segítséget nyújt a következő kérdések megválaszolásához:

### <a name="what-workloads-are-in-use"></a>Milyen számítási feladatok vannak használatban?

A Lightweight Azure Migrate berendezés használatával a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek és a fizikai kiszolgálók ügynök nélküli felderítését végezheti el. A folyamatos felderítés a számítógép-konfigurációt és a teljesítménnyel kapcsolatos metaadatokat gyűjti, és a telepített alkalmazások leltárának, valamint a helyszíni gépeken futó szerepköröknek/szolgáltatásoknak a beszerzésére is alkalmas. A Azure Migrate készülék a következőket gyűjti:

- A gépek, lemezek és hálózati adapterek metaadat-adatai

- Telepített alkalmazások, beleértve az alkalmazásokat és a szerepköröket/szolgáltatásokat  

- Teljesítményadatokat, beleértve a processzor-és memóriahasználat, a lemez IOPS és az átviteli sebességet

Ezután exportálja az alkalmazás leltári listáját, hogy megismerje a számítási feladatokon futó összes SQL Server példányt, és a Azure Migrate: adatbázis-értékelő eszköz használatával megértse a készültségét.

 ![Alkalmazás leltározása a portálon](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Alkalmazás-leltár exportálása](./media/concepts-migration-planning/application-inventory-export.png)

A kiszolgáló-értékelési eszköz felderítési adataival együtt a meglévő CMDB-adatokkal is felépítheti a kiszolgáló és az adatbázis-tulajdonos nézetét, és megismerheti a kiszolgálók eloszlását az üzleti egységek, az alkalmazások tulajdonosai, a földrajzi területek stb. esetében, amelyek segíthetnek az áttelepítendő munkaterhelések rangsorolásában.

### <a name="what-dependencies-exist-between-workloads"></a>Milyen függőségek léteznek a munkaterhelések között?

Miután felderítette a kiszolgálókat, az ügynök nélküli függőségi leképezés használatával jelenítheti meg és azonosíthatja a kiszolgálók közötti függőségeket és optimalizálási stratégiákat a függő kiszolgálók Azure-ba való áthelyezéséhez. A vizualizáció segítségével megtudhatja, hogy vannak-e használatban bizonyos gépek, vagy leszerelhetők a Migrálás helyett.  Győződjön meg arról, hogy a függőségek elemzése nem marad hátra, és az áttelepítés során ne legyen meglepő kimaradás. Miután elvégezte az alkalmazás leltározását és a függőségi leképezést, létrehozhat nagy megbízhatóságú csoportokat, és megkezdheti a kiszolgálók értékelését.

 ![Függőségek leképezése](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Optimalizálva vannak és megfelelően méretezettek?

Mivel az Azure rugalmasságot biztosít a felhő kapacitásának időbeli átméretezéséhez, a Migrálás lehetőséget nyújt a kiszolgálók számára lefoglalt CPU-és memória-erőforrások optimalizálására. Hozzon létre egy értékelést az azonosított csoport előtt, és Ismerje meg a számítási feladatok előzményeit, ami nagyon fontos szerepet játszik az Azure-beli Rights VM SKU-ket és lemezekkel kapcsolatos ajánlásokban.

## <a name="assess-your-readiness-for-migration"></a>Az áttelepítés készültségének felmérése

### <a name="readiness-and-suitability-analysis-for-azure"></a>Készültség és megfelelőség elemzése az Azure-hoz
Exportálja az Azure VM Assessment-jelentést, és a következő készültségi kategóriák szerint szűrje az Azure-beli virtuális gépek készültségének megismeréséhez:

- **Készen áll az Azure-ra**: ezeket a gépeket áttelepítheti az Azure-ba a módosítások nélkül  

- **Feltételesen készen áll az Azure**-ra: ezeket a gépeket áttelepítheti az Azure-ba, de a kiszolgálókon az értékeléshez megadott szervizelési útmutató alapján kisebb módosításokat kell végeznie.

- **Nem áll készen az Azure-ra**: ezek a gépek nem telepíthetők át az Azure-ba, és az áttelepítés előtt elhárítani kell a problémákat a Szervizelési útmutató szerint.

- A **készültség ismeretlen**: Azure Migrate nem tudja meghatározni a gép készültségét, mert nincs elegendő metaadat

Az adatbázis-értékelések használatával felmérhetővé teheti a SQL Server-adatközpont áttelepítésének készültségét Azure SQL Database vagy Azure SQL felügyelt példányokra. Az egyes SQL Server-példányok esetében az áttelepítési készültségi állapot százalékos aránya látható. Emellett az egyes példányok esetében a javasolt cél az Azure-ban, a potenciális áttelepítési blokkolók, a megszakított változások száma, az Azure SQL DB/Azure SQL-alapú virtuális gép készültségi szintje és a kompatibilitási szint is látható. Mélyebben is megtudhatja, hogy milyen hatással van az áttelepítési blokkolók és javaslatok a kijavítására.

 ![Adatbázis-értékelések](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Méretezési javaslatok

Ha a gép az Azure-hoz való használatra készként van megjelölve, a kiszolgáló értékelése során a rendszer méretezési javaslatokat tesz a virtuális gépekhez tartozó Azure-beli virtuális gép és SKU azonosítására Dönthet úgy, hogy a teljesítmény előzményei alapján megtekinti a méretezési javaslatot (így az erőforrások migrálása az áttelepítés során), vagy a helyszíni konfiguráció alapján, a teljesítmény előzményeinek figyelembevétele nélkül. Adatbázisok esetében az adatbázis-minősítéssel, az árképzési szinttel és a számítási szinttel kapcsolatos ajánlásokat tekintheti meg az adatbázisban.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Számítások a számítási feladatok Azure-beli futtatásához szükséges becsült költségek beszerzéséhez

Az értékelések *teljesítmény-alapú* jobb méretezési lehetősége lehetővé teszi az Azure-beli számítási feladatok optimalizálását. A megadásában kívül néhány más út is van, amelyek segítenek a költségmegtakarításban:

- **Fenntartott példányok**: fenntartott példányok esetén a költségek jelentős mértékben csökkenthetők a Windows és Linux rendszerű (VM-EK) és a 3 éves szerződéssel ellátott utólagos elszámolású díjszabáshoz képest.

- **Azure Hybrid Benefits**: a Windows Server-licenceket az Azure-ba frissítheti, és a fenntartott példányok lehetőségeivel kombinálhatja azokat.

- **Nagyvállalati szerződés ajánlat (EA)**: a nagyvállalati szerződés az előfizetésre vonatkozó beépített megtakarításokat kínál

- **Ajánlatok**: több Azure-ajánlat létezik, például Pay-As-You-Go Dev/Test és Enterprise dev/test, amely alacsonyabb sebességet biztosít a fejlesztési és tesztelési virtuális gépek számára

- **Virtuális gép rendelkezésre állása**: az időtartamot naponként, havonta és órában is megemlítheti, ha az Azure-beli virtuális gépek a költségek csökkentése érdekében futnak (nem alkalmazható a ri esetében)

- **Célcsoport**: több felmérést is létrehozhat különböző régiókban, amelyekkel összehasonlíthatja, ha a földrajz egy bizonyos régióba való migrálása költséghatékonyabb lehet

- **Teljesítmény-alapú javaslatok**: ajánlott eljárásként próbálja meg használni a tartalomvédelemmel elválasztott Azure-beli virtuális gépekre vonatkozó javaslatokat, amelyek segítenek a Felhőbeli költségek megtakarításában

### <a name="visualize-data"></a>Adatok vizualizációja

A kiszolgáló-értékelési jelentést az Azure Readiness és a havi költség-elosztással együtt tekintheti meg a portálon, valamint exportálhatja az értékelést, hogy további vizualizációkat alkalmazzon a felderítési és értékelési adatokat illetően, hogy az áttelepítési terv gazdagabb legyen. Több felmérést is létrehozhat a tulajdonságok különböző kombinációi számára, és kiválaszthatja a vállalat számára legmegfelelőbb tulajdonságok készletét.  

 ![Felmérések áttekintése](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Hiányosságok és lehetséges blokkolók kiértékelése

Az áttelepíteni kívánt alkalmazások és mögöttes infrastruktúra meghatározása során azonosítsa az alkalmazások állásidőre vonatkozó korlátozásait, és keresse meg az alkalmazások és a mögöttes infrastruktúra működési függőségeit. Ez az elemzés segítséget nyújt a helyreállítási időre vonatkozó célkitűzésnek (RTO) megfelelő áttelepítések megtervezésében, és minimálisan nulla adatvesztést biztosít. A Migrálás előtt javasoljuk, hogy tekintse át és csökkentse a kompatibilitási problémákat, illetve a nem támogatott szolgáltatásokat, amelyek letiltják a kiszolgálók és az SQL-adatbázisok áttelepítését a kiszolgáló-értékelési jelentés és a Azure Migrate: adatbázis-értékelési javaslatok segítségével.

### <a name="first-workloads-to-target-and-approach"></a>A cél és a megközelítés első munkaterhelése

Most, hogy minden fontos információval rendelkezik az áttelepítési döntéshez, rangsorolnia kell, hogy mely alkalmazások és munkaterhelések legyenek áttelepítve. Fejlesszen egy "alkalmazás és tanulás" megközelítést, amely lehetővé teszi a kívánt alkalmazások rendszeres és ellenőrizhető módon történő áttelepítését, így a stratégia bármilyen hibáját kihasználhatja a teljes körű Migrálás előtt. Olyan stratégiai tényezőket is használhat, mint például a-komplexitás és az idő az áttelepítés, az üzleti sürgősség, a termelési/nem üzemi környezet, a megfelelőség és a biztonsági követelmények, az alkalmazások ismerete stb. alapján, hogy rangsorolja az áttelepíteni kívánt alkalmazásokat.

Néhány ajánlott áttelepítési stratégia:

- **Rangsorolja a gyors WINS**-t: az értékelési jelentések segítségével azonosíthatja az alacsony teljesítményű gyümölcsöket, beleértve a teljes mértékben kész kiszolgálókat és adatbázisokat, és minimális erőfeszítést igényel az Azure-ba való Migrálás során:
    - Azure-kompatibilis: exportálja az értékelési jelentést, és szűrje az összes olyan gépet, amely készen áll az Azure-ra. Ez lehet a gépek első olyan csoportja, amelyet a Azure Migrate: Server áttelepítési eszközzel lehet feloldani és áttérni.
    - OPERÁCIÓS rendszer vége: az értékelési jelentés exportálása és a Windows Server 2008 és a Windows Server 2008 R2 operációs rendszert futtató összes gép szűrése. Ezek a SKU-támogatások megszűnnek, és az Azure-ba való Migrálás után csak az Azure ingyenes biztonsági frissítéseket biztosít. A fenntartott példányok kombinálása, Azure Hybrid Benefit és használata esetén a megtakarítás sokkal magasabb lehet.
    - SQL Server Migrálás: az adatbázis-értékelési javaslatok segítségével áttelepítheti az adatbázisokat az Azure SQL Database-adatbázisok számára a Azure Migrate használatával: adatbázis-áttelepítés és az Azure SQL virtuális gép számára kész adatbázisok a Azure Migrate: Server Migration használatával.
    - Szoftver támogatásának vége: az alkalmazás leltárának exportálása és a támogatás befejezését esetlegesen elérő szoftverek és bővítmények szűrése. Ezeket az alkalmazásokat rangsorolni kell.
    - Túlterhelt virtuális gépek: exportálja az értékelési jelentést és kiszűri a gépeket alacsony CPU-kihasználtsággal (%) és memória kihasználtsága (%).  Ezt a lehetőséget használhatja arra, hogy áttelepítsen egy virtuális gépre az Azure-ban, és mentse a kihasználatlan erőforrásokért fizetett árat.
    - Kapacitási megkötések: exportálja az értékelési jelentést és kiszűri a gépeket magas CPU-kihasználtsággal (%) és memória kihasználtsága (%).  Az Azure-ba való Migrálás és az igények kielégítése érdekében az automatikus skálázási képesség kihasználásával megakadályozható, hogy a virtuális gépek megszakítsák és növeljék a teljesítményt. Megtekintheti az értékelési jelentést is, hogy megértse a tárolási korlátozásokat a lemez IOPS és átviteli sebességének elemzésével, és keresse meg az igényeinek leginkább megfelelő ajánlott lemezt.

- **Kis-és nagyvállalati lépések**: Kezdje a minimális kockázatú és kevésbé összetett alkalmazások és munkaterhelések áthelyezését az áttelepítési stratégia megbízhatóságának kiépítéséhez. A fejlesztési és tesztelési környezet számítási feladatainak kipróbálásához és áttelepítéséhez a szervezet CMDB-tárházával is összemetszheti Azure Migrate értékelési javaslatait. Az ezekből a próbaverzióokból származó tanulás az éles számítási feladatok áttelepítése során használható.  

- **Megfelel a szabályozási/iparági követelményeknek**: az Azure a legnagyobb megfelelőségi portfóliót tartja az iparágban, az ajánlatok szélessége és mélysége tekintetében. Használja ezt a lehetőséget, hogy rangsorolja az Azure-ba való áttelepítést, és megfeleljen az országos, regionális és iparági szabványoknak és törvényeknek. Ez különösen igaz azoknak a szervezeteknek, amelyek üzleti szempontból kritikus fontosságú vagy bizalmas adatokkal rendelkeznek, vagy nagy mértékben szabályozott iparágakban vannak, ahol a szabványok és rendeletek bővelkednek, és bizonyos esetekben gyakran változhatnak, ami megnehezíti a lépést.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Az áttelepítési terv véglegesítése és felkészülés az áttelepítésre

Az áttelepítési terv véglegesítése előtt győződjön meg arról, hogy a kulcs áttelepítési szempontjai nem játszanak akadályt az áttelepítés megtervezésében:

- Értékelje ki a hálózati sávszélesség és a késés korlátozásait, ami váratlan késéseket okozhat, és megszakítja az áttelepítés replikálási sebességét.

- Az áttelepített alkalmazásokkal kapcsolatos teljesítmény-és felhasználói elfogadási teszteket, illetve az áttelepítés utáni alkalmazások finomhangolását, például az adatbázis-kapcsolati karakterláncok és a webkiszolgáló-konfigurációk frissítését, a átváltás és a tisztítást is végrehajtva.

- Tekintse át az ajánlott Azure-engedélyeket, valamint az áttelepítéshez szükséges kiszolgálói/adatbázis-hozzáférési szerepköröket és az engedélyek modelljét.

- Készítse elő a szervezetét, és győződjön meg arról, hogy a munkaerő igazodik a digitális átalakításhoz. A sikeres szervezeti változások szempontjából elengedhetetlen a Solid Training Foundation. Tekintse meg a [Microsoft Learnon](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)elérhető ingyenes képzést, beleértve az Azure alapjaival, a megoldás-architektúrával és a biztonsággal kapcsolatos tanfolyamokat. Ösztönözze csapatát az [Azure-minősítés](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)megismerésére   is.  

- Ha szükséges, támogatást kaphat a megvalósításhoz. Számos szervezet úgy dönt, hogy külső segítséget nyújt a felhőbe való Migrálás támogatásához. Ha az Azure-ba gyorsan és magabiztosan szeretne áttérni személyre szabott segítséggel, vegye fontolóra egy [Azure-szakértő által felügyelt szolgáltatót](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   vagy [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Hozzon létre egy hatékony felhőalapú áttelepítési tervet, amely részletes információkat tartalmaz az áttelepíteni kívánt alkalmazások listájáról/csoportjairól, az alkalmazás és az adatbázis rendelkezésre állásáról és az állásidő korlátozásáról, valamint a kívánt áttelepítési mérföldköveket. Az áttelepítési tervnek azt is figyelembe kell vennie, hogy mennyi időt vesz igénybe az Adatmásolás, és hogy az áttelepítés utáni tesztelési és átváltás tevékenységek esetében egy figyelmes puffert tartalmazzon. Az áttelepítés utáni tesztelésnek tartalmaznia kell a funkcionális, integrációs, biztonsági és teljesítmény-tesztelési használati eseteket annak biztosítására, hogy az áttelepített alkalmazások a várt módon működjenek, és az összes adatbázis-objektum és adatkapcsolat sikeresen át lett adva a felhőbe.  

Ezzel az elemzéssel létrehozhat egy áttelepítési ütemtervet, és deklarálhat egy karbantartási időszakot az alkalmazások és adatbázisok áttelepítéséhez minimálisan nulla állásidővel, és korlátozhatja a lehetséges működési/üzleti hatásokat az áttelepítés során.  

Azt javasoljuk, hogy mindig tesztelje és folytassa a Azure Migrate *tesztelési áttelepítési* funkciójának használatát, mielőtt az Azure-ba teljes körű Migrálás után kirúgja azokat. Ez a valós adat segít megbecsülni a tényleges időt, és az áttelepítési tervhez szükséges csípéseket tesz elérhetővé. A teszt áttelepítése lehetőséget nyújt arra is, hogy felderítse az áttelepítési terv esetleges problémáit, és kijavítsa azokat a tényleges áttelepítés előtt.  

Ha készen áll az áttelepítésre, használja a Azure Migrate *kiszolgáló-áttelepítési eszközét* , és Azure Migrate *adatáttelepítési szolgáltatását* a teljes körű nyomon követés érdekében a zökkenőmentes és integrált áttelepítési élmény érdekében. A kiszolgáló áttelepítési eszköze támogatja a helyszínen üzemeltetett virtuális gépek és kiszolgálók áttelepítését az ügyfelek adatközpontjában vagy bármely más magán-vagy nyilvános felhőben, beleértve az AWS-t, a GCP, valamint a nulla állásidőt. A Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel.  

> [!NOTE]
> A VMware virtuális gépek esetében a kiszolgáló értékelése a virtuális géphez megadott operációs rendszert használja vCenter Server a vendég operációs rendszer elemzésének kezeléséhez. A VMware-en futó Linux rendszerű virtuális gépek esetében jelenleg nem azonosítja a vendég operációs rendszer pontos kernel-verzióját.

## <a name="next-steps"></a>További lépések

- Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate)   Az Azure Cloud bevezetési keretrendszerében.
- A Azure Migrate [első lépései](https://youtu.be/wFfq3YPxYHE) .
- Hozzon létre egy értékelést a [VMWare virtuális gépek](tutorial-assess-vmware.md) vagy a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)számára.
