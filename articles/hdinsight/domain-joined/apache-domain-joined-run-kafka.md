---
title: Oktatóanyag – Apache Kafka & Enterprise Security – Azure HDInsight
description: Oktatóanyag – megtudhatja, hogyan konfigurálhatja a Kafka Apache Ranger-szabályzatait az Azure HDInsight Enterprise Security Package használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 58c5b3bdd6d50f2e512cccffe78bd4e70805d729
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78204735"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Oktatóanyag: Apache Kafka szabályzatok konfigurálása a HDInsight-ben Enterprise Security Package (előzetes verzió)

Ismerje meg, hogyan konfigurálhatja az Apache Ranger-házirendeket Enterprise Security Package (ESP) Apache Kafka-fürtökhöz. Az ESP-fürtök egy tartományhoz csatlakoznak, lehetővé téve a felhasználók számára a tartományi hitelesítő adatokkal való hitelesítést. Ebben az oktatóanyagban két Ranger-házirendet hoz létre, amelyek korlátozzák a hozzáférést a `sales` és `marketingspend` témakörökhöz.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tartományi felhasználók létrehozása
> * Ranger-házirendek létrehozása
> * Témakörök létrehozása egy Kafka-fürtön
> * Ranger-házirendek tesztelése

## <a name="prerequisite"></a>Előfeltételek

Egy [HDInsight Kafka-fürt Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Csatlakozás az Apache Ranger felügyeleti felhasználói felületéhez

1. Egy böngészőből lépjen be a Ranger rendszergazdai felhasználói felületére a következő címen: `https://ClusterName.azurehdinsight.net/Ranger/`. Ne felejtse el átírni a `ClusterName` elemet a Kafka-fürtje nevére. A Ranger hitelesítő adatai nem ugyanazok, mint a Hadoop-fürthöz használt hitelesítő adatok. Ha meg szeretné akadályozni, hogy a böngészők gyorsítótárazott Hadoop hitelesítő adatokat használjanak, egy új InPrivate-böngészőablakból csatlakozzon a Ranger rendszergazdai felhasználói felületéhez.

2. Jelentkezzen be az Azure Active Directory (AD) rendszergazdai hitelesítő adataival. Az Azure AD rendszergazdai hitelesítő adatok nem azonosak a HDInsight-fürt hitelesítő adataival vagy a Linux rendszerű HDInsight-csomópont SSH hitelesítő adataival.

   ![HDInsight Apache Ranger felügyeleti felhasználói felülete](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Tartományi felhasználók létrehozása

A [HDInsight-fürt létrehozása az Enterprise Security Package csomaggal](./apache-domain-joined-configure-using-azure-adds.md) oktatóanyag ismerteti a **sales_user** és **marketing_user** tartományi felhasználók létrehozását. Az éles forgatókönyvekben a tartományi felhasználókat az Active Directory-bérlő adja meg.

## <a name="create-ranger-policy"></a>Ranger-házirend létrehozása

Hozzon létre egy Ranger-házirendet a **sales_user** és **marketing_user** felhasználókhoz.

1. Nyissa meg a **Ranger rendszergazdai felhasználói felületét**.

2. Válassza ki **\<ClusterName > _kafka** a **Kafka**alatt. Előfordulhat, hogy a felsorolásban megjelenik egy előre beállított házirend.

3. Válassza az **új szabályzat hozzáadása** lehetőséget, és adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Házirend neve  |  hdi sales* policy   |
   |Témakör   |  sales* |
   |Felhasználó kiválasztása  |  sales_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   A témakör nevében a következő helyettesítő karakterek használhatók:

   * \* – Nulla vagy több karaktert helyettesíthet.
   * ? – Egy tetszőleges karaktert helyettesít.

   ![Apache Ranger felügyeleti felhasználói felület létrehozása Házirend1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Várjon néhány pillanatot, míg a Ranger szinkronizálódik az Azure AD-vel, ha a rendszer nem tölt be automatikusan egy tartományi felhasználót a **Select User** (Felhasználó kiválasztása) beállításhoz.

4. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

5. Válassza az **új szabályzat hozzáadása** lehetőséget, majd adja meg a következő értékeket:

   |Beállítás  |Ajánlott érték  |
   |---------|---------|
   |Házirend neve  |  hdi marketing policy   |
   |Témakör   |  marketingspend |
   |Felhasználó kiválasztása  |  marketing_user1 |
   |Engedélyek  | közzététel, felhasználás, létrehozás |

   ![Apache Ranger felügyeleti felhasználói felület létrehozása Policy2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. A házirend mentéséhez válassza a **Hozzáadás** lehetőséget.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Témakörök létrehozása egy Kafka-fürtön az ESP-vel

Két témakör létrehozásához `salesevents` és `marketingspend`:

1. A következő paranccsal nyisson meg egy SSH-kapcsolatot a fürttel:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Cserélje le a `DOMAINADMIN`t a fürt rendszergazdai felhasználója számára a fürt [létrehozása](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)során konfiguráltra, és cserélje le a `CLUSTERNAME`t a fürt nevére. Ha a rendszer kéri, adja meg a rendszergazdai felhasználói fiók jelszavát. Az `SSH` HDInsighttal való használatával kapcsolatos további információkat [az SSH a HDInsighttal való használatáról szóló cikkben](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) találhat.

2. Használja az alábbi parancsokat a fürtnév változóként való mentéséhez és egy JSON-elemző segédprogram, a `jq` telepítéséhez. Ha a rendszer kéri, adja meg a Kafka-fürt nevét.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. A Kafka-átvitelszervező gazdagépek beszerzéséhez használja a következő parancsokat. Ha a rendszer kéri, adja meg a fürt rendszergazdai fiókjának jelszavát.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   A továbblépés előtt szükség lehet a fejlesztési környezet beállítására, ha még nem tette meg. Szüksége lesz olyan összetevőkre, mint például a Java JDK, az Apache Maven és egy, az scp-t tartalmazó SSH-ügyfél. További információ: [telepítési utasítások](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Töltse le az [Apache Kafka tartományhoz csatlakoztatott előállítói és fogyasztói példákat](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Kövesse **az Apache Kafka Producer és Consumer API-k használatát bemutató oktatóanyag**[a példák létrehozását és üzembe helyezését](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) ismertető szakaszának 2. és 3. lépését.

1. Futtassa az alábbi parancsot:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger-házirendek tesztelése

A konfigurált Ranger-szabályzatok alapján a **sales_user** a témakört `salesevents` de nem `marketingspend`. Ezzel szemben a **marketing_user** a témakört `marketingspend` de nem `salesevents`.

1. Nyisson meg egy új SSH-kapcsolatot a fürthöz. Az alábbi paranccsal jelentkezzen be mint **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Hajtsa végre a következő parancsot:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. A következő környezeti változó megadásához használja az előző szakasz Broker-neveit:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Például: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Kövesse az oktatóanyagban **a példa készítése és üzembe helyezése** szakasz 3. lépését [: a Apache Kafka producer és a fogyasztói API](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) -k segítségével biztosíthatja, hogy a `kafka-producer-consumer.jar` is elérhető legyen **sales_user**.

**Megjegyzés: ebben az oktatóanyagban használja a Kafka-producer-Consumer. jar fájlt a "DomainJoined-producer-Consumer" projektben (nem a termelői fogyasztó projekt alatt, amely a nem tartományhoz csatlakoztatott forgatókönyvekhez tartozik).**

5. A következő parancs végrehajtásával ellenőrizze, hogy a **sales_user1** képes-e a `salesevents` témakörre:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Hajtsa végre a következő parancsot a `salesevents`témakörből való használathoz:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Ellenőrizze, hogy el tudja-e olvasni az üzeneteket.

7. Győződjön meg arról, hogy a **sales_user1** nem tud előállítani a témakörben `marketingspend` a következő ugyanabban az SSH-ablakban való végrehajtásával:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Engedélyezési hiba történik, amely figyelmen kívül hagyható.

8. Figyelje meg, hogy **marketing_user1** nem használható a témakör `salesevents`.

   Ismételje meg a 1-4. lépést, de ezúttal **marketing_user1**.

   Hajtsa végre a következő parancsot a `salesevents`témakörből való használathoz:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   A korábbi üzenetek nem láthatók.

9. A hozzáférési események naplózása a Ranger felhasználói felületről tekinthető meg.

   ![A Ranger felhasználói felületi házirendjének naplózási hozzáférési eseményei ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott Kafka-fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. A felső **keresőmezőbe** írja be a **HDInsight**kifejezést.
1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások**területen.
1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre az oktatóanyaghoz létrehozott fürt mellett. 
1. Kattintson a **Delete** (Törlés) gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="troubleshooting"></a>Hibakeresés
Ha a Kafka-producer-Consumer. jar nem működik tartományhoz csatlakozó fürtben, győződjön meg arról, hogy a Kafka-producer-Consumer. jar fájlt használja a "DomainJoined-producer-Consumer" projektben (nem pedig a nem tartományhoz tartozó gyártó-fogyasztói projekt alatt). csatlakoztatott forgatókönyvek).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ügyfél által felügyelt kulcs lemezének titkosítása](../disk-encryption.md)
