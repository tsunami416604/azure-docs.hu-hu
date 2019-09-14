---
title: Azure HDInsight virtuális hálózati architektúra
description: Ismerje meg a HDInsight-fürt Azure-Virtual Network való létrehozásakor elérhető erőforrásokat.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 340974201d62f97669db442f4a95439a6ac90a5e
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960625"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight virtuális hálózati architektúra

Ez a cikk ismerteti azokat az erőforrásokat, amelyek akkor jelennek meg, amikor egy HDInsight-fürtöt telepít egy egyéni Azure-Virtual Networkba. Ezek az információk segítséget nyújtanak a helyszíni erőforrások a HDInsight-fürthöz való összekapcsolásában az Azure-ban. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért lásd: [Mi az az azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Erőforrástípusok az Azure HDInsight-fürtökben

Az Azure HDInsight-fürtök különböző típusú virtuális gépekkel vagy csomópontokkal rendelkeznek. Minden csomópont-típus a rendszer működésében játszik szerepet. A következő táblázat összefoglalja ezeket a csomópont-típusokat és azok szerepköreit a fürtben.

| Type | Leírás |
| --- | --- |
| Átjárócsomópont |  Az Apache Storm kivételével az összes fürtjénél a fő csomópontok futtatják az elosztott alkalmazás végrehajtását kezelő folyamatokat. A fő csomópont egyben a csomópont is, amelyből SSH-ba helyezheti és végrehajthatja azokat az alkalmazásokat, amelyek a fürt erőforrásai között futnak. A fő csomópontok száma a fürt összes típusa esetében kettőnél van rögzítve. |
| ZooKeeper csomópont | A Zookeeper az adatfeldolgozást végző csomópontok között koordinálja a feladatokat. Emellett a fő csomópontot is vezeti, és nyomon követi, hogy melyik főcsomóponton fut egy adott főkiszolgáló. A ZooKeeper-csomópontok száma három helyen van rögzítve. |
| Munkavégző csomópont | Azokat a csomópontokat jelöli, amelyek támogatják az adatfeldolgozási funkciókat. A munkavégző csomópontok hozzáadhatók vagy eltávolíthatók a fürtből a számítási kapacitás méretezéséhez és a költségek kezeléséhez. |
| R Server peremhálózati csomópont | Az R Server Edge csomópont azt a csomópontot jelöli, amelyről SSH-ba, és végrehajthat olyan alkalmazásokat, amelyek a fürt erőforrásai között futnak. Az Edge-csomópontok nem vesznek részt a fürtön belüli adatelemzésben. Ez a csomópont R Studio Servert is üzemeltet, amely lehetővé teszi az R-alkalmazások futtatását böngésző használatával. |
| Régió csomópontja | A HBase-fürt típusához a régió csomópont (más néven adatcsomópont) futtatja a régió-kiszolgálót. A régió-kiszolgálók a HBase által felügyelt adat egy részét szolgálják és kezelik. A régió csomópontjai hozzáadhatók vagy eltávolíthatók a fürtből a számítási kapacitás méretezéséhez és a költségek kezeléséhez.|
| Nimbus csomópont | A Storm-fürt típusához a Nimbus csomópont a fő csomóponthoz hasonló funkciókat biztosít. A Nimbus-csomópont feladatokat rendel a fürt más csomópontjaihoz a Zookeeper használatával, amely a Storm-topológiák futtatását koordinálja. |
| Felügyeleti csomópont | A Storm-fürt típusához a felügyelő csomópont hajtja végre a Nimbus csomópont által megadott utasításokat a kívánt feldolgozás végrehajtásához. |

## <a name="basic-virtual-network-resources"></a>Alapszintű virtuális hálózati erőforrások

A következő ábra a HDInsight-csomópontok és hálózati erőforrások elhelyezését mutatja be az Azure-ban.

![Az Azure egyéni VNET létrehozott HDInsight-entitások ábrája](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Az alapértelmezett erőforrások akkor jelennek meg, ha a HDInsight üzembe helyezése Azure-Virtual Network történik, beleértve az előző táblázatban említett fürtcsomópontok típusait, valamint a virtuális hálózat és a külső hálózatok közötti kommunikációt támogató hálózati eszközöket is.

A következő táblázat összefoglalja azokat a kilenc fürtcsomópont-csomópontot, amelyek akkor jönnek létre, amikor a HDInsight-t egy egyéni Azure-Virtual Network telepítik.

| Erőforrás típusa | Szám jelen | Részletek |
| --- | --- | --- |
|Átjárócsomópont | kettő |    |
|Zookeeper-csomópont | három | |
|Munkavégző csomópont | kettő | Ez a szám a fürtkonfiguráció és a skálázás alapján változhat. Apache Kafkahez legalább három munkavégző csomópontra van szükség.  |
|Átjárócsomópont | kettő | Az átjáró-csomópontok olyan Azure-beli virtuális gépek, amelyek az Azure-ban jönnek létre, de nem láthatók az előfizetésében. Ha újra kell indítania ezeket a csomópontokat, forduljon az ügyfélszolgálathoz. |

A következő hálózati erőforrások automatikusan létrejönnek a HDInsight használt virtuális hálózaton belül:

| Hálózati erőforrás | Szám jelen | Részletek |
| --- | --- | --- |
|Load Balancer | három | |
|Hálózati illesztők | kilenc | Ez az érték egy normál fürtön alapul, ahol minden egyes csomópont saját hálózati adapterrel rendelkezik. A kilenc csatoló a két fő csomópontra, három Zookeeper-csomópontra, két feldolgozói csomópontra és az előző táblázatban említett két átjáró-csomópontra mutat. |
|Nyilvános IP-címek | kettő |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Végpontok a HDInsight való csatlakozáshoz

A HDInsight-fürtöt háromféle módon érheti el:

- Egy HTTPS-végpont a virtuális hálózaton kívül a `CLUSTERNAME.azurehdinsight.net`következő helyen:.
- Egy SSH-végpont, amely közvetlenül csatlakozik a átjárócsomóponthoz `CLUSTERNAME-ssh.azurehdinsight.net`a következő helyen:.
- Egy HTTPS-végpont a virtuális hálózaton `CLUSTERNAME-int.azurehdinsight.net`belül. Figyelje meg az "-int" kifejezést ebben az URL-ben. Ez a végpont a virtuális hálózatban lévő magánhálózati IP-címekre lesz feloldva, és nem érhető el a nyilvános internetről.

Ez a három végpont mindegyike hozzá van rendelve egy terheléselosztó.

A rendszer a nyilvános IP-címeket is megadja a két végpont számára, amelyek engedélyezik a kapcsolódást a virtuális hálózaton kívülről.

1. Egy nyilvános IP-cím van hozzárendelve a terheléselosztó számára a teljes tartománynév (FQDN) számára, amelyet az internetről `CLUSTERNAME.azurehdinsight.net`a fürthöz való csatlakozáskor kell használni.
1. A második nyilvános IP-cím csak az SSH-tartománynévhez `CLUSTERNAME-ssh.azurehdinsight.net`használatos.

## <a name="next-steps"></a>További lépések

* [Biztonságos bejövő forgalom HDInsight-fürtökhöz privát végponttal rendelkező virtuális hálózaton](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
