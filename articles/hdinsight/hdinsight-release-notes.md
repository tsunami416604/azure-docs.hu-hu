---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d4983ecd1b8afe1ec6bd3cc31df8b711cebaddf1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350262"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összegzés

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

Ha a kibocsátási megjegyzésekre szeretne előfizetni, tekintse meg a [jelen GitHub-adattár](https://github.com/hdinsight/release-notes/releases)kiadásait.

## <a name="release-date-11182020"></a>Kiadás dátuma: 11/18/2020

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Ügyfél által felügyelt kulcs titkosításának automatikus kulcsának elforgatása nyugalmi állapotban
Ettől a kiadástól kezdődően az ügyfelek az Azure KeyValut-verzió-kevesebb titkosítási kulcs URL-címét használhatják az ügyfelek által felügyelt kulcs titkosítására nyugalmi állapotban. A HDInsight automatikusan elforgatja a kulcsokat, és lecseréli azokat új verzióra. További információt [itt talál](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>A Spark, a Hadoop és a ML szolgáltatások Zookeeper eltérő virtuálisgép-méretek kiválasztásának lehetősége
A HDInsight korábban nem támogatta a Zookeeper-csomópontok méretének testreszabását a Spark, a Hadoop és a ML Services típusú fürtök esetében. Alapértelmezés szerint A2_v2/a2 virtuálisgép-méreteket tartalmaz, amelyek díjmentesen állnak rendelkezésére. Ebből a kiadásból kiválaszthatja a forgatókönyvhöz leginkább megfelelő Zookeeper virtuális gépek méretét. A A2_v2/a2 eltérő virtuálisgép-mérettel rendelkező Zookeeper-csomópontokat a rendszer felszámítja. A A2_v2 és az A2-es virtuális gépek továbbra is díjmentesen elérhetők.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. Ettől a kiadástól kezdve a szolgáltatás fokozatosan migrálva lesz az [Azure virtuálisgép-méretezési csoportokra](../virtual-machine-scale-sets/overview.md). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>A HDInsight 3,6 ML Services-fürt elavult változata
A HDInsight 3,6 ML Services-fürt típusa a támogatás végére kerül a 31 2020 decemberében. Az ügyfelek a 31 2020 decembere után nem hoznak létre új 3,6 ML-es szolgáltatási fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Tekintse meg a HDInsight-verziók és-fürtök támogatásának lejárati idejét [itt](./hdinsight-component-versioning.md#available-versions).

### <a name="disabled-vm-sizes"></a>Letiltott virtuálisgép-méretek
A HDInsight november 16 2020-től kezdődően megakadályozza, hogy az új ügyfelek standand_A8, standand_A9, standand_A10 és standand_A11 virtuálisgép-méretekkel hozzanak létre fürtöket. Az elmúlt három hónapban ezeket a virtuálisgép-méreteket használó meglévő ügyfeleket nem érinti a rendszer. A HDInsight január 9 2021-től kezdődően a standand_A8, standand_A9, standand_A10 és standand_A11 virtuálisgép-méretek használatával letiltja a fürtöket létrehozó összes ügyfelet. A meglévő fürtök futtatása a következőképpen történik:. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

### <a name="behavior-changes"></a>Viselkedési változások
Ebben a kiadásban nem változik a viselkedés.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A fürt alapértelmezett verziója 4,0-re változik
Február 2021-én a HDInsight-fürt alapértelmezett verziója 3,6-ről 4,0-ra módosul. További információ az elérhető verziókról: [elérhető verziók](./hdinsight-component-versioning.md#available-versions). További információ a [HDInsight 4,0](./hdinsight-version-release.md) újdonságáról

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6, 30 2021. június vége
A HDInsight 3,6 támogatás megszűnik. A 30 2021. június megkezdése után az ügyfelek nem hozhatnak létre új HDInsight 3,6-fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](./hdinsight-component-versioning.md)találja.