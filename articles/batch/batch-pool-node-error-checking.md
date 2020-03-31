---
title: Készlet- és csomóponthibák ellenőrzése – Azure Batch
description: Ez a cikk ismerteti a háttérben előforduló műveleteket, valamint a hibákat, hogy ellenőrizze, és hogyan lehet elkerülni őket, amikor készletek és csomópontok létrehozása.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472945"
---
# <a name="check-for-pool-and-node-errors"></a>Készlet- és csomóponthibák ellenőrzése

Az Azure Batch-készletek létrehozásakor és kezelésekor egyes műveletek azonnal megtörténnek. Egyes műveletek azonban aszinkronak, és a háttérben futnak, és több percet vesz igénybe.

Az azonnal lezajlott műveletek hibáinak észlelése egyszerű, mivel az API, a CLI vagy a felhasználói felület azonnal visszaadja a hibákat.

Ez a cikk a készletek és a készletcsomópontok esetén előforduló háttér-műveleteket ismerteti. Itt adható meg, hogyan észlelheti és kerülheti el a hibákat.

## <a name="pool-errors"></a>Készlethibák

### <a name="resize-timeout-or-failure"></a>Időtúlméretezés vagy -hiba átméretezése

Új készlet létrehozásakor vagy meglévő készlet átméretezésekor megadhatja a csomópontok célszámát.  A létrehozási vagy átméretezési művelet azonnal befejeződik, de az új csomópontok tényleges lefoglalása vagy a meglévő csomópontok eltávolítása több percet is igénybe vehet.  Megadhatja az átméretezési időtúltöltést a [létrehozási](https://docs.microsoft.com/rest/api/batchservice/pool/add) vagy [átméretezési](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API-ban. Ha a Batch nem tudja megszerezni a csomópontok célszámát az átméretezési időtúlidőszak alatt, a készlet állandó állapotba kerül, és hibákat jelent.

A legutóbbi kiértékelés [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) tulajdonsága felsorolja a bekövetkezett hibákat.

A hibák átméretezési gyakori okai a következők:

- Az időtúlméretezés túl rövid
  - A legtöbb esetben az alapértelmezett 15 perces időmeghosszabbítás elég hosszú a készletcsomópontok lefoglalásához vagy eltávolításához.
  - Ha nagy számú csomópontot foglal le, javasoljuk, hogy az átméretezési időtúlfoglalást 30 percre határozza meg. Például ha egy Azure Marketplace-lemezképből több mint 1000 csomópontra, vagy egy egyéni virtuálisgép-lemezkép több mint 300 csomópontra méretez.
- Elégtelen alapkvóta
  - A Batch-fiók korlátozott az összes készlet között lefoglalható magok száma. A köteg leállítja a csomópontok lefoglalását, amint a kvóta elérte. Növelheti [can increase](https://docs.microsoft.com/azure/batch/batch-quota-limit) az alapkvótát, hogy a Batch több csomópontot foglalhason le.
- Nincs elegendő alhálózati IP-kiszolgáló, ha egy [készlet virtuális hálózatban van](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A virtuális hálózati alhálózatnak elegendő hozzárendelés nélküli IP-címmel kell rendelkeznie ahhoz, hogy minden kért készletcsomóponthoz lefoglalja. Ellenkező esetben a csomópontok nem hozhatók létre.
- Nincs elegendő erőforrás, ha egy [készlet virtuális hálózatban van](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Előfordulhat, hogy erőforrásokat, például a terheléselosztók, nyilvános IP-k és a hálózati biztonsági csoportok ugyanabban az előfizetésben, mint a Batch-fiók. Ellenőrizze, hogy az előfizetési kvóták elegendőek-e ezekhez az erőforrásokhoz.
- Nagy készletek egyéni virtuálisgép-lemezképekkel
  - Az egyéni virtuálisgép-lemezképeket használó nagy készletek lefoglalása és átméretezése hosszabb időtvehet ki.  A korlátokkal és konfigurációval kapcsolatos javaslatokért [lásd: Készlet létrehozása a megosztott képtárral.](batch-sig-images.md)

### <a name="automatic-scaling-failures"></a>Automatikus skálázási hibák

Azt is beállíthatja, hogy az Azure Batch automatikusan méretezze a készletek ben lévő csomópontok számát. A [készlet automatikus méretezési képletének](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)paramétereit kell meghatározni. A Batch szolgáltatás a képlet segítségével rendszeresen kiértékeli a készletben lévő csomópontok számát, és új célszámot állít be. A következő típusú problémák fordulhatnak elő:

- Az automatikus skálázás kiértékelése sikertelen.
- Az eredményül kapott átméretezési művelet sikertelen, és időtúljár.
- Az automatikus méretezési képlettel kapcsolatos probléma helytelen csomópontcélértékekhez vezet. Az átméretezés vagy működik, vagy időtúljár.

Az [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) tulajdonság használatával kaphat információt az utolsó automatikus skálázáskiértékelésről. Ez a tulajdonság jelenti a kiértékelési időt, az értékeket és az eredményt, valamint a teljesítményhibákat.

A [készlet átméretezése teljes esemény](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) rögzíti az összes kiértékelés adatait.

### <a name="delete"></a>Törlés

Ha olyan készletet töröl, amely csomópontokat tartalmaz, az első Köteg törli a csomópontokat. Ezután törli magát a készletobjektumot. A készletcsomópontok törlése eltarthat néhány percig.

A Batch a [törlési](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) folyamat során a készlet állapotát **törlésre** állítja. A hívó alkalmazás észleli, ha a készlet törlése túl sokáig tart az **állapot** és **stateTransitionTime** tulajdonságok használatával.

## <a name="pool-compute-node-errors"></a>Számítási csomópont hibáinak kiszerelése

Még akkor is, ha a Batch sikeresen lefoglalja a csomópontokat egy készletben, a különböző problémák miatt egyes csomópontok nem megfelelő állapotúak lehetnek, és nem tudják futtatni a feladatokat. Ezek a csomópontok továbbra is díjakat, ezért fontos, hogy észlelje a problémákat, hogy ne kelljen fizetni a csomópontok, amelyek nem használhatók. A gyakori csomóponthibák mellett az aktuális [feladatállapot](/rest/api/batchservice/job/get#jobstate) ismerete is hasznos a hibaelhárításhoz.

### <a name="start-task-failures"></a>Tevékenységhibák indítása

Előfordulhat, hogy meg szeretne adni egy választható [indítási feladatot](/rest/api/batchservice/pool/add#starttask) egy készlethez. Mint minden feladat, használhatja a parancssori és erőforrás fájlokat letölteni a tárolóból. A kezdési feladat minden csomóponthoz fut, miután elvan indítva. A **waitForSuccess** tulajdonság azt adja meg, hogy a Batch megvárja-e, amíg a kezdési feladat sikeresen befejeződik, mielőtt bármilyen feladatot egy csomópontra ütemezne.

Mi a teendő, ha úgy állította be a csomópontot, hogy várjon a feladat sikeres befejezésére, de az indítási feladat sikertelen? Ebben az esetben a csomópont nem lesz használható, de továbbra is felszámítják.

Észlelheti a kezdési feladathibákat a legfelső szintű [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) csomóponttulajdonság [eredmény-](/rest/api/batchservice/computenode/get#taskexecutionresult) és [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) tulajdonságainak használatával.

A sikertelen indítási feladat azt is okozza, hogy a Batch állítsa be a csomópont [állapotát](/rest/api/batchservice/computenode/get#computenodestate) **starttaskfailed,** ha **waitForSuccess** értéke **igaz**.

Mint minden feladat, számos oka lehet a kezdő feladat sikertelen.  Hibaelhárításhoz ellenőrizze az stdout, stderr és minden további feladat-specifikus naplófájlokat.

A kezdési feladatoknak újra be kell lépniük, mivel lehetséges, hogy az indítási feladat többször is fut ugyanazon a csomóponton; az indítási feladat akkor fut, ha egy csomópontot újrarendszereznek vagy újraindítanak. Ritka esetekben egy indítási feladat egy csomópont újraindítását okozó esemény után fog futni, ahol az operációs rendszer vagy az ideiglenes lemezek egyikét újralemezelték, míg a másik nem. Mivel a Batch indítási feladatai (mint az összes Batch-feladat) az ideiglenes lemezről futnak, ez általában nem jelent problémát, de bizonyos esetekben, amikor a kezdési feladat egy alkalmazás telepítése az operációs rendszer lemezére, és más adatok at tart az ideiglenes lemezen, ez problémákat, mert a dolgok nincsenek szinkronban. Mindkét lemez használata esetén ennek megfelelően védje az alkalmazást.

### <a name="application-package-download-failure"></a>Alkalmazáscsomag letöltési hibája

Egy készlethez megadhat egy vagy több alkalmazáscsomagot. A Batch letölti a megadott csomagfájlokat minden csomópontra, és a csomópont megkezdése után, de a feladatok ütemezése előtt kibontja a fájlokat. Gyakori, hogy egy kezdő feladat parancssort használ az alkalmazáscsomagokhoz hasonlóan. Például fájlok másolása egy másik helyre, vagy a telepítő futtatása.

A [csomóponthibák](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság az alkalmazáscsomag letöltésének és tömörítésének törlését jelenti; a csomópont állapota **használhatatlan.**

### <a name="container-download-failure"></a>A tároló letöltésének hibája

Egy készleten megadhat egy vagy több tárolóhivatkozást. A Batch letölti a megadott tárolókat az egyes csomópontokra. A [csomóponthibák](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság a tároló letöltésének sikertelenségét jelenti, és a csomópont állapotát **használhatatlanra állítja.**

### <a name="node-in-unusable-state"></a>Csomópont használhatatlan állapotban

Az Azure Batch több okból is **használhatatlanra** állíthatja a [csomópont állapotát.](/rest/api/batchservice/computenode/get#computenodestate) Ha a csomópont állapota **használhatatlan,** a feladatok nem ütemezhetők a csomópontra, de továbbra is díjakat vonnak maga után.

A csomópontok **használhatatlan** állapotban vannak, de [hiba](/rest/api/batchservice/computenode/get#computenodeerror) nélkül azt jelenti, hogy a Batch nem tud kommunikálni a virtuális géptel. Ebben az esetben a Batch mindig megpróbálja helyreállítani a virtuális gép. A Batch nem kísérli meg automatikusan helyreállítani azokat a virtuális gépeket, amelyek nem tudták telepíteni az alkalmazáscsomagokat vagy -tárolókat, még akkor sem, ha állapotuk **használhatatlan.**

Ha a Batch meg tudja állapítani az okát, a [csomóponthibák](/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság jelenti azt.

A **használhatatlan** csomópontok okai a következők:

- Egy egyéni virtuálisgép-lemezkép érvénytelen. Például egy kép, amely nincs megfelelően előkészítve.

- A virtuális gép áthelyezése egy infrastruktúra-hiba vagy egy alacsony szintű frissítés miatt. A köteg helyreállítja a csomópontot.

- A virtuális gép lemezképe telepítve van a hardver, amely nem támogatja azt. Például egy CentOS HPC-lemezképet próbál futtatni egy [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) virtuális gépen.

- A virtuális gépek egy [Azure virtuális hálózatban](batch-virtual-network.md)vannak, és a forgalom le van tiltva a kulcsfontosságú portok.

- A virtuális gépek egy virtuális hálózatban vannak, de az Azure storage-ba irányuló kimenő forgalom le van tiltva.

- A virtuális gépek egy virtuális hálózatban egy ügyfél DNS-konfiguráció, és a DNS-kiszolgáló nem tudja feloldani az Azure storage.

### <a name="node-agent-log-files"></a>Csomópontügynök naplófájljai

Az egyes készletcsomópontokon futó Batch-ügynök folyamat naplófájlokat biztosíthat, amelyek hasznosak lehetnek, ha kapcsolatba kell lépnie az ügyfélszolgálattal egy készletcsomópont-problémával kapcsolatban. Egy csomópont naplófájljai feltölthetők az Azure Portalon, a Batch Exploreren vagy egy [API-n](/rest/api/batchservice/computenode/uploadbatchservicelogs)keresztül. Hasznos a naplófájlok feltöltése és mentése. Ezután törölheti a csomópontot vagy készletet a futó csomópontok költségének mentéséhez.

### <a name="node-disk-full"></a>Megtelt a csomópont lemeze

A készletcsomópont virtuális gépének ideiglenes meghajtóját a Batch feladatfájlokhoz, feladatfájlokhoz és megosztott fájlokhoz használja.

- Alkalmazáscsomagok fájljai
- Feladat-erőforrás fájlok
- Alkalmazásspecifikus fájlok, amelyek a Batch mappába töltődnek le
- Stdout és stderr fájlok minden feladat alkalmazás végrehajtása
- Alkalmazásspecifikus kimeneti fájlok

Ezek közül a fájlok közül néhány csak egyszer íródik a készletcsomópontok létrehozásakor, például készletalkalmazás-csomagok vagy készletindítási tevékenységerőforrás-fájlok. Még akkor is, ha csak egyszer írt, amikor a csomópont jön létre, ha ezek a fájlok túl nagy ok tudták kitölteni az ideiglenes meghajtót.

A többi fájl ki van írva minden csomóponton futó feladathoz, például az stdout és az stderr. Ha sok feladat fut ugyanazon a csomóponton és/vagy a feladatfájlok túl nagyok, kitölthetik az ideiglenes meghajtót.

Az ideiglenes meghajtó mérete a virtuális gép méretétől függ. A virtuális gép méretének kiválasztásakor az egyik szempont, hogy az ideiglenes meghajtó elegendő helyet.

- Az Azure Portalon egy készlet hozzáadásakor a virtuális gép méreteinek teljes listája megjeleníthető, és van egy "Erőforrás lemezméret" oszlop.
- Az összes virtuálisgép-méretet leíró cikkek "Ideiglenes tároló" oszloppal rendelkező táblákkal rendelkeznek; például [számítási optimalizált virtuálisgép-méretek](/azure/virtual-machines/windows/sizes-compute)

Az egyes feladatok által kiírt fájlok esetében minden feladathoz megadható megőrzési idő, amely meghatározza, hogy a feladatfájlok mennyi ideig maradjanak meg az automatikus törlés előtt. A megőrzési idő csökkenthető a tárolási követelmények csökkentése érdekében.


Ha az ideiglenes lemezen elfogy a hely (vagy nagyon közel van a hely fogytán), a csomópont [használhatatlan](/rest/api/batchservice/computenode/get#computenodestate) állapotba kerül, és egy csomóponthiba jelenik meg, mondván, hogy a lemez megtelt.

### <a name="what-to-do-when-a-disk-is-full"></a>Mi a teendő, ha a lemez megtelt?

Határozza meg, hogy miért van megtelt a lemez: Ha nem biztos abban, hogy mi foglal helyet a csomóponton, ajánlott a csomóponthoz való távoli helyre, és manuálisan vizsgálja meg, hogy hová ment a hely. A [Batch List Files API-t](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) a Batch List Files API-val is megvizsgálhatja a Batch felügyelt mappákban lévő fájlok (például feladatkimenetek) vizsgálatához. Vegye figyelembe, hogy ez az API csak a Batch által felügyelt könyvtárakban lévő fájlokat sorolja fel, és ha a feladatok máshol hoztak létre fájlokat, nem fogja látni őket.

Győződjön meg arról, hogy minden szükséges adatot lekért a csomópontról, vagy feltöltöttegy tartós tárolóba. A lemezteljes probléma minden megoldása az adatok törlésével jár, hogy helyet szabadítson fel.

### <a name="recovering-the-node"></a>A csomópont helyreállítása

1. Ha a készlet [egy C.loudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) készlet, a [Batch újralemezkép API-n](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage)keresztül újraelláthatja a csomópontot. Ez megtisztítja az egész lemezt. A [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) készletek jelenleg nem támogatottak az újraképzendő lemezkép.

2. Ha a készlet [virtualmachineconfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration), eltávolíthatja a csomópontot a készletből az [eltávolítási csomópontok API használatával.](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes) Ezután újra termesztheti a medencét, hogy a rossz csomópontot egy frissre cserélje.

3.  Régi befejezett feladatok vagy régi befejezett feladatok törlése, amelyek feladatadatai még mindig a csomópontokon vannak. Ha tippelhet arra, hogy milyen feladatok/feladatok vannak a csomópontokon, akkor a [Csomópont RecentTasks gyűjteményében](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) vagy [a csomóponton lévő fájlokban](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode)kereshet. Ha törli a feladatot, az törli a feladat összes feladatát, és a feladatban lévő feladatok törlése elindítja a törölni kívánt csomópont feladatkönyvtárainak adatait, így helyet szabadít fel. Miután elég helyet szabadított fel, indítsa újra a csomópontot, és újra ki kell mozdulnia a "Használhatatlan" állapotból, és újra "Tétlen" állapotba kell lépnie.

## <a name="next-steps"></a>További lépések

Ellenőrizze, hogy beállította-e az alkalmazást átfogó hibaellenőrzés végrehajtására, különösen az aszinkron műveletek hez. Kritikus fontosságú lehet a problémák azonnali észlelése és diagnosztizálása.
