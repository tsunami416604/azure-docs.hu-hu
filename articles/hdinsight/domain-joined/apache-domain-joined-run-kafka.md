---
title: A vállalati biztonsági csomaggal – Azure HDInsight az Apache Kafka-szabályzatok konfigurálása
description: Megtudhatja, hogyan konfigurálhat Apache Ranger-házirendeket a Kafkához az Azure HDInsightban az Enterprise Security Package csomaggal.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 381958b462b668447a6548029a1be616df7a3a4b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750222"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Az Apache Kafka-házirendek konfigurálása a HDInsight vállalati biztonsági csomaggal (előzetes verzió)

Megtudhatja, hogyan konfigurálhatja a vállalati biztonsági csomag (ESP) az Apache Kafka-fürtök az Apache Ranger-házirendet. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-házirendet hoz létre, amelyek korlátozzák a hozzáférést a `sales*` és `marketingspend` témakörökhöz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Témakörök létrehozása egy Kafka-fürtön
> * Ranger-házirendek tesztelése

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

* Hozzon létre egy [HDInsight Kafka-fürtöt az Enterprise Security Package csomaggal](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ne felejtse el átírni a `<ClusterName>` elemet a Kafka-fürtje nevére.

    > [!NOTE]  
    > A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

   ![Apache Ranger rendszergazdai felhasználói felület](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A [HDInsight-fürt létrehozása az Enterprise Security Package csomaggal](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) oktatóanyag ismerteti a **sales_user** és **marketing_user** tartományi felhasználók létrehozását. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-ranger-policy"></a>Ranger-házirend létrehozása

Hozzon létre egy Ranger-házirendet a **sales_user** és **marketing_user** felhasználókhoz.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**.

2. Kattintson a **\<Fürtnév > _kafka** lehetőségre a **Kafka** területen. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend.

3. Kattintson az **Add New Policy** (Új házirend hozzáadása) lehetőségre, majd adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  hdi sales* policy   |
   |Témakör   |  sales* |
   |Felhasználó kiválasztása  |  sales_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * * – Nulla vagy több karaktert helyettesíthet.
   * ? – Egy tetszőleges karaktert helyettesít.

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

5. Kattintson az **Add New Policy** (Új házirend hozzáadása) lehetőségre, majd adja meg a következő értékeket:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Házirend neve  |  hdi marketing policy   |
   |Témakör   |  marketingspend |
   |Felhasználó kiválasztása  |  marketing_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. A házirend mentéséhez kattintson a **Hozzáadás** gombra.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Témakörök létrehozása egy Kafka-fürtön az ESP-vel

Két témakör létrehozásához `salesevents` és `marketingspend`:

1. A következő paranccsal nyisson meg egy SSH-kapcsolatot a fürttel:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Cserélje le `DOMAINADMIN` során konfigurált-fürthöz tartozó rendszergazdai felhasználóval [fürtlétrehozás](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp), és cserélje le `CLUSTERNAME` a fürt nevére. Ha a rendszer kéri, adja meg a jelszót a rendszergazdai felhasználói fiókkal. Az `SSH` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) találhat.

2. Használja az alábbi parancsokat a fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram, a `jq` telepítéséhez. Ha a rendszer kéri, adja meg a Kafka-fürt nevét.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. A következő parancsok használatával a Kafka-közvetítő gazdagépek beolvasása. Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának jelszavát.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Mielőtt továbblépne, konfigurálnia kell a fejlesztési környezetet, ha még nem tette volna meg. A következő összetevőkre lesz szüksége: Java JDK, Apache Maven, valamint egy SSH-ügyfél SCP-vel. További részletekért lásd: [beállítási utasítások](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Töltse le az [Apache Kafka tartományhoz csatlakoztatott előállítói és fogyasztói példákat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Kövesse a lépéseket 2. és 3 alatt **létrehozása és üzembe helyezése a példa** a [oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Futtassa az alábbi parancsot:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

Konfigurálva, a Ranger-házirendek alapján **sales_user** termék/felhasználhat a témakör `salesevents` , de nem témakör `marketingspend`. Ezzel szemben **marketing_user** termék/felhasználhat a témakör `marketingspend` , de nem témakör `salesevents`.

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. Az alábbi paranccsal jelentkezzen be mint **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Hajtsa végre a következő parancsot:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Az előző szakaszban az átvitelszervező-nevek használatával a következő környezeti változót:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Például: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Hajtsa végre a 3. lépés alatt **létrehozása és üzembe helyezése a példa** a [oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) annak érdekében, hogy a `kafka-producer-consumer.jar` is rendelkezésre áll **sales_user**.

5. Ellenőrizze, hogy **sales_user1** hozhatnak, témakörbe `salesevents` futtassa az alábbi parancsot:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Futtassa a következő parancsot a témakörből lefoglalhatja `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Győződjön meg arról, hogy Ön tudja olvasni az üzeneteket.

7. Ellenőrizze, hogy a **sales_user1** témakörbe nem tud `marketingspend` az alábbiak végrehajtásával ugyanazon ssh ablakban:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Engedélyezési hiba történik, amely figyelmen kívül hagyható.

8. Figyelje meg, hogy **marketing_user1** nem tudja fogadni a témakör `salesevents`.

   Ismételje meg az 1 – 4 lépéseket fölött, de ezúttal, **marketing_user1**.

   Futtassa a következő parancsot a témakörből lefoglalhatja `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   A korábbi üzenetek nem láthatók.

9. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![A Ranger felhasználói felület házirendnaplózása](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>További lépések

* [Az Apache Kafka a saját kulcs használata](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Bevezetés az Apache Hadoop-biztonság, a vállalati biztonsági csomag](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
