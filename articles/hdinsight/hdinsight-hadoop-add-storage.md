---
title: További Azure storage-fiókok hozzáadása HDInsight |} Microsoft Docs
description: Ismerje meg, hogy további Azure storage-fiókok hozzáadása egy meglévő HDInsight-fürtre.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 569a41dfdbf2f9d911e67f283f413130ba7e1f79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight további storage-fiókok hozzáadása

A Parancsfájlműveletek segítségével további Azure storage-fiókok hozzáadása HDInsight útmutató. A jelen dokumentumban leírt lépések tárfiók hozzáadása egy meglévő Linux-alapú HDInsight-fürthöz.

> [!IMPORTANT]
> A jelen dokumentumban szereplő információk további tárhely hozzáadása a fürt létrehozása után van. A storage-fiókok hozzáadása a fürt létrehozása során további információkért lásd: [állítsa be a HDInsight Hadoop, Spark, Kafka és több fürt](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Működés

Ezt a parancsfájlt a következő paramétereket fogadja:

* __Az Azure storage-fiók neve__: a HDInsight-fürt hozzáadása a tárfiók nevét. A parancsfájl futtatása után HDInsight olvashat és írhat adatokat tárolja ezt a tárfiókot.

* __Azure storage-fiók kulcs__: A kulcs, amely hozzáférést biztosít a tárfiókhoz.

* __-p__ (nem kötelező): Ha meg van adva, a kulcs nem titkosított, és egyszerű szövegként a core-site.xml fájlban tárolja.

A feldolgozás során a parancsfájl a következő műveleteket hajtja végre:

* Ha a tárfiók már létezik a core-site.xml konfigurációban a fürt számára, a parancsfájl kilép, és nincs több teendő történik.

* Ellenőrzi, hogy a storage-fiók létezik-e, és a kulcs segítségével férhetők el.

* Titkosítja a kulcsot, a fürt hitelesítő adatokkal.

* A storage-fiók hozzáadása a core-site.xml fájl módosítása.

* Leállítja és újraindítja a Oozie, YARN, MapReduce2 és HDFS szolgáltatásokat. Ezek a szolgáltatások indítása és leállítása lehetővé teszi az új tárfiókot használja.

> [!WARNING]
> A storage-fiók egy másik helyen, mint a HDInsight-fürt használata nem támogatott.

## <a name="the-script"></a>A parancsfájl

__Parancsfájl-hely__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Követelmények__:

* A parancsfájl azon kell alkalmazni a __Átjárócsomópontokat__.

## <a name="to-use-the-script"></a>A parancsfájl használata

Ez a parancsfájl az Azure portál, Azure PowerShell vagy az Azure CLI 1.0 használható. További információkért lásd: a [testreszabása Linux-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentum.

> [!IMPORTANT]
> A testreszabási dokumentumban leírt lépések használata esetén olvassa el következő alkalmazni ezt a parancsfájlt:
>
> * A példa parancsfájlművelet URI cserélje le a parancsfájl URI-JÁNAK (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * A példa paramétereket cserélje le a az Azure storage-fiók nevét és a kulcsot a tárfiók hozzá kell adni a fürthöz. Az Azure portál használatával, ha ezeket a paramétereket szóközzel kell elválasztani.
> * Nem kell megjelölni a parancsprogramot __megőrzött__, ahogy azt közvetlenül frissíti az Ambari konfigurációját a fürt számára.

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Nem jelenik meg az Azure portálon vagy az eszközök a Storage-fiókok

A HDInsight-fürthöz az Azure portálon megtekintésekor kiválasztása a __Tárfiókok__ bejegyzésre az __tulajdonságok__ nem jelennek meg a parancsfájlművelet hozzáadva storage-fiókok. Az Azure PowerShell és az Azure parancssori felület ne jelenjen meg a további tárfiók vagy.

A tárolással kapcsolatos nem jelenik meg, mert a parancsfájl csak módosítja a core-site.xml konfigurációját a fürtben. Ezeket az információkat nem használja az Azure felügyeleti API-k használata a fürt-adatok beolvasása.

A parancsfájl a fürthöz hozzáadott tárfiókadatok megtekintéséhez használja az Ambari REST API-t. Az alábbi parancsokat használja ezt az információt talál a fürt beolvasásához:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Állítsa be `$clusterName` a HDInsight-fürt nevére. Állítsa be `$storageAccountName` a tárfiók nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazda) és a jelszót.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Állítsa be `$PASSWORD` az a fürt (rendszergazda) bejelentkezési fiók jelszavát. Állítsa be `$CLUSTERNAME` a HDInsight-fürt nevére. Állítsa be `$STORAGEACCOUNTNAME` a tárfiók nevére.
>
> Ez a példa [curl (http://curl.haxx.se/) ](http://curl.haxx.se/) és [jq (https://stedolan.github.io/jq/) ](https://stedolan.github.io/jq/) beolvasása és elemzése JSON-adatokat.

Ha ezzel a paranccsal cserélje le __CLUSTERNAME__ a HDInsight-fürt nevét. Cserélje le __jelszó__ a fürt HTTP bejelentkezési jelszavával. Cserélje le __STORAGEACCOUNT__ parancsfájlművelet segítségével adhatók hozzá a tárfiók nevével. Ez a parancs által visszaadott információ jelenik meg az alábbihoz hasonló:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ez a szöveg egy titkosított kulcsot, amely a tárfiók eléréséhez használt példája.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem érhető el tárhely kulcs módosítása után

Ha módosítja a tárfiók kulcsa, HDInsight már nem tud hozzáférni a tárfiókhoz. HDInsight a core-site.xml kulcs gyorsítótárazott másolatának a fürt használja. A gyorsítótárban található példányát frissíteni kell, hogy meg az új.

A parancsfájlművelet újra fut does __nem__ frissíti a kulcsot, a parancsfájl ellenőrzi, hogy ha egy bejegyzést a tárfiók már létezik. Ha már létezik egy bejegyzést, tegye a módosításokat.

Ez a probléma megoldása érdekében el kell távolítania a meglévő bejegyzést a tárfiók. Az alábbi lépések segítségével távolítsa el a meglévő bejegyzést:

1. Egy webböngészőben nyissa meg az Ambari webes felhasználói felülete a HDInsight-fürthöz. A megadott URI azonosító https://CLUSTERNAME.azurehdinsight.net. Cserélje le a __CLUSTERNAME__ elemet a fürt nevére.

    Amikor a rendszer kéri, adja meg a bejelentkezési felhasználói HTTP és a jelszót a fürt számára.

2. Válassza ki a listáról a lap bal oldalon található szolgáltatások __HDFS__. Válassza ki a __Configs__ fülre az oldal közepére.

3. Az a __szűrő...__  mezőbe írja be az érték __fs.azure.account__. Ez visszaad minden további tárfiókok a fürthöz hozzáadott bejegyzéseket. Két különböző bejegyzések; __keyprovider__ és __kulcs__. Mindkettő rendelkezik a kulcsnév részeként a tárfiók nevét.

    A bejegyzések a következők példa egy nevű tárfiók __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Miután azonosította a kulcsokat a tárfiók, el kell távolítania, használja a vörös "-" jobb oldalán a bejegyzés törli-e ikon. Ezután a __mentése__ gombra a módosítások mentéséhez.

5. Miután menti, a parancsfájl művelettel új kulcs értékét és a tárolási fiók hozzáadása a fürthöz.

### <a name="poor-performance"></a>Gyenge teljesítményt

Ha a tárfiók egy másik régióban, mint a HDInsight-fürthöz, gyenge teljesítményt tapasztalhat. Egy másik régióban található adatokhoz hozzáférő elküldi a hálózati forgalom, a regionális Azure adatközponton kívül és vethet fel várakozási ideje a nyilvános interneten keresztül történő.

> [!WARNING]
> A storage-fiók egy másik régióban, mint a HDInsight-fürt használata nem támogatott.

### <a name="additional-charges"></a>További díjak

Ha a tárfiók egy másik régióban, mint a HDInsight-fürthöz, további kilépő díjak Észreveheti az Azure számlázás a. Egy kimenő kell fizetni akkor érvényes, ha az adatok egy regionális adatközpont hagyja. Ez a díj lesz alkalmazva, akkor is, ha a forgalom egy másik Azure-adatközpont egy másik régióban szánt.

> [!WARNING]
> A storage-fiók egy másik régióban, mint a HDInsight-fürt használata nem támogatott.

## <a name="next-steps"></a>További lépések

Megtanulhatta, további tárfiókok hozzáadásáról meglévő HDInsight-fürtre. A Parancsfájlműveletek további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök parancsfájlművelet használatával](hdinsight-hadoop-customize-cluster-linux.md)
