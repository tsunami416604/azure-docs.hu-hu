---
title: Az SSL-titkosítás és hitelesítés az Azure HDInsight az Apache Kafka beállítása
description: A telepítő a Kafka-ügyfelek és a Kafka-közvetítők is közötti érvénnyel a Kafka-közvetítőkhöz közötti kommunikáció SSL-titkosítást. A telepítő ügyfél SSL-hitelesítést.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355656"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Secure Sockets Layer (SSL)-titkosítás és az Azure HDInsight-beli Apache kafka hitelesítés beállítása

Ez a cikk azt ismerteti, hogyan állíthatja be az Apache Kafka-ügyfelektől érkező SSL-titkosítást az Apache Kafka-közvetítőkhöz közötti, valamint az Apache Kafka-közvetítőkhöz. Állítsa be a hitelesítést (más néven kétirányú SSL) ügyfelek szükséges lépéseket is biztosít.

## <a name="server-setup"></a>Kiszolgáló beállítása

Az első lépéseként a kulcstárhoz és a Kafka-közvetítőkhöz a truststore, majd a hitelesítésszolgáltató (CA) és a közvetítő tanúsítványok importálása ezekkel az áruházakkal.

> [!Note] 
> Ez az útmutató fogja használni önaláírt tanúsítványokat, de a legbiztonságosabb megoldás, ha megbízható hitelesítésszolgáltatók által kibocsátott tanúsítványokat használ.

1. Hozzon létre egy ssl nevű mappát, és exportálja a kiszolgáló jelszava környezeti változóban. A jelen útmutató fennmaradó részében, csere `<server_password>` a kiszolgáló tényleges rendszergazdai jelszóval.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Ezután hozzon létre egy java keystore (kafka.server.keystore.jks) és a egy Hitelesítésszolgáltatói tanúsítvány.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Ezután hozzon létre a tanúsítvány a hitelesítésszolgáltató által aláírt az előző lépésben létrehozott aláírási kérelmet.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Most az aláírási kérést küldhet a hitelesítésszolgáltató, és az aláírt tanúsítvány beszerzése. Mert önaláírt tanúsítványt használ, hogy aláírja a tanúsítványt a hitelesítésszolgáltató használatával a `openssl` parancsot.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Hozzon létre egy megbízhatósági tároló, és importálja a tanúsítványt a CA.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. A kulcstár nyilvános hitelesítésszolgáltató tanúsítvány importálása.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. A kulcstár az aláírt tanúsítvány importálása.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Az aláírt tanúsítvány importálása a kulcstár az utolsó lépés egy Kafka-közvetítő truststore és keystore konfigurálása szükséges.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Az SSL használatát, és indítsa újra a közvetítők konfigurációjának frissítése

A telepítő minden egyes Kafka keystore és truststore közvetítse, és importálja a megfelelő tanúsítványok van.  Ezután az Ambari kapcsolódó Kafka konfiguráció tulajdonságainak módosítása, és indítsa újra a Kafka-közvetítőkhöz.

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure HDInsight az Apache Kafka-fürt.
1. Nyissa meg az Ambari felhasználói felületén kattintva **otthoni Ambari** alatt **fürt irányítópultjai**.
1. A **Kafka-közvetítő** állítsa be a **figyelői** tulajdonság `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. A **kafka-közvetítő speciális** állítsa be a **security.inter.broker.protocol** tulajdonság `SSL`

    ![A kafka ssl konfiguráció tulajdonságainak az Ambari szerkesztése](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. A **egyéni kafka-közvetítő** állítsa be a **ssl.client.auth** tulajdonságot `required`. Ez a lépés csak akkor szükséges, ha állítja be a hitelesítést, valamint a titkosítást.

    ![A kafka ssl konfiguráció tulajdonságainak az Ambari szerkesztése](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Konfigurációs tulajdonságokat adhat a Kafka `server.properties` hirdesse meg az IP-címek helyett a teljes minősített tartomány nevét (FQDN) fájlt.

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Ellenőrizze, hogy a korábbi módosításokat megfelelően, szükség esetén ellenőrizheti, hogy a következő sorokat a Kafka `server.properties` fájlt.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Indítsa újra a Kafka-közvetítők.

## <a name="client-setup-with-authentication"></a>Ügyfél telepítése (a hitelesítés)

> [!Note]
> A következő lépések szükségesek, csak akkor, ha mindkét SSL-titkosítást beállításakor **és** hitelesítést. Ha csak titkosítás beállítása, folytassa [hitelesítés nélküli ügyfél telepítése](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. Az ügyfél jelszó exportálása. Cserélje le `<client_password>` a tényleges rendszergazdai jelszóval, a Kafka-ügyfélszámítógépen.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Hozzon létre egy java keystore, és egy aláírt tanúsítvány beszerzése a közvetítő. Ezután másolja a tanúsítványt a virtuális gép, amelyen a hitelesítésszolgáltató fut-e.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Váltson a hitelesítésszolgáltató számítógép (wn0) aláírásához az ügyféltanúsítvány.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Nyissa meg az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappát. Ügyfélszámítógép az aláírt tanúsítvány másolja.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. A fájl megtekintéséhez `client-ssl-auth.properties` paranccsal `$cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

1. Az ügyfél jelszó exportálása. Cserélje le `<client_password>` a tényleges rendszergazdai jelszóval, a Kafka-ügyfélszámítógépen.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Nyissa meg az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappát. Ügyfélszámítógép az aláírt tanúsítvány másolja.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. A fájl megtekintéséhez `client-ssl-auth.properties` paranccsal `$cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>További lépések

* [Mi az Apache Kafka on HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)