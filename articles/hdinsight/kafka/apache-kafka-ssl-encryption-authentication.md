---
title: Apache Kafka TLS titkosítás & hitelesítés - Azure HDInsight
description: TLS titkosítás beállítása a Kafka ügyfelek és a Kafka brókerek, valamint a Kafka brókerek közötti kommunikációhoz. Az ügyfelek SSL-hitelesítésének beállítása.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 027a66f4b83225f3c776e1bff1d706f6f4dba976
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436997"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>TLS-titkosítás és -hitelesítés beállítása az Apache Kafkához az Azure HDInsightban

Ez a cikk bemutatja, hogyan állíthatja be a Transport Layer Security (TLS) titkosítást, amelyet korábban Secure Sockets Layer (SSL) titkosításnak neveznek az Apache Kafka ügyfelek és az Apache Kafka brókerek között. Azt is bemutatja, hogyan állíthatja be az ügyfelek (más néven kétirányú TLS) hitelesítését.

> [!Important]
> A Kafka alkalmazásokhoz két ügyfél használható: egy Java-ügyfél és egy konzolügyfél. Csak a `ProducerConsumer.java` Java kliens használhatja a TLS-t mind a gyártás, mind a felhasználás során. A konzolgyártó `console-producer.sh` ügyfele nem működik a TLS-sel.

> [!Note] 
> A HDInsight Kafka konzolgyártó 1.1-es verzióval nem támogatja az SSL-t.
## <a name="apache-kafka-broker-setup"></a>Apache Kafka bróker beállítása

A Kafka TLS-bróker beállítása négy HDInsight-fürtvirtuális gépet fog használni a következő módon:

* headnode 0 - Hitelesítésszolgáltató (CA)
* 0, 1 és 2 dolgozói csomópont - brókerek

> [!Note] 
> Ez az útmutató önaláírt tanúsítványokat fog használni, de a legbiztonságosabb megoldás a megbízható hitelesítésszolgáltatók által kiadott tanúsítványok használata.

A bróker beállítási folyamatának összegzése a következő:

1. A következő lépések ismétlődnek a három munkavégző csomópont mindegyikén:

    1. Állítson elő egy tanúsítványt.
    1. Hozzon létre egy tanúsítvány aláírási kérelmet.
    1. Küldje el a tanúsítványaláíró kérelmet a hitelesítésszolgáltatónak.
    1. Jelentkezzen be a hitelesítéshez, és írja alá a kérést.
    1. SCP az aláírt tanúsítványt vissza a munkavégző csomópontra.
    1. SCP a hitelesítésszolgáltató nyilvános tanúsítványa a munkavégző csomópontnak.

1. Miután az összes tanúsítványt, tedd a certs a cert store.
1. Menjen az Ambari-hoz, és módosítsa a konfigurációkat.

A bróker beállításának befejezéséhez kövesse az alábbi részletes utasításokat:

> [!Important]
> A következő kódrészletekben a wnX a három munkavégző csomópont egyikének rövidítése, és `wn0` `wn1` a, vagy `wn2` adott esetben helyettesítendő. `WorkerNode0_Name`és `HeadNode0_Name` fel kell helyettesíteni az adott gépek nevével.

1. Végezze el a kezdeti beállítást a 0-as főcsomóponton, amely a HDInsight számára betölti a hitelesítésszolgáltató (CA) szerepét.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Hajtsa végre ugyanazt a kezdeti beállítást az egyes brókerek (munkavégző csomópontok 0, 1 és 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. A munkavégző csomópontok mindegyikén hajtsa végre a következő lépéseket az alábbi kódrészlet használatával.
    1. Hozzon létre egy kulcstárolót, és népesítse be egy új privát tanúsítvánnyal.
    1. Hozzon létre egy tanúsítvány-aláíró kérelmet.
    1. SCP a tanúsítvány aláíró kérelmet a hitelesítésszolgáltató (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. A hitelesítésaszámítógépen futtassa a következő parancsot a hitelesítés- és hitelesítésfájl-fájlok létrehozásához:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Váltás a hitelesítéshez való hozzárendelési gépre, és az összes fogadott tanúsítványaláírási kérelem aláírása:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Az aláírt tanúsítványok visszaküldése a dolgozói csomópontoka a hitelesítésszolgáltató (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Küldje el a hitelesítésszolgáltató nyilvános tanúsítványát minden feldolgozócsomópontnak.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Minden munkavégző csomóponton adja hozzá a hitelesítésszolgáltatói nyilvános tanúsítványt a megbízhatósági tárhoz és a kulcstárolóhoz. Ezután adja hozzá a munkavégző csomópont saját aláírt tanúsítványát a kulcstárolóhoz

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>A Kafka-konfiguráció frissítése a TLS használatához és a brókerek újraindítása

Most már beállította az egyes Kafka-brókereket egy keystore-szal és megbízhatósági tárolóval, és importálta a megfelelő tanúsítványokat. Ezután módosítsa a kapcsolódó Kafka konfigurációs tulajdonságokat az Ambari használatával, majd indítsa újra a Kafka brókereket.

A konfigurációs módosítás végrehajtásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure HDInsight Apache Kafka-fürtöt.
1. Nyissa meg az Ambari felhasználói felületét, kattintson az **Ambari home** elemre **a Fürt irányítópultjai**csoportban.
1. Alatt **Kafka Broker** állítsa be a **hallgatók** ingatlan`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Az **Advanced kafka-broker** csoportban állítsa a **security.inter.broker.protocol** tulajdonságot`SSL`

    ![A Kafka ssl konfigurációs tulajdonságainak szerkesztése az Ambariban](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Az **Egyéni kafka-broker** csoportban állítsa az **ssl.client.auth** tulajdonságot a számára. `required` Ez a lépés csak akkor szükséges, ha hitelesítést és titkosítást állít be.

    ![Kafka ssl konfigurációs tulajdonságainak szerkesztése ambariban](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Új konfigurációs tulajdonságok hozzáadása a kiszolgáló.properties fájlhoz.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
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

1. Nyissa meg az Ambari konfigurációs felhasználói felületet, és ellenőrizze, hogy az új tulajdonságok megjelennek-e az **Advanced kafka-env** és a **kafka-env template** tulajdonság alatt.

    HDI 3.6-os verzió esetén:

    ![Kafka-env sablontulajdonság szerkesztése Ambariban](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    HDI 4.0-s verzió esetén:

     ![Kafka-env sablontulajdonság szerkesztése az Ambari 4-ben](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Indítsa újra az összes Kafka brókerek.
1. Indítsa el az admin ügyfél gyártói és fogyasztói lehetőségeket annak ellenőrzésére, hogy mind a gyártók, mind a fogyasztók dolgoznak-e a 9093-as porton.

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

Ha nincs szüksége hitelesítésre, a csak a TLS-titkosítás beállításának lépései a következők:

1. Jelentkezzen be a hitelesítésszolgáltatásba (aktív főcsomópont).
1. Másolja a hitelesítésatanúsítványt az ügyfélgépre a hitelesítés-hitelesítési gépről (wn0).
1. Jelentkezzen be az ügyfélgépre (hn1), `~/ssl` és keresse meg a mappát.
1. Importálja a hitelesítéshitelesítési tanúsítványt a megbízhatósági tárolóba.
1. Importálja a hitelesítéshitelesítési tanúsítványt a kulcstárolóba.

Ezeket a lépéseket az alábbi kódrészletek részletezik.

1. Jelentkezzen be a hitelesítésterület-csomópontba.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. A ca-cert másolása az ügyfélgépre

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Jelentkezzen be az ügyfélgépre (készenléti főcsomópont).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importálja a hitelesítésszolgáltatói tanúsítványt a megbízhatósági tárolóba.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importálja a hitelesítéshitelesítési tanúsítványt a kulcstárolóba.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Hozza létre `client-ssl-auth.properties`a fájlt . Meg kell a következő sorokat:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Ügyfél beállítása (hitelesítéssel)

> [!Note]
> A következő lépések re van szükség, ha a TLS-titkosítást **és** a hitelesítést is beállítja. Ha csak titkosítást állít be, olvassa el [az Ügyfél hitelesítés nélküli beállítása](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)című témakört.

A következő négy lépés az ügyfél beállításának befejezéséhez szükséges feladatokat foglalja össze:

1. Jelentkezzen be az ügyfélgépre (készenléti főcsomópont).
1. Hozzon létre egy java keystore, és kap egy aláírt tanúsítványt a bróker. Ezután másolja a tanúsítványt a virtuális gépre, ahol a hitelesítésszolgáltató fut.
1. Váltson át a hitelesítésszolgáltató imasgépre (aktív főcsomópont) az ügyféltanúsítvány aláírásához.
1. Nyissa meg az ügyfélgépet (készenléti főcsomópont), `~/ssl` és keresse meg a mappát. Másolja az aláírt tanúsítványt az ügyfélgépre.

Az egyes lépcsők részleteit az alábbiakban találja.

1. Jelentkezzen be az ügyfélgépre (készenléti főcsomópont).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Távolítsa el a meglévő Ssl könyvtárat.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Hozzon létre egy java keystore és hozzon létre egy tanúsítvány-aláírási kérelmet. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. A tanúsítványaláírási kérelem másolása a hitelesítésszolgáltatóhoz

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Váltson át a hitelesítésszolgáltató imasgépre (aktív főcsomópont), és írja alá az ügyféltanúsítványt.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Az aláírt ügyféltanúsítvány másolása a hitelesítésszolgáltatásból (aktív főcsomópont) az ügyfélgépre.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. A ca-cert másolása az ügyfélgépre

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Ügyféltároló létrehozása aláírt tanúsítványlal, és importálhatja a hitelesítési tanúsítványt a kulcstárolóba és a megbízhatósági tárolóba:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Hozzon `client-ssl-auth.properties`létre egy fájlt . Meg kell a következő sorokat:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Ellenőrzés

> [!Note]
> Ha a HDInsight 4.0 és a Kafka 2.1 telepítve van, a konzol gyártójával/fogyasztóival ellenőrizheti a beállítást. Ha nem, futtassa a Kafka gyártót a 9092-es porton, és küldjön üzeneteket a témának, majd használja a Kafka fogyasztót a 9093-as porton, amely a TLS-t használja.

### <a name="kafka-21-or-above"></a>Kafka 2.1 vagy újabb

1. Hozzon létre egy témakört, ha még nem létezik.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Indítsa el a konzolelőproducert, `client-ssl-auth.properties` és adja meg az elérési utat a gyártó konfigurációs fájljaként.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Nyisson meg egy másik ssh kapcsolatot az ügyfélszámítógéppel, és indítsa el a konzolfogyasztót, `client-ssl-auth.properties` és adja meg az elérési utat konfigurációs fájlként az ügyfél számára.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Hozzon létre egy témakört, ha még nem létezik.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Indítsa el a konzolelőproducert, és adja meg az ügyfél-ssl-auth.properties elérési útját a gyártó konfigurációs fájljaként.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Nyisson meg egy másik ssh kapcsolatot az ügyfélszámítógéppel, és indítsa el a konzolfogyasztót, `client-ssl-auth.properties` és adja meg az elérési utat konfigurációs fájlként az ügyfél számára.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>További lépések

* [Mi az a HDInsight-alapú Apache Kafka?](apache-kafka-introduction.md)
