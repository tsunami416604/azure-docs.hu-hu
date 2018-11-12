---
title: Kafka-házirendek konfigurálása a HDInsightban az Enterprise Security Package csomaggal – Azure
description: Megtudhatja, hogyan konfigurálhat Apache Ranger-házirendeket a Kafkához az Azure HDInsightban az Enterprise Security Package csomaggal.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 48cfba6f62d75470efd27e3a4cdcb995e716798b
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037141"
---
# <a name="tutorial-configure-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Kafka-házirendek konfigurálása a HDInsightban az Enterprise Security Package csomaggal (Előzetes verzió)

Megtudhatja, hogyan konfigurálhat Apache Ranger-házirendeket az Enterprise Security Package- (ESP-) alapú Kafka-fürtökön. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-házirendet hoz létre, amelyek korlátozzák a hozzáférést a `sales*` és `marketingspend` témakörökhöz.

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

A [HDInsight-fürt létrehozása az Enterprise Security Package csomaggal](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster) oktatóanyag ismerteti a **sales_user** és **marketing_user** tartományi felhasználók létrehozását. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

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

Hozzon létre két témakört **salesevents** és **marketingspend** néven:

1. A következő paranccsal nyisson meg egy SSH-kapcsolatot a fürttel:

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Cserélje le az `SSHUSER` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát. Az `scp` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) találhat.

   Éles forgatókönyvekben a fürt létrehozása során konfigurált tartományfelhasználók SSH használatával is bejelentkezhetnek a fürtre.

2. Használja az alábbi parancsokat a fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram, a `jq` telepítéséhez. Ha a rendszer kéri, adja meg a Kafka-fürt nevét.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. A Kafka közvetítő gazdagépeinek és a Zookeeper-gazdagépek beolvasását az alábbi parancsokkal végezheti el. Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának jelszavát.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```
> [!Note]
> Mielőtt továbblépne, konfigurálnia kell a fejlesztési környezetet, ha még nem tette volna meg. A következő összetevőkre lesz szüksége: Java JDK, Apache Maven, valamint egy SSH-ügyfél SCP-vel. További információkért lásd ezeket [a beállítási utasításokat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
1. Töltse le az [Apache Kafka tartományhoz csatlakoztatott előállítói és fogyasztói példákat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Kövesse [az Apache Kafka Producer és Consumer API-k használatát bemutató oktatóanyag](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) **a példák létrehozását és üzembe helyezését** ismertető szakaszának 2. és 3. lépését.

1. Futtassa az alábbi parancsot:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Csak a Kafka szolgáltatás folyamattulajdonosa, például a root írhat a `/config/topics` Zookeeper znode-csomópontokra. A Ranger-házirendeket nem kényszeríti ki a rendszer, ha egy nem kiemelt jogosultságú felhasználó hoz létre egy üzenettémakört. Ennek oka az, hogy a `kafka-topics.sh` szkript közvetlenül a Zookeeperrel kommunikál a témakör létrehozásához. A rendszer bejegyzéseket ad hozzá a Zookeeper-csomópontokhoz, amit a közvetítő oldalon a figyelők monitoroznak, és ennek megfelelően hoznak létre témaköröket. A hitelesítés nem végezhető el a Ranger beépülő modulon keresztül, a fenti parancs pedig a `sudo` használatával, a Kafka-közvetítőn keresztül lesz végrehajtva.


## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

A konfigurált Ranger-házirendek szerint a **sales_user** felhasználó létrehozhat és felhasználhat tartalmakat a **salesevents** témakörben, a **marketingspend** témakörben azonban nem. Ezzel szemben a **marketing_user** létrehozhat és felhasználhat a **marketingspend** témakörben, de a **salesevents** témakörben nem.

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. Az alábbi paranccsal jelentkezzen be mint **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Hajtsa végre a következő parancsot:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Használja az előző szakaszban ismertetett közvetítő és Zookeper-neveket az alábbi környezeti változók beállításához:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   Például: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   Például: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. Ellenőrizze, hogy a **sales_user1** létrehozhat-e tartalmakat a **salesevents** témakörben.
   
   A következő paranccsal indítsa el a **salesevents** témakör létrehozó konzolját:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   Írjon be néhány üzenetet a konzolon. A **Ctrl + C** billentyűkombinációval lépjen ki a létrehozó konzolból.

5. Futtassa a következő parancsot a **salesevents** témakör tartalmainak felhasználásához:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. Győződjön meg róla, hogy megjelennek az előző lépésben beírt üzenetek, valamint hogy a **sales_user1** felhasználó nem tud tartalmakat létrehozni a **marketingspend** témakörben.

   A fentebb említett SSH-ablakban futtassa a következő parancsot a **marketingspend** témakörben való tartalomlétrehozáshoz:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   Engedélyezési hiba történik, amely figyelmen kívül hagyható. 

7. Figyelje meg, hogy **marketing_user1** nem tudja felhasználni a **salesevents** témakör tartalmait.

   Ismételje meg a fenti 1–3. lépést, ám ezúttal a **marketing_user1** felhasználóként bejelentkezve.

   Futtassa a következő parancsot a **salesevents** témakör tartalmainak felhasználásához:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   A korábbi üzenetek nem láthatók.

8. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![A Ranger felhasználói felület házirendnaplózása](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>További lépések

* [Saját kulcs használata a Kafkában](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Bevezetés a Hadoop-biztonság használatába az Enterprise Security Package esetében](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
