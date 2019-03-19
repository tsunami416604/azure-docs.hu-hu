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
ms.openlocfilehash: cd850808f656b211bf1fdbfd2fe7c5799150b030
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839661"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Secure Sockets Layer (SSL)-titkosítás és az Azure HDInsight-beli Apache kafka hitelesítés beállítása

Ez a cikk bemutatja, hogyan állíthatja be az Apache Kafka-ügyfelek és az Apache Kafka-közvetítőkhöz közötti SSL-titkosítást. Állítsa be a hitelesítést (más néven kétirányú SSL) ügyfelek szükséges lépéseket is biztosít.

## <a name="server-setup"></a>Kiszolgáló beállítása

Az első lépéseként készítse el keystore és truststore mindegyik Kafka-közvetítő. Miután jön létre, a hitelesítésszolgáltató (CA) és a közvetítő tanúsítványok importálása ezekkel az áruházakkal.

> [!Note] 
> Ez az útmutató fogja használni önaláírt tanúsítványokat, de a legbiztonságosabb megoldás, ha megbízható hitelesítésszolgáltatók által kibocsátott tanúsítványokat használ.

Tegye a következőket a kiszolgáló telepítés befejezéséhez:

1. Hozzon létre egy ssl nevű mappát, és exportálja a kiszolgáló jelszava környezeti változóban. A jelen útmutató fennmaradó részében, csere `<server_password>` a kiszolgáló tényleges rendszergazdai jelszóval.
1. Ezután hozzon létre egy java keystore (kafka.server.keystore.jks) és a egy Hitelesítésszolgáltatói tanúsítvány.
1. Ezután hozzon létre a tanúsítvány a hitelesítésszolgáltató által aláírt az előző lépésben létrehozott aláírási kérelmet.
1. Most az aláírási kérést küldhet a hitelesítésszolgáltató, és az aláírt tanúsítvány beszerzése. Mert önaláírt tanúsítványt használ, hogy aláírja a tanúsítványt a hitelesítésszolgáltató használatával a `openssl` parancsot.
1. Hozzon létre egy megbízhatósági tároló, és importálja a tanúsítványt a CA.
1. A kulcstár nyilvános hitelesítésszolgáltató tanúsítvány importálása.
1. A kulcstár az aláírt tanúsítvány importálása.

A parancsok a lépések elvégzéséhez a következő kódrészlet látható.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

Az aláírt tanúsítvány importálása a kulcstár az utolsó lépés egy Kafka-közvetítő truststore és keystore konfigurálása szükséges.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Az SSL használatát, és indítsa újra a közvetítők Kafka-konfiguráció frissítése

Most telepítő minden egyes Kafka keystore és truststore közvetítse, és a megfelelő tanúsítványok importálva van.  Ezután az Ambari kapcsolódó Kafka konfiguráció tulajdonságainak módosítása, és indítsa újra a Kafka-közvetítőkhöz. 

A konfigurációs módosítás végrehajtásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure HDInsight az Apache Kafka-fürt.
1. Nyissa meg az Ambari felhasználói felületén kattintva **otthoni Ambari** alatt **fürt irányítópultjai**.
1. A **Kafka-közvetítő** állítsa be a **figyelői** tulajdonság `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. A **kafka-közvetítő speciális** állítsa be a **security.inter.broker.protocol** tulajdonság `SSL`

    ![A Kafka ssl konfiguráció tulajdonságainak az Ambari szerkesztése](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

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

Kövesse az alábbi lépéseket az ügyfél a telepítés végrehajtásához:

1. Jelentkezzen be az ügyfélszámítógép (hn1).
1. Az ügyfél jelszó exportálása. Cserélje le `<client_password>` a tényleges rendszergazdai jelszóval, a Kafka-ügyfélszámítógépen.
1. Hozzon létre egy java keystore, és egy aláírt tanúsítvány beszerzése a közvetítő. Ezután másolja a tanúsítványt a virtuális gép, amelyen a hitelesítésszolgáltató fut-e.
1. Váltson a hitelesítésszolgáltató számítógép (wn0) aláírásához az ügyféltanúsítvány.
1. Nyissa meg az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappát. Ügyfélszámítógép az aláírt tanúsítvány másolja.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Végül tekintse meg a fájl `client-ssl-auth.properties` paranccsal `cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Ügyfél beállítása (hitelesítés nélkül)

Ha már nincs szüksége a hitelesítés, a lépések csak az SSL-titkosítás beállítása a következők:

1. Jelentkezzen be az ügyfélszámítógép (hn1), és keresse meg a `~/ssl` mappa
1. Az ügyfél jelszó exportálása. Cserélje le `<client_password>` a tényleges rendszergazdai jelszóval, a Kafka-ügyfélszámítógépen.
1. Ügyfélszámítógép az aláírt tanúsítvány másolja a hitelesítésszolgáltató számítógépen (wn0).
1. A Hitelesítésszolgáltatói tanúsítvány importálása a truststore
1. A kulcstár a Hitelesítésszolgáltatói tanúsítvány importálása

Ezeket a lépéseket a következő kódrészlet látható.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Végül tekintse meg a fájl `client-ssl-auth.properties` paranccsal `cat client-ssl-auth.properties`. A következő sorokat kell rendelkeznie:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>További lépések

* [Mi az Apache Kafka on HDInsight?](apache-kafka-introduction.md)
