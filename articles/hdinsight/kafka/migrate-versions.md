---
title: Apache Kafka számítási feladatok áttelepítése az Azure HDInsight 4.0-ra
description: Ismerje meg, hogyan telepítheti át az Apache Kafka számítási feladatokat a HDInsight 3.6-on a HDInsight 4.0-s rendszerbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 66ea8a93723c117a364a188ec05b050e1ef60564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548084"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka számítási feladatok áttelepítése az Azure HDInsight 4.0-ra

Az Azure HDInsight 4.0 a legújabb nyílt forráskódú összetevőket kínálja, amelyek jelentős mértékben tovább növelik a teljesítményt, a kapcsolatot és a biztonságot. Ez a dokumentum bemutatja, hogyan telepítheti át az Apache Kafka számítási feladatokat a HDInsight 3.6-on a HDInsight 4.0-ra. Miután áttelepítette a számítási feladatokat a HDInsight 4.0-s verzióra, számos olyan új funkciót használhat, amelyek nem érhetők el a HDInsight 3.6-on.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka áttelepítési útvonalak

A HDInsight 3.6 a Kafka két verzióját támogatja: az 1.0.0 és az 1.1.0. A HDInsight 4.0 támogatja az 1.1.0-s és a 2.1.0-s verziót. Attól függően, hogy a Kafka melyik verzióját és a HDInsight melyik verzióját szeretné futtatni, több támogatott áttelepítési útvonal található. Ezeket a görbéket az alábbi ábrán ismertetjük és szemléltetjük.

* **Futtassa a Kafka és a HDInsight alkalmazást a legújabb verziókon (ajánlott)**: A HDInsight 3.6-os és a Kafka 1.0.0- vagy 1.1.0-s alkalmazás áttelepítése a HDInsight 4.0-ra a Kafka 2.1.0-s verziójával (a D és az E elérési utak alatt).
* **Futtassa a HDInsight alkalmazást a legújabb verzióra, de a Kafka csak egy újabb verzión:** A HDInsight 3.6 és a Kafka 1.0.0 alkalmazás áttelepítése a HDInsight 4.0-ra a Kafka 1.1.0-s verziójával (az alábbi B elérési út).
* **Futtassa a HDInsight alkalmazást a legújabb verzióra, tartsa meg a Kafka verzióját:** Migráljon egy HDInsight 3.6-os és Kafka 1.1.0-s alkalmazást a HDInsight 4.0-ra a Kafka 1.1.0-s verziójával (az alábbi C elérési út).
* **Futtassa a Kafka alkalmazást egy újabb verzión, tartsa meg a HDInsight-verziót:** Telepítse na egy Kafka 1.0.0-s alkalmazást az 1.1.0-s verziójára, és maradjon a HDInsight 3.6-on (az alábbi A elérési út). Vegye figyelembe, hogy ez a beállítás továbbra is új fürt telepítését igényli. A Kafka-verzió frissítése egy meglévő fürtön nem támogatott. Miután létrehozott egy fürtöt a kívánt verzióval, telepítse át a Kafka-ügyfeleket az új fürt használatára.

![Frissítési útvonalak apache Kafka a 3,6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka verziók

### <a name="kafka-110"></a>Kafka 1.1.0
  
Ha a Kafka 1.0.0-ról az 1.1.0-ra költözik, a következő új funkciókat használhatja ki:

* A Kafka vezérlő fejlesztései felgyorsítják a szabályozott leállítást, így újraindíthatja a brókereket, és gyorsabban helyreállíthatja a problémákat. 
* A [FetchRequests logika fejlesztései,](https://issues.apache.org/jira/browse/KAFKA-6254) amelyek lehetővé teszik, hogy több partíciót (és így több témakört) a fürtön. 
* A Kafka Connect támogatja a [rekordfejléceket](https://issues.apache.org/jira/browse/KAFKA-5142) és a [témakörök reguláris](https://issues.apache.org/jira/browse/KAFKA-3073) kifejezéseit. 

A frissítések teljes listáját az [Apache Kafka 1.1 kiadási megjegyzésekben található.](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Ha áttelepül a Kafka 2.1-re, az alábbi szolgáltatásokat használhatja ki:

* Jobb közvetítői rugalmasság a továbbfejlesztett replikációs protokoll nak köszönhetően.
* Új funkció a KafkaAdminClient API-ban.
* Konfigurálható kvótakezelés.
* Zstandard tömörítés támogatása.

A frissítések teljes listáját az [Apache Kafka 2.0 kiadási megjegyzések](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) és az Apache [Kafka 2.1 kiadási megjegyzések között található.](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)

## <a name="kafka-client-compatibility"></a>Kafka ügyfélkompatibilitás

Az új Kafka brókerek támogatják az idősebb ügyfeleket. [KIP-35 - A protokollverzió lekérése](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) egy kafka bróker és a KIP-97 funkcióinak dinamikus meghatározására szolgáló mechanizmust vezetett [be: A Továbbfejlesztett Kafka Client RPC kompatibilitási házirend](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) új kompatibilitási szabályzatot és garanciákat vezetett be a Java ügyfél számára. Korábban egy Kafka ügyfélnek kapcsolatba kellett lépnie egy ugyanazzal a verzióval vagy egy újabb verzióval rendelkező brókerrel. Mostantól a Java-ügyfelek és a KIP-35-öt `librdkafka` támogató egyéb ügyfelek újabb verziói, például visszaléphetnek a régebbi kérelemtípusokra, vagy megfelelő hibákat okozhatnak, ha a funkciók nem érhetők el.

![A Kafka-ügyfél kompatibilitásának frissítése](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Vegye figyelembe, hogy ez nem jelenti azt, hogy az ügyfél támogatja a régebbi brókerek.  További információt a [Kompatibilitási mátrix című témakörben talál.](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)

## <a name="general-migration-process"></a>Általános áttelepítési folyamat

A következő áttelepítési útmutató feltételezi, hogy egy Apache Kafka 1.0.0 vagy 1.1.0 fürt hdinsight 3.6-on egyetlen virtuális hálózatban üzembe helyezett. A meglévő brókernek van néhány témája, és aktívan használják a termelők és a fogyasztók.

![Jelenlegi Kafka feltételezett környezet](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Az áttelepítés végrehajtásához hajtsa végre az alábbi lépéseket:

1. **Telepítsen egy új HDInsight 4.0-s fürtöt és ügyfeleket tesztelésre.** Telepítsen egy új HDInsight 4.0 Kafka fürtöt. Ha több Kafka-fürtverzió is kijelölhető, ajánlott a legújabb verzió kiválasztása. Üzembe helyezés után szükség szerint állítson be néhány paramétert, és hozzon létre egy témakört a meglévő környezettel azonos nevű témakörrel. Is, állítsa be az SSL és hozd-your-own-key (BYOK) titkosítást, ha szükséges. Ezután ellenőrizze, hogy megfelelően működik-e az új fürttel.

    ![Új HDInsight 4.0-fürtök telepítése](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Váltson át a fürtre a termelői alkalmazáshoz, és várja meg, amíg az aktuális fogyasztók felhasználják az összes várólista-adatot.** Amikor az új HDInsight 4.0 Kafka fürt készen áll, váltson át a meglévő gyártói célaz új fürtre. Hagyja, ahogy van, amíg a meglévő Consumer alkalmazás fel nem használja a meglévő fürt összes adatát.

    ![Fürt váltás a termelői alkalmazáshoz](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Kapcsolja be a fürtöt a fogyasztói alkalmazásban.** Miután meggyőződött arról, hogy a meglévő fogyasztói alkalmazás befejezte a meglévő fürt összes adatának fogyasztását, váltson át a kapcsolatot az új fürtre.

    ![Fürt bekapcsolása a fogyasztói alkalmazáson](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Távolítsa el a régi fürtöt, és szükség szerint tesztelje az alkalmazásokat.** Miután a kapcsoló befejeződött és megfelelően működik, távolítsa el a régi HDInsight 3.6 Kafka fürtés a gyártók és a felhasználók által használt teszt szükség szerint.

## <a name="next-steps"></a>További lépések

* [Apache Kafka HDInsight-fürtök teljesítményoptimalizálása](apache-kafka-performance-tuning.md)
* [Rövid útmutató: Apache Kafka-fürt létrehozása az Azure HDInsightban az Azure Portal használatával](apache-kafka-get-started.md)
