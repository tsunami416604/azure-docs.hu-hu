---
title: Az SSL-titkosítás és hitelesítés az Azure HDInsight az Apache Kafka beállítása
description: Állítsa be a Kafka-ügyfelek és a Kafka-közvetítők is közötti érvénnyel a Kafka-közvetítőkhöz közötti kommunikáció SSL-titkosítást. Az ügyfelek SSL-hitelesítés beállítása.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464996"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Állítsa be a Secure Sockets Layer (SSL) titkosítást és a hitelesítést az Azure HDInsight-beli Apache kafka

Ez a cikk bemutatja, hogyan állíthatja be az Apache Kafka-ügyfelek és az Apache Kafka-közvetítőkhöz közötti SSL-titkosítást. Azt is bemutatja, hogyan állíthat be (más néven kétirányú SSL) ügyfél-hitelesítést.

> [!Important]
> A Kafka-alkalmazásokat is használhat, amely két ügyfél: Java ügyfél és a egy konzol ügyfél. Csak a Java ügyfél `ProducerConsumer.java` használható SSL létrehozásáért és felhasználásához. A konzol előállító ügyfél `console-producer.sh` SSL nem működik.

## <a name="apache-kafka-broker-setup"></a>Az Apache Kafka-közvetítő beállítása

A Kafka SSL broker telepítő négy HDInsight-fürt virtuális gépeit használja a következő módon:

* átjárócsomópont 0 - hitelesítésszolgáltató (CA)
* munkavégző csomópont 0, 1 és 2 - közvetítők

> [!Note] 
> Ez az útmutató fogja használni önaláírt tanúsítványokat, de a legbiztonságosabb megoldás, ha megbízható hitelesítésszolgáltatók által kibocsátott tanúsítványokat használ.

Az összefoglalás az ügynök telepítési folyamat a következőképpen történik:

1. Az alábbi lépéseket az egyes a három feldolgozó csomópontok ismétlődnek:

    1. Hozzon létre tanúsítványt.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. A tanúsítvány-aláírási kérelmet, a hitelesítésszolgáltató (CA) küldése.
    1. Jelentkezzen be a hitelesítésszolgáltató és a kérés aláírásához.
    1. Szolgáltatáskapcsolódási pont az aláírt tanúsítvány vissza a feldolgozó csomóponton.
    1. Szolgáltatáskapcsolódási pont a munkavégző csomópont a hitelesítésszolgáltató nyilvános tanúsítványát.

1. Után minden, a tanúsítványok, a tanúsítványok üzembe a tanúsítványtár.
1. Nyissa meg az Ambari, és módosítsa a beállításokat.

Kövesse az alábbi részletes utasításokat a közvetítő telepítés befejezéséhez:

> [!Important]
> Az alábbi kódrészleteket wnX rövidítése az egyik három feldolgozó csomópontot és alkalmazásnévre cseréli `wn0`, `wn1` vagy `wn2` megfelelő módon. `WorkerNode0_Name` és `HeadNode0_Name` például behelyettesíteni a megfelelő gépek neveinek kell `wn0-abcxyz` vagy `hn0-abcxyz`.

1. Hajtsa végre a kezdeti beállítás a 0 átjárócsomóponton, amely a HDInsight betelik a szerepkört, a hitelesítésszolgáltató (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Hajtsa végre az azonos kezdeti beállítás az egyes a közvetítők (0, 1. és 2 feldolgozó csomópont).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Az egyes a feldolgozó csomópontok hajtsa végre az alábbi kódrészlet használatával az alábbi lépéseket.
    1. Hozzon létre egy keystore, és a egy új privát tanúsítványt való feltöltéséhez.
    1. Hozzon létre egy tanúsítvány-aláírási kérelmet.
    1. Szolgáltatáskapcsolódási pont a tanúsítvány-aláírási kérelmet a Hitelesítésszolgáltatótól (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Módosítsa a hitelesítésszolgáltató számítógépen, és írja alá a kapott tanúsítvány-aláírási kérelem mindegyikét:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Az önaláírt tanúsítványokat küldi vissza a feldolgozó csomópontok a hitelesítésszolgáltatótól (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. A hitelesítésszolgáltató nyilvános tanúsítványa küldése az egyes munkavégző csomópontokhoz.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. A feldolgozó csomópontokon a CAs nyilvános tanúsítvány hozzáadása a truststore és keystore. Majd adja hozzá a munkavégző csomópont saját aláírású tanúsítványt a kulcstár

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Az SSL használatát, és indítsa újra a közvetítők Kafka-konfiguráció frissítése

Ezzel mindegyik Kafka-közvetítő keystore és truststore beállítása, és importálja a megfelelő tanúsítványokat. Ezután az Ambari kapcsolódó Kafka konfiguráció tulajdonságainak módosítása, és indítsa újra a Kafka-közvetítőkhöz.

A konfigurációs módosítás végrehajtásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure HDInsight az Apache Kafka-fürt.
1. Nyissa meg az Ambari felhasználói felületén kattintva **otthoni Ambari** alatt **fürt irányítópultjai**.
1. A **Kafka-közvetítő** állítsa be a **figyelői** tulajdonság `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. A **kafka-közvetítő speciális** állítsa be a **security.inter.broker.protocol** tulajdonság `SSL`

    ![A Kafka ssl konfiguráció tulajdonságainak az Ambari szerkesztése](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. A **egyéni kafka-közvetítő** állítsa be a **ssl.client.auth** tulajdonságot `required`. Ez a lépés csak akkor szükséges, ha azt állítja be a hitelesítést és titkosítást.

    ![A kafka ssl konfiguráció tulajdonságainak az Ambari szerkesztése](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Konfigurációs tulajdonságokat adhat a Kafka `server.properties` hirdesse meg az IP-címek helyett a teljes minősített tartomány nevét (FQDN) fájlt.

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Ellenőrizze, hogy a korábbi módosításokat megfelelően, szükség esetén ellenőrizheti, hogy a következő sorokat a Kafka `server.properties` fájlt.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Indítsa újra a Kafka-közvetítők.
1. Indítsa el a felügyeleti ügyfél producer és consumer beállításokkal ellenőrizheti, hogy a létrehozóknak és a felhasználóknak a porton 9093 működik.

## <a name="client-setup-with-authentication"></a>Ügyfél telepítése (a hitelesítés)

> [!Note]
> A következő lépések szükségesek, csak akkor, ha mindkét SSL-titkosítást beállításakor **és** hitelesítést. Ha csak titkosítás beállítása, folytassa [hitelesítés nélküli ügyfél telepítése](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

A következő lépéseket az ügyfél beállításának befejezéséhez:

1. Jelentkezzen be az ügyfélszámítógép (hn1).
1. Hozzon létre egy java keystore, és egy aláírt tanúsítvány beszerzése a közvetítő. Ezután másolja a tanúsítványt a virtuális gép, amelyen a hitelesítésszolgáltató fut-e.
1. Váltson a hitelesítésszolgáltató számítógép (hn0) aláírásához az ügyféltanúsítvány.
1. Nyissa meg az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappát. Ügyfélszámítógép az aláírt tanúsítvány másolja.

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

Végül tekintse meg a fájl `client-ssl-auth.properties` paranccsal `cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

Ha már nincs szüksége a hitelesítés, a csak az SSL-titkosítást beállításának lépései a következők:

1. Jelentkezzen be az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappa
1. Ügyfélszámítógép az aláírt tanúsítvány másolja a hitelesítésszolgáltató számítógépen (wn0).
1. A Hitelesítésszolgáltatói tanúsítvány importálása a truststore
1. A kulcstár a Hitelesítésszolgáltatói tanúsítvány importálása

Ezeket a lépéseket a következő kódrészlet látható.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Végül tekintse meg a fájl `client-ssl-auth.properties` paranccsal `cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>További lépések

* [Mi az Apache Kafka on HDInsight?](apache-kafka-introduction.md)
