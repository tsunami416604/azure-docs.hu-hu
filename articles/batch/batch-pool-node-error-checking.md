---
title: Batch-készlet és a csomópont hibák keresése
description: Hibák kereséséhez és elkerülésével készletek és a csomópontok létrehozásakor
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435216"
---
# <a name="checking-for-pool-and-node-errors"></a>Készlet és a csomópont hibák keresése

Amikor létrehozása és kezelése a Batch-készletek, műveletek, amelyek azonnal, és nincsenek az aszinkron műveletek, amelyek nem azonnali, fut a háttérben, és több percig is eltarthat.

Végzett, azonnali műveleteihez hibák észlelése az egyszerű, mivel az esetleges hibák azonnal által visszaadott az API-t, a CLI vagy a felhasználói felület.

Ez a cikk ismerteti a háttérbeli műveletek, amelyek elvégezhetők a készletek és a készlet csomópontjain, – azt adja meg, hogyan hibák észlelése, és hogyan el kell kerülni a hibákat.

## <a name="pool-errors"></a>Készlet hibák

### <a name="resize-timeout-or-failure"></a>Átméretezés időtúllépése vagy a hiba

Ha egy új készletet hoz létre, vagy egy meglévő készletbe, a csomópontok a célként megadott számok átméretezés van megadva.  A művelet azonnal befejeződik, de a tényleges elosztása az új csomópontok vagy a meglévő csomópontok eltávolításra kerül sor a háttérben keresztül lehet néhány percig.  Átméretezés időtúllépés van megadva a [létrehozása](https://docs.microsoft.com/rest/api/batchservice/pool/add) vagy [átméretezése](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API - csomópontok célszáma nem lehet megszerezni az átméretezési időtúllépés, ha majd a művelet leállítja a készlet egy stabil állapotot fog az és méretezési hibát kellene.

Átméretezés időtúllépés jelentette a [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) tulajdonsága az utolsó értékelés, amely felsorolja egy vagy több hiba történt.

Átméretezés időtúllépések gyakori okai:
- Átméretezés időtúllépési érték túl rövid
  - Az alapértelmezett időtúllépési érték 15 perc, amely általában bőséges időt a készlet csomópontjain lefoglalt vagy eltávolítani.
  - Amikor egy nagy számú csomópont (több mint 1000 csomópont egy Piactéri rendszerképből) vagy egy egyéni lemezképből több mint 300 csomópontok lefoglalása a készlet létrehozásakor vagy átméretezése során, egy 30 perces időkorlát ajánlott.
- Nincs elegendő Processzormagok kvótája
  - Batch-fiók lehet magok számának kvótával rendelkezik kiosztott összes készletek között.  A Batch nem lefoglalni a csomópontokat, a kvóta elérése után.  A Processzormagok kvótája [növelhető](https://docs.microsoft.com/azure/batch/batch-quota-limit) ahhoz, hogy további csomópontok lefoglalását.
- Megfelelő alhálózati IP-címek amikor egy [készlet használata a virtuális hálózaton](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Egy virtuális hálózat alhálózatához kell van-e elegendő hozzá nem rendelt IP-címek kiosztásához a kért készlet csomópontjain, ellenkező esetben a csomópontok nem hozható létre.
- Nincs elegendő erőforrás amikor egy [készlet használata a virtuális hálózaton](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - A Batch-fiók létrehozásához használt előfizetés-erőforrások, például a terheléselosztók, nyilvános IP-címek és NSG-k jönnek létre.  Ezekhez az erőforrásokhoz az előfizetési kvóták elegendőnek kell lennie.
- Nagy készletek egyéni Virtuálisgép-rendszerkép használata
  - Egyéni rendszerképekből nagy készleteket foglal le, majd méretezze át a időtúllépések hosszabb időt vesz igénybe is fordulhat elő.  Korlátozások és konfigurációs javaslatokat tartalmazza egy [vonatkozó cikket](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Automatikus skálázás sikertelen

Explicit módon beállítása a készlethez tartozó csomópontok célszámát a készlet létrehozásakor vagy átméretezése, helyett egy készletben lévő csomópontok automatikusan skálázhatók.  Egy [automatikus méretezési képlet is létrehozható egy készlet](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), állítsa be a készlethez tartozó csomópontok célszáma, konfigurálható rendszeres időközönkénti kiértékelendő.  A következő típusú hibák akkor fordulhat elő, és észlelt:

- Az automatikus skálázás kiértékelési sikertelen lehet.
- Az eredményül kapott átméretezési művelet sikertelen lesz, és időkorlátja.
- Előfordulhat, hogy az automatikus méretezési képlet, az átméretezés működik vagy túllépik az időkorlátot a helytelen csomópont célértékek, vezető problémájára.

Információ a legutóbbi automatikus skálázás használatával lekérte a [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) tulajdonság, amely az idő kiértékelése, értékek és az értékelést, és az esetleges hibákat, hajt végre a kiértékelés eredménye kapcsolatos jelentések.

Összes értékelés információ automatikusan rögzített egy [készlet átméretezése kész esemény](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Törlés

Az erőforráskészlet-objektumot magát követi, feltéve, hogy nincsenek a készletben lévő csomópontok, majd a készlet törlése művelet eredményeinek a csomópontok először törlése folyamatban.  A készlet csomópontjain törölni néhány percet is igénybe vehet.

A [állapot tárolókészlet](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) fog szerepelni a "Törlés" a törlése során.  A hívó alkalmazás képes észlelni, ha a készlet törlése a "state" és "stateTransitionTime" tulajdonság használatával túl sokáig tart.

## <a name="pool-compute-node-errors"></a>Készlet számítási csomópont hibák

Csomópontok a készlet sikeresen kiosztható, de különböző problémákat okozhat a folyamatban van a nem megfelelő állapotú csomópontokat, és nem lesz használható.  Miután a csomópontok a készlet le van foglalva, azok költségekkel, és ezért fontos, hogy észlelje a problémákat, hogy ne kelljen fizetnie, amely nem használható csomópontok.

### <a name="start-task-failure"></a>Indítási feladat hibája

Egy nem kötelező [kezdő tevékenység](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) készlet adható meg.  Minden olyan feladat, mint a parancssorból és az erőforrások fájljainak letöltése a storage-ból adható meg.  Az indítási tevékenység a készlet van megadva, de mindegyik csomóponton futtatni – minden csomópont elindítása után az indítási tevékenység fut majd.  Egy további tulajdonságot, a [kezdő tevékenység](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), "waitForSuccess", itt adhatja meg, hogy Batch várnia kell az indítási tevékenység sikeresen befejeződik, mielőtt csomóponthoz egyéb tevékenységeket ütemezne.

Ha egy indítási tevékenység meghiúsul, és a kezdési feladat konfigurációja megadott Várjon, amíg a művelet sikeresen befejeződött, a csomópont használhatatlan lesz, és továbbra is díjat számolunk.

Kezdő tevékenység sikertelen használatával észlelhető a [eredmény](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) és [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) tulajdonságait a legfelső szintű [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) csomópont-tulajdonságok.

Sikertelen az indítási tevékenység is vezet a csomópont [állapot](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) beállítása "starttaskfailed", de csak "waitForSuccess" be lett állítva. Ha "true".

A többi tevékenységnél, ott számos oka lehet az a kezdő tevékenység sikertelen.  Hibaelhárítás, minden további tevékenység-specifikus naplófájl, az stdout és stderr ellenőrizni kell.

### <a name="application-package-download-failure"></a>Alkalmazás-csomag letöltési hiba

Egy vagy több alkalmazáscsomagot igény szerint a megadott csomag fájljai, minden egyes csomópont alatt letöltve egy készlet megadott és a csomópont elindult, de feladatok ütemezése előtt tömörítetlen.  Az alkalmazáscsomagok együtt egy indítási feladat parancssor segítségével más helyre másolja a fájlokat, vagy futtassa a telepítőt, például szokás.

Töltse le, és a egy alkalmazáscsomagot kibontása sikertelen. a csomópont lesz jelentve [hibák](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság.  A csomópont állapota lesz beállítva "használhatatlan".

### <a name="node-in-unusable-state"></a>Csomópont használhatatlan állapotban

A [csomópont állapota](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) állíthat be, használhatatlan számos oka lehet.  "Használhatatlan" feladatot nem lehet ütemezni a csomópontra, de a csomópont továbbra is díjat számolunk.

A Batch minden esetben megpróbálja helyreállítani használhatatlan csomópontok, de helyreállítási is, vagy nem lehetséges, okától függően.

Az OK lehet meghatározni, ha azt a csomópont kerülnek [hibák](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) tulajdonság.

Néhány példa szemlélteti a "használhatatlan" csomópontok okok:

- Érvénytelen egyéni rendszerkép; nem lett előkészítve megfelelően, például.
- Infrastrukturális hiba vagy alacsony szintű frissítés és a mögöttes virtuális gép áthelyezését; A Batch a csomóponton állítja helyre.

### <a name="node-agent-log-files"></a>Csomópont-ügynök naplófájljainak

Ha egy készlet problémája kapcsolatban az ügyfélszolgálattól, majd minden egyes készlethez csomóponton futó ügynök kötegfolyamat naplófájlokat lehet beszerezni.  A naplófájlokban található a csomópont csak akkor tölthetők fel az Azure Portalon, a Batch Explorer, vagy egy [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Fel- és naplófájlok mentése rendkívül hasznosak, mint a csomópont vagy a futó csomópontok költsége a készlet lehet törölni.

## <a name="next-steps"></a>További lépések

Győződjön meg arról, az alkalmazás van megvalósítva, átfogó hibaellenőrzés, különösen az aszinkron műveletek, így a problémák gyorsan észlelt, és felderítette.
