---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c14e5537385b7518fd08d9d3599993bc6d82f88
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535501"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-10082020"></a>Kiadás dátuma: 10/08/2020

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Nyilvános IP-címet és privát hivatkozást nem tartalmazó privát fürtök HDInsight (előzetes verzió)
A HDInsight mostantól támogatja a nyilvános IP-címek nélküli fürtök létrehozását, valamint a fürtök előzetes verzióban való elérését. Az új speciális hálózatkezelési beállítások használatával egy teljesen elkülönített, nyilvános IP-címmel rendelkező fürtöt hozhat létre, és a saját privát végpontok használatával érheti el a fürtöt. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. Ettől a kiadástól kezdve a szolgáltatás fokozatosan migrálva lesz az [Azure virtuálisgép-méretezési csoportokra](../virtual-machine-scale-sets/overview.md). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>A HDInsight 3,6 ML Services-fürt elavult változata
A HDInsight 3,6 ML Services-fürt típusa a DEC 31 2020-es támogatás végén lesz. Az ügyfelek ezt követően nem hoznak létre új 3,6 ML-es szolgáltatási fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Tekintse meg a HDInsight-verziók és-fürtök támogatásának lejárati idejét [itt](./hdinsight-component-versioning.md#available-versions).

## <a name="behavior-changes"></a>Viselkedési változások
Ebben a kiadásban nem változik a viselkedés.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>A Spark, a Hadoop és a ML szolgáltatások Zookeeper eltérő virtuálisgép-méretek kiválasztásának lehetősége
A HDInsight jelenleg nem támogatja a Zookeeper-csomópontok méretének testreszabását a Spark, a Hadoop és a ML Services típusú fürtök esetében. Alapértelmezés szerint A2_v2/a2 virtuálisgép-méreteket tartalmaz, amelyek díjmentesen állnak rendelkezésére. A következő kiadásban kiválaszthatja a forgatókönyvhöz leginkább megfelelő Zookeeper virtuális gép méretét. A A2_v2/a2 eltérő virtuálisgép-mérettel rendelkező Zookeeper-csomópontokat a rendszer felszámítja. A A2_v2 és az A2-es virtuális gépek továbbra is díjmentesen elérhetők.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](./hdinsight-component-versioning.md)találja.
