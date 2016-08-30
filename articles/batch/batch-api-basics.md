<properties
    pageTitle="Az Azure Batch funkcióinak áttekintése | Microsoft Azure"
    description="Megismerheti a Batch szolgáltatás funkcióit és API-jait a fejlesztés szempontjából."
    services="batch"
    documentationCenter=".net"
    authors="yidingzhou"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/12/2016"
    ms.author="yidingz;marsma"/>

# Az Azure Batch funkcióinak áttekintése

Ez a cikk az Azure Batch szolgáltatás fő API-funkcióinak alapvető áttekintését nyújtja. Akár a [Batch REST][batch_rest_api] API-val, akár a [Batch .NET][batch_net_api] API-val fejleszt elosztott számítási megoldást, az alábbiakban tárgyaltak közül számos entitást és funkciót használni fog.

> [AZURE.TIP] A Batch magasabb szintű műszaki áttekintéséért lásd: [Azure Batch – alapok](batch-technical-overview.md).

## <a name="workflow"></a>A Batch szolgáltatás munkafolyamata

A következő magas szintű munkafolyamat szinte az összes, a Batch szolgáltatáson belül fejlesztett elosztott számítási forgatókönyvre jellemző:

1. Töltse fel az elosztott számítási forgatókönyvben használni kívánt *adatfájlokat* egy [Azure Storage][azure_storage] fiókba. Ezeknek a fájloknak a Storage-fiókban kell lenniük, hogy a Batch szolgáltatás elérhesse azokat. A tevékenységek letöltik ezeket a fájlokat a [számítási csomópontokra](#computenode) a futtatásukkor.

2. Töltse fel a független *bináris fájlokat* a Storage-fiókba. Ezek közé a bináris fájlok közé tartozik a tevékenységek által futtatandó program és annak függő szerelvényei. Ezeket a fájlokat a Storage-fiókból is el kell érni, hogy a tevékenységek letölthessék azokat a számítási csomópontokra.

3. Hozza létre a számítási csomópontok [készletét](#pool). Ön határozza meg a készlet létrehozásakor használni kívánt [számítási csomópontok méretét][cloud_service_sizes], és egy tevékenység futtatásakor ahhoz a rendszer hozzárendeli a készlet egyik csomópontját.

4. Hozzon létre egy [feladatot](#job). A feladatokkal gyűjtési tevékenységeket kezelhet.

5. Adjon hozzá [tevékenységeket](#task) a feladathoz. Mindegyik tevékenység a feltöltött programmal dolgoz fel információkat a Storage-fiókhoz feltöltött adatfájl(ok)ban.

6. Figyelje meg a feladat állapotát, és kérje le az eredményeket.

> [AZURE.NOTE] [Batch-fiókra](batch-account-create-portal.md) van szüksége a Batch szolgáltatás használatához, és majdnem mindegyik megoldás [Azure Storage][azure_storage] fiókot használ a fájltároláshoz és -lekéréshez. A Batch jelenleg csak az **Általános célú** tárfióktípust támogatja, amelynek leírása a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) fejezet 5., [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) című szakaszában található.

Az alábbi szakaszokban megismerheti a fenti munkafolyamatban említett egyes erőforrásokat, valamint a Batch számos más funkcióját, amelyek lehetővé teszik az elosztott számítási forgatókönyvet.

## <a name="resource"></a> A Batch szolgáltatás erőforrásai

A Batch használatakor a következők közül számos erőforrást fog használni. Ezen erőforrások némelyike, például a fiókok, a számítási csomópontok, a készletek, a feladatok és a tevékenységek minden Batch-megoldásban szerepelnek. Mások, például a feladatütemtervek és az alkalmazáscsomagok hasznos, de nem kötelező funkciók.

- [Fiók](#account)
- [Számítási csomópont](#computenode)
- [Készlet](#pool)
- [Feladat](#job)
- [Tevékenység](#task)
    - [Indítási tevékenység](#starttask)
    - [Feladatkezelő tevékenység](#jobmanagertask)
    - [Feladat-előkészítési és -kiadási tevékenységek](#jobpreprelease)
    - [Többpéldányos tevékenységek](#multiinstance)
    - [Tevékenységfüggőségek](#taskdep)
- [Feladatütemezések](#jobschedule)
- [Alkalmazáscsomagok](#appkg)

### <a name="account"></a>Fiók

A Batch-fiókok a Batch szolgáltatáson belül egyedileg azonosított entitások. Minden feldolgozás Batch-fiókkal van társítva. Amikor műveleteket végez a Batch szolgáltatásban, a fióknévre és a fiókkulcsra is szüksége van. Batch-fiók létrehozásához lásd: [Azure Batch-fiókok létrehozása és kezelése az Azure Portalon](batch-account-create-portal.md).

### <a name="computenode"></a>Számítási csomópont

A számítási csomópontok olyan Azure virtuális gépek, amelyek az alkalmazás adott számítási feladatához vannak kijelölve. A csomópont mérete határozza meg a CPU-magok számát, a memóriakapacitást és a csomóponthoz lefoglalt helyi fájlrendszeri méretet. A csomópontok bármilyen [felhőszolgáltatás-csomópontméretűek][cloud_service_sizes] lehetnek az A0 kivételével.

A csomópontok végrehajtható fájlokat és szkripteket futtathatnak, beleértve a végrehajtható fájlokat (.exe), a parancsfájlokat (.cmd), a Batch- (.bat) fájlokat és a PowerShell-szkripteket. A csomópontok a következő attribútumokkal is rendelkeznek:

- Mindegyik számítási csomóponton szabványos **mappastruktúra** és ahhoz kapcsolódó **környezeti változók** jönnek létre, amelyek részletezik az elérési útjukat. További információkat az alábbi [Fájlok és könyvtárak](#files) című szakaszban talál.
- Tevékenységek általi hivatkozáshoz elérhető **környezeti változók**.
- A hozzáférés vezérléséhez konfigurált **tűzfalbeállítások**.
- Ha egy számítási csomópont **távoli elérésére** van szükség (például hibakereséshez), beszerezhető egy RDP-fájl, amellyel ezután elérhető a csomópont *távoli asztalon* keresztül.

### <a name="pool"></a>Készlet

A készletek olyan csomópontok gyűjteményei, amelyeken az alkalmazás fut. A készletet manuálisan létrehozhatja Ön, vagy automatikusan létrehozhatja a Batch szolgáltatás az elvégezni kívánt munka meghatározásakor. Létrehozhat és kezelhet az alkalmazása igényeit kielégítő készletet, és a készleteket csak az a Batch-fiók használhatja, amelyben létrejöttek. Egy Batch-fiók több készlettel is rendelkezhet.

Az Azure Batch-készletek az alapvető Azure számítási platformra épülnek: A Batch készletek nagy méretű lefoglalást, alkalmazástelepítést, adatelosztást és állapotfigyelést nyújtanak, valamint a készleten belüli számítási csomópontok számának rugalmas módosítását (méretezést).

A készlethez adott minden csomóponthoz egyedi név és IP-cím van rendelve. Amikor eltávolít egy csomópontot egy készletből, elveszíti az operációs rendszer vagy a fájlok minden módosítását, és a nevét és az IP-címét a rendszer kiadja későbbi használatra. Amikor egy csomópont kikerül egy készletből, vége van az élettartamának.

Konfigurálhat úgy egy készletet, hogy lehetővé tegye a benne lévő csomópontok közötti kommunikációt. Ha egy készlethez készletek közötti kommunikációt kér, a Batch szolgáltatás engedélyezi az 1100-nál nagyobb portokat a készlet minden csomópontján. A készlet minden csomópontja úgy van konfigurálva, hogy csak ezen a porttartományon engedélyezze a bejövő kapcsolatokat, és csak a készleten belüli más csomópontoktól. Ha az alkalmazáshoz nincs szükség a csomópontok közötti kommunikációra, a Batch szolgáltatás nagy számú csomópont lefoglalására képes a készlethez számos különböző fürtből és adatközpontból, hogy lehetővé tegye a nagyobb párhuzamos feldolgozási teljesítményt.

Készlet létrehozásakor a következő attribútumokat adhatja meg:

- A készletben lévő **csomópontok mérete**
    - Ki kell választani a csomópontok megfelelő méretét, figyelembe véve a csomópontokon futtatni kívánt alkalmazás vagy alkalmazások jellemzőit és követelményeit. A csomópontméret kiválasztásakor általában az feltételezhető, hogy egyszerre egy feladat fog futni a csomóponton. Az olyan szempontok figyelembe vétele, mint hogy az alkalmazás többszálú-e vagy mennyi memóriát fogyaszt, segíthet meghatározni a legmegfelelőbb és legköltséghatékonyabb csomópontméretet. Több feladat rendelhető hozzá és több alkalmazáspéldány futhat párhuzamosan – ebben az esetben általában nagyobb csomópontot választanak. További információért lásd az alábbi „Tevékenységütemezési házirend” szakaszt.
    - A készletek csomópontjainak azonos méretűnek kell lennie. Ha különböző alkalmazásokat kíván futtatni eltérő rendszerkövetelményekkel és/vagy terhelési szinttel, különálló készleteket kell létrehozni.
    - Bármilyen [felhőszolgáltatás-csomópontméret][cloud_service_sizes] konfigurálható a készletekhez az A0 kivételével.

- A csomópontokon futó **operációsrendszer-család** és **verzió**
    - A Cloud Services feldolgozói szerepköreihez hasonlóan az *operációsrendszer-család* és az *operációsrendszer-verzió* is megadható (a feldolgozói szerepkörökkel kapcsolatos további információért lásd a [Tell me about cloud services][about_cloud_services] (Információk a felhőszolgáltatásokról) című szakaszt a *Compute Hosting Options Provided by Azure* (Az Azure által nyújtott számításüzemeltetési lehetőségek) című fejezetben).
    - Az operációsrendszer-család azt is meghatározza, hogy a .NET melyik verziója van telepítve az operációs rendszerrel.
    - A feldolgozói szerepkörökhöz hasonlóan ajánlott a `*` értéket megadni az operációs rendszer verziójához, hogy a csomópontok automatikusan frissüljenek, és hogy ne kelljen semmit végezni az újonnan kiadott verziókhoz. Az adott operációsrendszer-verzió kiválasztásának elsődleges célja annak biztosítása, hogy megmaradjon az alkalmazáskompatibilitás, hogy így a korábbi verziókkal való kompatibilitási tesztet lehessen végezni a verzió frissítésének engedélyezése előtt. A készlet operációsrendszer-verziójának ellenőrzése után a verzió frissíthető, és telepíthető az új operációs rendszerkép – minden futó feladat megszakad, és a rendszer újból a várólistára helyezi őket.

- Azon **csomópontok célszáma**, amelyeknek a készlethez elérhetőnek kell lenniük

- A készlet **méretezési házirendje**
    - A csomópontok száma mellett [automatikus méretezési képletet](batch-automatic-scaling.md) is megadhat a készletekhez. A Batch szolgáltatás végrehajtja a képletet, és beállítja a készletben lévő csomópontok számát a megadott különböző készlet-, feladat- és tevékenységparaméterek alapján.

- **Tevékenységütemezési** házirend
    - A [csomópontonkénti maximális feladatok](batch-parallel-node-tasks.md) konfigurációs lehetőség határozza meg a készleten belüli egyes csomópontokon egyidejűleg futtatható tevékenységek maximális számát.
    - Az alapértelmezett konfiguráció szerint egyszerre egy tevékenység fut egy számítási csomóponton, de vannak olyan forgatókönyvek, ahol előnyös, ha több tevékenység fut egyszerre egy csomóponton. Ennek egyik példája a csomópont kihasználtságának növelése, ha egy alkalmazásnak I/O műveletre kell várnia. Ha egyidőben több alkalmazás fut, az növeli a CPU-használatot. Egy másik példa a készletben lévő csomópontok számának csökkentése. Ez csökkentheti a nagy méretű referencia-adatkészletekhez szükséges adatátvitel mennyiségét – ha az A1 csomópontméret elegendő egy alkalmazáshoz, akkor ehelyett választható az A4 csomópontméret és a készlet 8 egyidejű feladatra konfigurálható, amelyek mindegyike egy magot használ.
    - A „kitöltéstípus” is megadható, amely meghatározza, hogy a Batch egyenletesen osztja-e el a tevékenységeket a csomópontok között, vagy mindegyik csomóponthoz hozzárendeli a maximális számú tevékenységet, mielőtt a készlet másik csomópontjához rendelne tevékenységeket.

- A készletben lévő csomópontok **kommunikációs állapota**
    - A készletek konfigurálhatók úgy, hogy engedélyezzék a készletben lévő csomópontok közötti kommunikációt, ami meghatározza az alapul szolgáló hálózati infrastruktúrát. Vegye figyelembe, hogy ez hatással van a fürtökben lévő csomópontok elhelyezésére is.
    - A legtöbb forgatókönyvben a tevékenységek függetlenül működnek és nem kell kommunikálniuk egymással, de lehet, hogy néhány alkalmazásban kommunikálniuk kell egymással a tevékenységeknek.

- A készletben lévő csomópontok **indítási tevékenysége**
    - Megadható egy *indítási tevékenység*, amelyet mindig végrehajt a rendszer, amikor egy számítási csomópont csatlakozik a készlethez, valamint a csomópontok újraindításakor. Ezt gyakran a csomóponton futó tevékenységek által használt alkalmazások telepítéséhez használják.

### <a name="job"></a>Feladat

A feladatok tevékenységek gyűjteményei, és meghatározzák a számítások elvégzésének módját a készletekben lévő számítási csomópontokon.

- A feladat meghatározza a **készletet**, amelyben a munka futni fog. A készlet lehet egy meglévő készlet, egy számos feladat általi használatra korábban létrehozott készlet vagy egy feladatütemezéshez társított feladatokhoz igény szerint létrehozott készlet, illetve egy feladatütemezéshez társított összes feladathoz létrehozott készlet.
- Választhatóan megadható **feladatprioritás**. Amikor az aktuálisan folyamatban lévő feladatnál nagyobb prioritású feladatot küld el, a nagyobb prioritású feladat tevékenységei az alacsonyabb prioritású feladat tevékenységei elé kerülnek a várólistán. Azonban nem előzik meg a már futó alacsonyabb prioritású feladatokat.
- A feladatok **korlátozásai** a feladatokra vonatkozó bizonyos korlátokat adnak meg.
    - A feladatokhoz beállítható **maximális futási idő**. Ha a feladatok a megadott maximális futási időnél hosszabb ideig futnak, akkor a feladat és az összes hozzá tartozó tevékenység befejeződik.
    - Az Azure Batch képes észlelni a meghiúsult tevékenységeket, és újrapróbálja a tevékenységeket. Korlátozásként megadható a **tevékenységek újrapróbálásának maximális száma**, beleértve azt is, hogy a rendszer mindig vagy soha ne próbálja újra az adott tevékenységet. A tevékenységek újrapróbálása azt jelenti, hogy a tevékenység ismét a várólistára kerül futtatáshoz.
- Az ügyfélalkalmazás tevékenységeket adhat a feladathoz, vagy megadható [Feladatkezelő tevékenység](#jobmanagertask). A feladatkezelő tevékenységek a Batch API-t használják, és tartalmazzák a feladatok tevékenységeinek létrehozásához szükséges információkat, ha a tevékenység a készlet egyik számítási csomópontján fut. A feladatkezelő tevékenységeket külön a Batch kezeli – a feladat létrehozásakor az a várólistára kerül és újraindul, ha meghiúsul. Feladatkezelő tevékenységre van szükség a feladatütemezés által létrehozott feladatokhoz, mivel csak így határozhatók meg a tevékenységek a feladat példányosítása előtt. Az alábbiakban talál további információt a feladatkezelő tevékenységekről.

### <a name="task"></a>Tevékenység

A tevékenységek olyan számítási egységek, amelyek egy feladathoz vannak társítva, és egy csomóponton futnak. A tevékenységek egy csomóponthoz vannak társítva futtatáshoz, vagy a várólistán vannak, amíg egy csomópont szabaddá válik. A tevékenységek a következő erőforrásokat használják:

- A feladat **parancssorában** meghatározott alkalmazás.

- A feldolgozni kívánt adatokat tartalmazó **erőforrásfájlok**. Ezeket a fájlokat a rendszer automatikusan a csomópontra másolja egy **általános célú** Azure Storage-fiók Blob Storage-ából. További információért lásd az alábbi *Indítási tevékenység* és [Fájlok és könyvtárak](#files) szakaszt.

- Az alkalmazás számára szükséges **környezeti változók**. További információkért lásd az alábbi [Környezeti beállítások tevékenységekhez](#environment) című szakaszt.

- Azon **korlátozások**, amelyeken belül a számításnak meg kell történnie. Például a maximális idő, amely alatt a tevékenység futhat, a tevékenység újrapróbálásainak maximális száma, ha meghiúsul, valamint a munkakönyvtárban lévő fájlok megőrzésének maximális ideje.

A csomóponton számítás végzéséhez meghatározott feladatok mellett a Batch szolgáltatás a következő speciális feladatokat is nyújtja:

- [Indítási tevékenység](#starttask)
- [Feladatkezelő tevékenység](#jobmanagertask)
- [Feladat-előkészítési és -kiadási tevékenységek](#jobmanagertask)
- [Többpéldányos tevékenységek](#multiinstance)
- [Tevékenységfüggőségek](#taskdep)

#### <a name="starttask"></a>Indítási tevékenység

Ha **indítási tevékenységet** társít egy készlethez, konfigurálhatja a csomópontjainak működési környezetét olyan műveletek elvégzésekor, mint a szoftverek telepítése vagy háttérfolyamatok indítása. Az indítási tevékenység minden csomópontindításkor fut, amíg a csomópont a készletben marad, például akkor is, amikor a csomópontot először adja a készlethez. Az indítási tevékenység elsődleges előnye, hogy tartalmazza a számítási csomópontok konfigurálásához szükséges összes információt és a feladattevékenység végrehajtásához szükséges telepítési alkalmazásokat. Így a készletekben a csomópontok számának növelése annyira egyszerű, mintha új cél csomópontszámot határozna meg – a Batch már rendelkezik az új csomópontok konfigurálásához szükséges összes információval, és előkészíti ezeket a feladatok fogadására.

Mint bármelyik Batch-tevékenység esetén, megadható az [Azure Storage][azure_storage] **erőforrásfájljainak** listája a végrehajtandó **parancssor** mellett. Az Azure Batch először másolja a fájlokat az Azure Storage-ból, majd futtatja a parancssort. Készlet indítási tevékenységéhez a fájllista általában tartalmazza az alkalmazáscsomagot vagy a fájlokat, de referenciaadatokat is tartalmazhat, amelyeket a számítási csomópontokon futó összes feladat használ. Az indítási tevékenység parancssora PowerShell-szkriptet futtathat, vagy `robocopy` műveletet végezhet, például hogy alkalmazásfájlokat másoljon a „shared” mappába, majd egy MSI-t vagy `setup.exe` parancsot futtasson.

> [AZURE.IMPORTANT] A Batch jelenleg *csak* az **Általános célú** tárfióktípust támogatja, amelynek leírása a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) fejezet 5., [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) című szakaszában található. A Batch-tevékenységeknek (beleértve a szabványos tevékenységeket, az indítási tevékenységeket, a feladat-előkészítést és a feladatkiadási tevékenységeket) olyan erőforrásfájlokat kell meghatározniuk, amelyek *csak* **általános célú** tárfiókokban találhatóak.

Általában célszerű, hogy a Batch szolgáltatás megvárja az indítási tevékenység befejezését, mielőtt a csomópontot késznek ítéli tevékenységek hozzárendeléséhez, de ez konfigurálható.

Ha egy indítási tevékenység meghiúsul egy számítási csomóponton, akkor a csomópont állapota úgy frissül, hogy tükrözze a hibát, és a csomópont nem lesz elérhető tevékenységek hozzárendeléséhez. Az indítási tevékenység meghiúsulhat, ha hiba történik az erőforrásfájlok tárolóról való másolásakor, vagy ha a parancssor által futtatott folyamat nullától eltérő kilépési kódot ad vissza.

#### <a name="jobmanagertask"></a>Feladatkezelő tevékenység

A **feladatkezelő tevékenységeket** általában a feladatok futtatásának vezérléséhez és/vagy megfigyeléséhez használják. Például a feladat tevékenységeinek létrehozásához és elküldéséhez, további futtatandó tevékenységek meghatározásához és annak meghatározásához, hogy a munka mikor van kész. A feladatkezelő tevékenységek nem korlátozódnak ezekre a tevékenységekre, azonban ez egy teljes körű tevékenység, amely a feladathoz szükséges bármely műveletet el tud végezni. Egy feladatkezelő tevékenység például letölthet egy paraméterként meghatározott fájlt, elemezheti a fájl tartalmát, és további tevékenységeket küldhet el a tartalom alapján.

A feladatkezelő tevékenységek a többi tevékenység előtt indulnak el, és a következő funkciókat nyújtják:

- A Batch szolgáltatás automatikusan küldi el ezeket tevékenységekként a feladat létrehozásakor.

- Úgy vannak ütemezve, hogy a feladatok más tevékenységei előtt fussanak.

- A hozzájuk társított csomópontot távolítja el a rendszer utoljára a készletből a készlet méretének csökkentésekor.

- A befejezésük a feladat összes tevékenységének befejezéséhez köthető.

- A feladatkezelő tevékenység a legnagyobb prioritást kapja, amikor újra kell indítani. Ha egy tétlen csomópont nem érhető el, a Batch szolgáltatás leállíthatja valamelyik másik futó tevékenységet a készletben, hogy helyet csináljon a feladatkezelő tevékenység futtatásához.

- Az egyik feladat feladatkezelő tevékenysége nem rendelkezik nagyobb prioritással, mint más feladatok tevékenységei. A feladatok között csak a feladatszintű prioritások érvényesek.

#### <a name="jobpreprelease"></a>Feladat-előkészítési és -kiadási tevékenységek

A Batch biztosítja a feladatelőkészítési tevékenységet a feladat végrehajtás előtti beállításához, valamint a feladatkiadási tevékenységet a végrehajtás utáni kezeléshez vagy törléshez.

- **Feladat-előkészítési tevékenység** – A feladat-előkészítési tevékenység a tevékenységek futtatására ütemezett összes számítási csomóponton fut minden más tevékenység végrehajtása előtt. A feladat-előkészítési tevékenységgel többek között az összes tevékenységre jellemző, de a feladat esetében egyedi adatokat másolhat.
- **Feladatkiadási tevékenység** – Amikor egy feladat elkészült, a feladatkiadási tevékenység a készlet minden csomópontján fut, amely legalább egy tevékenységet végzett. A feladatkiadási tevékenységgel a feladatelőkészítési tevékenység által másolt adatokat törölhet, vagy például diagnosztikai naplóadatokat tömöríthet és tölthet fel.

A feladatelőkészítési és -kiadási tevékenységek is lehetővé teszik futtatandó parancssor megadását a tevékenység indításakor, és olyan funkciókat nyújtanak, mint a fájlletöltés, az emelt szintű futtatás, az egyéni környezeti változók, a maximális végrehajtási idő, az újrapróbálkozások száma és a fájlmegőrzési idő.

A feladatelőkészítési és -kiadási tevékenységekkel kapcsolatos további információért lásd: [Feladat-előkészítési és -befejezési műveletek futtatása Azure Batch számítási csomópontokon](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Többpéldányos tevékenységek

A [többpéldányos tevékenységek](batch-mpi.md) olyan tevékenységek, amelyek több számítási csomóponton való egyidejű futtatásra vannak konfigurálva. Többpéldányos tevékenységek esetén olyan nagy teljesítményű feldolgozási forgatókönyveket engedélyezhet, mint a Message Passing Interface (MPI), amelyhez számítási csomópontok csoportját kell együtt lefoglalni egyetlen számítási feladat feldolgozásához.

A Batch szolgáltatásban az MPI-feladatok Batch .NET könyvtárral való futtatásának részletes leírásáért lásd: [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Többpéldányos tevékenységek használata Message Passing Interface- (MPI-) alkalmazások futtatásához az Azure Batch szolgáltatásban).

#### <a name="taskdep"></a>Tevékenységfüggőségek

A tevékenységfüggőségek, ahogyan a nevük is jelzi, lehetővé teszik annak megadását, hogy egy tevékenység más tevékenységek elvégzésétől függ a végrehajtása előtt. Ez a funkció olyan helyzeteket támogat, amelyekben egy „alsóbb rétegbeli” tevékenység egy „felsőbb rétegbeli” tevékenység kimenetét használja, vagy amikor egy felsőbb rétegbeli tevékenység alsóbb rétegbeli tevékenység által igényelt inicializálást végez. Ezen funkció használatához először engedélyeznie kell a tevékenységfüggőségeket a Batch-feladatban. Ezután az egy másik (vagy sok másik) tevékenységtől függő mindegyik tevékenységhez meg kell adnia azokat a tevékenységeket, amelyektől függnek.

A tevékenységfüggőségekkel a következőkhöz hasonló forgatókönyveket konfigurálhat:

* A *taskB* a *taskA* tevékenységtől függ (a *taskB* végrehajtása nem kezdődik meg a *taskA* befejeződéséig)
* A *taskC* a *taskA* és a *taskB* tevékenységtől is függ
* A *taskD* egy tevékenységtartománytól függ, például az *1*–*10.* tevékenységtől a végrehajtása előtt

Tekintse meg a [TaskDependencies][github_sample_taskdeps] kódmintát a [azure-batch-samples][github_samples] GitHub-adattárban. Ebben megismerheti, hogyan konfigurálhat más tevékenységektől függő tevékenységeket a [Batch .NET][batch_net_api] könyvtárral.

### <a name="jobschedule"></a>Ütemezett feladatok

A feladatütemezéssel rendszeresen előforduló feladatokat ütemezhet a Batch szolgáltatásban. A feladatütemezés meghatározza, mikor fussanak a feladatok, és tartalmazza a futtatandó feladatok specifikációit. A feladatütemezés lehetővé teszi az ütemezés időtartamának meghatározását (mennyi ideig és mikor érvényes az ütemezés), valamint azt, hogy ezen időszakon belül milyen gyakran kell létrehozni feladatokat.

### <a name="appkg"></a>Alkalmazáscsomagok

Az [alkalmazáscsomagok](batch-application-packages.md) funkció az alkalmazások egyszerű kezelését és üzembe helyezését nyújtja a készletek számítási csomópontjain. Az alkalmazáscsomagokkal könnyedén töltheti fel és kezelheti a tevékenységek által futtatott alkalmazások több verzióját, beleértve a bináris és támogató fájlokat, majd automatikusan üzembe helyezhet ezen alkalmazások közül egyet vagy többet a készlet számítási csomópontjain.

A Batch a háttérben kezeli az Azure Storage használatának részleteit az alkalmazáscsomagok biztonságos tárolása és számítási csomópontokon való üzembe helyezése érdekében, hogy a kódolás és a kezelés is egyszerűbb legyen.

Az alkalmazáscsomag funkcióval kapcsolatos további részletekért lásd: [Alkalmazástelepítés Azure Batch-alkalmazáscsomagokkal](batch-application-packages.md).

## <a name="files"></a>Fájlok és könyvtárak

Mindegyik tevékenység rendelkezik munkakönyvtárral, amelyben nulla vagy több fájlt és könyvtárat hoz létre a tevékenység által futtatott program, az általa feldolgozott adatok és a tevékenység által végzett feldolgozás kimenetének tárolásához. Ezeket a fájlokat és könyvtárakat ezután a többi tevékenység használhatja a feladatok futtatásakor. A csomóponton lévő összes tevékenység, fájl és könyvtár tulajdonosa egyetlen felhasználói fiók.

A Batch szolgáltatás „gyökérkönyvtárként” fedi fel a csomóponton lévő fájlrendszer egy részét. A gyökérkönyvtár a tevékenységek számára a(z) `%AZ_BATCH_NODE_ROOT_DIR%` környezeti változóhoz való hozzáféréssel érhető el. A környezeti változók használatával kapcsolatos további információért lásd: [Környezeti beállítások tevékenységekhez](#environment).

![Számítási csomópont könyvtárstruktúrája][1]

A gyökérkönyvtár a következő könyvtárstruktúrát tartalmazza:

- **Shared** – Ez a hely a csomóponton futó összes tevékenység megosztott könyvtára a feladattól függetlenül. A csomóponton a megosztott könyvtár a(z) `%AZ_BATCH_NODE_SHARED_DIR%` segítségével érhető el. Ez a könyvtár olvasási/írási hozzáférést nyújt a csomóponton végrehajtott összes tevékenység számára A tevékenységek fájlokat hozhatnak létre, olvashatnak, frissíthetnek és törölhetnek ebben a könyvtárban.

- **Startup** – Az indítási tevékenység ezt a helyet használja munkakönyvtárként. A Batch szolgáltatás által az indítási tevékenység indításához letöltött összes fájl is ebben a könyvtárban van tárolva. A csomóponton a Startup könyvtár a(z) `%AZ_BATCH_NODE_STARTUP_DIR%` környezeti változón keresztül érhető el. Az indítási tevékenység fájlokat hozhat létre, olvashat, frissíthet és törölhet ebben a könyvtárban, és az indítási tevékenységek ezzel a könyvtárral konfigurálhatják az operációs rendszert.

- **Tasks** – Létrejön egy könyvtár a csomóponton futó minden egyes feladathoz, amelyek a(z) `%AZ_BATCH_TASK_DIR%` változón keresztül érhetők el. A Batch szolgáltatás minden tevékenységkönyvtárban létrehoz egy munkakönyvtárat (`wd`), amelynek egyedi elérési útját a(z) `%AZ_BATCH_TASK_WORKING_DIR%` környezeti változó határozza meg. Ez a könyvtár olvasási/írási hozzáférést nyújt a tevékenységhez. A tevékenység fájlokat hozhat létre, olvashat, frissíthet és törölhet ebben a könyvtárban, és ez a könyvtár a tevékenységhez meghatározott *RetentionTime* korlátozás alapján van megőrizve.
  - `stdout.txt` és `stderr.txt` – Ezeket a fájlokat írja a tevékenységmappába a rendszer a tevékenység futtatásakor.

Amikor eltávolít egy csomópontot a készletből, a csomóponton tárolt összes fájlt is eltávolítja.

## <a name="lifetime"></a>Készlet és számítási csomópont élettartama

Az Azure Batch megoldás megtervezésekor el kell dönteni, hogyan és mikor jöjjenek létre készletek, és mennyi ideig legyenek elérhetőek a készletekben lévő számítási csomópontok.

A spektrum egyik végén létrehozható egy készlet minden egyes feladathoz a feladat elküldésekor, amelynek csomópontjai a tevékenységek végrehajtásának befejezésekor eltávolíthatók. Ez maximalizálja a kihasználtságot, mivel a csomópontok csak akkor vannak lefoglalva, amikor szükségesek, és leállnak, amint tétlenné válnak. Bár ez azt jelenti, hogy a feladatnak meg kell várnia a csomópontok lefoglalását, fontos megjegyezni, hogy a tevékenységek ütemezve lesznek a csomópontokon, ahogy önállóan elérhetővé válnak, le vannak foglalva és ahogy befejeződött az indítási tevékenység. A Batch *nem* várja meg a tevékenységek hozzárendelése előtt, hogy a készletben lévő összes csomópont elérhetővé váljon, így az összes elérhető csomópont maximális kihasználtságát biztosítja.

A spektrum másik végén, ha a feladatok azonnali elindítása a legfontosabb, létrehozható egy készlet idő előtt, és a csomópontjai elérhetővé tehetőek a feladatok elküldése előtt. Ebben a forgatókönyvben a feladatok tevékenységei azonnal indulhatnak, de a csomópontok tétlenek lehetnek a tevékenységek hozzárendeléséig.

Egy kombinált megközelítés, amelyet általában változó, de folyamatos terheléshez használnak, ha egy készlethez több feladat van elküldve, de a csomópontok számát a feladatterhelésnek megfelelően növelik vagy csökkentik (lásd az alábbi *Alkalmazások méretezése* szakaszt). Ez reaktív módon végezhető az aktuális terhelés alapján, vagy proaktív módon, ha a terhelés előrejelezhető.

## <a name="scaling"></a>Alkalmazások méretezése

Az [automatikus méretezéssel](batch-automatic-scaling.md) megadhatja, hogy a Batch szolgáltatás dinamikusan állítsa be a számítási csomópontok számát a készletben az aktuális számítási feladatok és a számítási forgatókönyv erőforrás-használata alapján. Ez lehetővé teszi az alkalmazásfuttatás teljes költségeinek csökkentését, mert csak a szükséges erőforrásokat használja, és feloldja a szükségteleneket. A készletek automatikus méretezési beállításait a készlet létrehozásakor határozhatja meg, vagy később engedélyezheti, majd frissítheti azon készlet méretezési beállításait, amely esetében az automatikus méretezés engedélyezve van.

Az automatikus méretezés egy **automatikus méretezési képlet** megadásával végezhető el a készlethez. A Batch szolgáltatás ezzel a képlettel határozza meg a készletben a csomópontok célszámát a következő méretezési időközhöz (amely megadható időköz).

Lehet például, hogy egy feladathoz számos tevékenységet kell elküldeni, amelyek futtatását ütemezni kell. Ekkor egy méretezési képletet rendelhet a készlethez, amely beállítja a készletben lévő csomópontok számát a függő tevékenységek aktuális száma és ezen tevékenységek befejezettségi állapota alapján. A Batch szolgáltatás időnként kiértékeli a képletet és átméretezi a készletet a számítási feladatok és a képlet beállításai alapján.

A méretezési képletek a következő mérőszámokon alapulhatnak:

- **Időmérőszámok** – A megadott számú órák során öt percenként gyűjtött statisztikák alapján.

- **Erőforrás-mérőszámok** – A CPU-használat, sávszélesség-használat, memóriahasználat és a csomópontok száma alapján.

- **Tevékenység-mérőszámok** – A tevékenységek állapota alapján (például Aktív, Függőben és Befejezve).

Amikor az automatikus méretezés csökkenti a készletben lévő számítási csomópontok számát, figyelembe kell venni az aktuálisan futó tevékenységeket. Ehhez igazodva a képlet tartalmazhat egy csomópont-felszabadítási házirend-beállítást, amely meghatározza, hogy a futó tevékenységek azonnal leállnak-e, vagy befejeződhetnek-e, mielőtt eltávolítja a csomópontot a készletből.

> [AZURE.TIP] A számítási erőforrások kihasználtságának maximalizálása érdekében állítsa nullára a csomópontok célszámát a feladatok végén, de engedélyezze a futó tevékenységek befejeződését.

Az alkalmazások automatikus méretezésével kapcsolatos további információért lásd: [Számítási csomópontok automatikus méretezése egy Azure Batch-készletben](batch-automatic-scaling.md).

## <a name="cert"></a>Biztonság tanúsítványokkal

Általában tanúsítványokat kell használnia tevékenységek bizalmas információinak, például az [Azure Storage-fiókok][azure_storage] kulcsának titkosításakor vagy visszafejtésekor. Ennek támogatása érdekében tanúsítványok telepíthetők a csomópontokra. A titkosított titkos kulcsok parancssori paraméterek segítségével vagy valamelyik tevékenység-erőforrásba ágyazva jutnak el a tevékenységekhez, és a telepített tanúsítványokkal fejthetők vissza.

A [Tanúsítvány hozzáadása][rest_add_cert] művelettel (Batch REST API) vagy a [CertificateOperations.CreateCertificate][net_create_cert] metódussal (Batch .NET API) adhat tanúsítványt Batch-fiókhoz. Ezután a tanúsítványt új vagy meglévő készlethez társíthatja. Amikor egy tanúsítvány egy készlethez van társítva, a Batch szolgáltatás telepíti a tanúsítványt a készlet minden csomópontján. A Batch szolgáltatás telepíti a megfelelő tanúsítványokat a csomópont indulásakor a tevékenységek indítása előtt, beleértve az indítási és a feladatkezelő tevékenységeket is.

## <a name="scheduling"></a>Ütemezési prioritás

Prioritást rendelhet a Batch szolgáltatásban létrehozott feladatokhoz. A Batch szolgáltatás a feladat prioritási értékével határozza meg a feladatütemezés sorrendjét a fiókokon belül. A prioritási értékek –1000 és 1000 közöttiek, ahol a –1000 a legalacsonyabb prioritás, az 1000 pedig a legmagasabb. A feladatok prioritásának frissítése a [Feladat tulajdonságainak frissítése][rest_update_job] művelettel (Batch REST API) vagy a [CloudJob.Priority][net_cloudjob_priority] tulajdonság (Batch .NET API) módosításával lehetséges.

Egy adott fiókban a magasabb prioritású feladatok élveznek elsőbbséget az ütemezésben az alacsonyabb prioritású feladatokkal szemben. Egy fiók magasabb prioritási értékkel rendelkező feladatai nem élveznek elsőbbséget egy másik fiók alacsonyabb prioritási értékű másik feladatával szemben.

A készletek között a feladatok ütemezése egymástól független. Különböző készletek között nem garantált, hogy egy magasabb prioritású feladat lesz először ütemezve, ha annak társított készletében nincsenek tétlen csomópontok. Egy adott készletben az azonos prioritású munkák ütemezésére ugyanannyi esély van.

## <a name="environment"></a>Környezeti beállítások tevékenységekhez

A Batch-feladatokban végrehajtott összes tevékenység hozzáfér a Batch szolgáltatás által megadott környezeti változókhoz (rendszer által meghatározott, lásd az alábbi táblázatot) és a felhasználói környezeti változókhoz is. A számítási csomópontokon lévő tevékenységek által futtatott alkalmazások és szkriptek hozzáférnek ezekhez a környezeti változókhoz a csomóponton való futtatáskor.

A felhasználói környezeti változókat akkor adhatja meg, amikor a [Tevékenység hozzáadása feladathoz][rest_add_task] műveletet használja (Batch REST API), vagy módosítja a [CloudTask.EnvironmentSettings][net_cloudtask_env] tulajdonságot (Batch .NET API), amikor tevékenységeket ad egy feladathoz.

A tevékenységek (rendszer és felhasználó által megadott) környezeti változóinak lekéréséhez használja a [Feladat információinak lekérése][rest_get_task_info] műveletet (Batch REST API), vagy nyissa meg a [CloudTask.EnvironmentSettings][net_cloudtask_env] tulajdonságot (Batch .NET API). Ahogy már említettük, a számítási csomópontokon futtatott folyamatok elérhetik az összes környezeti változót, például az ismert `%VARIABLE_NAME%` szintaxissal.

A feladatokon belül ütemezett minden tevékenység esetében a következő, rendszer által meghatározott környezeti változók készletét adja meg a Batch szolgáltatás:

| Környezeti változó neve       | Leírás                                                              |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME`         | A fiók neve, amelyhez a tevékenység tartozik.                       |
| `AZ_BATCH_JOB_ID`               | A feladat azonosítója, amelyhez a tevékenység tartozik.                             |
| `AZ_BATCH_JOB_PREP_DIR`         | A csomóponton a feladat-előkészítési tevékenység könyvtárának teljes elérési útja.         |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | A csomóponton a feladat-előkészítési tevékenység munkakönyvtárának teljes elérési útja. |
| `AZ_BATCH_NODE_ID`              | A csomópont azonosítója, amelyen a tevékenység fut.                         |
| `AZ_BATCH_NODE_ROOT_DIR`        | A csomóponton a gyökérkönyvtár teljes elérési útja.                         |
| `AZ_BATCH_NODE_SHARED_DIR`      | A csomóponton a shared könyvtár teljes elérési útja.                       |
| `AZ_BATCH_NODE_STARTUP_DIR`     | A csomóponton a számítási csomópont indítási tevékenysége könyvtárának teljes elérési útja.    |
| `AZ_BATCH_POOL_ID`              | A készlet azonosítója, amelyen a tevékenység fut.                         |
| `AZ_BATCH_TASK_DIR`             | A csomóponton a tevékenység könyvtárának teljes elérési útja.                         |
| `AZ_BATCH_TASK_ID`              | Az aktuális tevékenység azonosítója.                                              |
| `AZ_BATCH_TASK_WORKING_DIR`     | A csomóponton a tevékenység munkakönyvtárának teljes elérési útja.                 |

>[AZURE.NOTE] Nem írhatja felül a fenti, rendszer által meghatározott változókat – csak olvashatóak.

## <a name="errorhandling"></a>Hibakezelés

Szükséges lehet a Batch megoldáson belül a tevékenység és az alkalmazások hibáinak a kezelése is.

### Tevékenységhibák kezelése
A tevékenységhibák a következő kategóriákba esnek:

- **Ütemezési hibák**
    - Ha egy tevékenységhez megadott fájlok átvitele valamilyen okból meghiúsul, „ütemezési hiba” van beállítva a feladathoz.
    - Az ütemezési hibák azért fordulhatnak elő, mert a fájlok elmozdultak, a tárfiók már nem érhető el vagy más hiba történt, amely meggátolta a fájlok sikeres csomópontra másolását.
- **Alkalmazáshibák**
    - A tevékenység parancssora által meghatározott folyamat is meghiúsulhat. A folyamat sikertelennek minősül, amikor nullától eltérő kilépési kódot ad vissza a tevékenység által végrehajtott folyamat.
    - Alkalmazáshibák esetén úgy konfigurálhatja a Batch szolgáltatást, hogy automatikusan újrapróbálja a tevékenységet a megadott számú alkalommal.
- **Korlátozáshibák**
    - Beállítható olyan korlátozás, amely meghatározza a feladat vagy tevékenység maximális végrehajtási időtartamát, a *maxWallClockTime* értéket. Ez lefagyott feladatok leállításához lehet hasznos.
    - Amikor túllépte a maximális időtartamot, a tevékenység *befejezettként* van megjelölve, de a kilépési kód `0xC000013A` értékű, és a *schedulingError* mező `{ category:"ServerError", code="TaskEnded"}` értékű.

### Alkalmazáshibák keresése

A futtatás során az alkalmazások diagnosztikai kimenetet készíthetnek, amely hibaelhárításhoz használható. A fenti [Fájlok és könyvtárak](#files) fejezetben említettek szerint a Batch szolgáltatás stdout és stderr kimenetet küld a számítási csomópont tevékenységkönyvtárában lévő `stdout.txt` és `stderr.txt` fájlnak. A Batch .NET API [ComputeNode.GetNodeFile][net_getfile_node] és [CloudTask.GetNodeFile][net_getfile_task] parancsával lekérheti ezeket a fájlokat és más fájlokat hibaelhárítási célból.

Még kiterjedtebb hibakeresés végezhető, ha *távoli asztallal* jelentkezik be egy számítási csomópontra. [Lekérhet egy távoli asztali protokollfájlt egy csomópontról][rest_rdp] (Batch REST API), vagy a [ComputeNode.GetRDPFile][net_rdp] metódust (Batch .NET API) használhatja a távoli bejelentkezéshez.

>[AZURE.NOTE] Ha egy csomóponthoz RDP-n keresztül szeretne csatlakozni, először létre kell hoznia egy felhasználót a csomóponton. [Adjon egy felhasználói fiókot egy csomóponthoz][rest_create_user] a Batch REST API-ban, vagy használja a [ComputeNode.CreateComputeNodeUser][net_create_user] metódust a Batch .NET-ben.

### Tevékenységhibák vagy -megszakítások kezelése

A tevékenységek időnként meghiúsulhatnak vagy megszakadhatnak. Maga a tevékenységalkalmazás meghiúsulhat, vagy újraindulhat a tevékenységet futtató csomópont, vagy a rendszer eltávolíthatja a csomópontot a készletből egy átméretezési művelet során, ha a készlet felszabadítási házirendje úgy van megadva, hogy azonnal eltávolítsa a csomópontokat a tevékenységek befejeződésének megvárása nélkül. A tevékenységet a Batch minden esetben képes automatikusan újra a várólistára helyezni, hogy másik csomóponton legyen végrehajtva.

Az is előfordulhat, hogy egy ismétlődő hiba miatt egy tevékenység lefagy, vagy túl sok ideig tart a végrehajtása. Beállítható a tevékenységek maximális végrehajtási ideje, és ha ezt túllépik, a Batch megszakítja a tevékenységalkalmazást.

### „Rossz” számítási csomópontok hibaelhárítása

Olyan esetekben, ahol néhány tevékenység meghiúsul, a Batch ügyfélalkalmazás vagy szolgáltatás megvizsgálhatja a meghiúsult tevékenységek metaadatait a rosszul működő csomópontok azonosítása érdekében. A készletek minden csomópontja egyedi azonosítót kap, és a tevékenységet futtató csomópont szerepel a tevékenység metaadataiban. Az azonosítás után több műveletet végezhet:

- **Újraindíthatja a csomópontot** ([REST][rest_reboot] | [.NET][net_reboot])

    A csomópont újraindítása néha segít a rejtett problémákon, például az elakadt vagy összeomlott folyamatokon. Vegye figyelembe, hogy ha a készlet indítási tevékenységet használ, vagy a feladat feladat-előkészítési tevékenységet használ, ezeket a rendszer a csomópont újraindításakor végrehajtja.

- **Alaphelyzetbe állíthatja a csomópont rendszerképét** ([REST][rest_reimage] | [.NET][net_reimage])

    Ez újratelepíti az operációs rendszert a csomóponton. A csomópontok újraindításához hasonlóan újrafuttatja az indítási tevékenységeket és a feladat-előkészítési tevékenységeket a csomópont rendszerképének alaphelyzetbe állítása után.

- **Eltávolíthatja a csomópontot a készletből** ([REST][rest_remove] | [.NET][net_remove])

    Néha teljesen el kell távolítani a csomópontot a készletből.

- **Letilthatja a tevékenységütemezést a csomóponton** ([REST][rest_offline] | [.NET][net_offline])

    Ez tulajdonképpen „offline” állapotba helyezi a csomópontot, így ahhoz nem rendel további tevékenységeket, de engedélyezi, hogy a csomópont továbbra is fusson a készletben. Ez lehetővé teszi, hogy tovább vizsgálja a hibák okait a meghiúsult tevékenység adatainak elvesztése nélkül, és anélkül, hogy a csomópont további tevékenységhibákat okozna. Letilthatja például a tevékenységütemezést a csomóponton, majd távolról bejelentkezhet a csomópont eseménynaplóinak megvizsgálása vagy egyéb hibaelhárítás elvégzése érdekében. Ha végzett a vizsgálattal, online állapotba állíthatja a csomópontot a tevékenységütemezés engedélyezésével ([REST][rest_online], [.NET][net_online]), vagy a fentiekben említett más műveletek egyikét végezheti el.

> [AZURE.IMPORTANT] A fenti műveletek mindegyikével (újraindítás, rendszerkép alaphelyzetbe állítása, eltávolítás, tevékenységütemezés letiltása) meghatározhatja, hogy a rendszer hogyan kezelje a csomópontokon aktuálisan futó tevékenységeket a művelet elvégzésekor. Amikor például letiltja a tevékenységütemezést egy csomóponton a Batch .NET ügyfélkönyvtárral, megadhat egy [DisableComputeNodeSchedulingOption][net_offline_option] enumerálási értéket annak meghatározásához, hogy **leállítja** a futó tevékenységeket, **újból várólistára helyezi** azokat más csomópontokon való ütemezéshez, vagy engedélyezi a futó tevékenységek befejezését a művelet elvégzése előtt (**TaskCompletion**).

## Következő lépések

- Az első Batch-alkalmazás létrehozásához kövesse az [Ismerkedés az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md) című szakasz lépéseit
- Töltse le és állítsa össze a [Batch Explorer][batch_explorer_project] mintaprojektet a Batch megoldások fejlesztése alatti használathoz. A Batch Explorerrel többek között a következőket végezheti el:
  - Készletek, feladatok és tevékenységek megfigyelése és módosítása a Batch-fiókban
  - A `stdout.txt`, az `stderr.txt` és más fájlok letöltése csomópontokról
  - Felhasználók létrehozása csomópontokon és RDP-fájlok letöltése távoli bejelentkezéshez

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx



<!--HONumber=jun16_HO2-->


