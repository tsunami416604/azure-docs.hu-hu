---
title: Oktatóanyag – a vállalati biztonsági csomaggal – Azure HDInsight az Apache Kafka-szabályzatok konfigurálása
description: Oktatóanyag – ismerje meg, az Apache Ranger-házirendek konfigurálása a vállalati biztonsági csomag az Azure HDInsight Kafka számára.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: ba16a975aa3b1e60393006ef49a7e422c572931e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441373"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Az Apache Kafka-házirendek konfigurálása a HDInsight vállalati biztonsági csomaggal (előzetes verzió)

Megtudhatja, hogyan konfigurálhatja a vállalati biztonsági csomag (ESP) az Apache Kafka-fürtök az Apache Ranger-házirendet. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-házirendet hoz létre, amelyek korlátozzák a hozzáférést a `sales` és `marketingspend` témakörökhöz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Témakörök létrehozása egy Kafka-fürtön
> * Ranger-házirendek tesztelése

## <a name="prerequisite"></a>Előfeltétel

A [HDInsight Kafka-fürt a vállalati biztonsági csomaggal](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://ClusterName.azurehdinsight.net/Ranger/`. Ne felejtse el átírni a `ClusterName` elemet a Kafka-fürtje nevére. A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

   ![Apache Ranger rendszergazdai felhasználói felület](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A [HDInsight-fürt létrehozása az Enterprise Security Package csomaggal](./apache-domain-joined-configure-using-azure-adds.md) oktatóanyag ismerteti a **sales_user** és **marketing_user** tartományi felhasználók létrehozását. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-ranger-policy"></a>Ranger-házirend létrehozása

Hozzon létre egy Ranger-házirendet a **sales_user** és **marketing_user** felhasználókhoz.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**.

2. Válassza ki  **\<ClusterName > _kafka** alatt **Kafka**. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend.

3. Válassza ki **új házirend hozzáadása** , és adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Szabályzat neve  |  hdi sales* policy   |
   |Témakör   |  sales* |
   |Felhasználó kiválasztása  |  sales_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * \* – Nulla vagy több karaktert helyettesíthet.
   * ? – Egy tetszőleges karaktert helyettesít.

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. Válassza ki **Hozzáadás** mentse a szabályzatot.

5. Válassza ki **új házirend hozzáadása** és adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Szabályzat neve  |  hdi marketing policy   |
   |Témakör   |  marketingspend |
   |Felhasználó kiválasztása  |  marketing_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   ![Házirend létrehozása az Apache Ranger rendszergazdai felhasználói felületen](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Válassza ki **Hozzáadás** mentse a szabályzatot.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Témakörök létrehozása egy Kafka-fürtön az ESP-vel

Két témakör létrehozásához `salesevents` és `marketingspend`:

1. A következő paranccsal nyisson meg egy SSH-kapcsolatot a fürttel:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Cserélje le `DOMAINADMIN` során konfigurált-fürthöz tartozó rendszergazdai felhasználóval [fürtlétrehozás](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp), és cserélje le `CLUSTERNAME` a fürt nevére. Ha a rendszer kéri, adja meg a jelszót a rendszergazdai felhasználói fiókkal. Az `SSH` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. Használja az alábbi parancsokat a fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram, a `jq` telepítéséhez. Ha a rendszer kéri, adja meg a Kafka-fürt nevét.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. A következő parancsok használatával a Kafka-közvetítő gazdagépek beolvasása. Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának jelszavát.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Mielőtt továbblépne, szükség lehet a fejlesztési környezet beállítása, ha rendelkezik nem tette meg. Szüksége lesz összetevők például a Java JDK, Apache-Maven és SSH-ügyfelet, az SCP-je. További információkért lásd: [beállítási utasítások](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Töltse le az [Apache Kafka tartományhoz csatlakoztatott előállítói és fogyasztói példákat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Kövesse a lépéseket 2. és 3 alatt **létrehozása és üzembe helyezése a példa** a [oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

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

4. Hajtsa végre a 3. lépés alatt **létrehozása és üzembe helyezése a példa** a [oktatóanyag: Az Apache Kafka Producer és Consumer API-k használata](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) annak érdekében, hogy a `kafka-producer-consumer.jar` is rendelkezésre áll **sales_user**.

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje a Kafka-fürt, amely az alábbi lépéseket követve létrehozta:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Az a **keresési** tetején típus **HDInsight**.
1. Válassza ki **HDInsight-fürtök** alatt **szolgáltatások**.
1. A HDInsight-fürtök megjelenő listában kattintson a **...**  ebben az oktatóanyagban létrehozott fürt mellett. 
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Apache Kafka a saját kulcs használata](../kafka/apache-kafka-byok.md)