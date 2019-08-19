---
title: Az Azure HDInsight HDFS hibáinak megoldása
description: Választ kaphat a HDFS és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1b244b3d402d7896ade2fa2e84881a0079ef6bba
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515642"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Apache Hadoop HDFS hibáinak megoldása az Azure HDInsight használatával

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor a Hadoop elosztott fájlrendszer (HDFS) hasznos adattartalommal dolgozik az Apache Ambari-ban.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Hogyan hozzáférni a helyi HDFS egy fürtön belülről?

### <a name="issue"></a>Probléma

A helyi HDFS a parancssorból és az alkalmazás kódjából a HDInsight-fürtön belüli Azure Blob Storage vagy Azure Data Lake Storage használatával érheti el.   

### <a name="resolution-steps"></a>A megoldás lépései

1. A parancssorban a szó szerint `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` használja a következő parancsot:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. A forráskódból használja az URI `hdfs://mycluster/` -t a szó szoros értelmében, ahogy az alábbi példában is látható:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Futtassa a lefordított. jar-fájlt (például egy nevű `java-unit-tests-1.0.jar`fájlt) a HDInsight-fürtön a következő paranccsal:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.