---
title: "Megosztott hozzáférési aláírásokkal - Azure HDInsight-hozzáférés korlátozása |} Microsoft Docs"
description: "Ismerje meg megosztott hozzáférési aláírásokkal használata a HDInsight-hozzáférés korlátozása az Azure storage blobs szolgáltatásban tárolt adatokat."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2017
ms.author: larryfr
ms.openlocfilehash: 92ad526d034591b8f463ef6b01e115101b74e1ae
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Azure Storage megosztott hozzáférési aláírásokkal segítségével adatokat a hdinsight eszközben való hozzáférés korlátozása

HDInsight a fürthöz tartozó Azure Storage-fiókokat az adatok teljes hozzáféréssel rendelkezik. A blob tárolóra megosztott hozzáférési aláírásokkal használatával korlátozza a hozzáférést az adatokat. Például írásvédett hozzáférést biztosít az adatokat. Megosztott hozzáférési aláírásokkal (SAS) az Azure storage-fiókok egy szolgáltatása, amely lehetővé teszi az adatokhoz való hozzáférés korlátozása. Például az adatok csak olvasható hozzáférést biztosító.

> [!IMPORTANT]
> Apache Pletyka használó megoldás érdemes a HDInsight-tartományhoz. További információkért lásd: a [konfigurálása tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-configure.md) dokumentum.

> [!WARNING]
> HDInsight a fürt az alapértelmezett tároló teljes hozzáféréssel kell rendelkeznie.

## <a name="requirements"></a>Követelmények

* Azure-előfizetés
* C# vagy Python. C#-példakód a Visual Studio megoldás valósul meg.

  * A Visual Studio 2013, 2015-öt vagy 2017 verziót kell lennie.
  * Python 2.7 vagy újabb verzióját kell lennie.

* A Linux-alapú HDInsight-fürt vagy [Azure PowerShell] [ powershell] – Ha egy meglévő Linux-alapú fürtöt, Ambari egy közös hozzáférésű Jogosultságkód hozzáadása a fürt használhatja. Ha nem, az Azure PowerShell segítségével hozzon létre egy fürtöt, és egy közös hozzáférésű Jogosultságkód hozzáadása a fürt létrehozása során.

    > [!IMPORTANT]
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* A példa fájljainak [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). A tárház a következő elemeket tartalmazza:

  * Egy tároló, a tárolt házirend, és a SAS hozhat létre, és a HDInsight együttes használata a Visual Studio-projekt
  * Egy olyan tároló, a tárolt házirend és a SAS hozhat létre, és a HDInsight együttes használata a Python-parancsfájl
  * Egy PowerShell-parancsfájlt, amely létre HDInsight-fürtöt és konfigurálja úgy, hogy az SA-kat használjon.

## <a name="shared-access-signatures"></a>Megosztott hozzáférési aláírásokkal

Nincsenek megosztott hozzáférési aláírásokkal kétféle:

* Az ad hoc: A kezdési ideje, a lejárati idő és a SAS engedélyeinek összes adhatók meg a SAS URI-t.

* Hozzáférési házirendben tárolt: A tárolt házirend erőforrás tárolóba, mint a blobtárolót van definiálva. A házirend legalább egy közös hozzáférésű jogosultságkód megkötéseit kezelésére használható. SAS-kód társítása a tárolt házirend, a SAS - a kezdési ideje, a lejárat időpontjának és a-vonatkozó engedélyeit a tárolt házirend korlátozásait örökölnek.

A különbség a két űrlap fontos egyik-forgatókönyvben: visszavont tanúsítványok. SAS-kód egy URL-címet, így bárki, aki beolvassa a biztonsági Társítások használható, függetlenül attól, először aki kérte. Ha SAS-kód nyilvánosságra, bárki a világ használhatná. Terjesztett SAS-kód nem érvényes, amíg a négy dolog történik:

1. A lejárat időpontjának a SAS megadott elérésekor.

2. A lejárat időpontjának a tárolt hozzáférési házirendet a biztonsági Társítások által hivatkozott megadott elérésekor. A következő esetekben okozhat a lejárati időpont érhető el:

    * Az időtartam lejárt.
    * A tárolt házirend úgy módosul, hogy egy lejárati dátuma a múltban van. A lejárati időpont módosítjuk az egyik módja a biztonsági Társítások visszavonása.

3. A tárolt házirend SAS által hivatkozott törölve van, amely másik módja is visszavonja a biztonsági Társítások. A tárolt házirend ugyanazzal a névvel hozza létre, ha a korábbi házirendet minden SAS-tokenje érvényesek (Ha még nem múlt el a biztonsági Társítások a lejárati idő). Ha azt tervezi, a biztonsági Társítások visszavonni, ügyeljen arra, hogy más nevet használjon, ha a hozzáférési házirendben a jövőben egy lejárati idővel hozza létre újra.

4. A biztonsági Társítások létrehozásához használt fiók kulcs újragenerálják. A kulcs újragenerálása hatására az összes sikertelen hitelesítésre az előző kulcsot használó alkalmazások. Frissítse az összes összetevő az új kulccsal.

> [!IMPORTANT]
> A közös hozzáférésű jogosultságkód URI társított aláírásának létrehozására használt fiók a kulccsal, és a társított tárolja hozzáférési házirend (ha van ilyen). Ha nincs tárolt házirend van megadva, csak visszavonni egy közös hozzáférésű jogosultságkódot, módosíthatja a fiókkulcsot.

Javasoljuk, hogy mindig használjon tárolt hozzáférési házirendeket. Tárolt házirendek használatakor aláírások visszavonására, és a lejárati dátum meghosszabbításához igény szerint. Ez a dokumentum használatát lépéseit tárolt hozzáférési házirendek biztonsági Társítások létrehozásához.

További információ a megosztott hozzáférési aláírásokkal: [ismertetése a SAS-modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>A tárolt házirend és a C használatával SAS létrehozása\#

1. Nyissa meg a megoldást a Visual Studióban.

2. A Megoldáskezelőben kattintson a jobb gombbal a a **SASToken** projektre, és válassza ki **tulajdonságok**.

3. Válassza ki **beállítások** , és adjon értékeket az alábbi bejegyzéseket:

   * StorageConnectionString: A tárolt házirend és az SAS-kód létrehozásához használni kívánt tárfiók kapcsolati karakterlánca. A következő formátumban kell megadni `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` ahol `myaccount` a tárfiók neve és `mykey` a tárfiók kulcsa.

   * ContainerName: A tárfiókot, amely szeretné korlátozni a hozzáférést a tárolóhoz.

   * SASPolicyName: A tárolt házirend létrehozásához használni kívánt nevet.

   * FileToUpload: A tárolóba feltöltött fájl elérési útja

4. Futtassa a projektet. Az alábbi hasonló információk után a biztonsági Társítások hozott létre:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Mentse a SAS házirend jogkivonat, a tárfiók nevét és a tároló neve. A storage-fiók társítása a HDInsight-fürt használja ezeket az értékeket.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Hozzon létre egy tárolt házirend és a SAS pythonos környezetekben

1. Nyissa meg a SASToken.py fájlt, és módosítsa a következő értékeket:

   * házirend\_name: a tárolt házirend létrehozásához használni kívánt nevet.

   * tárolási\_fiók\_name: a tárfiók nevét.

   * tárolási\_fiók\_kulcs: a tárfiók kulcsa.

   * tárolási\_tároló\_name: a tárfiókot, amely szeretné korlátozni a hozzáférést a tárolóhoz.

   * Példa\_fájl\_elérési út: egy a tárolóba feltöltött fájl elérési útját.

2. Futtassa a szkriptet. A parancsfájl lefutásakor a SAS-jogkivonatot az alábbihoz hasonló jeleníti meg:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Mentse a SAS házirend jogkivonat, a tárfiók nevét és a tároló neve. A storage-fiók társítása a HDInsight-fürt használja ezeket az értékeket.

## <a name="use-the-sas-with-hdinsight"></a>A biztonsági Társítások használhat a hdinsight eszközzel

HDInsight-fürtök létrehozásakor meg kell adnia egy elsődleges tárfiók, és opcionálisan megadhat további tárfiókokat. Mindkét módszer tároló hozzáadása a storage-fiókok és a tárolók használt teljes hozzáférést igényelnek.

Egy közös hozzáférésű Jogosultságkód segítségével tárolóba való hozzáférés korlátozására, hogy egyéni bejegyzés hozzáadása a **core-hely** a fürt konfigurációját.

* A **Windows-alapú** vagy **Linux-alapú** HDInsight-fürtök, a bejegyzést adhat PowerShell-lel fürt létrehozása során.
* A **Linux-alapú** a HDInsight-fürtök, módosítsa a Ambari használatával fürt létrehozása után.

### <a name="create-a-cluster-that-uses-the-sas"></a>A biztonsági Társítások használó fürt létrehozása

A SAS használó HDInsight-fürtök létrehozására láthat példát megtalálható a `CreateCluster` mappában található a tárházban. A használatához tegye a következőket:

1. Nyissa meg a `CreateCluster\HDInsightSAS.ps1` fájlt egy szövegszerkesztőben, és módosítsa a következő értékeket a dokumentum elején.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Például `'mycluster'` a létrehozni kívánt fürt nevét. Az SAS-értékeket egy tárfiók és a SAS-jogkivonat létrehozásakor meg kell felelnie az értékeket az előző lépésekben.

    Ha módosította az értékeket, mentse a fájlt.

2. Nyisson meg egy új Azure PowerShell-parancssorba. Ha nem ismeri az Azure PowerShell, vagy nem telepítette azt, lásd: [telepítse és konfigurálja az Azure Powershellt][powershell].

1. A parancssorból a következő paranccsal, hogy az Azure-előfizetéshez hitelesítést:

    ```powershell
    Login-AzureRmAccount
    ```

    Amikor a rendszer kéri, jelentkezzen be az Azure-előfizetéshez tartozó fiókkal.

    Ha a fiók több Azure-előfizetéssel társítva, szükség lehet használandó `Select-AzureRmSubscription` való válassza ki a használni kívánt előfizetést.

4. A parancssorból lépjen a `CreateCluster` HDInsightSAS.ps1 fájlt tartalmazó könyvtár. Az alábbi parancs segítségével futtassa a parancsfájlt

    ```powershell
    .\HDInsightSAS.ps1
    ```

    A parancsfájl futtatása, mert azt naplóit kimeneti a PowerShell-parancssorba csoport és a storage-fiókokat készít az erőforrás. Adja meg a HTTP-felhasználó a HDInsight-fürthöz kéri. Ez a fiók HTTP/s hozzáférést a fürthöz biztonságossá tételére szolgál.

    A Linux-alapú fürt létrehozásakor egy SSH felhasználói fiók felhasználónevét és jelszavát kéri. Ez a fiók segítségével távolról jelentkezzen be a fürthöz.

   > [!IMPORTANT]
   > Ha a HTTP/s- vagy SSH-felhasználónév és jelszó megadására kéri, meg kell adnia egy jelszót, amely megfelel a következő feltételeknek:
   >
   > * Legalább 10 karakter hosszúságúnak kell lennie.
   > * Legalább egy számot kell tartalmaznia.
   > * Legalább egy nem alfanumerikus karaktert kell tartalmaznia
   > * Tartalmaznia kell legalább egy nagy- vagy kisbetűt

Egy ideig, míg a parancsfájl végrehajtására, általában körülbelül 15 percet vesz igénybe. Ha a parancsfájl hiba nélkül befejeződött, a fürt létrehozását.

### <a name="use-the-sas-with-an-existing-cluster"></a>Az SA-kat használ egy meglévő fürthöz

Ha egy meglévő Linux-alapú fürtöt, a SAS-t is hozzáadhat a **core-hely** konfigurációja az alábbi lépéseket követve:

1. Nyissa meg a fürt Ambari webes felhasználói Felületét. Ez a lap címe https://YOURCLUSTERNAME.azurehdinsight.net. Amikor a rendszer kéri, a fürtre, a felügyeleti neve (rendszergazda) használatával hitelesíteni és jelszó használatával, ha a fürt létrehozása.

2. Válassza ki az Ambari webes felhasználói felület bal oldalán, **HDFS** , és válassza a **Configs** fülre az oldal közepén.

3. Válassza ki a **speciális** lapot, és görgessen, amíg meg nem látja a **egyéni core-hely** szakasz.

4. Bontsa ki a **egyéni core-hely** szakaszban, majd görgessen a célból, és válassza ki a **tulajdonság hozzáadása...**  hivatkozásra. A következő értékeket használja a **kulcs** és **érték** mezők:

   * **Kulcs**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Érték**: az SAS futtatta korábban C# vagy Python-alkalmazás által visszaadott

     Cserélje le **CONTAINERNAME** tároló nevű használt a C# vagy SAS alkalmazást. Cserélje le **STORAGEACCOUNTNAME** használt fiók nevével.

5. Kattintson a **Hozzáadás** gombra kattint, hogy a kulcs-érték mentéséhez, majd kattintson a **mentése** gombra a beállítások módosításainak mentéséhez. Amikor a rendszer kéri, adjon meg egy leírást a változás ("hozzáadása SAS tárolók eléréséhez" például), és kattintson a **mentése**.

    Kattintson a **OK** Ha végrehajtotta a módosításokat.

   > [!IMPORTANT]
   > A módosítás érvénybe léptetéséhez újra kell indítania számos szolgáltatást.

6. Válassza ki az Ambari webes felhasználói felület **HDFS** a bal oldali listában, és válassza **indítsa újra az összes** a a **szolgáltatás műveletek** legördülő listában kattintson a jobb. Amikor a rendszer kéri, válassza ki a **kapcsolja be a karbantartási mód** és majd válassza ki __Conform indítsa újra az összes ".

    Ismételje meg ezt a folyamatot MapReduce2 és YARN.

7. A szolgáltatások újraindítása, ha mindegyiknél válassza ki, és a karbantartási mód letiltása a **szolgáltatás műveletek** legördülő listán.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Ha ellenőrizni szeretné, hogy korlátozott hozzáféréssel rendelkező, az alábbi módszerekkel:

* A **Windows-alapú** a HDInsight-fürtök, a távoli asztal használatával csatlakozzon a fürthöz. További információkért lásd: [csatlakozás RDP Funkciót használnak a HDInsight](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Miután csatlakozott, használja a **parancssori Hadoop** ikonjára az asztal nyisson meg egy parancssort.

* A **Linux-alapú** a HDInsight-fürtök az SSH segítségével csatlakozzon a fürthöz. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Miután csatlakozott a fürthöz, a következő lépések segítségével győződjön meg arról, hogy a csak olvasható és lista elemeket a biztonsági Társítások tárfiók is:

1. A tároló tartalmának listájában, használja a következő parancsot a parancssorból: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Cserélje le **SASCONTAINER** a SAS-tárfiók létrehozása a tároló nevét. Cserélje le **SASACCOUNTNAME** SAS használt tárfiók nevével.

    A lista tartalmazza a fájl feltöltése során a tároló és a SAS hoztak létre.

2. A következő parancs használatával győződjön meg arról, hogy a fájl tartalmát érheti el. Cserélje le a **SASCONTAINER** és **SASACCOUNTNAME** ahogy az előző lépésben. Cserélje le **Fájlnév** jelennek meg az előző parancs a fájl nevét:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Ez a parancs felsorolja a fájl tartalmát.

3. Az alábbi parancs segítségével töltse le a fájlt a helyi fájlrendszer:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Ez a parancs letölti a fájlt egy helyi fájlba nevű **példa.txt**.

4. A következő paranccsal egy új fájlt a helyi fájl feltöltése **testupload.txt** a SAS-tároló:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Az alábbihoz hasonló üzenet jelenhet meg:

        put: java.io.IOException

    Ez a hiba akkor fordul elő, mivel a tárolási hely olvasási + lista csak. A következő paranccsal helyezze az adatokat a fürthöz, írható alapértelmezett tárolón:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Most, a művelet sikeresen befejeződik.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="a-task-was-canceled"></a>A feladat meg lett szakítva

**A jelenség**: a PowerShell-parancsfájlt a fürt létrehozásakor a következő hibaüzenet jelenhet:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**OK**: Ez a hiba akkor fordulhat elő, ha a rendszergazda/HTTP felhasználó a fürt számára, vagy (a Linux-alapú fürtök) az SSH-felhasználó használja jelszó.

**Megoldási**: használhat olyan jelszót, amely megfelel a következő feltételeknek:

* Legalább 10 karakter hosszúságúnak kell lennie.
* Legalább egy számot kell tartalmaznia.
* Legalább egy nem alfanumerikus karaktert kell tartalmaznia
* Tartalmaznia kell legalább egy nagy- vagy kisbetűt

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a korlátozott hozzáférésű tároló felvétele a HDInsight-fürt rendelkezik, ismerje meg, a fürtön lévő adatokkal dolgozni egyéb módjai:

* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
