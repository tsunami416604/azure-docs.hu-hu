---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564405"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-06112020"></a>Kiadás dátuma: 06/11/2020

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight az Azure Virtual Machines használatával kiépíti a fürtöt. Ebből a kiadásból az újonnan létrehozott HDInsight-fürtök az Azure virtuálisgép-méretezési csoport használatával kezdődnek. A változást fokozatosan kivezetjük. Nem kell megszakítani a változást. További információ az [Azure-beli virtuálisgép-méretezési csoportokról](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Virtuális gépek újraindítása a HDInsight-fürtben
Ebben a kiadásban támogatjuk a virtuális gépek újraindítását a HDInsight-fürtben a nem válaszoló csomópontok újraindításához. Jelenleg csak API-n keresztül végezheti el, a PowerShell és a parancssori felület támogatása is. Az API-val kapcsolatos további információkért tekintse meg [ezt a dokumentációt](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Elavulás
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>A Spark 2.1 és 2.2 elavulása a HDInsight 3.6 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,1 és 2,2 HDInsight 3,6-ben. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye figyelembe, hogy a Spark 2,3-ra való áttérés a HDInsight 30 2020 3,6-re, a lehetséges rendszer-és támogatási megszakítás elkerülése érdekében.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>A Spark 2.3 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2,3-mel a HDInsight 4,0-on. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Spark 2.4-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>A Kafka 1.1 elavulása a HDInsight 4.0 Kafka-fürtjein
Július 1 2020-től kezdődően az ügyfelek nem tudnak új Kafka-fürtöket létrehozni a HDInsight 4,0-on futó Kafka 1,1-mel. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Kafka 2.1-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.
 
## <a name="behavior-changes"></a>Viselkedési változások
### <a name="esp-spark-cluster-head-node-size-change"></a>ESP Spark-fürt fő csomópontjának méretének változása 
Az ESP Spark-fürt minimálisan engedélyezett fő csomópontjának mérete Standard_D13_V2ra módosul. Az alacsony maggal és memóriával rendelkező virtuális gépek a főcsomópontok esetében a viszonylag alacsony CPU és a memória kapacitása miatt ESP-fürtöket okozhatnak. A kiadástól kezdődően a Standard_D13_V2nál nagyobb SKU-t használjon, és az ESP Spark-fürtökhöz tartozó főcsomópontként Standard_E16_V3.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>A fő csomóponthoz legalább 4 magos virtuális gépnek kell tartoznia 
A HDInsight-fürtök magas rendelkezésre állásának és megbízhatóságának biztosítása érdekében legalább 4 magos virtuális gépnek kell lennie a fő csomópont számára. Április 6 2020-től kezdődően az ügyfelek csak 4 magos vagy annál újabb virtuális gépet választhatnak az új HDInsight-fürtök fő csomópontjának kiválasztásával. A meglévő fürtök továbbra is a várt módon fognak futni. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Fürt munkavégző csomópontjának kiépítési változása
Ha a feldolgozó csomópontok 80%-a elkészült, a fürt **működési** fázisba lép. Ezen a ponton az ügyfelek az összes olyan adatsík-műveletet elvégezhetik, mint a parancsfájlok és feladatok futtatása. Az ügyfelek azonban nem végezhetnek olyan vezérlési sík műveletet, mint a fel-vagy leskálázás. Csak a törlés támogatott.
 
A **működési** fázis után a fürt egy másik 60 percet vár a fennmaradó 20%-os munkavégző csomópontra. Ennek a 60 percnek a végén a fürt akkor is átkerül a **futó** szakaszba, ha az összes feldolgozó csomópont még nem érhető el. Ha egy fürt belép a **futó** szakaszba, azt a megszokott módon használhatja. Mind a vezérlési terv, mint például a fel-és leskálázási műveletek, valamint az adattervezési műveletek, például a parancsfájlok és a feladatok futtatása is elfogadható. Ha a kért munkavégző csomópontok némelyike nem érhető el, a fürt részleges sikerként lesz megjelölve. A sikeresen üzembe helyezett csomópontok díjat számítunk fel. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Új egyszerű szolgáltatásnév létrehozása a HDInsight-en keresztül
Korábban a fürt létrehozásával az ügyfelek létrehozhatnak egy új egyszerű szolgáltatást, amely hozzáfér a csatlakoztatott ADLS Gen 1 fiókhoz Azure Portal. A 15 2020. júniustól kezdődően az ügyfelek nem hozhatnak létre új szolgáltatásnevet a HDInsight-létrehozási munkafolyamatban, csak a meglévő egyszerű szolgáltatásnév támogatott. Lásd: [egyszerű szolgáltatásnév és tanúsítványok létrehozása Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Időtúllépés a fürt létrehozásával végzett parancsfájl-műveletekhez
A HDInsight támogatja a fürt létrehozásával végzett parancsfájlok futtatását. Ettől a kiadástól kezdve a fürt létrehozásához szükséges összes parancsfájl **60 percen**belül befejeződik, vagy időtúllépés történik. A futó fürtökhöz küldött parancsfájl-műveletek nincsenek hatással a rendszerre. További információt [itt talál](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Közelgő változások
Nincs szükség a közelgő változtatásokra, amelyekre figyelnie kell.
 
## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 
 
## <a name="component-version-change"></a>Összetevő verziójának módosítása
### <a name="hbase-20-to-216"></a>HBase 2,0 – 2.1.6
A HBase verziója a 2,0-es verzióról 2.1.6-re lett frissítve.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 – 2.4.4
A Spark verziója a 2.4.0 verzióról a 2.4.4-re lett frissítve.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 – 2.1.1
A Kafka verziója a 2.1.0 verzióról 2.1.1-re lett frissítve.
 
A HDInsight 4,0 ad HDInsight 3,6 jelenlegi összetevő-verzióit [ebben a dokumentumban](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) találja

## <a name="known-issues"></a>Ismert problémák

### <a name="hive-warehouse-connector-issue"></a>A méhkas Warehouse-összekötő hibája
Ebben a kiadásban probléma van a méhkas Warehouse-összekötővel kapcsolatban. A javítás a következő kiadásban fog szerepelni. A jelen kiadás előtt létrehozott meglévő fürtöket nem érinti a rendszer. Ha lehetséges, ne dobja el és ne hozza létre újra a fürtöt. Ha további segítségre van szüksége, nyissa meg a támogatási jegyet.
