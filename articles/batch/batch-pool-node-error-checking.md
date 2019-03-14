---
title: Ellenőrizze a készletet és a csomópont hibákat – Azure Batch
description: Hibák kereséséhez és elkerülésével őket, készletek és a csomópontok létrehozásakor
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8df9935e935ac8d5a1194cfab103a006cf5546
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791341"
---
# <a name="check-for-pool-and-node-errors"></a>Ellenőrizze a készletet és a csomópont hibákat

Ha Ön létrehozása és kezelése az Azure Batch-készletek, bizonyos műveleteket azonnal történik. Azonban néhány művelet aszinkron és futtatása a háttérben is. Ezek is igénybe vehet néhány percet.

Végzett, azonnali műveleteihez hibák észlelése rendkívül egyszerű, mert az esetleges hibák az API-t, a CLI vagy a felhasználói felület által azonnal visszaadott.

Ez a cikk ismerteti a háttérbeli műveletek, amely akkor fordulhat elő, készletek és a készlet csomópontjain. Azt adja meg, hogyan észlelheti és hibák elkerülése érdekében.

## <a name="pool-errors"></a>Készlet hibák

### <a name="resize-timeout-or-failure"></a>Átméretezés időtúllépése vagy a hiba

Amikor egy új készletet hoz létre, vagy egy meglévő átméretezése tárolókészlet, adja meg a csomópontok célszámát.  A művelet azonnal befejeződik, de a tényleges elosztása az új csomópontok vagy a meglévő csomópontok eltávolításának több percig is eltarthat.  Az átméretezés időtúllépési a megadott a [létrehozása](https://docs.microsoft.com/rest/api/batchservice/pool/add) vagy [átméretezése](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API-t. Ha a Batch az átméretezés időtúllépési ideje alatt nem lehet megszerezni a csomópontok célszámát, leállítja a műveletet. A készlet egy stabil állapotba kerül, és jelentéseket méretezze át a hibákat.

A [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) legutóbbi kiértékelésének tulajdonsága egy átméretezés időtúllépési jelentés, és bármely fellépő hibák listája.

Átméretezés időtúllépések gyakori okai:

- Átméretezés időtúllépési érték túl rövid
  - A legtöbb esetben az alapértelmezett időtúllépési érték 15 perc lefoglalva vagy eltávolítani a készlet csomópontjain elég hosszú.
  - Ha nagy számú csomópont van lefoglalása, ajánlott az átméretezés időtúllépési beállítást 30 perc. Például, ha meg van átméretezése több mint 1000 csomópontok az Azure Marketplace-rendszerképpel, vagy egy egyéni Virtuálisgép-rendszerképről több mint 300 csomópontok.
- Nincs elegendő Processzormagok kvótája
  - Batch-fiók korlátozza a magok száma, amelyek azt foglalhat le minden készletek között. Batch a csomópontok lefoglalása a kvóta elérése után leáll. Ön [növelheti](https://docs.microsoft.com/azure/batch/batch-quota-limit) a magkvóta úgy, hogy a Batch további csomópontok foglalhat le.
- Megfelelő alhálózati IP-címek amikor egy [készlet használata a virtuális hálózaton](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Egy virtuális hálózat alhálózatához kell van-e elegendő hozzá nem rendelt IP-címek lefoglalása minden kért készlet csomópont számára. Ellenkező esetben a csomópontok nem hozható létre.
- Nincs elegendő erőforrás amikor egy [készlet használata a virtuális hálózaton](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A Batch-fiókkal azonos előfizetésben található erőforrások, például a terheléselosztók nyilvános IP-címek és a hálózati biztonsági csoportok létrehozhat. Ellenőrizze, hogy megfelelőek-e az előfizetési kvóták ezekhez az erőforrásokhoz.
- Nagy készletek egyéni Virtuálisgép-rendszerképek
  - Nagy készletek egyéni Virtuálisgép-rendszerképek használó tovább tarthat a lefoglalni, és méretezze át a időtúllépések akkor fordulhat elő.  Lásd: [hozzon létre egy virtuálisgép-készletek egyéni rendszerkép használatával](https://docs.microsoft.com/azure/batch/batch-custom-images) kapcsolatos korlátozások és konfigurációs javaslatokat.

### <a name="automatic-scaling-failures"></a>Az automatikus skálázási hibák

Beállíthatja az Azure Batch automatikusan skálázható az egy készletben lévő csomópontok száma is. A paraméterek meghatározása a [automatikus méretezési képlet készlet](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). A Batch szolgáltatás rendszeresen értékeli a készletben lévő csomópontok számát, és állítson be egy új cél-számot a képlet használatával. A következő típusú hibák akkor fordulhat elő:

- Az automatikus skálázási kiértékelés sikertelen lesz.
- Az eredményül kapott átméretezés végrehajtása meghiúsul, és túllépi az időkorlátot.
- Az automatikus méretezési képlet probléma helytelen csomópont célértékek vezet. Az átméretezés működik, vagy túllépi az időkorlátot.

Utolsó automatikus skálázási kiértékelésével kapcsolatos információkat szerezhet a használatával a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) tulajdonság. Ez a tulajdonság jelentések, a kiértékelés időpontja, az értékek és eredmény és a teljesítmény hibákat.

A [készlet átméretezése kész esemény](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) összes értékelés vonatkozó adatokat rögzíti.

### <a name="delete"></a>Törlés

Ha töröl egy konfigurációt használó csomópontokat tartalmazó készletet, az első Batch törli a csomópontok. Ezután a háttérfeladat törli magát az erőforráskészlet-objektumot. A készlet csomópontjain törölni néhány percet is igénybe vehet.

Batch-készletek a [állapot tárolókészlet](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) való **törlése** a törlése során. A hívó alkalmazás képes észlelni, ha a készlet törlése használatával túl sokáig tart az **állapot** és **stateTransitionTime** tulajdonságait.

## <a name="pool-compute-node-errors"></a>Készlet számítási csomópont hibák

Akkor is, ha a Batch sikeresen lefoglalja a készlet, különböző problémákat okozhat, néhány nem megfelelő állapotú, és nem használható kell csomópont. Ezek a csomópontok díjkötelesek. Fontos problémák észlelése, így nem kell fizetnie használhatatlan csomópontok.

### <a name="start-task-failure"></a>Indítási feladat hibája

Előfordulhat, hogy szeretne megadni egy nem kötelező [kezdő tevékenység](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) -készlet. A többi tevékenységnél, használhatja a parancssorból és az erőforrások fájljainak letöltése a storage-ból. Az indítási tevékenység fut az egyes csomópontok lett indítás után. A **waitForSuccess** tulajdonság határozza meg, hogy a Batch megvárja, amíg az indítási tevékenység sikeresen befejeződik, mielőtt azt ütemezi a feladatokat, hogy egy csomópont.

Mi történik, ha konfigurálta a Várakozás tevékenység befejezése sikeres kezdő, de az indítási tevékenység sikertelen lesz a csomópont? Ebben az esetben a csomópont nem használható, de továbbra is tekintetében számítunk fel díjat.

Észlelheti a kezdő tevékenység sikertelen használatával a [eredmény](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) és [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) tulajdonságait a legfelső szintű [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) csomópont-tulajdonságok.

Nem sikerült az indítási tevékenység jelentkezésekor a Batch szolgáltatást, állítsa be a csomópont [állapot](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) való **starttaskfailed** állíthatja be, ha **waitForSuccess** való **igaz**.

A többi tevékenységnél, ott számos oka lehet az a kezdő tevékenység sikertelen.  A hiba elhárításához ellenőrizze minden további tevékenység-specifikus naplófájl, az stdout és stderr.

### <a name="application-package-download-failure"></a>Alkalmazás-csomag letöltési hiba

Megadhat egy vagy több alkalmazáscsomagot az egy készletben. A Batch letölti a megadott csomag fájlokat minden egyes csomópontot, és kibontja a fájlokat, miután a csomópont elindult, de mielőtt feladatok ütemezése. Szokás a kezdő tevékenység parancssorának, az alkalmazáscsomagok együtt használja. Ha például fájlok másolása egy másik helyre, vagy futtassa a telepítőt.

A csomópont [hibák](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság letöltéséhez, és bontsa ki az alkalmazáscsomag hibát jelez. A Batch állítja a csomópont állapota **használhatatlan**.

### <a name="node-in-unusable-state"></a>Csomópont használhatatlan állapotban

Az Azure Batch előfordulhat, hogy állítsa be a [csomópont állapota](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) való **használhatatlan** ennek számos oka lehet. A csomópont állapota értékre **használhatatlan**, a feladatok nem lehet ütemezni a csomópontra, de továbbra is tekintetében számítunk fel díjat.

A Batch minden esetben megpróbálja helyreállítani a használhatatlan csomópontok, de helyreállítási is, vagy nem lehetséges okától függően.

Ha a Batch megadhatja, hogy az okot, a csomópont [hibák](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság jelzi azt.

Okairól további példái **használhatatlan** csomópontokat tartalmazza:

- Egyéni Virtuálisgép-rendszerkép nem érvényes. Ha például olyan lemezképet, amely nem megfelelően van-e előkészítve.
- Virtuális gép átkerül az infrastruktúra meghibásodása vagy egy alacsony szintű frissítése miatt. A Batch a csomóponton állítja helyre.

### <a name="node-agent-log-files"></a>Csomópont-ügynök naplófájljainak

A Batch minden készlet csomóponton futó ügynök folyamat akkor lehet hasznos, ha egy készlet csomópont problémával kapcsolatban az ügyfélszolgálattól kell naplófájlokban biztosíthat. A csomópont csak akkor tölthetők fel az Azure Portalon, a Batch Explorer, a naplófájlok és a egy [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Ez hasznos feltöltésére, és mentse a rendszernapló fájljaiban. Ezt követően törölheti a csomópont- vagy a futó csomópontok költsége a készlet.

## <a name="next-steps"></a>További lépések

Ellenőrizze, hogy beállította-e az alkalmazás átfogó hibaellenőrzés, különösen az aszinkron műveletek végrehajtásához. Lehet, kritikus fontosságú haladéktalan észlelheti és diagnosztizálhatja a problémákat.
