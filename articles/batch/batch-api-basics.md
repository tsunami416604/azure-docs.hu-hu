---
title: Az Azure Batch áttekintése fejlesztők számára | Microsoft Docs
description: Megismerheti a Batch szolgáltatás funkcióit és API-jait a fejlesztés szempontjából.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0a18f975530d2a291e529308ee53d6d48a68e42
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Nagy léptékű párhuzamos számítási megoldások fejlesztése a Batch segítségével

Ebben a cikkben áttekintjük az Azure Batch legfontosabb alkotóelemeit, valamint szót ejtünk a szolgáltatás elsődleges funkcióiról és erőforrásairól, amelyek segítenek a Batch-fejlesztőknek nagy léptékű párhuzamos számítási megoldásokat létrehozni.

Akár olyan elosztott számítási alkalmazást vagy szolgáltatást fejleszt, amely közvetlen [REST API][batch_rest_api]-hívásokkal működik, akár valamelyik [Batch SDK-t](batch-apis-tools.md#azure-accounts-for-batch-development) használja, biztosan szüksége lesz a cikkben tárgyalt erőforrásokra és funkciókra.

> [!TIP]
> A Batch szolgáltatás részletesebb leírásáért olvassa el a következő cikket: [Basics of Azure Batch](batch-technical-overview.md) (Az Azure Batch alapjai). Lásd továbbá a [Batch szolgáltatás legújabb frissítéseit](https://azure.microsoft.com/updates/?product=batch).
>
>

## <a name="batch-service-workflow"></a>A Batch szolgáltatás munkafolyamata
Az alább olvasható elméleti szintű munkafolyamat gyakorlatilag a Batch szolgáltatást párhuzamos számítási feladatok feldolgozására használó összes alkalmazásra és szolgáltatásra érvényes:

1. Töltse fel a feldolgozni kívánt **adatfájlokat** egy [Azure Storage][azure_storage]-fiókba. A Batch beépített támogatást biztosít az Azure Blob-tárhelyekhez, így a tevékenységek futtatásakor a rendszer le tudja tölteni a fájlokat a [számítási csomópontokra](#compute-node).
2. Töltse fel az **alkalmazásfájlokat**, amelyeket a tevékenységek futtatni fognak. Ezek lehetnek bináris fájlok vagy parancsfájlok, illetve ezek függőségei, futtatásukat a feladatokban lévő tevékenységek végzik. A tevékenységek képesek letölteni ezeket a fájlokat az Ön tárfiókjából, de az alkalmazások felügyeletére és üzembe helyezésére a Batch [alkalmazáscsomagok](#application-packages) funkcióját is használhatja.
3. Hozza létre a számítási csomópontok [készletét](#pool). A készlet létrehozásakor meg kell adnia a készletbe tartozó számítási csomópontok számát, méretét, valamint a használt operációs rendszer típusát. Amikor a rendszer lefuttatja a feladatokat, végrehajtásuk céljából hozzájuk rendeli a készlet egyik csomópontját.
4. Hozzon létre egy [feladatot](#job). A feladatok tevékenységek gyűjteményeit kezelik. Az egyes feladatokat konkrét készlethez kell rendelni; itt fognak lefutni a feladathoz tartozó tevékenységek.
5. Adjon hozzá [tevékenységeket](#task) a feladathoz. A tevékenységek lefuttatják az Ön által korábban feltöltött alkalmazást vagy parancsfájlt, és feldolgozzák a tárfiókból letöltött adatfájlokat. Amikor befejeződnek a tevékenységek, feltöltik az eredményüket az Azure Storage-fiókba.
6. Kövesse figyelemmel a tevékenység előrehaladását, és kérje le a feladatok eredményét az Azure Storage-fiókból.

A következő részekben bemutatjuk a Batch fentiekben említett funkcióit és más erőforrásait, amelyek segítségével létrehozhatja elosztott számítási megoldásait.

> [!NOTE]
> A Batch szolgáltatás használatához [Batch-fiókra](#account) van szüksége. A legtöbb Batch-megoldás esetében szükség van egy társított [Azure Storage][azure_storage]-fiókra is a fájlok tárolásához és lekéréséhez. 
>
>

## <a name="batch-service-resources"></a>A Batch szolgáltatáshoz szükséges erőforrások
Egyes erőforrásokra (fiókokra, számítási csomópontokra, készletekre, feladatokra és tevékenységekre) a Batch szolgáltatást használó összes megoldáshoz szükség van. Mások, például a feladatütemezések vagy az alkalmazáscsomagok ugyan hasznosak, de használatuk nem kötelező.

* [Fiók](#account)
* [Számítási csomópont](#compute-node)
* [Készlet](#pool)
* [Feladat](#job)
  * [Feladatütemezések](#scheduled-jobs)
* [Tevékenység](#task)
  * [Indítási tevékenység](#start-task)
  * [Feladatkezelő-tevékenység](#job-manager-task)
  * [Feladat-előkészítési és -kiadási tevékenységek](#job-preparation-and-release-tasks)
  * [Többpéldányos tevékenység (MPI)](#multi-instance-tasks)
  * [Tevékenységfüggőségek](#task-dependencies)
* [Alkalmazáscsomagok](#application-packages)

## <a name="account"></a>Fiók
A Batch-fiókok a Batch szolgáltatáson belül egyedileg azonosított entitások. Minden feldolgozás Batch-fiókkal van társítva.

Azure Batch-fiókot az [Azure Portalon](batch-account-create-portal.md)hozhat létre, vagy programozott módon, például a [Batch Management .NET könyvtár](batch-management-dotnet.md) használatával. Egy fiók létrehozásakor társíthatja azt egy Azure Storage-fiókhoz, a feladatokkal kapcsolatos bemeneti vagy kimeneti adatok, illetve alkalmazások tárolása céljából.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

> [!NOTE]
> Egy Batch-fiók létrehozásakor általában érdemes az alapértelmezett **Batch szolgáltatás** módot választani, amelynek keretében a készleteket a rendszer a háttérben foglalja le az Azure által felügyelt előfizetésekben. A szintén választható **felhasználói előfizetés** mód esetében, amelynek használata a legtöbb forgatókönyvben már nem javasolt, a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, az előfizetését az Azure Batchben is regisztrálnia kell, és egy Azure Key Vaulttal is társítania kell.
>


## <a name="azure-storage-account"></a>Azure Storage-fiók

Az erőforrásfájlok és a kimeneti fájlok tárolására a legtöbb Batch-megoldás az Azure Storage-ot használja.  

A Batch jelenleg kizárólag az általános célú tárfióktípust támogatja, amelynek leírása a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md) fejezet 5., [Tárfiók létrehozása](../storage/common/storage-create-storage-account.md#create-a-storage-account) című szakaszában található. A Batch-tevékenységeknek (beleértve a szabványos tevékenységeket, az indítási tevékenységeket, a feladat-előkészítési és a feladatkiadási tevékenységeket) olyan erőforrásfájlokat kell meghatározniuk, amelyek általános célú tárfiókokban találhatóak.


## <a name="compute-node"></a>Számítási csomópont
A számítási csomópontok olyan Azure-alapú virtuális gépek vagy Cloud Service-virtuális gépek, amelyek az alkalmazás adott számítási feladatának feldolgozására vannak kijelölve. A csomópont mérete határozza meg a CPU-magok számát, a memóriakapacitást és a csomóponthoz lefoglalt helyi fájlrendszeri méretet. Windows- és Linux-csomópontokból az Azure Cloud Services, az [Azure Virtual Machines Marketplace][vm_marketplace]-ről származó rendszerképek vagy az Ön által előkészített egyéni rendszerképek segítségével hozhat létre készleteket. Ezekről a lehetőségekről további információkat tudhat meg az alábbi, [Készlet](#pool) című fejezetben.

A csomópontok minden olyan végrehajtható fájlt vagy parancsprogramot képesek futtatni, amelyet a csomópont operációsrendszer-környezete támogat. Ezek közé Windows esetén az \*.exe-, a \*.cmd-, a \*.bat-fájlok és a PowerShell-parancsfájlok tartoznak, Linux esetén pedig a bináris fájlok, valamint rendszerhéj- és Python-parancsfájlok.

A Batch szolgáltatásban működő számítási csomópontok emellett a következőket is tartalmazzák:

* Szabványos [mappastruktúra](#files-and-directories), valamint az ehhez tartozó [környezeti változók](#environment-settings-for-tasks), amelyekre a tevékenységek hivatkozni tudnak.
* A hozzáférés vezérléséhez konfigurált **tűzfalbeállítások**.
* [Távelérés](#connecting-to-compute-nodes) a windowsos (RDP-) és a linuxos (SSH-) csomópontokhoz.

## <a name="pool"></a>Készlet
A készletek olyan csomópontok gyűjteményei, amelyeken az alkalmazás fut. A készletet manuálisan létrehozhatja Ön, vagy beállíthatja, hogy a Batch szolgáltatás hozza létre automatikusan az elvégezni kívánt munka meghatározásakor. Érdemes olyan készletet létrehozni és felügyelni, amely megfelel az alkalmazás erőforrásigényeinek. A készleteket kizárólag az a Batch-fiók használhatja, amelyben létrehozták őket. Egy Batch-fiók több készlettel is rendelkezhet.

Az Azure Batch-fiókok az Azure számítási platformjára épülnek. A készletek nagy méretű lefoglalást, alkalmazástelepítést, adatelosztást és állapotfigyelést biztosítanak, valamint a készletben működő számítási csomópontok számának rugalmas módosítását (azaz a [skálázást](#scaling-compute-resources)) is lehetővé teszik.

A készlethez adott minden csomóponthoz egyedi név és IP-cím van rendelve. Amikor eltávolít egy csomópontot a készletből, elveszíti az operációs rendszeren vagy a fájlokon végzett módosításokat, és a rendszer kiadja a csomópont nevét és IP-címét további használatra. Amikor egy csomópont kikerül egy készletből, vége van az élettartamának.

Készlet létrehozásakor a következő attribútumokat adhatja meg:

- A számítási csomópont operációs rendszere és verziója
- A számítási csomópont típusa és a csomópontok kívánt száma
- A számítási csomópontok mérete
- Skálázási szabályzat
- Tevékenységütemezési szabályzat
- A számítási csomópontok kommunikációs állapota
- A számítási csomópontok indítási tevékenységei
- Alkalmazáscsomagok
- Hálózati konfiguráció

Ezen beállítások részletesebb leírását az alábbi szakaszokban találja.

> [!IMPORTANT]
> A Batch-fiókhoz tartozik egy alapértelmezett kvóta, amely korlátozza a magok számát a Batch-fiókokban. A magok száma a számítási csomópontok számának felel meg. Az alapértelmezett kvótákat és a [kvóták növelésével](batch-quota-limit.md#increase-a-quota) kapcsolatos útmutatásokat [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai) című témakörben találja. A magkvóta lehet az oka, hogy a készlet esetleg nem éri el a csomópontok kitűzött számát.
>


### <a name="compute-node-operating-system-and-version"></a>A számítási csomópont operációs rendszere és verziója

A Batch-készlet létrehozásakor megadhatja az Azure virtuálisgép-konfigurációt és a készlet egyes számítási csomópontokon futtatni kívánt operációs rendszer típusát. A Batch szolgáltatásban az alábbi két konfigurációtípus használható:

- A **Virtuális gépek konfigurációja** megadja, hogy a készlet Azure virtuális gépekből áll. Ezek a virtuális gépek Linux- vagy Windows-rendszerképből is létrehozhatók. 

    Ha a virtuálisgép-konfiguráción alapuló készletet hoz létre, a csomópontok mérete és a létrehozásukhoz használt rendszerképek forrása mellett a **virtuális gép képhivatkozását** és a csomópontokra telepítendő **Batch-csomóponti ügynök SKU-ját** is meg kell adnia. A készlet e tulajdonságainak megadásával kapcsolatos további információk: [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Linuxos számítási csomópontok kiépítése Azure Batch-készletekben). Csatolhat egy vagy több üres adatlemezt a Marketplace-ről származó lemezképekből létrehozott virtuális gépek készletté alakításához, vagy adatlemezeket foglalhat az egyéni rendszerképekbe a virtuális gépek létrehozásához.

- A **Cloud Services-konfiguráció**, amely megadja, hogy a készlet Azure Cloud Services-csomópontokból áll. A Cloud Services *kizárólag* windowsos számítási csomópontok létrehozására használható.

    A Cloud Services-konfigurációval beállított készletekhez elérhető operációs rendszerek listáját az [Azure Guest OS releases and SDK compatibility matrix](../cloud-services/cloud-services-guestos-update-matrix.md) (Vendég operációs rendszerek kiadásai és SDK-kompatibilitási mátrix az Azure-hoz) című cikk ismerteti. Ha Cloud Services-konfigurációt használó csomópontokat tartalmazó készletet hoz létre, meg kell adnia a csomópont méretét és az *operációsrendszer-családot*. A Cloud Services gyorsabban üzembe helyezhető az Azure-ban, mint a Windows rendszerű virtuális gépek. Ha windowsos számítási csomópontok készleteire van szükség, előfordulhat, hogy a Cloud Services üzembe helyezési idő szempontjából teljesítményelőnyt nyújt.

    * Az *operációsrendszer-család* azt is meghatározza, hogy a .NET melyik verziója van telepítve az operációs rendszerrel.
    * Ahogy a Cloud Services feldolgozói szerepkörei esetében, itt is megadhatja az *operációs rendszer verzióját* (a feldolgozói szerepkörökkel kapcsolatos további információkért olvassa el [a Cloud Services áttekintésével](../cloud-services/cloud-services-choose-me.md) foglalkozó cikket).
    * A feldolgozói szerepkörökhöz hasonlóan ajánlott a `*` értéket megadni az *Operációs rendszer verziója* beállításnál, hogy a csomópontok automatikusan frissüljenek, és ne kelljen semmilyen műveletet végeznie, ha új verzió jelenik meg. Azért tanácsos megadni a konkrét operációsrendszer-verziót, mert így garantálható az alkalmazások kompatibilitása, hogy így a korábbi verziókkal való kompatibilitási tesztet lehessen végezni a verziófrissítés engedélyezése előtt. Az ellenőrzést követően frissítheti a készlet *operációsrendszer-verzióját*, és telepítheti az új operációsrendszer-képet. A rendszer ilyenkor megszakítja a futó feladatokat, és újból a várólistára helyezi őket.

Készletek létrehozásakor ki kell választania a megfelelő **nodeAgentSkuId** értéket a virtuális merevlemez alapképének operációs rendszerétől függően. A rendelkezésre álló csomóponti ügynök SKU-knak az operációsrendszerkép-hivatkozásokra való leképezéséhez hívhatja [Támogatott csomóponti ügynökök SKU-azonosítói](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) műveletet.


#### <a name="custom-images-for-virtual-machine-pools"></a>Egyéni rendszerképek virtuálisgép-készletekhez

Egyéni rendszerkép használatához elő kell készítenie a rendszerképet annak normalizálásával. További tudnivalókat az Azure-beli virtuális gépekről származó egyéni Linux-rendszerképek előkészítéséről a [rendszerkép virtuális gépről vagy virtuális merevlemezről történő készítését](../virtual-machines/linux/capture-image.md) ismertető cikkben találhat. További tudnivalókat az Azure-beli virtuális gépekről származó egyéni Windows-rendszerképek előkészítéséről a [felügyelt rendszerkép egy Azure-beli általánosított virtuális gépről történő létrehozását](../virtual-machines/windows/capture-image-resource.md) ismertető cikkben találhat. 

A részletes követelményekkel és lépésekkel kapcsolatban lásd a [virtuálisgép-készletek egyéni rendszerkép használatával történő létrehozását](batch-custom-images.md) ismertető témakört.

#### <a name="container-support-in-virtual-machine-pools"></a>Tárolótámogatás a virtuálisgép-készletekben

Amikor a Batch API-kkal hoz létre virtuálisgép-konfigurációs készletet, beállíthatja a készletet, hogy Docker-tárolókban futtasson feladatokat. Jelenleg a Docker-tárolókat támogató rendszerképpel kell létrehoznia a készletet. Használja a Windows Server 2016 Datacentert az Azure Marketplace-ről származó tárolói rendszerképpel, vagy adjon meg olyan egyéni virtuálisgép-rendszerképet, amely tartalmazza a Docker Community Edition vagy Enterprise kiadását és az összes szükséges illesztőt. A készlet beállításainak tartalmazniuk kell egy [tárolókonfigurációt](/rest/api/batchservice/pool/add#definitions_containerconfiguration), amely tárolórendszerképeket másol a virtuális gépekre a készlet létrehozásakor. A készleten futó feladatok ezután hivatkozhatnak a tárolórendszerképre és a tároló által futtatott beállításokra.

További információt a [Docker-tárolóalkalmazásoknak az Azure Batch-ben történő futtatásával](batch-docker-container-workloads.md) foglalkozó cikkben tekinthet meg.

## <a name="compute-node-type-and-target-number-of-nodes"></a>A számítási csomópont típusa és a csomópontok kívánt száma

Készlet létrehozásakor megadható, hogy milyen típusú számítási csomópontokat és azokból mennyit kíván. A számítási csomópontok két típusa:

- **Dedikált számítási csomópontok.** A dedikált számítási csomópontok az adott feladatra vannak fenntartva. Költségesebbek az alacsony prioritású csomópontoknál, de biztosan nem szorulnak háttérbe.

- **Alacsony prioritású számítási csomópontok.** Az alacsony prioritású csomópontok az Azure többletkapacitását használják ki a Batch-feladatok futtatásához. Az alacsony prioritású csomópontok olcsóbbak óránként, mint a dedikáltak, és nagy számítási teljesítményt igénylő feladatok futtatását teszik lehetővé. További információ: [Alacsony prioritású virtuális gépek használata a Batch szolgáltatással](batch-low-pri-vms.md).

    Az alacsony prioritású számítási csomópontok háttérbe szorulhatnak, ha az Azure-ban nem áll rendelkezése elég többletkapacitás. Amennyiben egy csomópont feladatok futása közben szorul háttérbe, akkor a feladatok visszakerülnek a várakozási sorba, és újra futnak, amikor a számítási csomópont ismét elérhetővé válik. Az alacsony prioritású csomópontokat akkor érdemes választani, ha a feladat végrehajtási ideje rugalmas, és a munka sok csomóponton oszlik meg. Mielőtt az alacsony prioritású csomópontok használata mellett döntene, győződjön meg arról, hogy az előzetes lefoglalás miatt elveszett munka minimális és könnyen újra létrehozható lesz-e.

    
Ugyanabban a készletben alacsony prioritású és dedikált csomópontok is lehetnek. Mindkét csomóponttípus (&mdash;az alacsony prioritású és a dedikált&mdash; is) saját célbeállításokkal rendelkezik, amelyhez megadható a csomópontok kívánt száma. 
    
A számítási csomópontok számát azért nevezzük *cél*-értéknek, mert előfordulhat, hogy a készletben nem lehet a kívánt számú csomópontot alkalmazni. Egy készlet például nem érheti el a célértéket, ha először a Batch-fiók [magkvótáját](batch-quota-limit.md) éri el. Az is előfordulhat, hogy a készlet azért nem éri el a célértéket, mert automatikusan skálázó képlet van megadva hozzá a csomópontok maximális számának korlátozására.

Az alacsony prioritású és dedikált számítási csomópontok díjszabását a [Batch díjszabása](https://azure.microsoft.com/pricing/details/batch/) írja le.

### <a name="size-of-the-compute-nodes"></a>A számítási csomópontok mérete

Azure Batch-készlet létrehozásakor az Azure-ban elérhető szinte összes virtuálisgép-család és -méret közül választhat. Az Azure számos különböző virtuálisgép-méretet nyújt a különböző számítási feladatokhoz, beleértve a speciális [HPC-](../virtual-machines/linux/sizes-hpc.md) vagy [GPU-kompatibilis](../virtual-machines/linux/sizes-gpu.md) virtuálisgép-méreteket. 

További információ: [Számítási csomópontok virtuálisgép-méretének kiválasztása Azure Batch-készletben](batch-pool-vm-sizes.md).

### <a name="scaling-policy"></a>Skálázási szabályzat

Dinamikus számítási feladatoknál megírhat egy [automatikus skálázási képletet](#scaling-compute-resources), amelyet aztán alkalmazhat a készletre. A Batch szolgáltatás rendszeresen ellenőrzi a készletet a képlet alapján, és az Ön által megadott, a készletre, a feladatokra és a tevékenységekre vonatkozó paraméterek alapján szükség esetén módosítja a készletben lévő csomópontok számát.

### <a name="task-scheduling-policy"></a>Tevékenységütemezési szabályzat

A [csomópontonkénti maximális tevékenység](batch-parallel-node-tasks.md) konfigurációs lehetőség határozza meg a készleten belüli egyes számítási csomópontokon egyidejűleg futtatható tevékenységek maximális számát.

Az alapértelmezett konfiguráció meghatározza, hogy egyszerre egy tevékenység fut egy csomóponton, de bizonyos esetekben előnyös lehet, ha kettő vagy több feladat is végrehajtható egyszerre egy csomóponton. Nézze meg a [csomópontokon végzett egyidejű tevékenységekről szóló cikkben](batch-parallel-node-tasks.md) szereplő [mintaforgatókönyvet](batch-parallel-node-tasks.md#example-scenario), ha kíváncsi rá, hogy milyen esetekben lehet hasznos, ha több tevékenység fut az egyes csomópontokon.

Megadhatja a *kitöltéstípust* is, amely azt határozza meg, hogy a Batch egyenletesen ossza-e el a tevékenységeket a készletben szereplő csomópontok között, vagy először töltse ki az egyes csomópontok teljes tevékenységkapacitását, és csak ezt követően lépjen tovább a következő csomópontra.

### <a name="communication-status-for-compute-nodes"></a>A számítási csomópontok kommunikációs állapota

A legtöbb esetben a tevékenységek egymástól függetlenül működnek, nincs szükség közöttük kommunikációra. Vannak azonban olyan alkalmazások (például [MPI-megoldások](batch-mpi.md)), ahol a tevékenységeknek kommunikálniuk kell egymással.

Beállíthatja, hogy a készlet engedélyezze a **csomópontok közötti kommunikációt**, hogy az egy készleten belüli csomópontok kommunikáljanak futás közben. Ha engedélyezte a csomópontok közötti kommunikációt, a Cloud Services-konfigurációt használó készletekben működő csomópontok az 1100-nál magasabb portszámú portokon, a virtuálisgép-konfigurációs készletek csomópontjai pedig bármely porton képesek lesznek kommunikálni egymással.

Ne feledje, hogy a csomópontok közötti kommunikáció engedélyezése hatással lesz a csomópontok fürtökben való elhelyezkedésére, illetve azt, hogy az üzembe helyezés korlátozásai következtében ez korlátozhatja a készletben használható csomópontok maximális számát. Ha az alkalmazáshoz nincs szükség a csomópontok közötti kommunikációra, a Batch szolgáltatás nagy számú csomópont lefoglalására képes a készlethez számos különböző fürtből és adatközpontból, hogy lehetővé tegye a nagyobb párhuzamos feldolgozási teljesítményt.

### <a name="start-tasks-for-compute-nodes"></a>A számítási csomópontok indítási tevékenységei

Ha szeretne, *indítási tevékenységet* is beállíthat, amelyet a rendszer minden alkalommal végrehajt, amikor egy csomópont csatlakozik a készlethez, vagy amikor a csomópontot újraindítják, vagy rendszerképét alaphelyzetbe állítják. Az indítási tevékenység különösen hasznos a számítási csomópontok tevékenységek végrehajtására való előkészítéséhez, például a tevékenységek által a számítási csomópontokon futtatott alkalmazások telepítéséhez.

### <a name="application-packages"></a>Alkalmazáscsomagok

A készlethez tartozó számítási csomópontokra üzembe helyezni kívánt [alkalmazáscsomagokat](#application-packages) is meghatározhatja. Az alkalmazáscsomagok leegyszerűsítik a tevékenységek által futtatott alkalmazások üzembe helyezését és verziókezelését. A készlethez beállított alkalmazáscsomagokat a rendszer a készlethez csatlakozó összes csomópontra telepíti, illetve minden alkalommal telepíti őket, amikor egy csomópontot újraindítanak vagy rendszerképét alaphelyzetbe állítják.

> [!NOTE]
> Az alkalmazáscsomagok az összes 2017. július 5. után létrehozott Batch-készleten támogatottak. A 2016. március 10. és 2017. július 5. között létrehozott Batch-készletek esetében csak akkor támogatottak, ha a készlet felhőszolgáltatás-konfigurációval lett létrehozva. A 2016. március 10. előtt létrehozott Batch-készletek nem támogatják az alkalmazáscsomagokat. További információkat az alkalmazások a Batch-csomópontokon alkalmazáscsomagok használatával történő központi telepítéséről a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
>
>

### <a name="network-configuration"></a>Hálózati konfiguráció

Megadhatja annak az Azure [virtuális hálózatnak (VNet)](../virtual-network/virtual-networks-overview.md) alhálózatát, amelyen létre szeretné hozni a készlet számítási csomópontjait. További információért lásd a [Készlet hálózati konfigurációja](#pool-network-configuration) című szakaszt.


## <a name="job"></a>Feladat
A feladatok tevékenységek gyűjteményei. A feladatok határozzák meg, hogyan végezzék el a hozzájuk tartozó tevékenységek a számítási feladatokat a készlet számítási csomópontjaiban.

* A feladat meghatározza azt a **készletet**, amelyben a munkának futnia kell. Az egyes feladatokhoz saját készletet hozhat létre, de egyetlen készletet is használhat több feladathoz. A feladatütemezésbe tartozó egyes feladatokhoz külön-külön készletet hozhat létre, vagy létrehozhat egy készletet, amely a feladatütemezésbe tartozó összes feladatot tartalmazza.
* Ha szeretné, megadhatja a **feladatok prioritását** is. Amikor az aktuálisan folyamatban lévő feladatoknál magasabb prioritású feladatot küld el, a rendszer az alacsonyabb prioritású feladatok elé helyezi el a magasabb prioritású feladat tevékenységeit a várólistán. Ezek azonban nem előzik meg a már futó alacsonyabb prioritású feladat tevékenységeit.
* A feladatok **korlátozásai** segítségével korlátokat szabhat a feladatokhoz:

    Beállíthatja a **maximális valós időt**, ami azt jelenti, hogy ha egy feladat az itt megadott időtartamnál hosszabban fut, a rendszer leállítja a feladatot és a hozzá tartozó összes tevékenységet.

    A Batch képes észlelni, ha egy tevékenység sikertelen volt, és megpróbálni újra elvégezni. Korlátozásként a **tevékenység-újrapróbálások maximális száma** is beállítható, beleértve azt is, hogy a rendszer *mindig* vagy *soha ne* próbálja meg újra elvégezni az adott tevékenységet. A tevékenységek újrapróbálása azt jelenti, hogy a tevékenység ismét a várólistára kerül, hogy a rendszer lefuttathassa.
* Tevékenységeket az ügyfélalkalmazás is adhat a feladatokhoz, vagy megadhat egy [feladatkezelői tevékenységet](#job-manager-task) is. A feladatkezelői tevékenységek tartalmazzák a feladatok tevékenységeinek létrehozásához szükséges információkat, és a készlet egyik számítási csomópontján futnak. A feladatkezelői tevékenységet a Batch kezeli – a feladat létrehozásakor ez a tevékenység azonnal a várólistára kerül, és ha sikertelen, újraindul. A *feladatütemezés* által létrehozott feladatokhoz [kötelező](#scheduled-jobs) feladatkezelői tevékenységet beállítani, mivel ez az egyetlen mód arra, hogy még a példányosítás előtt meghatározza a tevékenységeket.
* Alapértelmezés szerint a feladatok akkor is aktív állapotban maradnak, ha már a hozzájuk tartozó összes tevékenység lefutott. Ezt módosíthatja, és beállíthatja, hogy a rendszer automatikusan megszüntesse a feladatot, amikor az ahhoz tartozó összes tevékenység befejeződött. Állítsa a feladat **onAllTasksComplete** tulajdonságát ([OnAllTasksComplete][net_onalltaskscomplete] a Batch .NET-ben) a *terminatejob* értékre, ha azt szeretné, hogy a rendszer automatikusan megszüntesse a feladatot, amikor a hozzá tartozó összes tevékenység befejezett állapotba kerül.

    Vegye figyelembe, hogy a Batch szolgáltatás úgy tekinti, hogy az összes tevékenység befejeződött, ha a feladathoz *nem* tartoznak tevékenységek. Ezért ezt a funkciót általában egy [feladatkezelői tevékenységgel](#job-manager-task) használjuk. Ha feladatkezelő nélkül szeretné használni az automatikus feladatmegszüntetési funkciót, először állítsa az új feladat **onAllTasksComplete** tulajdonságát a *noaction* értékre, és csak akkor állítsa be a *terminatejob* értéket, ha már az összes kívánt tevékenységet hozzáadta a feladathoz.

### <a name="job-priority"></a>A feladatok prioritása
A Batch szolgáltatásban létrehozott feladatokhoz prioritást rendelhet. A Batch szolgáltatás a feladat prioritási értékével határozza meg a feladatütemezés sorrendjét a fiókokon belül (ez nem tévesztendő össze az [ütemezett feladatokkal](#scheduled-jobs)). A prioritási értékek –1000 és 1000 közöttiek, ahol a –1000 a legalacsonyabb prioritás, az 1000 pedig a legmagasabb. A feladatok prioritásának frissítése a [Feladat tulajdonságainak frissítése][rest_update_job] művelettel (Batch REST) vagy a [CloudJob.Priority][net_cloudjob_priority] tulajdonság (Batch .NET) módosításával lehetséges.

Egy adott fiókban a magasabb prioritású feladatok élveznek elsőbbséget az ütemezésben az alacsonyabb prioritású feladatokkal szemben. Egy fiók magasabb prioritási értékű feladatai nem élveznek elsőbbséget egy másik fiók alacsonyabb prioritási értékű másik feladatával szemben.

A készletek között a feladatok ütemezése egymástól független. Különböző készletek között nem garantált, hogy a rendszer egy magasabb prioritású feladatot előbbre ütemez, ha annak társított készletében nincsenek tétlen csomópontok. Egy adott készletben az azonos prioritású munkák ütemezésére ugyanannyi esély van.

### <a name="scheduled-jobs"></a>Ütemezett feladatok
A [Feladatütemezéssel][rest_job_schedules] rendszeresen előforduló feladatokat ütemezhet a Batch szolgáltatásban. A feladatütemezés meghatározza, mikor fussanak a feladatok, és tartalmazza a futtatandó feladatok specifikációit. Megadhatja az ütemezés időtartamát is (azaz azt, hogy mennyi ideig, és mikor legyen érvényes az ütemezés), valamint azt, hogy a rendszer milyen gyakran hozzon létre feladatokat az ütemezett időtartam alatt.

## <a name="task"></a>Tevékenység
A tevékenységek olyan számítási egységek, amelyek feladathoz vannak társítva, és egy csomóponton futnak. A tevékenységek egy csomóponthoz vannak társítva futtatáshoz, vagy a várólistán vannak, amíg egy csomópont szabaddá válik. Egyszerűen fogalmazva a tevékenységek egy vagy több programot vagy parancsfájlt futtatnak a számítási csomóponton, és ezzel elvégzik az Önnek szükséges feladatokat.

Amikor létrehozza a tevékenységet, a következőket kell megadnia:

* A tevékenységhez tartozó **parancssort**. Ez a parancssor futtatja az alkalmazást vagy parancsfájlt a számítási csomóponton.

    Vegye figyelembe, hogy a parancssor nem rendszerhéj alatt fut. Így nem tudja natívan kihasználni a rendszerhéj előnyeit, például a [környezeti változók](#environment-settings-for-tasks) értékének behelyettesítését (ide tartozik a `PATH` is). E funkciók használatához meg kell hívnia a rendszerhéjat a parancssorból, windowsos csomópontokon például a `cmd.exe` elindításával, Linuxon pedig a `/bin/sh` paranccsal:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Ha a tevékenységeknek olyan alkalmazást vagy parancsfájlt is futtatniuk kell, amely nem tartozik a csomópont `PATH`- vagy referenciakörnyezet-változói közé, hívja meg explicit módon a rendszerhéját a feladat parancssorából.
* A feldolgozni kívánt adatokat tartalmazó **erőforrásfájlok**. Ezeket a fájlokat a rendszer automatikusan a csomópontra másolja egy általános célú Azure Storage-fiók Blob Storage-tárhelyéből, mielőtt végrehajtja a tevékenység parancssorát. További információkért olvassa el az [Indítási tevékenység](#start-task) és a [Fájlok és könyvtárak](#files-and-directories) című fejezetet.
* Az alkalmazás számára szükséges **környezeti változók**. További információkért olvassa el a [Környezeti beállítások tevékenységekhez](#environment-settings-for-tasks) című fejezetet.
* Azok a **korlátozások**, amelyeken belül a tevékenység végrehajtható. Korlátozás például a maximális időtartam, ameddig a tevékenység futhat, a meghiúsult tevékenységek újrapróbálásának maximális száma, valamint a tevékenység munkakönyvtárában tárolt fájlok megőrzésének maximális ideje.
* Az ütemezett tevékenység futtatására beállított számítási csomópontra üzembe helyezni kívánt **alkalmazáscsomagok**. Az [alkalmazáscsomagok](#application-packages) leegyszerűsítik a tevékenységek által futtatott alkalmazások üzembe helyezését és verziókezelését. A tevékenységszintű alkalmazáscsomagok különösen megosztott készletes környezetekben hasznosak, ahol a különböző feladatok egy készletben futnak, és a rendszer nem törli a készletet a feladat befejezésekor. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
* Egy, a Docker Hubban vagy egy privát beállításjegyzékben található **tárolórendszerkép**-referencia és további beállítások egy olyan Docker-tároló létrehozásához, amelyben a feladatok a csomóponton futnak. Ezt az információt csak akkor kell meghatározni, ha a készlet tárolókonfigurációval van beállítva.

> [!NOTE]
> Egy tevékenység maximális élettartama a feladathoz való hozzáadástól a befejezéséig 7 nap. A befejezett tevékenységek korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett tevékenységek adatai nem elérhetőek.

A csomóponton a számítások elvégzéséhez meghatározott tevékenységek mellett a Batch szolgáltatás a következő speciális tevékenységek használatát is lehetővé teszi:

* [Indítási tevékenység](#start-task)
* [Feladatkezelő-tevékenység](#job-manager-task)
* [Feladat-előkészítési és -kiadási tevékenységek](#job-preparation-and-release-tasks)
* [Többpéldányos tevékenységek (MPI)](#multi-instance-tasks)
* [Tevékenységfüggőségek](#task-dependencies)

### <a name="start-task"></a>Indítási tevékenység
A készlethez társított **indítási tevékenység** segítségével előkészítheti a csomópontok működési környezetét. Elvégeztethet például különböző műveleteket, például a tevékenységek által futtatandó alkalmazások telepítését, illetve a háttérfolyamatok elindítását. Az indítási tevékenység minden csomópontindításkor fut, mindaddig, amíg a csomópont a készletben van, például akkor is, amikor a csomópontot először adja a készlethez, vagy amikor a csomópontot újraindítják, vagy rendszerképét alaphelyzetbe állítják.

Az indítási tevékenység elsődleges előnye, hogy tartalmazhatja a számítási csomópontok konfigurálásához szükséges, illetve a feladatok végrehajtásához szükséges alkalmazások telepítéséhez szükséges összes információt. Így a készletekben működő csomópontok számának növelése rendkívül egyszerű, csupán a csomópontok új tervezett számát kell meghatároznia. Az indítási tevékenység biztosítja a Batch szolgáltatás számára az új csomópontok konfigurálásához, illetve a tevékenységek fogadásához szükséges összes információt.

Ahogy a többi Azure Batch-tevékenységnél, itt is megadhatja az [Azure Storage][azure_storage]-tárterületen tárolt **erőforrásfájlokat**, illetve a futtatandó **parancssort**. A Batch szolgáltatás először az erőforrásfájlokat másolja a csomópontra az Azure Storage-tárterületből, majd futtatja a megadott parancssort. A készletekhez tartozó indítási tevékenységek esetében a fájllista általában a tevékenységhez tartozó alkalmazást és annak függőségeit tartalmazza.

Ezeken felül azonban szerepelhetnek az indítási tevékenységben a számítási csomóponton futó összes tevékenység számára felhasználható referenciaadatok is. Az indítási tevékenység parancssora például egy `robocopy` művelet elvégzésével képes az indítási feladat [munkakönyvtárából](#files-and-directories) a [megosztott mappába](#files-and-directories) másolni az alkalmazás fájljait (amelyeket erőforrásfájlokként definiált, és letöltött a csomópontra), majd lefuttatni egy MSI-fájlt vagy egy `setup.exe`-t.

Általában célszerű, hogy a Batch szolgáltatás megvárja az indítási tevékenység befejezését, mielőtt a csomópontot késznek ítéli tevékenységek hozzárendelésére, de ez konfigurálható.

Ha egy indítási tevékenység meghiúsul egy számítási csomóponton, akkor a csomópont állapota úgy frissül, hogy tükrözze a hibát, és a csomóponthoz nem lesz hozzárendelve egyetlen tevékenység sem. Az indítási tevékenység meghiúsulhat, ha hiba történik az erőforrásfájloknak a tárterületről való másolásakor, illetve ha a parancssor által futtatott folyamat nullától eltérő kilépési kódot ad vissza.

Amikor meglévő készlethez ad indítási tevékenységet, vagy ilyen készlethez tartozó tevékenységet frissít, újra kell indítania a számítási csomópontokat, hogy a rendszer az összes csomópontra alkalmazza az indítási tevékenységet.

>[!NOTE]
> Az indítási tevékenységek összesített mérete nem lehet nagyobb, mint 32768 karaktert, beleértve az erőforrásfájlokat és a környezeti változókat. Két módszer egyikét használhatja annak ellenőrzésére, hogy az indítási tevékenység megfelel-e ennek a követelménynek:
>
> 1. Az alkalmazáscsomagok segítségével alkalmazások vagy adatok terjeszthetők a Batch-készlet összes csomópontján. Az alkalmazáscsomagokkal kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
> 2. Manuálisan is létrehozhatja az alkalmazások fájljait tartalmazó tömörített archívumot. Töltse fel a tömörített archívumot az Azure Storage-ba blobként. Adja meg a kezdő tevékenységhez erőforrásfájlként a ZIP-archívumot. A kezdő tevékenység parancssorának futtatása előtt csomagolja ki a parancssorból az archívumot. 
>
>    Az archívum kibontásához használhatja az Ön által választott archiválási eszközt. Mellékelnie kell azt az eszközt, amellyel az archívumot kicsomagolja a kezdő tevékenység erőforrás-fájljaként.
>
>

### <a name="job-manager-task"></a>Feladatkezelő tevékenység
A feladatok végrehajtásának szabályozása és/vagy figyelése általában egy **feladatkezelői tevékenység** segítségével történik, például ez végzi a feladatokhoz tartozó tevékenységek létrehozását és beküldését, a további futtatandó tevékenységek meghatározását, illetve ez határozza meg, hogy mikor fejeződött be a művelet. A feladatkezelői tevékenység azonban más célokra is használható, hiszen ez is egy teljes értékű tevékenység, amely a feladathoz szükséges bármilyen művelet elvégzésére alkalmas. A feladatkezelői tevékenység például letöltheti a paraméterként meghatározott fájlt, elemezheti a fájl tartalmát, és további tevékenységeket küldhet el a tartalom alapján.

A rendszer minden más feladat előtt indítja el a feladatkezelői tevékenységeket. Ez a tevékenység a következő jellemzőkkel bír:

* A Batch szolgáltatás automatikusan küldi el ezeket tevékenységekként a feladat létrehozásakor.
* Úgy vannak ütemezve, hogy a feladatok más tevékenységei előtt fussanak.
* A hozzájuk társított csomópontot távolítja el a rendszer utoljára a készletből a készlet méretének csökkentésekor.
* A befejezésük a feladat összes tevékenységének befejezéséhez köthető.
* A feladatkezelői tevékenység a legmagasabb prioritást kapja, ha újra kell indítani. Ha egy tétlen csomópont nem érhető el, a Batch szolgáltatás leállíthatja valamelyik másik futó tevékenységet a készletben, hogy helyet szabadítson fel a feladatkezelői tevékenység futtatásához.
* Az egyik feladat feladatkezelő tevékenysége nem rendelkezik nagyobb prioritással, mint más feladatok tevékenységei. A feladatok között csak a feladatszintű prioritások érvényesek.

### <a name="job-preparation-and-release-tasks"></a>Feladat-előkészítési és -kiadási tevékenységek
A Batch a feladatok előtt elvégzendő beállításokhoz feladat-előkészítési tevékenységeket biztosít. A feladatkiadási tevékenységek ezzel szemben a feladat elvégzése utáni karbantartási vagy takarítási műveletekhez használhatók.

* **Feladat-előkészítési tevékenység**: a feladat-előkészítési tevékenységek a tevékenységek futtatására ütemezett összes számítási csomóponton, minden más tevékenység végrehajtása előtt lefutnak. A feladat-előkészítési tevékenység használható például az összes tevékenységre jellemző, de a feladat esetében egyedi adatok másolására.
* **Feladatkiadási tevékenység**: amikor egy feladat befejeződött, a feladatkiadási tevékenység a készlet minden csomópontján lefut, amely legalább egy tevékenységet elvégzett. A feladatkiadási tevékenységgel törölheti a feladat-előkészítési tevékenység által másolt adatokat, vagy például tömörítheti, majd feltöltheti a diagnosztikai naplóadatokat.

Mind a feladat-előkészítési, mind a feladatkiadási tevékenységeknél megadhat egy parancssort, amelyet futtatni szeretne a tevékenység meghívásakor. Ezek segítségével számos különböző funkciót érhet el, például fájlokat tölthet le, emelt jogosultsági szintű futtatást végezhet, egyéni környezeti változókat adhat meg, illetve beállíthatja a maximális végrehajtási időt, az újrapróbálások számát, illetve a fájlmegőrzési időt.

A feladatelőkészítési és -kiadási tevékenységekkel kapcsolatos további információért lásd: [Feladat-előkészítési és -befejezési műveletek futtatása Azure Batch számítási csomópontokon](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Többpéldányos tevékenység
A [többpéldányos tevékenységek](batch-mpi.md) olyan tevékenységek, amelyek több számítási csomóponton való egyidejű futtatásra vannak konfigurálva. Többpéldányos tevékenységek esetén olyan nagy teljesítményű számítási megoldásokat használhat, amelyeknél a rendszer számítási csomópontok egy csoportját együtt foglalja le egyetlen közös számítási feladat (például a Message Passing Interface (MPI)) feldolgozásához.

A Batch szolgáltatásban az MPI-feladatok Batch .NET-könyvtárral való futtatásának részletes leírásáért lásd: [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Többpéldányos tevékenységek használata Message Passing Interface- (MPI-) alkalmazások futtatásához az Azure Batch szolgáltatásban).

### <a name="task-dependencies"></a>Tevékenységfüggőségek
Ahogyan a név is jelzi, a [tevékenységfüggőségek](batch-task-dependencies.md) segítségével beállíthatja, hogy egy tevékenység végrehajtásához más tevékenységek előzetes befejezése legyen szükséges. Ez a funkció olyan helyzetekben lehet hasznos, amikor egy „alsóbb rétegbeli” tevékenység egy „felsőbb rétegbeli” tevékenység kimenetét használja, vagy amikor egy felsőbb rétegbeli tevékenység alsóbb rétegbeli tevékenység által igényelt inicializálást végez. Ezen funkció használatához először engedélyeznie kell a tevékenységfüggőségeket a Batch-feladatban. Ezután az egy másik (vagy sok másik) tevékenységtől függő mindegyik tevékenységhez meg kell adnia azokat a tevékenységeket, amelyektől függnek.

A tevékenységfüggőségekkel a következőkhöz hasonló forgatókönyveket konfigurálhat:

* A *taskB* a *taskA* tevékenységtől függ (a *taskB* végrehajtása nem kezdődik meg a *taskA* befejeződéséig).
* A *taskC* a *taskA* és a *taskB* tevékenységtől is függ.
* A *taskD* egy tevékenységtartománytól függ, például az *1* – *10.* tevékenység befejeződéséig nem hajtja végre a rendszer.

A funkció részletesebb bemutatásért olvassa el a [Task dependencies in Azure Batch](batch-task-dependencies.md) (Tevékenységfüggőségek az Azure Batch szolgáltatásban) című cikket, vagy tekintse meg a Github [azure-batch-samples][github_samples] nevű adattárában található [TaskDependencies][github_sample_taskdeps] kódmintát.

## <a name="environment-settings-for-tasks"></a>Környezeti beállítások tevékenységekhez
A Batch-szolgáltatás által végrehajtott minden egyes feladat hozzáférhet azokhoz a környezeti változókhoz, amelyeket a számítási csomópontokon beállít. Ez magában foglalja a Batch szolgáltatás által definiált környezeti változókat ([service-defined][msdn_env_vars]) és a tevékenységek számára definiálható egyéni környezeti változókat. A tevékenységek által végrehajtott alkalmazások és parancsfájlok a végrehajtás során szintén elérik ezeket a környezeti változókat.

Az egyéni környezeti változókat a tevékenységek és a feladatok szintjén is megadhatja: ehhez töltse ki a kívánt elemek *környezeti beállítások* tulajdonságait. Ilyen például az [Add a task to a job][rest_add_task] (Tevékenység hozzáadása feladathoz) művelet (Batch REST API), vagy a Batch .NET [CloudTask.EnvironmentSettings][net_cloudtask_env] és [CloudJob.CommonEnvironmentSettings][net_job_env] tulajdonsága.

Az ügyfélalkalmazás vagy szolgáltatás a [Get information about a task][rest_get_task_info] (Tevékenység információinak lekérése) művelet (Batch REST) segítségével, vagy a [CloudTask.EnvironmentSettings][net_cloudtask_env] tulajdonság (Batch .NET) elérésével képes beszerezni a tevékenység (szolgáltatás által meghatározott és egyéni) környezeti változóit. A számítási csomóponton végrehajtott folyamatok például a közismert `%VARIABLE_NAME%` (Windows) vagy a `$VARIABLE_NAME` (Linux) szintaxis segítségével képesek elérni ezeket és más környezeti változókat a csomóponton.

Az összes szolgáltatás által definiált környezeti változót tartalmazó teljes listát megtalálja a [Számítási csomópont környezeti változói][msdn_env_vars] című részben.

## <a name="files-and-directories"></a>Fájlok és könyvtárak
Minden tevékenységhez tartozik egy *munkakönyvtár*, amelyben a tevékenység létrehozza a további fájlokat és alkönyvtárakat, ha ilyenekre szükség van. Ez a munkakönyvtár használható a tevékenység által futtatott program, az ez által feldolgozott adatok, valamint az eredményként létrejövő kimenet tárolására. A tevékenységhez tartozó összes fájlnak és könyvtárnak a tevékenység felhasználója a tulajdonosa.

A Batch szolgáltatás *gyökérkönyvtárként* megjeleníti a csomóponton lévő fájlrendszer egy részét. A tevékenységek az `AZ_BATCH_NODE_ROOT_DIR` környezeti változóra hivatkozva tudják elérni a gyökérkönyvtárat. A környezeti változók használatával kapcsolatos további információért lásd: [Környezeti beállítások tevékenységekhez](#environment-settings-for-tasks).

A gyökérkönyvtár a következő könyvtárstruktúrát tartalmazza:

![Számítási csomópont könyvtárstruktúrája][1]

* **shared**: ez a könyvtár olvasási/írási hozzáférést nyújt a csomóponton futó *összes* tevékenység számára. Ebben a könyvtárban a csomóponton futó összes tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni. A tevékenységek az `AZ_BATCH_NODE_SHARED_DIR` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat.
* **startup**: az indítási tevékenység ezt a könyvtárat használja munkakönyvtárként. Az indítási tevékenység által a csomópontra letöltött összes fájlt ez a könyvtár tárolja. Ebben a könyvtárban az indítási tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni. A tevékenységek az `AZ_BATCH_NODE_STARTUP_DIR` környezeti változóra hivatkozva tudják elérni ezt a könyvtárat.
* **Tasks**: létrejön egy könyvtár a csomóponton futó minden egyes feladathoz. Ez a könyvtár a `AZ_BATCH_TASK_DIR` környezeti változóra való hivatkozással érhető el.

    A Batch szolgáltatás minden tevékenységkönyvtárban létrehoz egy munkakönyvtárat (`wd`), amelynek egyedi elérési útját a(z) `AZ_BATCH_TASK_WORKING_DIR` környezeti változó határozza meg. Ez a könyvtár olvasási/írási hozzáférést nyújt a tevékenységhez. A tevékenység jogosult fájlokat létrehozni, olvasni, módosítani és törölni ebben a könyvtárban. A rendszer a tevékenységhez beállított *RetentionTime* korlátozás értékének megfelelően őrzi meg a könyvtárat.

    `stdout.txt` és `stderr.txt`: a rendszer ezeket a fájlokat írja a tevékenységmappába a tevékenység futtatásakor.

> [!IMPORTANT]
> Amikor eltávolít egy csomópontot a készletből, a csomóponton tárolt *összes* fájlt is eltávolítja.
>
>

## <a name="application-packages"></a>Alkalmazáscsomagok
Az [alkalmazáscsomagok](batch-application-packages.md) funkció az alkalmazások egyszerű kezelését és üzembe helyezését nyújtja a készletek számítási csomópontjain. A tevékenységek által futtatott alkalmazásokból, illetve ezek bináris és támogatófájljaiból több különböző verziót is feltölthet és kezelhet. Ezt követően automatikusan üzembe helyezheti ezeket az alkalmazásokat a készletben működő számítási csomópontokon.

Az alkalmazáscsomagokat a készletek és a tevékenységek szintjén is beállíthatja. Ha készletszintű alkalmazáscsomagokat állít be, a rendszer a készlet összes csomópontján üzembe helyezi az alkalmazást. Ha tevékenységszintű alkalmazáscsomagokat állít be, a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyen a feladathoz kapcsolódó tevékenységek közül legalább egy futásra van beütemezve. Az üzembe helyezés közvetlenül a tevékenység parancssorának futtatása előtt történik.

Az Azure Storage-tárfiókkal való együttműködés részleteit a Batch kezeli: a rendszer itt tárolja az alkalmazáscsomagokat, és innen helyezi őket üzembe a számítási csomópontokra, így a kód és a felügyelet is egyszerűbbé válik.

Az alkalmazáscsomag szolgáltatással kapcsolatos további részletekért lásd: [Alkalmazások telepítése számítási csomópontokon Batch-alkalmazáscsomagokkal](batch-application-packages.md).

> [!NOTE]
> Ha egy *meglévő* készlethez ad készletszintű alkalmazáscsomagokat, ahhoz, hogy a rendszer üzembe tudja helyezni ezeket a csomópontokon, újra kell indítania a számítási csomópontokat.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Készlet és számítási csomópont élettartama
Az Azure Batch-megoldás tervezésekor el kell döntenie, hogy hogyan és mikor jöjjenek létre a készletek, illetve, hogy mennyi ideig legyenek elérhetőek a készletekben lévő számítási csomópontok.

Az egyik szélsőség, ha azt állítja be, hogy a rendszer hozza létre a készletet az Ön által elküldött egyes feladatokhoz, és azonnal törölje a készletet, amint az egyes feladatok tevékenységeinek végrehajtása befejeződött. Ez maximalizálja a kihasználtságot, mivel a rendszer csak akkor foglalja le a csomópontokat, amikor szükség van rájuk, és rögtön leállítja őket, amint tétlenek lesznek. Bár ez azt jelenti, hogy a feladatnak meg kell várnia a csomópontok lefoglalását, fontos megjegyezni, hogy a tevékenységek ütemezve lesznek a végrehajtásra, amint a csomópontok önállóan elérhetővé válnak, le vannak foglalva, és amint az indítási tevékenység befejeződött. A Batch *nem* vár addig a tevékenységek csomópontokhoz rendelésével, amíg a készletben lévő összes csomópont elérhetővé válik. Így garantálható az összes elérhető csomópont maximális kihasználtsága.

A másik végletet akkor érdemes választani, ha a feladatok azonnali indítása a legfontosabb: ilyenkor a feladatok elküldése előtt hozza létre a készletet, és tegye elérhetővé a csomópontokat. Ebben a megoldásban a tevékenységek azonnal indulhatnak, de a csomópontok tétlenek lehetnek a tevékenységek hozzárendeléséig.

A változó természetű, ám folyamatos terhelések kezeléséhez általában a fenti két megoldás kombinációját használjuk. Létrehozhat egy készletet, amelynek akár több feladatot is el lehet küldeni, de beállíthatja, hogy a rendszer a feladathoz tartozó terhelés alapján csökkentse vagy növelje a csomópontok számát (lásd a következő, [A számítási erőforrások méretezése](#scaling-compute-resources) című fejezetet). Ez reaktív módon, az aktuális terhelés alapján is elvégezhető, de proaktív módszert is használhat, ha a terhelés előrejelezhető.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>A virtuális hálózat (VNet) és a tűzfal konfigurálása 

Amikor számítási csomópontok készletét hozza létre a Batchben, hozzárendelheti a készletet egy Azure-beli [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) alhálózatához. Egy Azure-beli virtuális hálózat használatához a Batch-ügyfél API-jának Azure Active Directory- (AD-) hitelesítést kell használnia. Az Azure AD Azure Batch-támogatásának dokumentációjáért lásd a [Batch szolgáltatás Active Directoryval történő hitelesítésével](batch-aad-auth.md) foglalkozó témakört.  

### <a name="vnet-requirements"></a>A virtuális hálózat követelményei
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

A Batch-készletek virtuális hálózatban való beállítására vonatkozó további információért lásd: [Virtuális gépek készletének létrehozása a virtuális hálózattal](batch-virtual-network.md).

## <a name="scaling-compute-resources"></a>A számítási erőforrások méretezése
Az [automatikus méretezéssel](batch-automatic-scaling.md) megadhatja, hogy a Batch szolgáltatás dinamikusan állítsa be a számítási csomópontok számát a készletben az aktuális számítási feladatok és a számítási forgatókönyv erőforrás-használata alapján. Ez lehetővé teszi az alkalmazásfuttatás teljes költségeinek csökkentését, mivel csak a szükséges erőforrásokat használja, és felszabadítja a szükségteleneket.

Az automatikus skálázáshoz írnia kell egy [automatikus skálázási képletet](batch-automatic-scaling.md#automatic-scaling-formulas), amelyet aztán társítania kell a készlethez. A Batch szolgáltatás ezzel a képlettel határozza meg a készletben működő csomópontok célszámát a következő skálázási intervallumhoz (amelyet Ön állíthat be). A készlet automatikus skálázását a készlet létrehozásakor és később egyaránt bekapcsolhatja. A skálázási beállításokat a skálázás bekapcsolása után is módosíthatja.

Előfordulhat például, hogy az adott feladathoz rendkívül nagyszámú végrehajtandó tevékenység tartozik. Ekkor skálázási képletet rendelhet a készlethez, amely a sorban álló tevékenységek aktuális száma és a feladathoz tartozó tevékenységek befejezettségi állapota alapján határozza meg a készletben működő csomópontok számát. A Batch szolgáltatás időnként kiértékeli a képletet és átméretezi a készletet a számítási feladatok és a képlet egyéb beállításai alapján. A szolgáltatás szükség szerint csomópontokat ad hozzá, amikor nagy számú tevékenység van a várakozási sorban, és eltávolít csomópontokat, amikor nincs futó tevékenység a várakozási sorban.

A méretezési képletek a következő mérőszámokon alapulhatnak:

* Az **időmérőszámok** a megadott számú órák során öt percenként gyűjtött statisztikák alapján kaphatók meg.
* Az **erőforrás-mérőszámok** a CPU-használat, a sávszélesség-használat, a memóriahasználat és a csomópontok száma alapján számíthatók ki.
* A **tevékenységmetrikák** alapját a tevékenységállapotok, például *Aktív* (sorban áll), *Fut* vagy *Befejezve* képezik.

Amikor az automatikus skálázás csökkenti a készletben működő csomópontok számát, érdemes megfontolni, hogy mi történjen a csökkentési művelet idején még futó tevékenységekkel. A Batch *csomópont-felszabadítási funkciót* kínál ennek megkönnyítésére, amelyet felvehet a képletbe. Megadhatja például, hogy a rendszer azonnal leállítsa a futó tevékenységeket, majd egy másik csomóponton sorba állítsa a tevékenységeket végrehajtás céljából, vagy hagyja őket lefutni, és csak ezután távolítsa el a csomópontot a készletből.

Az alkalmazások automatikus méretezésével kapcsolatos további információért lásd: [Számítási csomópontok automatikus méretezése egy Azure Batch-készletben](batch-automatic-scaling.md).

> [!TIP]
> A számítási erőforrások kihasználtságának maximalizálása érdekében állítsa nullára a csomópontok célszámát a feladatok végén, de engedélyezze a futó tevékenységek befejeződését.
>
>

## <a name="security-with-certificates"></a>Biztonság tanúsítványokkal
Általában tanúsítványokat kell használnia tevékenységek bizalmas információinak, például az [Azure Storage-fiókok][azure_storage] kulcsának titkosításakor vagy visszafejtésekor. Ehhez tanúsítványokat telepíthet a csomópontokra. A titkosított titkos kulcsok parancssori paraméterek segítségével vagy valamelyik tevékenység-erőforrásba ágyazva jutnak el a tevékenységekhez, és a telepített tanúsítványokkal fejthetők vissza.

A [Tanúsítvány hozzáadása][rest_add_cert] művelettel (Batch REST) vagy a [CertificateOperations.CreateCertificate][net_create_cert] metódussal (Batch .NET) adhat tanúsítványt Batch-fiókhoz. Ezután a tanúsítványt új vagy meglévő készlethez társíthatja. Amikor egy tanúsítvány egy készlethez van társítva, a Batch szolgáltatás telepíti a tanúsítványt a készlet minden csomópontján. A Batch szolgáltatás a csomópont indulásakor, még a tevékenységek (ideértve az indítási és a feladatkezelői tevékenységeket is) indítása előtt telepíti a megfelelő tanúsítványokat.

Amikor *meglévő* készlethez ad tanúsítványokat, újra kell indítania a számítási csomópontokat, hogy a rendszer az összes csomópontra alkalmazza a tanúsítványokat.

## <a name="error-handling"></a>Hibakezelés
Szükség lehet a Batch megoldáson belül a tevékenység és az alkalmazások hibáinak a kezelése is.

### <a name="task-failure-handling"></a>Tevékenységhibák kezelése
A tevékenységhibák a következő kategóriákba esnek:

* **Előfeldolgozási hibák**

    Ha egy tevékenység nem indul el, a tevékenységhez egy előfeldolgozási hiba lesz beállítva.  

    Előfeldolgozási hibák akkor fordulhatnak elő, ha a tevékenység erőforrásfájljait áthelyezték, a tárfiók már nem érhető el, vagy más olyan hiba történt, amely meggátolta a fájlok másolását a csomópontra.

* **Sikertelen fájlfeltöltés**

    Ha egy tevékenységhez megadott fájlok feltöltése valamilyen okból meghiúsul, a rendszer a feltöltési hiba állapotot társítja a tevékenységhez.

    Fájlfeltöltési hiba akkor fordulhat elő, ha az Azure Storage eléréséhez megadott közös hozzáférésű jogosultságkód (SAS) érvénytelen vagy nem biztosít írási jogosultságot, ha a tárfiók már nem érhető el, vagy egyéb probléma miatt nem sikerült a fájlok másolása a csomópontról.    

* **Alkalmazáshibák**

    A tevékenység parancssora által meghatározott folyamat is meghiúsulhat. A folyamat sikertelennek minősül, amikor nullától eltérő kilépési kódot ad vissza a tevékenység által végrehajtott folyamat (lásd az alábbi, *Tevékenységekhez tartozó kilépési kódok* című fejezetet).

    Alkalmazáshibák esetén úgy konfigurálhatja a Batch szolgáltatást, hogy az automatikusan újrapróbálja a tevékenységet a megadott számú alkalommal.

* **Korlátozáshibák**

    Olyan korlátozás is beállítható, amely meghatározza a feladat vagy a tevékenység maximális végrehajtási időtartamát. Ez a *maxWallClockTime* érték. Ez olyan feladatok leállításához lehet hasznos, amelyek nem haladnak.

    Ha eltelik a maximális időtartam, a tevékenység *befejezve* állapotot kap, de a kilépési kódhoz a `0xC000013A` értéket, a *schedulingError* mezőhöz pedig a `{ category:"ServerError", code="TaskEnded"}` értéket társítja a rendszer.

### <a name="debugging-application-failures"></a>Alkalmazáshibák keresése
* `stderr` és `stdout`

    A futtatás során az alkalmazások diagnosztikai kimenetet készíthetnek, amely felhasználható a hibák elhárításához. A fenti [Fájlok és könyvtárak](#files-and-directories) fejezetben említettek szerint a Batch szolgáltatás a számítási csomópont tevékenységkönyvtárában helyezi el a standard kimeneti és a standard hibakimeneti adatokat tartalmazó `stdout.txt` és `stderr.txt` fájlokat. A fájlokat az Azure Portal webhelyről vagy valamelyik Batch SDK útján lehet letölteni. Ezeket, illetve más hibaelhárítási célú fájlokat letöltheti például a [ComputeNode.GetNodeFile][net_getfile_node] és a [CloudTask.GetNodeFile][net_getfile_task] metódussal, amely a Batch .NET-kódtárában érhető el.

* **Tevékenységek kilépési kódjai**

    Ahogy azt korábban említettük, a Batch szolgáltatás sikertelenként értékeli a tevékenységet, ha a tevékenység által végrehajtott folyamat kilépési kódja nullától eltérő. Amikor a tevékenységek folyamatokat hajtanak végre, a Batch a *folyamat visszatérési kódjával* tölti ki a tevékenység kilépési kód tulajdonságát. Fontos tudni, hogy a tevékenységek kilépési kódját **nem** a Batch szolgáltatás határozza meg. Egy tevékenység kilépési kódját maga a folyamat határozza meg, vagy az operációs rendszer, amelyen a folyamatot végrehajtották.

### <a name="accounting-for-task-failures-or-interruptions"></a>Tevékenységhibák vagy -megszakítások kezelése
A tevékenységek időnként meghiúsulhatnak vagy megszakadhatnak. Maga a tevékenységalkalmazás is meghiúsulhat, vagy újraindulhat a tevékenységet futtató csomópont, vagy a rendszer eltávolíthatja a csomópontot a készletből egy átméretezési művelet során, ha a készlet felszabadítási szabályzatát úgy állították be, hogy ne várja meg a tevékenységek befejeződését, hanem azonnal távolítsa el a csomópontokat. A tevékenységet a Batch minden esetben képes automatikusan újra a várólistára helyezni, hogy egy másik csomópont hajtsa végre.

Az is előfordulhat, hogy egy ismétlődő hiba miatt egy tevékenység lefagy, vagy túl sok ideig tart a végrehajtása. Beállíthatja a tevékenységekhez engedélyezett maximális végrehajtási intervallumot is. Ha átlépi a maximális végrehajtási intervallumot, a Batch szolgáltatás megszakítja a tevékenységalkalmazást.

### <a name="connecting-to-compute-nodes"></a>Csatlakozás számítási csomópontokhoz
Ha távolról bejelentkezik a számítási csomópontra, további hibakeresési lehetőségeket használhat. Az Azure Portal webhely segítségével letöltheti az RDP-fájlt a windowsos csomópontokra, vagy beszerezheti az SSH-kapcsolathoz szükséges információkat a linuxos csomópontokhoz. Ez a Batch API-k, például a [Batch .NET][net_rdpfile] vagy a [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) segítségével is megvalósítható.

> [!IMPORTANT]
> Ha RDP-n vagy SSH-n keresztül szeretne csatlakozni a csomóponthoz, először létre kell hoznia egy felhasználót a csomóponton. Ehhez használja az Azure Portal webhelyet: [adjon hozzá egy felhasználót a csomóponthoz][rest_create_user] a Batch REST API segítségével, és hívja meg a [ComputeNode.CreateComputeNodeUser][net_create_user] metódust a Batch .NET-ben, vagy az [add_user][py_add_user] metódust a Batch Python modulban.
>
>

Ha korlátoznia kell vagy le kell tiltania a számítási csomópontok RDP- vagy SSH-hozzáférését, tekintse át az [Azure Batch-készletbe tartozó számítási csomópontok távelérésének konfigurálásával vagy letiltásával](pool-endpoint-configuration.md) foglalkozó cikket.

### <a name="troubleshooting-problematic-compute-nodes"></a>Problémás számítási csomópontok hibaelhárítása
Olyan esetekben, ahol néhány tevékenység meghiúsul, a Batch ügyfélalkalmazás vagy szolgáltatás megvizsgálhatja a meghiúsult tevékenységek metaadatait a rosszul működő csomópontok azonosítása érdekében. A készletek minden csomópontja egyedi azonosítót kap, és a tevékenységet futtató csomópont szerepel a tevékenység metaadataiban. Ha sikerült azonosítani a problematikus csomópontot, számos különböző műveletet elvégezhet vele:

* **Újraindíthatja a csomópontot** ([REST][rest_reboot] | [.NET][net_reboot])

    A csomópont újraindítása néha segít a rejtett problémák, például az elakadt vagy összeomlott folyamatok megoldásában. Vegye figyelembe, hogy ha a készlet indítási tevékenységet használ, vagy a feladat feladat-előkészítési tevékenységet használ, ezeket a rendszer a csomópont újraindításakor végrehajtja.
* **Alaphelyzetbe állíthatja a csomópont rendszerképét** ([REST][rest_reimage] | [.NET][net_reimage])

    Ez újratelepíti az operációs rendszert a csomóponton. A csomópontok újraindításához hasonlóan újrafuttatja az indítási tevékenységeket és a feladat-előkészítési tevékenységeket a csomópont rendszerképének alaphelyzetbe állítása után.
* **Eltávolíthatja a csomópontot a készletből** ([REST][rest_remove] | [.NET][net_remove])

    Néha teljesen el kell távolítani a csomópontot a készletből.
* **Letilthatja a tevékenységütemezést a csomóponton** ([REST][rest_offline] | [.NET][net_offline])

    Ez tulajdonképpen offline állapotba helyezi a csomópontot, így ahhoz nem rendel további tevékenységeket, de engedélyezi, hogy a csomópont továbbra is fusson a készletben. Ez lehetővé teszi, hogy tovább vizsgálja a hibák okait a meghiúsult tevékenység adatainak elvesztése nélkül, és anélkül, hogy a csomópont további tevékenységhibákat okozna. Letilthatja például a tevékenységütemezést a csomóponton, majd [távolról bejelentkezhet](#connecting-to-compute-nodes) a csomópont eseménynaplóinak megvizsgálása vagy egyéb hibaelhárítás elvégzése érdekében. Ha végzett a vizsgálattal, online állapotba állíthatja a csomópontot a tevékenységütemezés engedélyezésével ([REST][rest_online] | [.NET][net_online]), vagy a fentiekben említett más műveletek egyikét is elvégezheti.

> [!IMPORTANT]
> A fejezetben leírt műveletekkel (újraindítás, rendszerkép alaphelyzetbe állítása, eltávolítás, tevékenységütemezés letiltása) meghatározhatja, hogy a rendszer hogyan kezelje a csomópontokon aktuálisan futó tevékenységeket a művelet elvégzésekor. Amikor például a Batch .NET ügyfélkódtára segítségével letiltja a tevékenységütemezést egy csomóponton, megadhat egy [DisableComputeNodeSchedulingOption][net_offline_option] enumerálási értéket, amellyel megszabhatja, hogy mit szeretne tenni: **leállítani** a futó tevékenységeket, **újból várólistára helyezni** azokat más csomópontokon való ütemezéshez, vagy engedélyezni a futó tevékenységek befejezését a művelet elvégzése előtt (**TaskCompletion**).
>
>

## <a name="next-steps"></a>További lépések
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* Tekintsen át lépésről lépésre egy Batch-mintaalkalmazást az [Ismerkedés az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md) című cikkben. Az oktatóanyagból [Python-verzió](batch-python-tutorial.md) is elérhető, amelyben a számítási feladat linuxos számítási csomópontokon fut.
* Töltse le és telepítse a [BatchLabs][batch_labs] alkalmazást, amelyet bármikor használhat, ha a Batch-megoldások fejlesztése során segítségre van szüksége. A BatchLabs alkalmazással Azure Batch-alkalmazásokat hozhat létre és monitorozhat, és a felmerülő hibáikat is elháríthatja. 
* Tanulja meg, hogyan hozhat létre [készletet linuxos számítási csomópontokból](batch-linux-nodes.md).
* Keresse fel az [Azure Batch fórumot][batch_forum] az MSDN-en. Akár még csak most tanulja a Batch használatát, akár képzett szakértőnek számít, a fórum remek terepet biztosít, ahol felteheti kérdéseit.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchLabs/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
