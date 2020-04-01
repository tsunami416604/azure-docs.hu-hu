---
title: Gyakori kérdések az Apache Kafka szolgáltatásról az Azure HDInsightban
description: Válaszok az Apache Kafkával kapcsolatos gyakori kérdésekre az Azure HDInsight ban, amely egy felügyelt Hadoop felhőszolgáltatás.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436922"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Gyakori kérdések az Azure HDInsight-beli Apache Kafkáról

Ez a cikk az Apache Kafka Azure HDInsight-on való használatával kapcsolatos gyakori kérdéseket ismerteti.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Milyen Kafka verziókat támogat a HDInsight?

További információ a HDInsight hivatalosan támogatott összetevő-verzióiról: [Melyek az Apache Hadoop-összetevők és -verziók a HDInsight segítségével?](../hdinsight-component-versioning.md#supported-hdinsight-versions) Javasoljuk, hogy mindig a legújabb verziót használja a lehető legjobb teljesítmény és felhasználói élmény biztosítása érdekében.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Milyen erőforrásokat biztosít egy HDInsight Kafka-fürt, és milyen erőforrásokért kell fizetnem?

A HDInsight Kafka-fürt a következő erőforrásokat tartalmazza:

* Fő csomópontok
* Zookeeper csomópontok
* Közvetítő (dolgozó) csomópontok 
* A közvetítői csomópontokhoz csatlakoztatott Azure felügyelt lemezek
* Átjárócsomópontok

Ezek az erőforrások díja a [HDInsight-díjszabási modell](https://azure.microsoft.com/pricing/details/hdinsight/)alapján kerül felszámolásra, kivéve az átjárócsomópontokat. Az átjárócsomópontokért nem kell fizetnie.

A különböző csomóponttípusok részletesebb leírását az [Azure HDInsight virtuális hálózati architektúrában található.](../hdinsight-virtual-network-architecture.md) A díjszabás a csomópont percenkénti használatán alapul. Az árak a csomópont méretétől, a csomópontok számától, a használt felügyelt lemez típusától és a régiótól függően változnak.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Az Apache Kafka API-k együttműködnek a HDInsightdal?

Igen, a HDInsight natív Kafka API-kat használ. Az ügyfélalkalmazás kódját nem kell módosítani. Tekintse meg [az oktatóanyagot: Az Apache Kafka produceri és fogyasztói API-k segítségével megtudhatja,](./apache-kafka-producer-consumer-api.md) hogyan használhatja a Java-alapú gyártói/fogyasztói API-kat a fürtjével.

## <a name="can-i-change-cluster-configurations"></a>Módosíthatom a fürtkonfigurációkat?

Igen, az Ambari portálon keresztül. A portál minden egyes összetevője rendelkezik egy **konfigurációs szakasz,** amely az összetevők konfigurációjának módosítására használható. Egyes módosítások szükségessé tehetik a közvetítő újraindítását.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Milyen típusú hitelesítést támogat a HDInsight az Apache Kafka számára?

Az [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)használatával témakörszintű biztonságot kaphat a Kafka-fürtökhöz. [További információ: Az Apache Kafka-házirendek konfigurálása a HDInsight ban vállalati biztonsági csomaggal (előzetes verzió)](../domain-joined/apache-domain-joined-run-kafka.md)című témakörben talál további információt.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Titkosítva vannak az adataim? Használhatom a saját kulcsaimat?

A kezelt lemezeken lévő összes Kafka-üzenet az [Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md)titkosításával van titkosítva. A tranzitadatok (például az ügyfelektől a brókereknek továbbított adatok, és fordítva) alapértelmezés szerint nem titkosított. Lehetőség van az ilyen forgalom titkosítására a [TLS beállításával.](./apache-kafka-ssl-encryption-authentication.md) Emellett a HDInsight lehetővé teszi, hogy saját kulcsokat kezeljen az inaktív adatok titkosításához. További információt [az Ügyfél által felügyelt kulcslemezek titkosítása](../disk-encryption.md)című témakörben talál.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hogyan csatlakoztathatom az ügyfeleket a fürthöz?

A Kafka ügyfelek kommunikálni Kafka brókerek, képesnek kell lennie arra, hogy elérje a brókerek a hálózaton keresztül. HDInsight-fürtök esetén a virtuális hálózat (VNet) a biztonsági határ. Ezért a legegyszerűbb módja az ügyfelek hdinsight-fürthöz való csatlakoztatásának, ha a fürttel azonos virtuális hálózaton belül hoz létre ügyfeleket. Egyéb forgatókönyvek a következők:

* Ügyfelek csatlakoztatása egy másik Azure-virtuális hálózatban – Társviszonyba a fürt virtuális hálózat és az ügyfél virtuális hálózat, és konfigurálja a fürt [ET IP-reklám.](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising) Az IP-hirdetések használatakor a Kafka ügyfeleknek bróker IP-címeket kell használniuk a brókerekhez való csatlakozáshoz a teljesen minősített tartománynevek (FQDNs) helyett.

* Helyszíni ügyfelek csatlakoztatása – VPN-hálózat használata és egyéni DNS-kiszolgálók beállítása az [Azure HDInsight virtuális hálózatának megtervezése](../hdinsight-plan-virtual-network-deployment.md)című részben leírtak szerint.

* Nyilvános végpont létrehozása a Kafka szolgáltatáshoz – Ha a vállalati biztonsági követelmények ezt lehetővé teszik, üzembe helyezhet egy nyilvános végpontot a Kafka-brókerek számára, vagy egy nyilvános végpontot egy saját felügyelt nyílt forráskódú REST-végponthoz.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Hozzáadhatok több lemezterületet egy meglévő fürtön?

A Kafka-üzenetek számára rendelkezésre álló terület növeléséhez növelheti a csomópontok számát. Jelenleg nem támogatott további lemezek hozzáadása egy meglévő fürthöz.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Működhet egy Kafka-fürt databricks?Can a Kafka cluster work with Databricks? 

Igen, a Kafka-fürtök mindaddig dolgozhatnak databricks- tel, amíg ugyanabban a virtuális hálózatban vannak. Kafka-fürt databricks használatával hozzon létre egy virtuális hálózat egy HDInsight Kafka-fürt benne, majd adja meg, hogy a virtuális hálózat, amikor létrehozza a Databricks munkaterületet, és vnet-injektálás használata. További információ: [Azure Databricks telepítése az Azure virtuális hálózatban (VNet injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). A Databricks-munkaterület létrehozásakor meg kell adnia a Kafka-fürt bootstrap broker neveit. A Kafka brókernevek beolvasásáról az [Apache Zookeeper és broker gazdagép adatainak beolvasása című témakörben talál további információt.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)

## <a name="how-can-i-have-maximum-data-durability"></a>Hogyan érhetem el a maximális adattartósságot?

Az adatok tartóssága lehetővé teszi az üzenetvesztés legkisebb kockázatának elérését. Az adatok maximális tartósságának elérése érdekében a következő beállításokat javasoljuk:

* a legtöbb régióban 3-as minimális replikációs tényezőt alkalmaznak
* legalább 4 replikációs tényezőt használ két tartalék tartománnyal rendelkező régiókban
* letiltja tisztátalan vezető választások
* állítsa **a min.insync.replicas-t** 2 vagy többre - ez megváltoztatja a replikák számát, amelyeknek teljesen szinkronban kell lenniük a vezetővel, mielőtt az írás folytatódhat
* az **acks** tulajdonság beállítása **az összesre** - ez a tulajdonság megköveteli az összes kópiát az összes üzenet nyugtázásához

A Kafka konfigurálása a nagyobb adatkonzisztencia hatással van a brókerek a kérelmek teljesítéséhez.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Replikálhatom az adataimat több fürtre?

Igen, az adatok több fürtre replikálhatók a Kafka MirrorMaker használatával. Lásd a mirrormaker beállításának részleteit a [Mirror Apache Kafka témakörökben](apache-kafka-mirroring.md)találja. Emellett vannak más, saját maga által felügyelt nyílt forráskódú technológiák és gyártók, amelyek segíthetnek a több fürtre, például a Brooklin-ra való replikáció [elérésében.](https://github.com/linkedin/Brooklin/)

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Frissíthetem a fürtömet? Hogyan frissíthetem a fürtömet?

Jelenleg nem támogatjuk a fürt verziófrissítéseit. A fürt magasabb Kafka-verzióra való frissítéséhez hozzon létre egy új fürtöt a kívánt verzióval, és telepítse át a Kafka-ügyfeleket az új fürt használatára.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Hogyan figyelhetem a Kafka-fürtömet?

Az Azure-figyelő vel elemezheti [a Kafka-naplókat.](./apache-kafka-log-analytics-operations-management.md)

## <a name="next-steps"></a>További lépések

* [TLS-titkosítás és -hitelesítés beállítása az Apache Kafkához az Azure HDInsightban](./apache-kafka-ssl-encryption-authentication.md)
* [A MirrorMaker használata Apache Kafka-témakörök replikálására a Kafka on HDInsight esetében](./apache-kafka-mirroring.md)
