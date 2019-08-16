---
title: Gyakori kérdések az Azure HDInsight Apache Kafka
description: Választ kaphat az Azure HDInsight, egy felügyelt Hadoop Cloud Service-ről szóló Apache Kafka kapcsolatos gyakori kérdésekre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 057c77d4ddb4a760e196c0dc8d508efe15e6699d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520126"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Gyakori kérdések az Azure HDInsight Apache Kafka

Ez a cikk néhány gyakori kérdést tárgyal az Azure HDInsight Apache Kafka használatáról.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>A HDInsight által támogatott Kafka-verziók?

További információk a HDInsight által támogatott összetevőkről: a [HDInsight-összetevők és-verziók elérhetők a Apache Hadoop](../hdinsight-component-versioning.md#supported-hdinsight-versions)-ben?. Javasoljuk, hogy mindig a legújabb verziót használja a lehető legjobb teljesítmény és felhasználói élmény biztosítása érdekében.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Milyen erőforrásokat biztosítanak egy HDInsight Kafka-fürtben, és milyen erőforrásokat kell fizetnem?

A HDInsight Kafka-fürt a következő erőforrásokat tartalmazza:

* Átjárócsomópontok
* Zookeeper-csomópontok
* Közvetítő (Worker) csomópontok 
* A Broker-csomópontokhoz csatolt Azure-Managed Disks
* Az átjárócsomópontok

Az összes ilyen erőforrást a [HDInsight díjszabási modellje](https://azure.microsoft.com/pricing/details/hdinsight/)alapján számítjuk fel, az átjáró-csomópontok kivételével. Az átjáró-csomópontok esetében nem számítunk fel díjat.

A különböző csomópont-típusok részletesebb ismertetését az [Azure HDInsight virtuális hálózati architektúrája](../hdinsight-virtual-network-architecture.md)című témakörben tekintheti meg. A díjszabás percenkénti csomópont-használat alapján történik. Az árak a csomópont méretétől, a csomópontok számától, a felhasznált felügyelt lemez típusától és a régiótól függően változnak.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Működik Apache Kafka API-k az HDInsight-mel?

Igen, a HDInsight a natív Kafka API-kat használja. Az ügyfélalkalmazás kódját nem kell módosítania. Lásd [az oktatóanyagot: A Apache Kafka producer és fogyasztói API](./apache-kafka-producer-consumer-api.md) -k segítségével megtudhatja, hogyan használhatja a Java-alapú producer/fogyasztói API-kat a fürthöz.

## <a name="can-i-change-cluster-configurations"></a>Módosíthatom a fürtkonfiguráció beállításait?

Igen, a Ambari-portálon keresztül. A portál minden összetevője tartalmaz egy **konfigurációk** szakaszt, amely az összetevők konfigurációjának módosítására használható. Előfordulhat, hogy egyes módosítások a közvetítő újraindítását igénylik.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Milyen típusú hitelesítést támogat a Apache Kafka HDInsight?

A [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)használatával megtekintheti a Kafka-fürtök témakör szintű biztonságát. Lásd [az oktatóanyagot: A HDInsight Apache Kafka szabályzatait Enterprise Security Package (előzetes verzió)](../domain-joined/apache-domain-joined-run-kafka.md)beállítással konfigurálhatja további információért.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Titkosítva vannak az adataim? Használhatom a saját kulcsokat?

A felügyelt lemezeken található összes Kafka-üzenet titkosítva van az [Azure Storage Service encryption (SSE)](../../storage/common/storage-service-encryption.md)szolgáltatással. Az adatok átvitele (például az ügyfelektől a közvetítők felé továbbított adatok és a másik módszer) alapértelmezés szerint nincs titkosítva. Az ilyen adatforgalom titkosítása az [SSL beállításával](./apache-kafka-ssl-encryption-authentication.md)lehetséges. Emellett a HDInsight lehetővé teszi a saját kulcsaik kezelését a REST-adatok titkosításához. További információért lásd: [saját kulcs használata az Azure HDInsight Apache Kafka](apache-kafka-byok.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hogyan csatlakozni az ügyfelekhez a fürthöz?

Ahhoz, hogy Kafka-ügyfelek kommunikáljanak a Kafka-közvetítőkkal, el kell tudniuk érni a közvetítőket a hálózaton keresztül. A HDInsight-fürtök esetében a Virtual Network (VNet) a biztonsági határ. Ezért az ügyfelek a HDInsight-fürthöz való összekapcsolásának legegyszerűbb módja, ha az ügyfeleket a fürttel azonos VNet belül hozza létre. A további forgatókönyvek a következők:

* Ügyfelek csatlakoztatása egy másik Azure-VNet – a fürt VNet és az ügyfél VNet és a fürt konfigurálása az [IP-hirdetésekhez](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Ha IP-hirdetést használ, a Kafka-ügyfeleknek a közvetítői IP-címeit kell használniuk a brókerekkel való csatlakozáshoz, a teljes tartománynevek (FQDN) helyett.

* Helyszíni ügyfelek csatlakoztatása – VPN-hálózat és egyéni DNS-kiszolgálók beállítása az [Azure HDInsight virtuális hálózatának](../hdinsight-plan-virtual-network-deployment.md)megtervezése című témakörben leírtak szerint.

* Nyilvános végpont létrehozása a Kafka szolgáltatás számára – ha a vállalati biztonsági követelmények lehetővé teszik, üzembe helyezhet egy nyilvános végpontot a Kafka-közvetítők számára, vagy egy saját felügyeletű, nyílt forráskódú REST-végpontot nyilvános végponttal.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Hozzáadhatok több lemezterületet egy meglévő fürthöz?

A Kafka-üzenetek számára elérhető szabad terület növeléséhez növelheti a csomópontok számát. Jelenleg nem támogatott több lemez hozzáadása egy meglévő fürthöz.

## <a name="how-can-i-have-maximum-data-durability"></a>Hogyan használhatom a maximális adattartósságot?

Az adattartósság lehetővé teszi az üzenet elvesztésének legalacsonyabb kockázatát. A maximális adattartósság érdekében a következő beállításokat javasoljuk:

* a legtöbb régióban 3 minimális replikációs tényezőt használjon
* legalább 4-es replikációs tényező használata csak két tartalék tartománnyal rendelkező régiókban
* a nem tiszta Leader-választások letiltása
* Állítsa be a **min. inSync. replikákat** 2 vagy több értékre – ez módosítja azon replikák számát, amelyeknek teljesen szinkronban kell lenniük a vezetővel, mielőtt az írás folytatódhat.
* a **nyugták** tulajdonság beállítása az **összes** értékre – ez a tulajdonság megköveteli, hogy minden replika visszaigazolja az összes üzenetet.

A Kafka nagyobb adatkonzisztencia beállítása befolyásolja a közvetítők rendelkezésre állását a kérelmek előállításához.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Több fürtre is replikálható az adataim?

Igen, az adatreplikáció több fürtre is lehetséges a Kafka MirrorMaker használatával. A MirrorMaker beállításával kapcsolatos részletek a [Mirror Apache Kafka témakörökben](apache-kafka-mirroring.md)találhatók. Emellett vannak olyan, önállóan felügyelt nyílt forráskódú technológiák és szállítók is, amelyek segítségével több fürt, például a [Brooklin](https://github.com/linkedin/Brooklin/)replikációja is megvalósítható.

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Frissíthetem a fürtöt? Hogyan frissíthetem a fürtöt?

Jelenleg nem támogatott a helyi fürt verziófrissítésének frissítése. Ha egy magasabb szintű Kafka-verzióra szeretné frissíteni a fürtöt, hozzon létre egy új fürtöt a kívánt verzióval, és telepítse át a Kafka-ügyfeleket az új fürt használatára.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Hogyan figyeli a Kafka-fürtöt?

Az Azure monitor használatával elemezheti a [Kafka](./apache-kafka-log-analytics-operations-management.md)-naplókat.

## <a name="next-steps"></a>További lépések

* [Az SSL (SSL) titkosítás és a hitelesítés beállítása az Azure HDInsight Apache Kafkahoz](./apache-kafka-ssl-encryption-authentication.md)
* [A MirrorMaker használata Apache Kafka-témakörök replikálására a Kafka on HDInsight esetében](./apache-kafka-mirroring.md)
