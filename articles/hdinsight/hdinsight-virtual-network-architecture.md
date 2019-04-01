---
title: Az Azure HDInsight virtuális hálózat architektúrája
description: Ismerje meg a rendelkezésre álló erőforrások egy Azure virtuális hálózaton egy HDInsight-fürt létrehozásakor.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 13ea4acb5a3b5b3e7ac6d53013193fddbfcbf9b7
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759476"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Az Azure HDInsight virtuális hálózat architektúrája

Ez a cikk ismerteti az erőforrást, amely jelenik meg, ha telepít egy HDInsight-fürtöt egy egyéni Azure virtuális hálózatban. Ez az információ segít a helyszíni erőforrásokhoz csatlakozhat a HDInsight-fürt az Azure-ban. Az Azure Virtual Network további információkért lásd: [Mi az Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök erőforrástípusok

Az Azure HDInsight-fürtök különböző típusú virtuális gépeket, vagy a csomópontok rendelkeznek. Mindegyik csomóponttípus szerepet játszik a rendszer a műveletet. Az alábbi táblázat foglalja össze, ezeket csomóponttípusok és a fürt szerepkörökhöz.

| Typo | Leírás |
| --- | --- |
| Átjárócsomópont |  Az Apache Storm kivételével minden fürt esetében az átjárócsomópontok a folyamatok, amelyek az elosztott alkalmazás végrehajtását a kezelése. A fő csomópontot az is a csomópont, az ssh-n, és hajtsa végre az alkalmazásokat, amelyek majd koordinálja a fürterőforrások közötti futtatása. Fő csomópontok száma az összes fürt esetében két meghatározott. |
| ZooKeeper-csomópont | Zookeeper koordinálja feladatokat végeznek adatokat feldolgozó csomópontok között. Azt is nem vezetőválasztási a fő csomópont, és nyomon követi, hogy melyik fő csomópontot futtat egy adott fő szolgáltatást. Két meghatározott ZooKeeper-csomópontok száma. |
| Munkavégző csomópont | A csomópontok adatfeldolgozási funkciókat támogató jelöli. Munkavégző csomópontok hozzáadására vagy a fürt méretezése a számítástechnikai képességek és a költségek kezelése távolítva. |
| Az R Server élcsomóponthoz | Az R Server élcsomóponthoz jelöli az ssh-n be csomópontot, és hajtsa végre az alkalmazásokat, amelyek majd koordinálja a fürt erőforrásai között futtatásához. Élcsomópont adatok elemzése a fürtön belül nem részt. Ebben a csomópontban is futtatja az R Studio Server, lehetővé téve a böngésző használata az R-alkalmazás futtatásához. |
| Régió csomópont | A HBase-fürt típusa a régió csomópont (más néven egy adatcsomópont) futtatható: a régió kiszolgáló. Régiókiszolgálók szolgál, és kezelheti a HBase által kezelt adatok egy részét. Régió csomópontok hozzáadására vagy eltávolítja a fürt méretezése a számítástechnikai képességek és a költségek kezelése.|
| Nimbus-csomópont | A Storm-fürt típusa arról a Nimbus csomópont az átjárócsomópont hasonló funkcionalitást biztosít. A Nimbus csomópont rendeli hozzá a feladatokat Zookeeper, a futó Storm-topológiák alkalmazástelepítéseket keresztül a fürt más csomópontjaira. |
| Felügyelő csomópont | A Storm-fürt típusa, a a felügyeleti csomópont végrehajtja a kívánt feldolgozást végez a Nimbus csomópont utasításait. |

* = Apache Hadoop, Apache Hive, az Apache Kafka, Apache Spark, az Apache HBase és az R Server fürttípusok

## <a name="basic-virtual-network-resources"></a>Alapszintű virtuális hálózati erőforrások

Az alábbi ábrán látható a HDInsight-csomópontok és a hálózati erőforrások helyét, az Azure-ban.

![Az Azure egyéni virtuális hálózatban létrehozott HDInsight entitásokat ábrája](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Az alapértelmezett erőforrásokat jelenik meg, ha a HDInsight helyezünk üzembe az Azure Virtual Network az említett az előző táblázatban, valamint a hálózati eszközök, amelyek támogatják a kommunikációt a virtuális hálózat között, és hálózatokon kívül a fürtcsomópont-típusokat tartalmazza.

A következő táblázat összefoglalja a kilenc fürtcsomópontok jön létre, amikor a HDInsight egy egyéni Azure virtuális hálózatban van üzembe helyezve.

| Erőforrás típusa | Jelenlegi száma | Részletek |
| --- | --- | --- |
|Átjárócsomópont | kettő |    |
|Zookeeper-csomópont | három | |
|Munkavégző csomópont | kettő | Ez a szám fürtkonfiguráció és a méretezés függően változhat. Az Apache Kafka legalább három feldolgozó csomópontot van szükség.  |
|Átjárócsomópont | kettő | Az átjárócsomópontok olyan Azure virtuális gépek jönnek létre az Azure-ban, de nem láthatók az előfizetésében. Ha újra kell indítania az ezeken a csomópontokon, forduljon az ügyfélszolgálathoz. |

A HDInsight együttes virtuális hálózaton belül automatikusan jönnek létre a következő hálózati erőforrásokat:

| Hálózati erőforrás | Jelenlegi száma | Részletek |
| --- | --- | --- |
|Terheléselosztó | három | |
|Hálózati illesztők | kilenc | Ez az érték normál fürt, ahol minden egyes csomópontja rendelkezik-e a saját hálózati adapter alapul. A következő kilenc felületek a két fő csomópont, három zookeeper-csomópontok, két feldolgozócsomópontot és az előző táblázatban említett két átjárócsomópontok vonatkoznak. |
|Nyilvános IP-címek | kettő |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Csatlakozhat a HDInsight végpontok

A HDInsight-fürt három módon érheti el:

- HTTPS-végpontokat, a virtuális hálózaton kívüli `CLUSTERNAME.azurehdinsight.net`.
- Egy SSH-végpontjának való közvetlen csatlakozás az átjárócsomóponthoz: `CLUSTERNAME-ssh.azurehdinsight.net`.
- HTTPS-végpont, a virtuális hálózaton belül `CLUSTERNAME-int.azurehdinsight.net`. Figyelje meg a "– int" ezt az URL-címben. Ezt a végpontot rendszer feloldja a kiválasztott virtuális hálózatban egy magánhálózati IP-címet, és nem a nyilvános internetről érhető el.

Ezeket a végpontokat rendelt egyes egy terheléselosztó.

Nyilvános IP-címek is biztosítják a végpontokat, amelyek lehetővé teszik a kapcsolatot a virtuális hálózaton kívülről.

1. Egy nyilvános IP-cím van rendelve a terheléselosztóhoz a teljesen minősített tartománynév (FQDN) használja, ha az internetről a fürthöz való csatlakozás `CLUSTERNAME.azurehdinsight.net`.
1. Második nyilvános IP-címként szolgál az SSH csak tartománynév `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök privát végponttal rendelkező virtuális hálózat bejövő adatforgalom biztosítása](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
