---
title: Oktatóanyag – Apache Kafka & Enterprise Security – Azure HDInsight
description: Oktatóanyag – Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat a Kafkához az Azure HDInsight ban az Enterprise Security Package csomaggal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 58c5b3bdd6d50f2e512cccffe78bd4e70805d729
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78204735"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Az Apache Kafka-házirendek konfigurálása a HDInsightban vállalati biztonsági csomaggal (előzetes verzió)

Ismerje meg, hogyan konfigurálhatja az Apache Ranger-szabályzatokat az Enterprise Security Package (ESP) Apache Kafka-fürtökhöz. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-házirendet hoz létre, amelyek korlátozzák a hozzáférést a `sales` és `marketingspend` témakörökhöz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Témakörök létrehozása egy Kafka-fürtön
> * Ranger-házirendek tesztelése

## <a name="prerequisite"></a>Előfeltétel

[HDInsight Kafka-fürt vállalati biztonsági csomaggal](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://ClusterName.azurehdinsight.net/Ranger/`. Ne felejtse el átírni a `ClusterName` elemet a Kafka-fürtje nevére. A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

   ![HDInsight Apache Ranger felügyeleti felhasználói felülete](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A [HDInsight-fürt létrehozása az Enterprise Security Package csomaggal](./apache-domain-joined-configure-using-azure-adds.md) oktatóanyag ismerteti a **sales_user** és **marketing_user** tartományi felhasználók létrehozását. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-ranger-policy"></a>Ranger-házirend létrehozása

Hozzon létre egy Ranger-házirendet a **sales_user** és **marketing_user** felhasználókhoz.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**.

2. Válassza ** \<a ClusterName>_kafka** **>_kafka**csoportban. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend.

3. Válassza **az Új házirend hozzáadása lehetőséget,** és adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Házirend neve  |  hdi sales* policy   |
   |Témakör   |  sales* |
   |Felhasználó kiválasztása  |  sales_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * * – Nulla vagy több karaktert helyettesíthet.
   * ? – Egy tetszőleges karaktert helyettesít.

   ![Apache Ranger felügyeleti felhasználói felületlétrehozása szabályzat1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

5. Válassza **az Új házirend hozzáadása lehetőséget,** majd adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Házirend neve  |  hdi marketing policy   |
   |Témakör   |  marketingspend |
   |Felhasználó kiválasztása  |  marketing_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   ![Apache Ranger admin felhasználói felület létrehozása szabályzat2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Témakörök létrehozása egy Kafka-fürtön az ESP-vel

Két témakör létrehozása, `salesevents` `marketingspend`és:

1. A következő paranccsal nyisson meg egy SSH-kapcsolatot a fürttel:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Cserélje `DOMAINADMIN` le a [fürt létrehozása](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)során konfigurált `CLUSTERNAME` fürt rendszergazdájára, és cserélje le a fürt nevére. Ha a rendszer kéri, adja meg a rendszergazdai felhasználói fiók jelszavát. Az `SSH` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. Használja az alábbi parancsokat a fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram, a `jq` telepítéséhez. Ha a rendszer kéri, adja meg a Kafka-fürt nevét.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. A következő parancsokkal lejuthat a Kafka brókergazda. Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának jelszavát.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   A folytatás előtt szükség lehet a fejlesztői környezet beállítására, ha még nem tette meg. Szüksége lesz olyan összetevőkre, mint a Java JDK, az Apache Maven és egy SSH-ügyfél scp-vel. További információt a [telepítési utasításokban](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)talál.

1. Töltse le az [Apache Kafka tartományhoz csatlakoztatott előállítói és fogyasztói példákat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Kövesse [az Apache Kafka Producer és Consumer API-k használatát bemutató oktatóanyag](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)**a példák létrehozását és üzembe helyezését** ismertető szakaszának 2. és 3. lépését.

1. Futtassa az alábbi parancsot:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

A Ranger-házirendek **sales_user** konfigurálása alapján `salesevents` sales_user létrehozhat/feltud használni témakört, `marketingspend`de nem. Ezzel **szemben, marketing_user** képes `marketingspend` / fogyasztanak témát, de nem téma `salesevents`.

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. Az alábbi paranccsal jelentkezzen be mint **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Hajtsa végre a következő parancsot:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Az előző szakasz brókerneveivel állítsa be a következő környezeti változót:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Például: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Kövesse a **sales_user** **3.** [Tutorial: Use the Apache Kafka Producer and Consumer APIs](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) `kafka-producer-consumer.jar`

**Megjegyzés: Ebben az oktatóanyagban használja a kafka-producer-consumer.jar -t a "DomainJoined-Producer-Consumer" projekt alatt (nem pedig a Termelő-fogyasztó projekt, amely a nem domainhez kapcsolt forgatókönyvekhez tartozik).**

5. Ellenőrizze, hogy **sales_user1** `salesevents` képesek-e a következő parancs végrehajtásával a témához:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. A témakörből `salesevents`felemésztő következő parancs végrehajtása:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Ellenőrizze, hogy el tudja-e olvasni az üzeneteket.

7. Ellenőrizze, hogy a **sales_user1** nem `marketingspend` tud-e a témakört létrehozni, ha ugyanabban az ssh ablakban hajtja végre a következőket:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Engedélyezési hiba történik, amely figyelmen kívül hagyható.

8. Figyelje **meg,** hogy marketing_user1 `salesevents`nem fogyaszthat a témából.

   Ismételje meg a **marketing_user1**fenti 1-4.

   A témakörből `salesevents`felemésztő következő parancs végrehajtása:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   A korábbi üzenetek nem láthatók.

9. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![A Ranger felhasználói felületházirend-naplózási hozzáférési eseményei ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott Kafka-fürtöt:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.**
1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**
1. A megjelenő HDInsight-fürtök listájában kattintson az oktatóanyaghoz létrehozott fürt melletti **...** elemre. 
1. Kattintson a **Törlés** gombra. Kattintson **az Igen gombra.**

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a kafka-producer-consumer.jar nem működik egy tartományhoz csatlakozó fürtben, kérjük, győződjön meg róla, hogy a kafka-producer-consumer.jar-ot használja a "DomainJoined-Producer-Consumer" projekt alatt (nem a Termelő-fogyasztó projekt, amely a nem tartományhoz csatlakozott forgatókönyvekhez tartozik).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ügyfél által felügyelt kulcson alapuló lemeztitkosítás](../disk-encryption.md)
