---
title: Az SSL-titkosítás és-hitelesítés beállítása az Azure HDInsight Apache Kafka
description: Az SSL-titkosítás beállítása a Kafka-ügyfelek és a Kafka-közvetítők, valamint a Kafka-közvetítők közötti kommunikációhoz. Az ügyfelek SSL-hitelesítésének beállítása.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: d555c51838f3595367e931341a3cf6161857faef
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554609"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Az SSL (SSL) titkosítás és a hitelesítés beállítása az Azure HDInsight Apache Kafkahoz

Ez a cikk bemutatja, hogyan állíthatja be az SSL-titkosítást Apache Kafka-ügyfelek és a Apache Kafka-ügynökök között. Azt is bemutatja, hogyan állíthatja be az ügyfelek hitelesítését (más néven kétirányú SSL).

> [!Important]
> Két ügyfél használhatja a Kafka-alkalmazásokhoz: egy Java-ügyfelet és egy konzol-ügyfelet. Csak a Java-ügyfél `ProducerConsumer.java` használhatja az SSL-t mind a gyártó, mind a fogyasztó számára. A konzol termelői ügyfele `console-producer.sh` nem működik az SSL protokollal.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker beállítása

A Kafka SSL Broker telepítője négy HDInsight-fürtöt fog használni a következő módon:

* átjárócsomóponthoz 0 – hitelesítésszolgáltató (CA)
* feldolgozói csomópont: 0, 1 és 2 – közvetítők

> [!Note] 
> Ez az útmutató önaláírt tanúsítványokat fog használni, de a legbiztonságosabb megoldás a megbízható hitelesítésszolgáltatók által kiadott tanúsítványok használata.

A közvetítő telepítési folyamatának összefoglalása a következő:

1. A következő lépéseket a három feldolgozó csomóponton kell megismételni:

    1. Tanúsítvány létrehozása.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. Küldje el a tanúsítvány-aláírási kérelmet a hitelesítésszolgáltatónak (CA).
    1. Jelentkezzen be a HITELESÍTÉSSZOLGÁLTATÓba, és írja alá a kérelmet.
    1. SZOLGÁLTATÁSKAPCSOLÓDÁSI pont az aláírt tanúsítvány visszahívása a munkavégző csomópontra.
    1. SCP a HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa a munkavégző csomóponthoz.

1. Az összes tanúsítvány után helyezze a tanúsítványokat a tanúsítvány-tárolóba.
1. Nyissa meg a Ambari, és módosítsa a konfigurációkat.

A következő részletes utasításokat követve hajthatja végre a Broker telepítését:

> [!Important]
> A következő kódrészletekben a wnX a három munkavégző csomópont egyikének rövidítése, és szükség esetén `wn0`, `wn1` vagy `wn2` értékkel kell helyettesíteni. `WorkerNode0_Name` és `HeadNode0_Name` a megfelelő gépek nevével kell helyettesíteni, például `wn0-abcxyz` vagy `hn0-abcxyz`.

1. Hajtsa végre a kezdeti beállítást a 0. fő csomóponton, amely a HDInsight kitölti a hitelesítésszolgáltató (CA) szerepkörét.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Hajtsa végre ugyanezt a kezdeti beállítást minden egyes brókernél (feldolgozói csomópontok: 0, 1 és 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Mindegyik munkavégző csomóponton hajtsa végre a következő lépéseket az alábbi kódrészlet használatával.
    1. Hozzon létre egy tárolót, és töltse fel egy új privát tanúsítvánnyal.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. SZOLGÁLTATÁSKAPCSOLÓDÁSI pont a tanúsítvány-aláírási kérelem a CA számára (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. A hitelesítésszolgáltatói számítógépen futtassa a következő parancsot a CA-CERT és a CA-Key fájlok létrehozásához:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Váltson a HITELESÍTÉSSZOLGÁLTATÓI gépre, és írja alá az összes fogadott tanúsítvány-aláírási kérelmet:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Küldje vissza az aláírt tanúsítványokat a munkavégző csomópontoknak a HITELESÍTÉSSZOLGÁLTATÓTÓL (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Küldje el a HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványát az egyes munkavégző csomópontoknak.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Minden munkavégző csomóponton adja hozzá a CAs nyilvános tanúsítványt a truststore és a tárolóhoz. Ezután adja hozzá a munkavégző csomópont saját aláírt tanúsítványát a tárolóhoz.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>A Kafka konfigurációjának frissítése az SSL használatára és a brókerek újraindítására

Most már beállította az egyes Kafka-átvitelszervezőket egy truststore, és importálta a megfelelő tanúsítványokat. Ezután módosítsa a kapcsolódó Kafka-konfiguráció tulajdonságait a Ambari használatával, majd indítsa újra a Kafka-közvetítőket.

A konfiguráció módosításának befejezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba, és válassza ki az Azure HDInsight Apache Kafka-fürtöt.
1. Nyissa meg a Ambari felhasználói felületét a **Ambari** elemre kattintva a **fürt irányítópultok**területén.
1. A **Kafka-közvetítő** alatt állítsa be a **figyelők** tulajdonságot a következőre: `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. A **speciális Kafka-bróker** alatt állítsa be a **Security. Inter. Broker. Protocol** tulajdonságot a következőre: `SSL`

    ![A Kafka SSL konfigurációs tulajdonságainak szerkesztése a Ambari-ben](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Az **Egyéni Kafka-bróker** alatt állítsa be az **SSL. Client. auth** tulajdonságot a következőre: `required`. Ez a lépés csak akkor szükséges, ha a hitelesítést és a titkosítást állítja be.

    ![A Kafka SSL konfigurációs tulajdonságainak szerkesztése a Ambari-ben](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. A **speciális Kafka-env** alatt adja hozzá a következő sorokat a **Kafka-env template** tulajdonság végéhez.

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Kafka-env template tulajdonság szerkesztése a Ambari-ben](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Indítsa újra az összes Kafka-közvetítőt.
1. Indítsa el a felügyeleti ügyfelet a producer és a fogyasztói lehetőségek közül annak ellenőrzéséhez, hogy mindkét gyártó és a fogyasztó dolgozik-e a 9093-es porton.

## <a name="client-setup-with-authentication"></a>Ügyfél beállítása (hitelesítéssel)

> [!Note]
> Az alábbi lépéseket csak akkor kell végrehajtania, ha az SSL-titkosítást **és** a hitelesítést is beállítja. Ha csak a titkosítást állítja be, akkor a [hitelesítés nélkül folytassa az ügyfél beállításával](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Az ügyfél telepítésének befejezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az ügyfélszámítógépre (hn1).
1. Hozzon létre egy Java-tárolót, és szerezzen be egy aláírt tanúsítványt a közvetítő számára. Ezután másolja a tanúsítványt arra a virtuális gépre, amelyen a HITELESÍTÉSSZOLGÁLTATÓ fut.
1. Váltson a HITELESÍTÉSSZOLGÁLTATÓI gépre (hn0) az ügyféltanúsítvány aláírásához.
1. Nyissa meg az ügyfélszámítógépet (hn1), és keresse meg a `~/ssl` mappát. Másolja az aláírt tanúsítványt az ügyfélszámítógépre.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Végül tekintse meg a `client-ssl-auth.properties` fájlt a `cat client-ssl-auth.properties` paranccsal. A következő sorokkal kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

Ha nincs szüksége a hitelesítésre, a csak az SSL-titkosítás beállításának lépései a következők:

1. Jelentkezzen be az ügyfélszámítógépre (hn1), és navigáljon a `~/ssl` mappába.
1. Másolja az aláírt tanúsítványt az ügyfélszámítógépre a HITELESÍTÉSSZOLGÁLTATÓI gépről (wn0).
1. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány importálása a truststore
1. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány importálása a rendszertárolóba

Ezek a lépések a következő kódrészletben jelennek meg.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Végül tekintse meg a `client-ssl-auth.properties` fájlt a `cat client-ssl-auth.properties` paranccsal. A következő sorokkal kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Következő lépések

* [Mi a HDInsight Apache Kafka?](apache-kafka-introduction.md)
