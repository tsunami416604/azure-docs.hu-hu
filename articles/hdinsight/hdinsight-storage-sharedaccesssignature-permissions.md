---
title: Közös hozzáférésű Jogosultságkódokat – Azure HDInsight-hozzáférés korlátozása
description: Ismerje meg, hogyan lehet közös hozzáférési aláírások használata a HDInsight-hozzáférés korlátozása az Azure storage blobokban tárolt adatokat.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 76e40f5178457f366ed386dba7a1817ddde3c8ac
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090228"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Az Azure Storage közös hozzáférési aláírások használatával a HDInsight adatokhoz hozzáférésének korlátozása

HDInsight a fürthöz társított Azure Storage-fiókokat az adatok teljes hozzáféréssel rendelkezik. A blob-tároló a közös hozzáférési aláírások használatával korlátozza a hozzáférést az adatokat. A közös hozzáférésű Jogosultságkódok (SAS) érhetők el Azure storage-fiókot, amely lehetővé teszi, hogy az adatokhoz való hozzáférés korlátozására. Például villámgyors csak olvasható adatok.

> [!IMPORTANT]
> Az Apache Ranger segítségével megoldások érdemes lehet tartományhoz csatlakoztatott HDInsight. További információkért lásd: a [tartományhoz csatlakoztatott HDInsight konfigurálása](./domain-joined/apache-domain-joined-configure.md) dokumentumot.

> [!WARNING]
> HDInsight a fürthöz az alapértelmezett tároló teljes hozzáféréssel kell rendelkeznie.

## <a name="requirements"></a>Követelmények

* Azure-előfizetés
* C#- vagy Python. C#-példakódot biztosítunk a Visual Studio megoldás.

  * A Visual Studio 2013, 2015 vagy 2017 verziót kell lennie.
  * Python 2.7-es vagy újabb verzióját kell lennie.

* Egy Linux-alapú HDInsight-fürt vagy [Azure PowerShell-lel] [ powershell] – Ha rendelkezik meglévő Linux-alapú fürt, az Ambari segítségével egy közös hozzáférésű Jogosultságkód hozzáadni a fürthöz. Ha nem, az Azure PowerShell segítségével hozzon létre egy fürtöt, és a egy közös hozzáférésű Jogosultságkód hozzáadása a fürt létrehozása során.

    > [!IMPORTANT]
    > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* A példa fájlt [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ez a tárház a következő elemeket tartalmazza:

  * Visual Studio-projekt, amely egy storage-tárolót, a tárolt házirend és a SAS hozhat létre, a HDInsight segítségével
  * Egy Python-szkriptet, amely egy storage-tárolót, a tárolt házirend és a SAS hozhat létre, a HDInsight segítségével
  * Egy PowerShell-parancsprogram, amely egy HDInsight-fürt létrehozása és konfigurálása, hogy a SAS kódot használja.

## <a name="shared-access-signatures"></a>Közös hozzáférésű Jogosultságkódok

Kétféle közös hozzáférési aláírások van:

* Az ad hoc: A kezdési idő, lejárati időpontot és engedélyeket a SAS az összes megadott SAS URI-n.

* Tárolt hozzáférési szabályzatot: egy tárolt hozzáférési szabályzat van definiálva egy erőforrás-tárolón, például egy blob-tárolóba. A házirend legalább egy közös hozzáférési aláírások megkötések kezelésére használható. SAS társítása egy tárolt hozzáférési szabályzatot, az SAS örökli a megkötések - a kezdési idő, lejárati idő és a tárolt hozzáférési szabályzat definiált - engedélyek.

A különbség a két formáját fontos egyik legfontosabb előny a: visszavont tanúsítványok. SAS egy URL-címe, így bárki, aki szerzi be a SAS használatával is, először aki kérte, függetlenül. Ha SAS nyilvánosan közzé van téve, használat szerint, a világ bármely részéről. Elosztott SAS nem érvényes, amíg a négy dolog történik:

1. Az SAS a megadott lejárati idő elérésekor.

2. A SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati idő elérésekor. A következő esetekben okozhat a lejárati időpont érhető el:

    * Lejárt az időtartam alatt.
    * A tárolt hozzáférési szabályzat módosul, egy lejárati dátuma a múltban van. A lejárati időpont módosítjuk az egyik módja visszavonja az SAS.

3. A tárolt hozzáférési szabályzatot a SAS által hivatkozott törlődik, amely visszavonja a SAS egy másik módja. Ha újra létrehozza ugyanazzal a névvel a tárolt hozzáférési szabályzatot, az előző házirend minden SAS-jogkivonatok érvényesek (Ha nem ment át az SAS a lejárati ideje). Ha visszavonja a SAS szeretne, mindenképpen használjon másik nevet, ha a hozzáférési szabályzat a későbbiekben egy lejárati idővel hozza létre újra.

4. A fiók SAS létrehozásához használt kulcs újragenerálása van. A kulcs újragenerálása hatására a minden alkalmazás, amely a korábbi kulcs használatával a hitelesítése sikertelen legyen. Frissítse az összes összetevő az új kulccsal.

> [!IMPORTANT]
> A fiókkulcs aláírásának létrehozására használt társítva egy közös hozzáférésű jogosultságkód URI-t, és a társított tárolt hozzáférési szabályzat (ha van). Ha nincs tárolt hozzáférési szabályzat van megadva, a csak visszavonása a közös hozzáférésű jogosultságkód módja a fiókkulcs módosításához.

Javasoljuk, hogy mindig használjon tárolt hozzáférési szabályzatok. Tárolt szabályzatok használatakor az aláírások visszavonása, vagy kiterjesztheti a lejárati dátum, igény szerint. Ez a dokumentum használatát a lépések tárolt hozzáférési szabályzatok SAS előállítása.

A közös hozzáférésű Jogosultságkódokat további információkért lásd: [a SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Hozzon létre egy tárolt házirend és a SAS-C használatával\#

1. Nyissa meg a megoldást a Visual Studióban.

2. A Megoldáskezelőben kattintson a jobb gombbal a a **SASToken** projektre, és válassza **tulajdonságok**.

3. Válassza ki **beállítások** , és adja hozzá az értékeket az alábbi bejegyzéseket:

   * StorageConnectionString: Szeretne létrehozni egy tárolt házirend- és az SAS-tároló tárfiók kapcsolati karakterláncát. A formátum `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` ahol `myaccount` a tárfiók neve és `mykey` a tárfiók kulcsa.

   * ContainerName: A tároló, amely korlátozza a hozzáférést a kívánt storage-fiókban.

   * SASPolicyName: A nevét, a tárolt házirend létrehozásához.

   * FileToUpload: A fájl elérési útja a tárolóba feltöltött.

4. Futtassa a projektet. Az alábbi szöveghez hasonló információt SAS létrehozása után jelenik meg:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Mentse a SAS házirend jogkivonat, a tárfiók nevét és a tároló neve. A storage-fiókot társít a HDInsight-fürt használja ezeket az értékeket.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Hozzon létre egy tárolt házirend és a SAS-ban a Python használatával

1. Nyissa meg a SASToken.py fájlt, és módosítsa a következő értékeket:

   * a házirend\_name: A nevét, a tárolt házirend létrehozásához.

   * tárolási\_fiók\_name: a tárfiók nevére.

   * tárolási\_fiók\_kulcs: az a tárfiók kulcsát.

   * tárolási\_tároló\_name: A tároló, amely korlátozza a hozzáférést a kívánt storage-fiókban.

   * Példa\_fájl\_elérési útja: a tárolóba feltöltött fájl elérési útját.

2. Futtassa a szkriptet. A parancsfájl lefutásakor megjeleníti a SAS-jogkivonatát az alábbi szöveghez hasonló:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Mentse a SAS házirend jogkivonat, a tárfiók nevét és a tároló neve. A storage-fiókot társít a HDInsight-fürt használja ezeket az értékeket.

## <a name="use-the-sas-with-hdinsight"></a>SAS használata a HDInsight

Egy HDInsight-fürt létrehozásakor meg kell adnia egy elsődleges tárfiók, és megadhat további tárfiókok. Mindkét módszer a tárolóeszközök hozzáadása a storage-fiókok és a használt tárolók teljes hozzáférést igényelnek.

Egy közös hozzáférésű Jogosultságkód használatával korlátozhatja a hozzáférést a tárolóhoz, adjon hozzá egy egyéni bejegyzést a **hely** a fürt konfigurációját.

* A **Windows-alapú** vagy **Linux-alapú** HDInsight-fürtök, a PowerShell használatával a fürt létrehozásakor a bejegyzés is hozzáadhat.
* A **Linux-alapú** HDInsight-fürtök az Ambari fürt létrehozása után konfigurációjának módosítása.

### <a name="create-a-cluster-that-uses-the-sas"></a>A SAS kódot használó fürt létrehozása

Egy HDInsight-fürt által használt SAS létrehozása egy példát tartalmaz a `CreateCluster` a tárház könyvtárába. A használatához használja az alábbi lépéseket:

1. Nyissa meg a `CreateCluster\HDInsightSAS.ps1` fájlt egy szövegszerkesztőben, és módosítsa a következő értékeket, a dokumentum elején.

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

    Módosítsa például `'mycluster'` a létrehozni kívánt fürt nevét. Az SAS-értékeket meg kell egyeznie az értékeket az előző lépésekben egy tárfiókot és SAS-jogkivonat létrehozása során.

    Ha módosította az értékeket, mentse a fájlt.

2. Nyisson meg egy új Azure PowerShell-parancssort. Ha ismeri az Azure PowerShell-lel, vagy nem telepítette azt, [telepítse és konfigurálja az Azure Powershellt][powershell].

1. A rendszer kéri alkalmazás hitelesítéséhez az Azure-előfizetéshez az alábbi parancsot:

    ```powershell
    Connect-AzureRmAccount
    ```

    Amikor a rendszer kéri, jelentkezzen be azzal a fiókkal az Azure-előfizetés.

    Ha a fiók több Azure-előfizetéssel társítva, szükség lehet használandó `Select-AzureRmSubscription` használni kívánt előfizetés kiválasztásához.

4. A parancssorból módosítsa a könyvtárat, a `CreateCluster` a HDInsightSAS.ps1 fájlt tartalmazó könyvtárra. Az alábbi parancs segítségével futtassa a parancsfájlt

    ```powershell
    .\HDInsightSAS.ps1
    ```

    A parancsfájl futása naplózza kimeneti a PowerShell-parancssorba, csoport és a storage-fiókok létrehozásakor az erőforrást. Adja meg a HTTP-felhasználót a HDInsight-fürt kéri. Ennek a fióknak a HTTP/s hozzáférést a fürt biztonságossá tételére szolgál.

    Egy Linux-alapú fürt létrehozásakor egy SSH-felhasználói fiók nevét és a jelszó megerősítését. Ez a fiók segítségével távolról jelentkezzen be a fürthöz.

   > [!IMPORTANT]
   > Ha a HTTP/HTTPS vagy SSH-felhasználónevet és jelszót kér, meg kell adnia egy jelszót, amely megfelel a következő feltételeknek:
   >
   > * Legalább 10 karakter hosszúságúnak kell lennie.
   > * Legalább egy számjegyet kell tartalmaznia.
   > * Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
   > * Legalább egy nagy- vagy kisbetűt kell tartalmaznia

Míg ez a szkript végrehajtásához, általában körülbelül 15 percet vesz igénybe. Miután a parancsfájl futása befejeződött, hibák nélkül, a fürt létrejött.

### <a name="use-the-sas-with-an-existing-cluster"></a>SAS használata egy meglévő fürthöz

Ha rendelkezik meglévő Linux-alapú fürt, adhat hozzá az SAS a **hely** -konfigurációjának az alábbi lépéseket követve:

1. Nyissa meg az Ambari webes felhasználói felület a fürt számára. Ez az oldal címe https://YOURCLUSTERNAME.azurehdinsight.net. Amikor a rendszer kéri, a fürthöz hitelesíteni kívánt rendszergazda neve (rendszergazdai) és a használt jelszót, amikor a fürt létrehozásához.

2. Az Ambari webes felhasználói felület a bal oldali menüjében válassza **HDFS** majd válassza ki a **Configs** fülre az oldal közepén.

3. Válassza ki a **speciális** lapra, és görgessen, amíg meg nem találja a **egyéni hely** szakaszban.

4. Bontsa ki a **egyéni hely** szakaszban, majd görgessen lefelé a végfelhasználók, és válassza a **tulajdonság hozzáadása...**  hivatkozásra. A következő értékeket használja a **kulcs** és **érték** mezők:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Érték**: az SAS a korábban már futtatta a C#- vagy Python kérelem által visszaadott

     Cserélje le **CONTAINERNAME** a C#- vagy SAS-alkalmazással használt tároló nevét. Cserélje le **STORAGEACCOUNTNAME** a a használt tárfiók nevét.

5. Kattintson a **Hozzáadás** gombra kattintva mentse a kulcs-érték, majd kattintson a **mentése** gombot a konfigurációs módosítások mentéséhez. Amikor a rendszer kéri, adjon meg egy leírást a változás ("Hozzáadás SAS-tároló hozzáférés" például), és kattintson a **mentése**.

    Kattintson a **OK** Ha végrehajtotta a változásokat.

   > [!IMPORTANT]
   > A módosítás előtt újra kell indítania több szolgáltatást.

6. Az Ambari webes felhasználói felületen válassza **HDFS** a bal oldali listából, majd **indítsa újra az összes érintett** a a **szolgáltatás műveletek** legördülő listában a jobb oldalon. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

    Ismételje meg ezt a folyamatot MapReduce2 és YARN.

7. A szolgáltatás újraindítása, ha egyenként válassza ki, és tiltsa le a karbantartási módnak a **szolgáltatás műveletek** legördülő menü.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Annak ellenőrzéséhez, hogy a hozzáférés korlátozottá, az alábbi módszerekkel:

* A **Windows-alapú** HDInsight-fürtök esetén a távoli asztal használatával csatlakozhat a fürthöz. További információkért lásd: [csatlakozás az RDP-vel HDInsight](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Ha csatlakoztatva van, használja a **Hadoop parancssori** ikonjára az asztalon, nyisson meg egy parancssort.

* A **Linux-alapú** HDInsight-fürtök, SSH használatával csatlakozhat a fürthöz. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Miután csatlakozott a fürthöz, a következő lépések segítségével győződjön meg arról, hogy a tárfiók SAS csak olvasási és a lista elemek is:

1. A tároló tartalmának listázásához használja a következő parancsot a parancssorba: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Cserélje le **SASCONTAINER** a SAS-tárfiók létrehozása a tároló nevére. Cserélje le **SASACCOUNTNAME** SAS használt tárfiók nevére.

    A lista tartalmazza a fájl feltöltése a tárolóba, és a SAS létrehozása.

2. A következő parancs használatával győződjön meg arról, hogy a fájl tartalmát áttekintheti. Cserélje le a **SASCONTAINER** és **SASACCOUNTNAME** ahogy az előző lépésben. Cserélje le **FILENAME** a fájlt az előző parancsban megjelenített nevére:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Ez a parancs felsorolja a fájl tartalmát.

3. A következő paranccsal töltse le a fájlt a helyi fájlrendszerben:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Ez a parancs letölti a fájlt egy helyi fájlba nevű **példa.txt**.

4. A következő paranccsal egy új fájlt a helyi fájl feltöltése **testupload.txt** SAS tárolására:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Az alábbi szöveghez hasonló üzenetet kapja:

        put: java.io.IOException

    Ez a hiba oka az, hogy a tárolási hely olvasási + csak lista. A következő paranccsal helyezze az adatokat a fürthöz, írható alapértelmezett tárolására:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Ebben az esetben a művelet sikeresen befejeződik.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="a-task-was-canceled"></a>A feladatot törölték

**A jelenség**: a PowerShell-szkripttel fürtöt hoz létre, amikor jelenhet meg a következő hibaüzenetet kapja:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**OK**: Ez a hiba akkor fordulhat elő, ha az SSH-felhasználó használata egy jelszót a rendszergazdai/HTTP-felhasználó, a fürt számára, vagy (Linux-alapú fürtök).

**Feloldási**: használjon olyan jelszót, amely megfelel a következő feltételeknek:

* Legalább 10 karakter hosszúságúnak kell lennie.
* Legalább egy számjegyet kell tartalmaznia.
* Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
* Legalább egy nagy- vagy kisbetűt kell tartalmaznia

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan korlátozott hozzáférésű tár hozzáadása a HDInsight-fürthöz, ismerje meg, hogyan más adatokkal szeretne dolgozni a fürtön:

* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
