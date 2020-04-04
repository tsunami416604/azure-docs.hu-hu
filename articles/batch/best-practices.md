---
title: Gyakorlati tanácsok – Azure Batch
description: Ismerje meg az Azure Batch-megoldás fejlesztésével kapcsolatos gyakorlati tanácsokat és hasznos tanácsokat.
author: LauraBrenner
ms.author: labrenne
ms.date: 04/03/2020
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 94483f8e15b0cd90f76e369034e987bec6da127c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655850"
---
# <a name="azure-batch-best-practices"></a>Az Azure Batch gyakorlati tanácsok

Ez a cikk ismerteti az Azure Batch szolgáltatás hatékony és hatékony használatával kapcsolatos gyakorlati tanácsok gyűjteménye. Ezek az ajánlott eljárások a Batch-szel és a Batch-ügyfelek tapasztalataival kapcsolatos tapasztalatainkból származnak. Fontos megérteni ezt a cikket, hogy elkerülje a tervezési buktatókat, a lehetséges teljesítményproblémákat és az anti-mintákat a Köteg fejlesztése és használata során.

Ebben a cikkben a következőket olvashatja:

> [!div class="checklist"]
> - Melyek a bevált gyakorlatok
> - Miért érdemes az ajánlott eljárásokat alkalmazni?
> - Mi történhet, ha nem követi a legjobb gyakorlatokat?
> - Az ajánlott eljárások követése

## <a name="pools"></a>Készletek

A kötegkészletek a Batch szolgáltatás feladateinek végrehajtásához szükséges számítási erőforrások. A következő szakaszok útmutatást nyújtanak a kötegkészletek kelése során követendő legjobb gyakorlati tanácsokhoz.

### <a name="pool-configuration-and-naming"></a>Készlet konfigurációja és elnevezése

- **Készletfelosztási mód** Kötegelt fiók létrehozásakor két készletfoglalási mód közül választhat: **Kötegelt szolgáltatás** vagy **felhasználói előfizetés**. A legtöbb esetben az alapértelmezett Batch szolgáltatás módot kell használnia, amelyben a készletek a színfalak mögött vannak lefoglalva a Kötegelt felügyelt előfizetésekben. A szintén választható „Felhasználói előfizetés” mód esetében a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. A felhasználói előfizetési fiókok elsősorban a forgatókönyvek fontos, de kis részhalmazának engedélyezésére szolgálnak. A felhasználói előfizetési módról a [Felhasználói előfizetési mód további konfigurációja a felhasználó előfizetési módjában olvashat bővebben.](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)

- **Vegye figyelembe a feladat és a feladat futási idejét a feladat készletleképezésének meghatározásakor.**
    Ha a feladatok elsősorban rövid futó feladatokból állnak, és a várható teljes feladatszám kicsi, így a feladat várható teljes futási ideje nem hosszú, ne foglaljon új készletet minden feladathoz. A csomópontok lefoglalási ideje csökkenti a feladat futási idejét.

- **A készleteknek egynél több számítási csomódat kell rendelkezniük.**
    Az egyes csomópontok nem garantált, hogy mindig elérhető. Bár nem gyakori, a hardverhibák, az operációs rendszer frissítései és számos egyéb probléma miatt az egyes csomópontok offline állapotban maradhatnak. Ha a Batch számítási feladatok determinisztikus, garantált folyamatot igényelnek, több csomót tartalmazó készleteket kell lefoglalnia.

- **Ne használja fel újra az erőforrásneveket.**
    A kötegelt erőforrások (feladatok, készletek stb.) gyakran idővel jönnek és mennek. Létrehozhat például egy készletet hétfőn, törölheti kedden, majd csütörtökön létrehozhat egy másik készletet. Minden létrehozott új erőforrásnak egyedi nevet kell adni, amelyet korábban nem használt. Ezt guid használatával teheti meg (akár a teljes erőforrásnévként, akár annak részeként), vagy beágyazhatja az erőforrás létrehozásának idejét az erőforrás nevében. A Batch támogatja [a DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet)nevet, amellyel emberileg olvasható nevet adhat egy erőforrásnak, még akkor is, ha a tényleges erőforrás-azonosító nem olyan emberi barát. Az egyedi nevek használatával könnyebben megkülönbséget tehet anaplókban és a metrikákban, hogy melyik erőforrás tett valamit. Azt is eltávolítja a kétértelműséget, ha valaha is fájlt egy támogatási eset egy erőforrás.

- **Folytonosság a készlet karbantartása és meghibásodása során.**
    A legjobb, ha a feladatok dinamikusan használják a készleteket. Ha a feladatok mindenhez ugyanazt a készletet használják, akkor van esély arra, hogy a feladatok nem fognak futni, ha valami elromlik a készlettel. Ez különösen fontos az időérzékeny számítási feladatok. A probléma megoldásához jelöljön ki vagy hozzon létre dinamikusan egy készletet az egyes feladatok ütemezésekor, vagy felülbírálhassa a készlet nevét, hogy megkerülhesse a nem megfelelő állapotú készletet.

- **Üzletmenet folytonossága a készlet karbantartása és meghibásodása során** Számos lehetséges oka lehet annak, hogy a készlet a kívánt méretre növekedjen, például belső hibák, kapacitáskorlátok stb. Ebből az okból készen kell lennie arra, hogy újra megcélozza a feladatokat egy másik készletben (esetleg egy másik virtuális gép méretével - A Batch támogatja ezt [az UpdateJob-on](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)keresztül), ha szükséges. Ne használjon statikus készletazonosítót azzal az elvárással, hogy soha nem törlődik, és soha nem változik.

### <a name="pool-lifetime-and-billing"></a>A készlet élettartama és számlázása

A készlet élettartama a foglalás módjától és a készlet konfigurációjához alkalmazott beállításoktól függően változhat. A készletek tetszőleges élettartammal és változó számú számítási csomóponttal rendelkezhetnek a készletben bármely időpontban. Az Ön felelőssége, hogy a készlet számítási csomópontjait explicit módon vagy a szolgáltatás által biztosított funkciókon (automatikus skálázás vagy automatikus készlet) keresztül kezelje.

- **Tartsa medencék friss.**
    Néhány havonta nullára kell méreteznie a készleteket, hogy biztosan megkapja a legújabb csomópontügynök-frissítéseket és hibajavításokat. A készlet csak akkor kapja meg a csomópontügynök-frissítéseket, ha újra létrejön, vagy átméretezi 0 számítási csomópontra. A készlet újbóli létrehozása vagy átméretezése előtt javasoljuk, hogy töltse le a csomóponti ügynök naplók hibakeresési célokra, [a](#nodes) csomópontok szakaszban tárgyalt.

- **Készlet újbóli létrehozása** Hasonló megjegyzésként nem ajánlott naponta törölni és újra létrehozni a készleteket. Ehelyett hozzon létre egy új készletet, frissítse a meglévő feladatokat, hogy az új készletre mutasson. Miután az összes tevékenységet áthelyezték az új készletbe, törölje a régi készletet.

- **Pool hatékonyság és számlázás** Maga a köteg nem jár többletköltségekkel, de a felhasznált számítási erőforrásokért díjat kell fizetnie. A készlet minden számítási csomópontja számlázásra van szüksége, függetlenül attól, hogy milyen állapotban van. Ez magában foglalja a csomópont futtatásához szükséges díjakat, például a tárolási és hálózati költségeket. További gyakorlati tanácsok: [Költségelemzés és költségkeretek az Azure Batch hez.](budget.md)

### <a name="pool-allocation-failures"></a>Készletfelosztási hibák

Készletfoglalási hibák az első foglalás vagy az azt követő átméretek során bármikor előfordulhatnak. Ennek oka lehet egy régióideiglenes kapacitáskimerülése vagy más Azure-szolgáltatások hibái, amelyeken a Batch támaszkodik. Az alapkvóta nem garancia, hanem korlát.

### <a name="unplanned-downtime"></a>Nem tervezett leállás

A batch-készletek az Azure-ban is megtapasztalhatják az állásidő-eseményeket. Ezt fontos szem előtt tartani a forgatókönyv vagy a munkafolyamat tervezésekor és fejlesztésekor a Batch számára.

Abban az esetben, ha egy csomópont meghibásodik, a Batch automatikusan megpróbálja helyreállítani ezeket a számítási csomópontokat az Ön nevében. Ez elindíthatja a helyreállított csomóponton futó feladat átütemezését. A megszakított feladatokról az [Újrapróbálkozások tervezése.](#designing-for-retries-and-re-execution)

- **Az Azure régió függősége** Azt tanácsoljuk, hogy ne függjön egyetlen Azure-régióban, ha egy idő-érzékeny vagy éles számítási feladatok. Bár ritka, vannak olyan problémák, amelyek hatással lehetnek egy egész régióban. Ha például a feldolgozásnak egy adott időpontban kell elkezdődnie, fontolja meg a készlet felskálázását az elsődleges régióban *jóval a kezdési időpont előtt.* Ha ez a készletskálán sikertelen, akkor visszatérhet egy készlet felskálázása egy tartalék régióban (vagy régiókban). A különböző régiókban több fiók között lévő készletek egy kész, könnyen elérhető biztonsági mentést biztosítanak, ha valami elromlik egy másik készlettel. További információ: [Az alkalmazás tervezése magas rendelkezésre állás érdekében.](high-availability-disaster-recovery.md)

## <a name="jobs"></a>Feladatok

A feladat olyan tároló, amely több száz, ezer vagy akár több millió feladatot tartalmaz.

- **Sok feladat belehelyezheti a feladatba** Egyetlen feladat futtatásához nem hatékony feladat használata. Hatékonyabb például egyetlen feladatot használni, amely 1000 feladatot tartalmaz, ahelyett, hogy 100 feladatot hozna létre, amelyek egyenként 10 feladatot tartalmaznak. 1000 feladat futtatása, amelyek mindegyike egyetlen feladattal, a legkevésbé hatékony, leglassabb és legdrágább megközelítést alkalmazná.

    Ne tervezzen olyan Batch-megoldást, amely egyszerre több ezer aktív feladatot igényel. A feladatokra nincs kvóta, így a lehető legkevesebb feladat végrehajtása a lehető legtöbb feladat alatt, hatékonyan használja a [feladat- és feladatütemezési kvótákat.](batch-quota-limit.md#resource-quotas)

- **A munka élettartama** A kötegelt feldolgozás élettartama meghatározatlan, amíg el nem törli kitasztja a rendszerből. A feladat állapota azt határozza meg, hogy el fogadhat-e további feladatokat az ütemezéshez. A feladat csak akkor lép át automatikusan befejezett állapotba, ha kifejezetten le van állítva. Ez automatikusan aktiválható az [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) tulajdonsággal vagy a [maxWallClockTime tulajdonsággal.](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)

Van egy alapértelmezett [aktív feladat- és feladatütemezési kvóta.](batch-quota-limit.md#resource-quotas) A befejezett állapotban lévő feladatok és feladatütemezések nem számítanak bele ebbe a kvótába.

## <a name="tasks"></a>Feladatok

A feladatok olyan munkaegységek, amelyek egy feladatot tartalmaznak. A feladatokat a felhasználó küldi el, és a Batch ütemezi a számítási csomópontokra. A feladatok létrehozásakor és végrehajtásakor számos tervezési szempontot kell figyelembe venni. A következő szakaszok ismertetik a gyakori forgatókönyveket, és azt, hogy miként tervezheti meg a feladatokat a problémák kezelésére és a hatékony végrehajtására.

- **Tevékenységadatok mentése a feladat részeként.**
    A számítási csomópontok természetüknél fogva mulandóak. A Batch számos olyan szolgáltatása van, mint például az automatikus készlet és az automatikus skálázás, amelyek megkönnyítik a csomópontok eltűnését. Amikor a csomópontok elhagyják a készletet (átméretezés vagy készlettörlés miatt), a csomópontokon lévő összes fájl is törlődik. Emiatt azt javasoljuk, hogy mielőtt egy feladat befejeződik, áthelyezi a kimenetet a csomópont fut, és egy tartós tárolóba, hasonlóképpen, ha egy feladat sikertelen, akkor át kell helyeznie a hiba diagnosztizálásához szükséges naplókat egy tartós tárolóba. A Batch integrált támogatással támogatja az Azure Storage-t az adatok [kimeneti fájlokon](batch-task-output-files.md)keresztüli feltöltéséhez, valamint számos megosztott fájlrendszeren keresztül, vagy elvégezheti a feltöltést saját maga a feladatokban.

### <a name="task-lifetime"></a>Feladat élettartama

- **A feladatok törlése, ha elkészültek.**
    Törölje a feladatokat, ha már nincs rájuk szükség, vagy állítson be [egy megőrzési idő](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) feladatmegkötést. Ha `retentionTime` a van beállítva, a Batch automatikusan törli `retentionTime` a feladat által használt lemezterületet, amikor az lejár.

    A feladatok törlése két dolgot tesz lehetővé. Ez biztosítja, hogy nem rendelkezik a feladatok felhalmozódása a feladat, így lekérdezése / keresése a feladat érdekli nehezebb (mert akkor kell szűrni a befejezett feladatok). Emellett törli a megfelelő feladatadatokat a csomóponton (feltéve, hogy `retentionTime` még nem lett lement). Ez biztosítja, hogy a csomópontok ne töltsék meg a feladatadatokat, és ne fussanak ki a lemezterületből.

### <a name="task-submission"></a>Feladat benyújtása

- **Nagy számú tevékenység beküldése egy gyűjteményben.**
    A feladatok at egyénileg vagy beszedésekből lehet benyújtani. A feladatok at legfeljebb [100-as gyűjteményekbe](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) küldheti el a feladatok tömeges benyújtásakor, hogy csökkentse a rezsiköltségeket és a beküldési időt.

### <a name="task-execution"></a>Feladat végrehajtása

- **A maximális feladatok kiválasztása csomópontonként** A Batch támogatja a csomópontokon végzett túlírási feladatokat (több feladatot futtat, mint amennyit egy csomópont rendelkezik magokkal). Ez rajtad múlik, hogy a feladatok "illeszkednek" a csomópontok a medencében. Előfordulhat például, hogy egy leromlott élmény, ha megkísérli ütemezni nyolc feladatokat, amelyek minden fogyasztanak 25%-os CPU-használat-ra egy csomópont (egy készletben). `maxTasksPerNode = 8`

### <a name="designing-for-retries-and-re-execution"></a>Tervezés újrapróbálkozáshoz és újravégrehajtáshoz

A feladatokat a Batch automatikusan újrapróbálkozhatja. Az újrapróbálkozásoknak két típusa van: a felhasználó által vezérelt és a belső. A felhasználó által vezérelt újrapróbálkozásokat a feladat [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)értéke határozza meg. Ha a feladatban megadott program nem nulla kilépési kóddal lép ki, a feladat `maxTaskRetryCount`újra próbálkozik a értékig.

Bár ritka, a feladat a számítási csomópont hibái miatt újrapróbálkozható, például a belső állapot frissítése vagy a csomópont meghibásodása a feladat futása közben. A feladat újra próbálkozik ugyanazon a számítási csomóponton, ha lehetséges, egy belső korlátig, mielőtt feladná a feladatot, és elhalasztaná a feladatot a Batch által átütemezendő, potenciálisan egy másik számítási csomóponton.

- **Tartós feladatok létrehozása** A feladatokat úgy kell megtervezni, hogy ellenálljanak a hibáknak, és alkalmazkodjanak az újrapróbálkozáshoz. Ez különösen fontos a hosszú ideig futó feladatok esetében. Ehhez győződjön meg arról, hogy a feladatok ugyanazt az egyetlen eredményt generálják, még akkor is, ha többször futnak. Ennek egyik módja az, hogy a feladatok "célkereső". Egy másik módja annak, hogy győződjön meg arról, hogy a feladatok idempotens (feladatok ugyanazt az eredményt, függetlenül attól, hogy hányszor futnak).

    Gyakori példa a fájlok számítási csomópontba másolása. Egy egyszerű megközelítés olyan feladat, amely minden futtatáskor az összes megadott fájlt másolja, ami nem hatékony, és nem úgy készült, hogy ellenálljon a hibáknak. Ehelyett hozzon létre egy feladatot annak biztosítására, hogy a fájlok a számítási csomóponton legyenek; olyan feladat, amely nem másolja újra a már meglévő fájlokat. Ily módon a feladat ott folytatja, ahol abbahagyta, ha megszakadt.

- **Alacsony prioritású csomópontok** Nincsenek tervezési különbségek, ha a feladatokat dedikált vagy alacsony prioritású csomópontokon hajtja végre. Függetlenül attól, hogy egy feladat preempted futtatása közben egy alacsony prioritású csomóponton, vagy megszakadt egy hiba egy dedikált csomópont, mindkét helyzet mérséklődik a feladat kialakítása, hogy ellenálljon a hiba.

- **Feladat végrehajtási ideje** Kerülje a rövid végrehajtási idejű feladatokat. A csak egy-két másodpercig futó feladatok nem ideálisak. Meg kell próbálnia jelentős mennyiségű munkát végezni egy adott tevékenységben (legalább 10 másodperc, akár órákra vagy napokra). Ha minden feladat végrehajtása egy percig (vagy több), majd az ütemezési terhelés, mint a teljes számítási idő töredéke kicsi.

## <a name="nodes"></a>Csomópontok

- **A kezdési feladatoknak idempotenseknek kell lenniük** Más feladatokhoz hasonlóan a csomópont indítási feladatának idempotensnek kell lennie, mivel a csomópont minden egyes indításakor újra kell futnia. Az idempotens feladat egyszerűen olyan feladat, amely többszörös futtatáskor konzisztens eredményt ad.

- **Kezelje a hosszú ideig futó szolgáltatásokat az operációs rendszer szolgáltatási felületén keresztül.**
    Néha szükség van egy másik ügynök futtatására a Batch-ügynök mellett a csomóponton, például a csomópontról származó adatok gyűjtéséhez és jelentéséhez. Azt javasoljuk, hogy ezeket az ügynököket operációs rendszer-szolgáltatásként, például Windows-szolgáltatásként vagy Linux-szolgáltatásként `systemd` kell telepíteni.

    A szolgáltatások futtatásakor nem vehetnek fájlzárolást a csomópont Batch által felügyelt könyvtáraiban lévő fájlokon, mert ellenkező esetben a Batch nem tudja törölni ezeket a könyvtárakat a fájlzárolások miatt. Ha például egy Windows-szolgáltatást telepít egy indítási feladatba, ahelyett, hogy a szolgáltatást közvetlenül a kezdő feladat munkakönyvtárából indítanád el, mássza máshová a fájlokat (ha a fájlok léteznek, csak hagyja ki a másolatot). Telepítse a szolgáltatást erről a helyről. Amikor a Batch újrafuttatja a kezdő feladatot, törli a kezdő feladat munkakönyvtárát, és újra létrehozza azt. Ez azért működik, mert a szolgáltatás fájlzárolással rendelkezik a másik könyvtárban, nem pedig a kezdő feladat munkakönyvtárán.

- **Ne hozzon létre címtárcsomópontokat a Windows rendszerben** A címtár-csomópontokat, más néven a könyvtári merevlemez-kapcsolatokat nehéz kezelni a feladat- és feladattisztítás során. Használja symlinks (soft-links) helyett hard-links.

- **A Batch-ügynök naplóinak összegyűjtése, ha probléma merül fel** Ha olyan problémát észlel, amely egy csomópont vagy egy csomóponton futó feladatok viselkedését érinti, javasoljuk, hogy a batch-ügynök naplóit a kérdéses csomópontok felosztása előtt gyűjtse össze. A Batch-ügynök naplók gyűjthetők a Batch-szolgáltatás naplók feltöltése API-t. Ezek a naplók a Microsoft támogatási jegyének részeként adhatók meg, és segítenek a problémák elhárításában és a megoldásban.

## <a name="security"></a>Biztonság

### <a name="security-isolation"></a>Biztonsági elkülönítés

Az elkülönítés céljából, ha a forgatókönyv a feladatok elkülönítését igényli egymástól, akkor különítse el ezeket a feladatokat külön készletekben. A készlet a kötegelt biztonsági elkülönítéshatár, és alapértelmezés szerint két készlet nem látható vagy nem tud kommunikálni egymással. Ne használjon külön Batch-fiókokat elkülönítési eszközként.

## <a name="moving"></a>Mozgó

### <a name="move-batch-account-across-regions"></a>Kötegfiók áthelyezése régiók között

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő Batch-fiók egyik régióból a másikba. Előfordulhat például, hogy a vész-helyreállítási tervezés részeként egy másik régióba szeretne áthelyezni.

Az Azure Batch-fiókok nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a Batch-fiók meglévő konfigurációját.  Ezután egy másik régióban lévő erőforrást úgy helyezheti el, hogy a Batch-fiókot egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba. Miután feltöltötte a sablont az új régióba, újra létre kell hoznia a tanúsítványokat, a feladatütemezéseket és az alkalmazáscsomagokat. A módosítások véglegesítéséhez és a Batch-fiók áthelyezésének befejezéséhez ne felejtse el törölni az eredeti Batch-fiókot vagy erőforráscsoportot.

Az Erőforrás-kezelőről és a sablonokról további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

## <a name="connectivity-to-the-batch-service"></a>A kötegelt szolgáltatással való kapcsolat

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Hálózati biztonsági csoportok (NSG-k) és felhasználó által definiált útvonalak (UDRs)

A [kötegkészletek virtuális hálózatban](batch-virtual-network.md)történő kiépítésekor győződjön meg arról, `BatchNodeManagement` hogy szorosan betartahatja a szolgáltatáscímke, a portok, a protokollok és a szabály irányának használatára vonatkozó irányelveket.
A szolgáltatáscímke használata erősen ajánlott, és nem az alapul szolgáló Batch szolgáltatás IP-címeit, mivel ezek idővel változhatnak. A Batch szolgáltatás IP-címeinek közvetlen használata a Batch-készletek instabilitása, megszakítása vagy kimaradása ként nyilvánulhat meg, mivel a Batch szolgáltatás frissíti az idő múlásával használt IP-címeket. Ha jelenleg használja a Batch szolgáltatás IP-címeket az NSG-szabályokban, javasoljuk, hogy váltson a szolgáltatáscímke használatára.

A felhasználó által definiált útvonalak esetében győződjön meg arról, hogy rendelkezik egy folyamattal a Batch szolgáltatás IP-címeinek rendszeres frissítéséhez az útvonaltáblában, mivel ezek idővel változnak. A Batch-szolgáltatás [IP-címeinek](../virtual-network/service-tags-overview.md)listájának beszerzéséről a helyszíni Szolgáltatáscímkék című témakörben olvashat. A Batch szolgáltatás IP-címei `BatchNodeManagement` a szolgáltatáscímkéhez (vagy a Batch-fiók régiójának megfelelő regionális változathoz) lesznek társítva.

### <a name="honoring-dns"></a>A DNS tiszteletben

Győződjön meg arról, hogy rendszerei tiszteletben tartják a DNS Time-to-Live (TTL) szolgáltatás url-címét. Ezenkívül győződjön meg arról, hogy a Batch szolgáltatás ügyfelei és a Batch szolgáltatás más kapcsolódási mechanizmusai nem támaszkodnak AZ IP-címekre.

Ha a kérelmek 5xx szintű HTTP-válaszokat kapnak, és a válaszban "Kapcsolat: bezárás" fejléc található, a Batch szolgáltatás ügyféljének be kell tartania a javaslatot a meglévő kapcsolat bezárásával, a Batch-fiók szolgáltatás URL-címének DNS-ének újrafelmegoldásával, és új kapcsolatra vonatkozó kéréseket követve.

### <a name="retrying-requests-automatically"></a>Kérelmek automatikus újrapróbálkozása

Győződjön meg arról, hogy a Batch szolgáltatás ügyfelei rendelkeznek a megfelelő újrapróbálkozási szabályzatokkal, hogy automatikusan újrapróbálkozzanak a kérelmekkel, még normál működés közben is, és nem kizárólag a szolgáltatás karbantartási időszakai alatt. Ezek az újrapróbálkozási házirendek legalább 5 perces időtartamúaknak kell megfelelniük. Az automatikus újrapróbálkozási lehetőségek különböző batch SDK-khoz, például a [.NET RetryPolicyProvider osztályhoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)tartoznak.

