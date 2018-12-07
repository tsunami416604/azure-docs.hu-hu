---
title: A HDInsight további Azure storage-fiókok hozzáadása
description: Ismerje meg, további Azure storage-fiókok hozzáadása egy meglévő HDInsight-fürthöz.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a75514013a1945d9ca5718be115184f6ba9950d9
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015755"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>A HDInsight további tárfiókok hozzáadása

Ismerje meg, hogy parancsfájlműveletekkel további Azure storage-fiókok hozzáadása a HDInsight használatával. A jelen dokumentumban leírt lépések tárfiók hozzáadása egy meglévő Linux-alapú HDInsight-fürtön.

> [!IMPORTANT]
> A jelen dokumentumban lévő információk további tárhely hozzáadása egy fürt létrehozása után van. Információk a storage-fiókok hozzáadása a fürt létrehozása során: [fürtök beállítása a HDInsight az Apache Hadoop, az Apache Spark, az Apache Kafka és további](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Működés

Ez a szkript a következő paramétereket fogadja:

* __Az Azure storage-fiók neve__: a HDInsight-fürt hozzáadása a tárfiók nevére. A szkript futtatása után HDInsight olvashat és írhat a tárfiókban tárolt adatokat.

* __Az Azure storage-fiókkulcs__: egy kulcsot, amely engedélyezi a hozzáférést a tárfiókhoz.

* __-p__ (nem kötelező): Ha meg van adva, a kulcs nem titkosított, és egyszerű szövegként a core-site.xml fájlban tárolja.

A feldolgozás során a szkript a következő műveleteket hajtja végre:

* A tárfiók már létezik a core-site.xml a fürt konfigurációját, ha a parancsfájl kilép, és nincs több teendő történik.

* Ellenőrzi, hogy a tárfiók létezik, és a kulcs segítségével érhetők el.

* Titkosítja a kulcsot, a fürt hitelesítő adatok használatával.

* A storage-fiók hozzáadása a core-site.xml fájlt.

* Leállítja és újraindítja a [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), és [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) szolgáltatások. Ezek a szolgáltatások indítása és leállítása lehetővé teszi, hogy az új tárfiókot használni őket.

> [!WARNING]
> A HDInsight-fürt, mint egy másik helyen lévő tárfiókok használata nem támogatott.

## <a name="the-script"></a>A parancsfájl

__Parancsfájl helye__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Követelmények__:

* A parancsfájl a alkalmazni kell a __Átjárócsomópontokhoz__.

## <a name="to-use-the-script"></a>A parancsfájl használata

Ez a szkript az Azure Portalon, az Azure PowerShell vagy az Azure klasszikus parancssori felület is használható. További információkért lásd: a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentumot.

> [!IMPORTANT]
> A Testreszabás dokumentumban leírt lépések használata esetén használja a következő információkat a alkalmazni ezt a parancsfájlt:
>
> * Bármely példa parancsfájlművelet URI cserélje le az URI-t, a szkript (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Cserélje le a példában paramétereket az Azure storage-fiók nevét és a fürthöz hozzáadni kívánt a tárfiók kulcsára. Az Azure portal használatával, ha ezeket a paramétereket szóközzel kell elválasztani.
> * Nem kell megjelölni, ez a szkript __megőrzött__, ahogy azt közvetlenül frissíti a fürt Ambari konfigurációját.

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Storage-fiókok nem jelenik meg az Azure Portalon vagy az eszközök

A HDInsight-fürt az Azure Portalon megtekintésekor kiválasztása a __Tárfiókok__ bejegyzés alatt __tulajdonságok__ nem jelennek meg a parancsprogram-művelet során hozzáadott tárfiókok. Az Azure PowerShell és az Azure klasszikus parancssori felület nem jelennek meg a további tárfiókot vagy.

A storage-adatokat nem jelenik meg, mert a parancsfájl csak módosítja a core-site.xml a fürt konfigurációját. Ezeket az információkat nem használja az Azure felügyeleti API-k használatával fürtinformációkat lekérésekor.

Az Ambari REST API használatával hozzá a fürthöz, a parancsfájl tárfiók-információ megtekintéséhez. A következő parancsok használatával lekérheti a fürt ezt az információt:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Állítsa be `$clusterName` a HDInsight-fürt nevére. Állítsa be `$storageAccountName` a tárfiók nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) és a jelszót.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Állítsa be `$PASSWORD` a fürt bejelentkezési (rendszergazdai) fiókjelszóra. Állítsa be `$CLUSTERNAME` a HDInsight-fürt nevére. Állítsa be `$STORAGEACCOUNTNAME` a tárfiók nevére.
>
> Ez a példa [curl (https://curl.haxx.se/) ](https://curl.haxx.se/) és [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) lekérésére és JSON-adatok elemzése.

Ez a parancs használata esetén cserélje le a __CLUSTERNAME__ a HDInsight-fürt nevére. Cserélje le __jelszó__ a fürt bejelentkezési jelszavával HTTP. Cserélje le __STORAGEACCOUNT__ szkriptműveletekkel hozzáadva a tárfiók nevére. Ez a parancs által visszaadott információ jelenik meg az alábbi szöveghez hasonló:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ez a szöveg, amelyek egy titkosított kulcs, amely a tárfiók eléréséhez használatos.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem fér hozzá a tárolási kulcs módosítása után

Ha módosítja a tárfiók kulcsát, HDInsight már nem tud hozzáférni a tárfiókhoz. HDInsight a core-site.xml kulcs gyorsítótárazott másolatának a fürt használja. Ezt a gyorsítótárazott példányt frissíteni kell, hogy az új kulcs egyezik.

Parancsfájl művelet ismételt futtatása does __nem__ frissíteni a kulcsot, mert a parancsfájl ellenőrzi, hogy a storage-fiókhoz tartozó bejegyzés már létezik. Ha már létezik egy bejegyzést, azt nem végezze el a módosításokat.

A probléma megkerüléséhez, el kell távolítania a meglévő bejegyzést a tárfiókhoz. Az alábbi lépések segítségével, meglévő bejegyzés eltávolításához:

1. Egy webböngészőben nyissa meg az Ambari webes felhasználói Felületet, a HDInsight-fürt számára. Az URI-ja https://CLUSTERNAME.azurehdinsight.net. Cserélje le a __CLUSTERNAME__ elemet a fürt nevére.

    Amikor a rendszer kéri, adja meg a HTTP-bejelentkezési felhasználónév és jelszó a fürt számára.

2. Az oldal bal oldalán a szolgáltatások listájából válassza ki a __HDFS__. Válassza ki a __Configs__ fülre az oldal közepén.

3. Az a __szűrése...__  mezőben adja meg a egy értéke __fs.azure.account__. Ez a lettek hozzáadva a fürt minden további tárfiókok bejegyzést ad vissza. Két típusú bejegyzéseket; __keyprovider__ és __kulcs__. A kulcsnév részeként a tárfiók nevét is tartalmaz.

    A következő nevű tárfiókok példa tételt __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Miután azonosította, hogy a kulcsokat a tárfiók, el kell távolítania, használja a piros "-" ikont jobb oldalán a bejegyzés törli-e. Ezután a __mentése__ gombra a módosítások mentéséhez.

5. Módosítások mentése után a parancsfájl művelettel a storage-fiók és az új kulcs-érték hozzáadása a fürthöz.

### <a name="poor-performance"></a>Gyenge teljesítményt

Ha a tárfiók más régióban, mint a HDInsight-fürt, gyenge teljesítményt tapasztalhat. Egy másik régióban lévő adatok elérése elküldi a hálózati forgalom, a regionális Azure adatközponton kívül és késés megjelentetni nyilvános interneten keresztül.

> [!WARNING]
> Storage-fiók egy másik régióban, mint a HDInsight-fürt használata nem támogatott.

### <a name="additional-charges"></a>További díjak

Ha a tárfiók más régióban, mint a HDInsight-fürt, az Azure számlázását a előfordulhat, hogy figyelje meg a további kimenő forgalom költségeit. Egy kimenő forgalmi díjat adatok kikerül egy regionális adatközpont esetén is alkalmazva lesz. A díj akkor is, ha a forgalom egy másik Azure-adatközpontban egy másik régióban lévő szánt van alkalmazva.

> [!WARNING]
> Storage-fiók egy másik régióban, mint a HDInsight-fürt használata nem támogatott.

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan adhat hozzá további tárfiókok meglévő HDInsight-fürtre. Parancsfájlműveletekkel további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
