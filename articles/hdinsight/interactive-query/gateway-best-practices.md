---
title: Átjáró részletes merülés és gyakorlati tanácsok az Apache Hive-hoz az Azure HDInsightban
description: Ismerje meg, hogyan navigálhat a Hive-lekérdezések Azure HDInsight-átjárón keresztüli futtatásához ajánlott eljárásokban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586976"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Átjáró részletes merülés és gyakorlati tanácsok az Apache Hive-hoz az Azure HDInsightban

Az Azure HDInsight átjáró (Gateway) a HDInsight-fürtök HTTPS-előtér. Az átjáró felelős: hitelesítés, állomásfeloldás, szolgáltatásfelderítés és a modern elosztott rendszerhez szükséges egyéb hasznos funkciók. Az átjáró által biztosított szolgáltatások némi többletterhelést eredményeznek, amelyhez ez a dokumentum ismerteti a navigáláshoz ajánlott eljárásokat. Az átjáróhiba-elhárítási technikákat is tárgyaljuk.

## <a name="the-hdinsight-gateway"></a>A HDInsight átjáró

A HDInsight-átjáró a HDInsight-fürt egyetlen olyan része, amely nyilvánosan elérhető az interneten keresztül. Az átjáró szolgáltatás a `clustername-int.azurehdinsight.net` nyilvános interneten való keresztül a belső átjáró végpont használatával érhető el. A belső átjáróvégpont lehetővé teszi az átjárószolgáltatáshoz való csatlakozások létesítését anélkül, hogy kilépne a fürt virtuális hálózatáról. Az átjáró kezeli a fürtnek küldött összes kérelmet, és továbbítja ezeket a kérelmeket a megfelelő összetevőknek és fürtállomásoknak.

Az alábbi ábra részletesen bemutatja, hogy az átjáró hogyan biztosít absztrakciót a HDInsight különböző állomásfelbontási lehetőségei előtt.

![Állomásfelbontási diagram](./media/gateway-best-practices/host-resolution-diagram.png "Állomásfelbontási diagram")

## <a name="motivation"></a>Motiváció

Az átjáró HDInsight-fürtök elé helyezésének motivációja az, hogy felületet biztosítson a szolgáltatásfelderítéshez és a felhasználói hitelesítéshez. Az átjáró által biztosított hitelesítési mechanizmusok különösen fontosak az ESP-kompatibilis fürtök számára.

A szolgáltatásfelderítés, az átjáró előnye, hogy a fürt minden egyes összetevője érhető el, `clustername.azurehdinsight.net/hive2`mint a különböző végpont `host:port` a Gateway webhely ( ), szemben a párosítások sokasága.

A hitelesítéshez az átjáró lehetővé `username:password` teszi a felhasználók számára a hitelesítő adatok párhasználatával történő hitelesítést. Esp-kompatibilis fürtök esetén ez a hitelesítő adat a felhasználó tartományának felhasználóneve és jelszava lenne. A HDInsight-fürtök nek az átjárón keresztül történő hitelesítéséhez nem szükséges az ügyfélnek kerberos-jegyet szereznie. Mivel az átjáró `username:password` elfogadja a hitelesítő adatokat, és beszerzi a felhasználó Kerberos-jegyét a felhasználó nevében, biztonságos kapcsolatok létesíthetők az átjáróval bármely ügyfélállomásról, beleértve az (ESP) fürttől eltérő AA-DDS tartományokhoz csatlakozott ügyfeleket is.

## <a name="best-practices"></a>Ajánlott eljárások

Az átjáró egyetlen szolgáltatás (két állomásra kiterhelt terhelés), amely a kérelmek továbbításáért és hitelesítésért felelős. Az átjáró egy bizonyos méretet meghaladó Hive-lekérdezések átviteli szűk keresztmetszetévé válhat. Lekérdezési teljesítménycsökkenés figyelhető meg, ha nagyon nagy **SELECT** lekérdezések végrehajtása az átjárón keresztül ODBC vagy JDBC. A "nagyon nagy" olyan lekérdezéseket jelent, amelyek több mint 5 GB adatot alkotnak sorokban vagy oszlopokban. Ez a lekérdezés tartalmazhat egy hosszú listát a sorok és, vagy egy széles oszlopszám.

Az átjáró teljesítményének csökkenése a nagy méretű lekérdezések körül azért van, mert az adatokat át kell vinni az alapul szolgáló adattárból (ADLS Gen2) a HDInsight Hive-kiszolgálóra, az átjáróba, végül pedig a JDBC- vagy ODBC-illesztőprogramok az ügyfélgazdagépbe.

Az alábbi ábra a SELECT lekérdezés lépéseit mutatja be.

![Eredménydiagram](./media/gateway-best-practices/result-retrieval-diagram.png "Eredménydiagram")

Az Apache Hive egy relációs absztrakció egy HDFS-kompatibilis fájlrendszeren. Ez az absztrakció azt **jelenti,** hogy a HIV-ben lévő SELECT utasítások megfelelnek a fájlrendszer **READ** műveleteinek. A **READ** műveletek lefordítása a megfelelő sémába, mielőtt jelenteni a felhasználónak. A folyamat késése az adatok méretével és a végfelhasználó eléréséhez szükséges összes ugrással nő.

Hasonló viselkedés fordulhat elő nagy méretű adatok **CREATE** vagy **INSERT** utasítások végrehajtásakor is, mivel ezek a parancsok megfelelnek az alapul szolgáló fájlrendszer **ÍRÁSi** műveleteinek. Fontolja meg az adatok ( például a nyers ORC ) írását a fájlrendszerbe/datalake-be, ahelyett, hogy **az INSERT** vagy a **LOAD**használatával töltene be.

Az Enterprise Security Pack-kompatibilis fürtökben a kellően összetett Apache Ranger-házirendek a lekérdezésfordítási idő lassulását okozhatják, ami az átjáró időtúllépéséhez vezethet. Ha egy ESP-fürtben egy átjáró időtúltöltése figyelhető meg, fontolja meg a ranger-házirendek számának csökkentését vagy kombinálását.

## <a name="troubleshooting-techniques"></a>Hibaelhárítási technikák

A fenti viselkedés részeként több helyszín is létezik a teljesítményproblémák csökkentésére és megértésére. A HDInsight-átjárón keresztüli lekérdezési teljesítménycsökkenés esetén használja az alábbi ellenőrzőlistát:

* Nagy **SELECT-lekérdezések** végrehajtásakor használja a **LIMIT** záradékot. A **LIMIT** záradék csökkenti az ügyfélállomásnak jelentett összes sorot. A **LIMIT** záradék csak az eredmény generálására van hatással, és nem módosítja a lekérdezési tervet. Ha a **LIMIT** záradékot szeretné alkalmazni `hive.limit.optimize.enable`a lekérdezési tervre, használja a configuration .t. **A LIMIT** a **LIMIT x, y**argumentuműrlap segítségével kombinálható eltolással.

* A **SELECT \* **helyett a SELECT program **futtatásakor** nevezze el az érdeklődésre számot tartó oszlopokat. Ha kevesebb oszlopot jelöl ki, az csökkenti az olvasott adatok mennyiségét.

* Próbálja meg futtatni az érdeklődésre számot tartó lekérdezést az Apache Beeline-on keresztül. Ha az Apache Beeline-on keresztüli eredménylekérés hosszabb időt vesz igénybe, akkor késésre számíthat, amikor ugyanazt az eredményt külső eszközökön keresztül kéri le.

* Tesztelje az alapvető Hive-lekérdezést annak érdekében, hogy létre lehessen hozni a HDInsight-átjáróhoz való csatlakozást. Próbáljon meg egy egyszerű lekérdezést futtatni két vagy több külső eszközről, hogy megbizonyosodjon arról, hogy egyetlen eszköz sem fut problémákba.

* Tekintse át az Apache Ambari riasztásokat, és győződjön meg arról, hogy a HDInsight-szolgáltatások kifogástalanok. Ambari riasztások integrálható az Azure Monitor jelentéskészítésés elemzés.

* Ha egy külső Hive Metastore, ellenőrizze, hogy az Azure SQL DB DTU a Hive Metastore nem érte el a korlátot. Ha a DTU közeledik a korláthoz, növelnie kell az adatbázis méretét.

* Győződjön meg arról, hogy a harmadik féltől származó eszközök, például a PBI vagy a Tableau tördelést használnak a táblák vagy adatbázisok megtekintéséhez. A tördeléssel kapcsolatos segítségért forduljon a támogatási partnerekhez. A tördeléshez használt fő eszköz `fetchSize` a JDBC paraméter. Egy kis lehívási méret az átjáró teljesítményének csökkenését eredményezheti, de a lehívási méret túl nagy lehet az átjáró időtúltöltése. A lehívási méret hangolását munkaterhelés alapján kell elvégezni.

* Ha az adatfolyamat nagy mennyiségű adatot tartalmaz a HDInsight-fürt mögöttes tárolójából, fontolja meg egy olyan eszköz használatát, amely közvetlenül kapcsolódik az Azure Storage-hoz, például az Azure Data Factoryhoz

* Az Apache Hive LLAP használatát érdemes lehet használni az interaktív számítási feladatok futtatásakor, mivel az LLAP gördülékenyebb élményt nyújthat a lekérdezési eredmények gyors visszaadásához

* Fontolja meg a Hive Metastore szolgáltatáshoz elérhető `hive.server2.thrift.max.worker.threads`szálak számának növelését. Ez a beállítás különösen akkor fontos, ha nagy számú egyidejű felhasználó küld lekérdezést a fürtnek

* Csökkentse az átjáró külső eszközökről történő eléréséhez használt újrapróbálkozások számát. Ha több újrapróbálkozást használ, fontolja meg egy exponenciális visszalépési házirend követését

* Fontolja meg a tömörítési `hive.exec.compress.output` Hive engedélyezését a konfigurációk és `hive.exec.compress.intermediate`használatával.

## <a name="next-steps"></a>További lépések

* [Apache Beeline a HDInsight-on](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [A HDInsight átjáró időeltetési hibaelhárítási lépései](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Virtuális hálózatok a HDInsight-hoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight expressz útvonallal](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)