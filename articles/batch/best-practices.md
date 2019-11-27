---
title: Ajánlott eljárások – Azure Batch
description: Ismerje meg az ajánlott eljárásokat és hasznos tippeket a Azure Batch megoldás fejlesztéséhez.
author: laurenhughes
ms.author: lahugh
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: gwallace
ms.openlocfilehash: 19c5b6acaeddb915af49cf62a884da0678075f15
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535664"
---
# <a name="azure-batch-best-practices"></a>Azure Batch ajánlott eljárások

Ez a cikk a Azure Batch szolgáltatás hatékony és hatékony használatához ajánlott eljárások gyűjteményét tárgyalja. Ezek az ajánlott eljárások a Batch és a Batch-ügyfelek tapasztalataiból származnak. Fontos megérteni ezt a cikket a buktatók, a lehetséges teljesítménybeli problémák és a mintázatok kialakításának elkerülése érdekében a Batch használata során.

Ebben a cikkben a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Az ajánlott eljárások
> - Miért érdemes az ajánlott eljárásokat használni
> - Mi történhet, ha nem sikerül követnie az ajánlott eljárásokat
> - Az ajánlott eljárások követése

## <a name="pools"></a>Készletek

A Batch-készletek a feladatok a Batch szolgáltatásban való végrehajtásához szükséges számítási erőforrások. A következő szakaszokban útmutatást talál a Batch-készletekkel végzett munka során követendő legjobb gyakorlatokról.

### <a name="pool-configuration-and-naming"></a>Készlet konfigurációja és elnevezése

- **Készletlefoglalási mód**  
    Batch-fiók létrehozásakor választhat két készlet kiosztási módja közül: **Batch szolgáltatás** vagy **felhasználói előfizetés**. A legtöbb esetben az alapértelmezett batch szolgáltatás módot kell használnia, amelyben a készletek a Batch által felügyelt előfizetésekben a színfalak mögött vannak lefoglalva. A szintén választható „Felhasználói előfizetés” mód esetében a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. A felhasználói előfizetési fiókok elsődlegesen a fontos, de a forgatókönyvek kis részhalmazának engedélyezésére használatosak. A felhasználói előfizetési móddal kapcsolatos további információkért tekintse meg a [felhasználói előfizetés üzemmódjának további konfigurációját](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Vegye figyelembe a feladat és a feladat futási idejét a készlet hozzárendelésének meghatározásakor.**  
    Ha a feladatok elsősorban rövid ideig futó tevékenységekből állnak, és a tevékenységek várható összesített száma kicsi, így a feladat teljes várható futási ideje nem hosszú, ne foglaljon le új készletet az egyes feladatokhoz. A csomópontok lefoglalási ideje csökkenti a feladatok futási idejét.

- **A készleteknek több számítási csomóponttal kell rendelkezniük.**  
    Az egyes csomópontok nem garantáltak, hogy mindig elérhetők legyenek. Habár a nem gyakori, a hardverhiba, az operációsrendszer-frissítések és más problémák egy állomása az egyes csomópontok offline állapotba helyezését eredményezheti. Ha a Batch-munkafolyamathoz determinisztikus szükséges, a garantált előrehaladást kell biztosítania, több csomóponttal rendelkező készleteket kell kiosztania.

- **Ne használja újra az erőforrásnevek nevét.**  
    A Batch-erőforrások (feladatok, készletek stb.) gyakran jönnek, és idővel eltérhetnek. Például hétfőn is létrehozhat egy készletet, törölheti azt keddre, majd csütörtökön létrehoz egy másik készletet. Minden létrehozott új erőforráshoz egyedi nevet kell adni, amelyet korábban még nem használt. Ezt egy GUID használatával végezheti el (akár a teljes erőforrás neve, akár a részeként), vagy beágyazhatja az erőforrásnak az erőforrás nevében történő létrehozását. A Batch támogatja a [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet)paramétert, amely egy adott erőforrás számára elérhetővé teheti az erőforrásokat, még akkor is, ha a tényleges erőforrás-azonosító olyan dolog, amely nem az emberi barát. Az egyedi nevek használata megkönnyíti az Ön számára, hogy megkülönböztesse az adott erőforrást a naplókban és a mérőszámokban. Emellett a kétértelműség is törlődik, ha az erőforráshoz nem kell támogatási esetet benyújtani.

- **Folytonosság a készlet karbantartása és meghibásodása közben.**  
    A legjobb megoldás, ha a feladatok dinamikusan használják a készleteket. Ha a feladatok minden esetben ugyanazt a készletet használják, akkor a feladatok nem fognak futni, ha valami nem stimmel a készlettel. Ez különösen fontos az időérzékeny számítási feladatokhoz. Ennek kijavításához válasszon ki vagy hozzon létre egy készletet dinamikusan, amikor az egyes feladatokat ütemezi, vagy ha úgy szeretné felülbírálni a készlet nevét, hogy el tudja kerülni a nem megfelelő állapotú készletet.

- **Üzletmenet-folytonosság a készlet karbantartása és meghibásodása során**  
    Számos lehetséges ok miatt előfordulhat, hogy a készlet nem növekszik a kívánt méretre, például a belső hibákra, a kapacitás korlátozására stb. Emiatt készen kell állnia arra, hogy a feladatokat egy másik készletben (lehetőleg egy másik virtuálisgép-mérettel) lehessen átcélozni, ha szükséges, a Batch ezt a [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)-on keresztül támogatja. Kerülje a statikus készlet AZONOSÍTÓjának használatát azzal a várakozással, hogy soha nem lesz törölve, és soha nem változik.

### <a name="pool-lifetime-and-billing"></a>Készlet élettartama és számlázása

A készlet élettartama eltérő lehet a készlet-konfigurációra alkalmazott foglalási és beállítási módtól függően. A készletek tetszőleges időtartammal rendelkezhetnek, és a készletben lévő számítási csomópontok száma bármikor megadható. Az Ön felelőssége, hogy a készletben lévő számítási csomópontokat explicit módon, vagy a szolgáltatás által biztosított szolgáltatások (az autoscale vagy az autopool) segítségével kezelje.

- **A készletek frissen tartása.**  
    A készleteket a csomópontok legújabb frissítéseinek és hibajavításának biztosítása érdekében minden hónapban nulla értékűre kell méreteznie. A készlet nem kapja meg a csomópont-ügynök frissítéseit, kivéve, ha újból létrehozták, vagy 0 számítási csomópontra méretezi át őket. A készlet újbóli létrehozása vagy átméretezése előtt javasoljuk, hogy [a csomópontok szakaszban](#nodes) ismertetett módon letöltse a csomóponti ügynökök naplóit a hibakereséshez.

- **Készlet ismételt létrehozása**  
    Hasonló megjegyzés esetén nem ajánlott napi rendszerességgel törölni és újból létrehozni a készleteket. Ehelyett hozzon létre egy új készletet, és frissítse a meglévő feladatokat, hogy az új készletre mutasson. Miután az összes feladatot áthelyezte az új készletbe, törölje a régi készletet.

- **Készlet hatékonysága és számlázása**  
    A Batch saját maga nem számít fel külön díjat, de a felhasznált számítási erőforrásokért díjat számítunk fel. A készletben lévő összes számítási csomópont után számítunk fel díjat, függetlenül attól, hogy milyen állapotban vannak. Ez magában foglalja a csomópont futtatásához szükséges díjakat, például a tárolási és hálózati költségeket. Az ajánlott eljárások megismeréséhez tekintse meg [Azure Batch a Cost Analysis és a költségkeretet](budget.md).

### <a name="pool-allocation-failures"></a>A készlet lefoglalási hibái

A készlet-foglalási hibák az első kiosztáskor vagy az azt követő átméretezések során bármikor megtörténhetnek. Ennek oka az lehet, hogy egy adott régióban vagy más Azure-szolgáltatásokban a Batch által kiváltott átmeneti kapacitás kimerítése lehetséges. Az alapvető kvóta nem garancia, hanem korlát.

### <a name="unplanned-downtime"></a>Nem tervezett leállás

Lehetséges, hogy a Batch-készletek az Azure-ban leállási eseményeket tapasztalnak. Fontos szem előtt tartani a Batch-forgatókönyv vagy-munkafolyamat tervezésekor és fejlesztésekor.

Abban az esetben, ha egy csomópont meghibásodik, a Batch automatikusan megkísérli helyreállítani ezeket a számítási csomópontokat az Ön nevében. Ez a helyreállított csomóponton futó feladatok újraütemezését eredményezheti. A megszakított feladatokkal kapcsolatos további tudnivalókért tekintse meg [az újrapróbálkozások tervezése](#designing-for-retries-and-re-execution) című témakört.

- **Azure region-függőség**  
    Azt javasoljuk, hogy ne függjön egyetlen Azure-régiótól, ha időérzékeny vagy éles számítási feladattal rendelkezik. Ritkán előfordul, hogy olyan problémák merülnek fel, amelyek befolyásolhatják a teljes régiót. Ha például a feldolgozásnak egy adott időpontban kell kezdődnie, érdemes lehet a készletet az elsődleges régióban is felmérni a *kezdési időpont előtt*. Ha a készlet skálázása meghiúsul, visszatérhet egy készlet egy biztonsági mentési régióban (vagy régiókban) való méretezésére. A különböző régiókban lévő több fiókból álló készletek egy kész, könnyen hozzáférhető biztonsági mentést biztosítanak, ha egy másik készlettel valamilyen hiba történik. További információ: [az alkalmazás megtervezése a magas rendelkezésre állás érdekében](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Feladatok

A feladat egy olyan tároló, amely több száz, több ezer vagy akár több millió feladatot tartalmaz.

- **Több feladat elhelyezése egy feladatban**  
    A feladatok egyetlen feladat futtatására való használata nem hatékony. Például hatékonyabban használhatja a 1000 feladatokat tartalmazó egyetlen feladatot, és nem hoz létre olyan 100-feladatokat, amelyek mindegyike 10 feladatot tartalmaz. A 1000-es feladatok futtatása egyetlen feladattal, a legkevésbé hatékony, leglassabb és legdrágább módszer.  

    Ne tervezzen olyan batch-megoldást, amely egyszerre több ezer aktív feladatot igényel. A feladatokhoz nem tartozik kvóta, így a lehető legtöbb feladat végrehajtása a feladatok és a feladatok [ütemezett kvótái](batch-quota-limit.md#resource-quotas)alapján történik.

- **Feladatok élettartama**  
    A Batch-feladatok határozatlan élettartammal rendelkeznek, amíg nem törlik a rendszerből. A feladatok állapota azt jelzi, hogy több feladatot is el tud-e fogadni az ütemezéshez. A feladatok nem helyezhetők automatikusan befejezett állapotba, kivéve, ha explicit módon megszakították. Ezt a [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) tulajdonság vagy a [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)automatikusan aktiválhatja.

Az alapértelmezett [aktív feladatok és a feladatok ütemezett kvótája](batch-quota-limit.md#resource-quotas). A Befejezett állapotú feladatok és feladatok Ütemtervei nem számítanak bele a kvótába.

## <a name="tasks"></a>Feladatok

A feladatok olyan egyedi Munkaegységek, amelyek feladatból állnak. A feladatokat a felhasználó küldi el és ütemezi a számítási csomópontokra. A feladatok létrehozásakor és végrehajtásakor több kialakítási szempontot is figyelembe kell venni. A következő szakaszokban ismertetjük a gyakori forgatókönyveket, valamint azt, hogy miként lehet megtervezni a problémákat és hatékonyan elvégezni a feladatokat.

- **Mentse a feladatokat a feladat részeként.**  
    A számítási csomópontok természetüknél fogva elmúlóak. A Batch számos funkciója van, például az autopool és az autoscale, amelyek megkönnyítik a csomópontok eltűnnek. Ha a csomópontok elhagyják a készletet (az átméretezés vagy a készlet törlése miatt), akkor a csomópontokon lévő összes fájl is törlődik. Ezért azt javasoljuk, hogy mielőtt egy feladat befejeződik, áthelyezi a kimenetét a-on futó csomópontból, és egy tartós tárolóba, hasonlóan ha egy feladat meghibásodik, át kell helyeznie azokat a naplókat, amelyek szükségesek ahhoz, hogy egy tartós tároló hibáját diagnosztizálják. A Batch integrált támogatást nyújt az Azure Storage-hoz az adatok [OutputFiles](batch-task-output-files.md)-n keresztüli feltöltéséhez, valamint számos megosztott fájlrendszerhez, vagy a feladatokban a feltöltéshez is.

### <a name="task-lifetime"></a>Feladat élettartama

- **Feladatok törlése, ha elkészült.**  
    Törölje a feladatokat, ha már nincs rájuk szükség, vagy állítson be egy [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) -feladatra vonatkozó korlátozást. Ha egy `retentionTime` be van állítva, a Batch automatikusan törli a feladat által használt lemezterületet, amikor a `retentionTime` lejár.  

    A tevékenységek törlése két dolgot hajt végre. Gondoskodik arról, hogy a feladatban ne legyenek felépítve feladatok, így a lekérdezés/a feladat megkeresése nehezebben megkereshető (mivel a Befejezett feladatok alapján kell szűrnie). Emellett megtisztítja a megfelelő tevékenységadatokat a csomóponton (a megadott `retentionTime` még nem érte el). Ez biztosítja, hogy a csomópontok ne töltsenek fel a feladattal kapcsolatos adatokkal, és elfogyjon a szabad lemezterület.

### <a name="task-submission"></a>Feladat elküldése

- **Nagy számú feladat elküldése egy gyűjteményben.**  
    A feladatokat egyedi alapon vagy gyűjteményekben lehet beküldeni. A feladatok tömeges beküldése a terhelés és a beküldési idő csökkentése érdekében akár 100 [gyűjteményben](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) is elvégezhető.

### <a name="task-execution"></a>Feladat végrehajtása

- **A feladatok maximális száma a csomóponton**  
    A Batch támogatja a csomópontokon lévő feladatok túllépését (több feladatot futtat, mint a magok). Így biztosíthatja, hogy a feladatok "illeszkedjenek" a készlet csomópontjaihoz. Előfordulhat például, hogy csökkentett teljesítményű élményre van szüksége, ha nyolc feladatot próbál ütemezni, amelyek mindegyike 25%-os CPU-használatot használ az egyik csomópontra (a készletben `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Újrapróbálkozások és újbóli végrehajtás tervezése

A Batch automatikusan újrapróbálkozik a feladatokat. Az újrapróbálkozások két típusa létezik: a felhasználó által vezérelt és a belső. A felhasználó által vezérelt újrapróbálkozásokat a tevékenység [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)adja meg. Ha a feladatban megadott program nem nulla kilépési kóddal kilép, a feladat újra próbálkozik a `maxTaskRetryCount`értékével.

Bár ritka, a feladat a számítási csomópont meghibásodása miatt újra próbálkozhat, például nem lehet frissíteni a belső állapotot vagy a csomópont meghibásodását a feladat futása közben. A feladat újra próbálkozik ugyanazon a számítási csomóponton, ha lehetséges, akár egy belső korláttal, mielőtt felveszi a feladatot, és elhalasztja, hogy a Batch által átütemezett feladatot, esetleg egy másik számítási csomóponton.

- **Tartós feladatok készítése**  
    A feladatokat úgy kell kialakítani, hogy elbírja a meghibásodást, és az újrapróbálkozást. Ez különösen fontos a hosszú ideig futó feladatok esetében. Ehhez győződjön meg arról, hogy a feladatok ugyanazt az eredményt használják, még akkor is, ha egynél többször futnak. Ennek az az egyik módja, hogy a feladatok "cél keresése". A másik lehetőség, hogy ellenőrizze, hogy a feladatok idempotens-e (a tevékenységek a futtatásuk időpontját is megegyeznek).

    Gyakori példa a fájlok számítási csomópontba másolására szolgáló feladat. Az egyszerű megközelítés egy olyan feladat, amely minden egyes futtatásakor átmásolja az összes megadott fájlt, ami nem hatékony, és nem áll készen a sikertelen működésre. Ehelyett hozzon létre egy feladatot, amely biztosítja, hogy a fájlok a számítási csomóponton legyenek. olyan feladat, amely nem másolja újra a már meglévő fájlokat. Így a feladat felveszi a feladatot, ha megszakadt.

- **Alacsony prioritású csomópontok**  
    A feladatok dedikált vagy alacsony prioritású csomópontokon történő végrehajtásakor nincsenek tervezési különbségek. Azt jelzi, hogy egy feladat előzik alacsony prioritású csomóponton fut-e, vagy egy dedikált csomópont meghibásodása miatt megszakadt, mindkét szituációt enyhíteni kell, ha a feladat megtervezésével ellenáll a meghibásodásnak.

- **Feladat végrehajtási ideje**  
    Kerülje a rövid végrehajtási idővel rendelkező feladatokat. Azok a feladatok, amelyek csak egy-két másodpercre futnak, nem ideálisak. Érdemes nagy mennyiségű munkát végrehajtani egy adott feladatban (10 másodperc minimum, akár óra vagy nap is). Ha az egyes feladatok egy percen (vagy több) vannak végrehajtva, akkor az ütemezési terhelés a teljes számítási idő töredékének kis hányada.

## <a name="nodes"></a>Csomópontok

- **Az indítási tevékenységeknek idempotens kell lenniük**  
    A többi feladathoz hasonlóan a csomópont indítási tevékenységének is idempotens kell lennie, mivel a csomópont minden indításakor újra futni fog. Egy idempotens feladat egyszerűen egy, amely konzisztens eredményt állít elő többszöri futtatásakor.

- **A hosszú ideig futó szolgáltatások kezelése az operációs rendszer szolgáltatásainak felületén keresztül.**  
    Előfordulhat, hogy egy másik ügynököt kell futtatnia a csomópontban található batch-ügynök mellett, például az adatoknak a csomópontból való összegyűjtéséhez és jelentéséhez. Javasoljuk, hogy ezek az ügynökök operációs rendszerként, például Windows-szolgáltatásként vagy Linux `systemd`-szolgáltatásként legyenek telepítve.  

    Ezeknek a szolgáltatásoknak a futtatásakor a rendszer nem tudja zárolni a fájl zárolásait a csomóponton található batch által felügyelt könyvtárakban, mert máskülönben a Batch nem fogja tudni törölni ezeket a címtárakat a fájlok zárolása miatt. Ha például Windows-szolgáltatást telepít egy indítási feladatba, ahelyett, hogy közvetlenül az indítási tevékenység munkakönyvtárból indítja el a szolgáltatást, másolja máshová a fájlokat (ha a fájlok már csak kihagyják a másolatot). Telepítse a szolgáltatást az adott helyről. Ha a Batch újrakezdi a kezdési feladatot, az törli a feladat indítása munkakönyvtárat, és újra létrehozza azt. Ez azért működik, mert a szolgáltatás fájl-zárolások vannak a másik címtárban, nem pedig az indítási tevékenység munkakönyvtára.

- **Ne hozzon létre címtár-csomópontokat a Windowsban**  
    A címtár-összekapcsolásokat, más néven a címtárbeli rögzített hivatkozásokat, nehéz kezelni a feladatok és a feladatok karbantartása során. A rögzített hivatkozások helyett használjon szimbolikus hivatkozásokat (Soft-Links).

- **A Batch-ügynök naplófájljainak összegyűjtése, ha probléma merül fel**  
    Ha egy csomóponton futó csomópont vagy feladatok viselkedésével kapcsolatos problémát tapasztal, javasoljuk, hogy a szóban forgó csomópontok felszabadítása előtt Gyűjtse össze a Batch-ügynök naplóit. A Batch-ügynök naplóit a Batch szolgáltatás naplófájljainak API-jával töltheti össze. Ezek a naplók a Microsoft támogatási jegyének részeként is megadhatók, és segítenek a hibaelhárításban és a megoldásban.

## <a name="security"></a>Biztonság

### <a name="security-isolation"></a>Biztonság elkülönítése

Az elkülönítés érdekében, ha a forgatókönyv megköveteli a feladatok elkülönítését egymástól, akkor el kell különíteni ezeket a feladatokat külön készletekben. A készlet a Batch biztonsági elkülönítési határa, és alapértelmezés szerint a két készlet nem látható, vagy nem tud kommunikálni egymással. Kerülje a különálló batch-fiókok elkülönítésének módját.
