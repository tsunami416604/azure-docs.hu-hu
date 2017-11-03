---
title: "Légifelvételes kép besorolás |} Microsoft Docs"
description: "Útmutatás a valós forgatókönyvekben légifelvételes lemezkép osztályozásra"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 10/27/2017
ms.openlocfilehash: cfffe5145f8762558e6ee573f6f2bb69d32424ad
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="aerial-image-classification"></a>Légifelvételes kép besorolás

Ez a példa bemutatja, hogyan összehangolja elosztott képzési és operationalization kép besorolási modell Azure Machine Learning-munkaterület segítségével. Két megközelítés képzési használjuk: i finomítása a mély Neurális hálózat using egy [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU-fürtöt, és (ii) használatával a [Microsoft Machine Learning az Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) csomag pretrained CNTK modellek featurize lemezképeket és származtatott szolgáltatásaival osztályozó betanításához. Majd érvénybe lépni a betanított modellek párhuzamos módon nagy méretű kép csoportokra be a felhő egy [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) fürthöz, így nekünk sebessége képzési és operationalization méretezési hozzáadásával vagy eltávolításával munkavégző csomópontokhoz.

Ebben a példában két megközelítés tanulási, amely kihasználja a képzés mély Neurális hálózatokat teljesen új járó költségek elkerülése érdekében pretrained modellek átvitele mutatja be. A mély Neurális hálózat általában átképezési GPU számítási igényel, de a pontosabb is vezethet, ha a betanítási készlet nem elég nagy. Egy egyszerű osztályozó featurized képek betanítása nem igényel GPU számítási eredendően gyors és méretezhető önkényesen és kevesebb paraméterek megfelelő. Ez a módszer esetén ezért egy kiváló választás néhány képzési minták érhetők el –, mint a gyakran egyéni használatából adódó. 

Ebben a példában használt Spark-fürt 40 munkavégző csomópontokhoz rendelkezik, és költségek ~$40/hr működéséhez; a kötegelt AI fürterőforrások nyolc Feldolgozóegységekkel tartalmazza, és a működéséhez ~$10/hr költség. Ez a forgatókönyv befejezése vesz a körülbelül három óra. Ha elkészült, a tisztítás követésével távolítsa el az erőforrások hozott létre, és állítsa le az ezzel járó költségek.

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

A valós forgatókönyv nyilvános GitHub-tárházban összes anyagok, beleértve a mintakódok, szükséges ehhez a példához tartalmazza:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Nagybetűk leírás használata

Ebben a forgatókönyvben azt betanítása a machine learning modellek 224-mérő x 224-mérő előkészítésére légifelvételes képe látható föld típusú besorolása. Föld használja a besorolási modell segítségével nyomon követheti a urbanization, erdőirtás, élőhelyek megszűnését, és egyéb fő környezeti trendek segítségével rendszeres időközönként gyűjtött légifelvételes szimbólumokat. A Microsoft készítse elő a képzés és -ellenőrzés az Amerikai Egyesült Államokból szimbólumokat alapján kép beállítása Nemzeti mezőgazdaság szimbólumokat Program és a föld az Egyesült Államok közzétett címkék használata Nemzeti föld borítóján adatbázis. Példa képek föld használja az osztályok itt látható:

![Minden szárazföldi területeinek példa címke használata](media/scenario-aerial-image-classification/example-labels.PNG)

Képzési és a osztályozó modell ellenőrzése után fog érvénybe lépni az átfedés Middlesex megye, MA – home, a Microsoft új Angliai kutatás és fejlesztés (NERD) Center--bemutatják, hogyan ezek a modellek segítségével városi trendeket tanulmányozzák légifelvételes lemezképek fejlesztési.

Egy kép osztályozó tanulási átvitellel létrehozásához a adatszakértőkön gyakran módszerek tartománnyal több modellek létrehozásához, és válassza ki a legtöbb performant modell. Az Azure Machine Learning-munkaterület mobiladatok kutatók képzési összehangolják a különböző számítási környezetek között, nyomon, és hasonlítsa össze több modellek teljesítményét, és a kiválasztott modell alkalmazása a felhő nagy adatkészletek.

## <a name="scenario-structure"></a>A forgatókönyv struktúra

Ebben a példában a kép adatai és pretrained modellek elhelyezni az Azure storage-fiók. Egy Azure HDInsight Spark-fürt elolvassa ezeket a fájlokat, és egy kép besorolási modell MMLSpark használatával. A betanított modell és az előrejelzés majd a tárfiókot, ahol azok elemzése és helyileg futó Jupyter notebook által ábrázolt írja. Az Azure Machine Learning-munkaterület koordinálja a Spark-fürt parancsfájlok távoli végrehajtás. Emellett több modellek betanítása eltérő módszerekkel, így a felhasználó számára a legtöbb performant modell pontosságát metrikáját követi nyomon.

![A kép légifelvételes besorolás valós forgatókönyvekben sematikus nézete](media/scenario-aerial-image-classification/scenario-schematic.PNG)

A [részletesen](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) végigvezeti a létrehozása és előkészítése az Azure storage-fiók és a Spark-fürt, beleértve az adatokat átvitel és a függőségi telepítés megkezdéséhez. Majd bemutatják a képzés feladatok elindításához, és hasonlítsa össze az eredményül kapott modellek teljesítményét. Végül azok bemutatják, hogyan lehet alkalmazni a választott modellek egy nagy méretű kép készlet Spark-fürt, és helyileg az előrejelzés eredményeket elemezni.


## <a name="set-up-the-execution-environment"></a>A végrehajtási környezet beállítása

Az alábbi utasításokat ismerteti, hogyan kell beállítani végrehajtási környezetet ebben a példában a folyamatot.

### <a name="prerequisites"></a>Előfeltételek
- Egy [Azure-fiók](https://azure.microsoft.com/en-us/free/) (az ingyenes próbaverzió érhetők el)
    - Egy HDInsight Spark-fürt 40 munkavégző csomópontokhoz (168 magok teljes) együtt fogja létrehozni. Győződjön meg arról, hogy a fiók rendelkezik-e elegendő rendelkezésre álló magot megtekintésével, a "használati + kvóták" Azure-portálon az előfizetéshez tartozó lapon.
       - Ha kevesebb magok érhető el, módosíthatja a HDInsight fürt sablon kiépített dolgozók számának csökkentéséhez. Ehhez útmutatást "A HDInsight Spark-fürt létrehozása" szakaszban jelennek meg.
    - Ez a minta egy kötegelt AI képzési fürtöt hoz létre két NC6 (1 GPU, 6 vCPU) virtuális gépeket. Ellenőrizze, hogy a fiók számára elegendő rendelkezésre álló magot USA keleti régiójában megtekintésével, a "használati + kvóták" Azure-portálon az előfizetéshez tartozó lapon.
- [Az Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md)
    - Kövesse a [telepítése és gyors üzembe helyezés létrehozása](quickstart-installation.md) telepítése az Azure Machine Learning-munkaterület, és kísérletezés és modell felügyeleti fiókok létrehozása.
- [A Batch-AI](https://github.com/Azure/BatchAI) Python SDK és Azure CLI 2.0
    - A kötegelt AI SDK és Azure CLI 2.0 utasításait követve telepítse a [receptet Előfeltételek szakaszában](https://github.com/Azure/BatchAI/tree/master/recipes).
        - Től írásának, az Azure Machine Learning-munkaterület használja az Azure CLI 2.0 külön elágazás. Az átláthatóság érdekében a parancssori felület "egy az Azure Machine Learning-munkaterület indítani CLI", a munkaterület verziója és az általános eszköz-verzió (is tartalmazó kötegelt AI) lesz az "Azure CLI 2.0."
    - Hozzon létre egy Azure Active Directory alkalmazás és szolgáltatás egyszerű [ezeket az utasításokat](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Rekord az ügyfél-azonosító, a titkos kulcs és a bérlő azonosítója.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), egy ingyenes Azure storage-fiókok közötti fájlátvitel összehangolására segédprogram
    - Gondoskodjon arról, hogy a mappát, amely tartalmazza az AzCopy végrehajtható fájlt a rendszer PATH környezeti változóba. (Érhetők el a környezeti változók módosítani [Itt](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Egy SSH-ügyfél; ajánlott [PuTTY](http://www.putty.org/).

Ez a példa tesztelték a Windows 10 rendszerű; bármely Windows gépről, beleértve az Azure Data tudományos virtuális gépek futtatható kell lennie. Az Azure CLI 2.0 telepítették egy olyan MSI Csomaghoz, a következők szerint [ezeket az utasításokat](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Kisebb módosításokat segítségére lesz szüksége (például filepaths módosításai) ebben a példában a macOS jelentés futtatásakor.

### <a name="set-up-azure-resources"></a>Azure-erőforrások beállítása

Ebben a példában egy HDInsight Spark-fürt és a gazdagép megfelelő fájlok Azure storage-fiók szükséges. Kövesse ezeket az utasításokat, ezeket az erőforrásokat az új Azure erőforráscsoport létrehozásához:

#### <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Légifelvételes kép besorolás", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra
 
#### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

1. Után az Azure Machine Learning-munkaterület a projekt betöltésekor, nyissa meg egy parancssori felület (CLI) gombra kattintva a fájl -> Nyissa meg a parancssort.
    Az oktatóanyag a legtöbb a a CLI verzió használatára. (A jelzett, meg kell adnia nyissa meg a parancssori felület készíti elő az erőforrások kötegelt AI egy másik verziója.)

1. A parancssori felületről jelentkezzen be az Azure-fiókjával a következő parancs futtatásával:

    ```
    az login
    ```

    A rendszer felkéri látogasson el az egy URL-cím és írja be a megadott ideiglenes code; a webhely az Azure-fiók hitelesítő adatait kéri.
    
1. Bejelentkezési befejeződése után térjen vissza a CLI-t, és annak meghatározására, hogy melyik Azure-előfizetések az Azure-fiókjával érhető el a következő parancsot:

    ```
    az account list
    ```

    Ez a parancs kilistázza az Azure-fiókjával társított előfizetéseket. Az használni szeretné az előfizetés-azonosító található. Írás az előfizetés-azonosító a következő parancsot, ahol ezután állítsa be az aktív előfizetéssel a következő parancs végrehajtásakor:

    ```
    az account set --subscription [subscription ID]
    ```

1. Ebben a példában létrehozott Azure-erőforrások együtt tárolja az Azure-erőforráscsoportot. Válassza ki az egyedi erőforráscsoport-nevet és azokra írják a jelzett, majd hajtsa végre mindkét parancsok az Azure erőforráscsoport létrehozásához:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>A storage-fiók létrehozása

Azt hozni a tárfiókot, hogy állomások projektfájlok, amelyet a HDInsight Spark kell kezelni.

1. Válassza ki a tárfiók egyedi nevét és azokra írják a következőket a jelzett `set` parancsot, majd hozzon létre egy Azure storage-fiók mindkét parancsok végrehajtásával:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. A tárfiókkulcsok listázása a következő parancs futtatásával:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Jegyezze fel az értéket a `key1` kulcsként storage a következő parancsot, majd futtassa a parancsot a érték tárolására.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. A kedvenc szövegszerkesztőjével, betöltése a `settings.cfg` az Azure Machine Learning-munkaterület projekt "Code" alkönyvtár fájlt, és helyezze be a tárfiók nevét és a kulcs jelöli. Mentse és zárja be a `settings.cfg` fájlt.
1. Ha még nem tette, töltse le és telepítse a [AzCopy](http://aka.ms/downloadazcopy) segédprogramot. Győződjön meg arról, hogy az AzCopy végrehajtható fájl elérési úton van a rendszer írja be a "AzCopy", majd lenyomja az Enter billentyűt a hozzá tartozó dokumentáció megjelenítése.
1. A következő parancsokat az összes a mintaadatok pretrained modellek és modell betanítási parancsfájlok a megfelelő helyekre a tárfiókban lévő ki:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Várt fájlátvitel legfeljebb 20 percet vesz igénybe. Várakozással, folytassa a következő részt: nyisson meg egy másik parancssori felületen keresztül munkaterületet, majd definiálja újra az ideiglenes változók szeretne.

#### <a name="create-the-hdinsight-spark-cluster"></a>A HDInsight Spark-fürt létrehozása

Az ajánlott módszer a HDInsight-fürtök létrehozásához használja a HDInsight Spark fürt resource manager-sablon szerepel a projekt "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" almappája.

1. A HDInsight Spark-fürt sablon "template.json" fájlt a projekt "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" almappája. Alapértelmezés szerint a sablon egy Spark-fürt 40 munkavégző csomópontokhoz hoz létre. Ha úgy dönt, hogy a kívánt kell, nyissa meg a sablon a kedvenc szövegszerkesztőjével, és cserélje le a munkavégző csomópont száma a kiválasztott "40" az összes példányát.
    - Ha úgy dönt, a feldolgozó csomópontok száma kis kevés a memória hibák fordulhatnak elő. Elleni észlelt memóriahibák jelentésére, futtathatja a képzés és operationalization parancsfájlok a rendelkezésre álló adatok egy részét a jelen dokumentum későbbi szakaszában leírtak szerint.
2. Válassza ki egy egyedi nevet és jelszót a hdinsight fürt, és beírhatók az alábbi parancs a jelzett: a parancsok kiadása a fürt létrehozásához:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

A fürttelepítés (beleértve az üzembe helyezési és parancsfájl művelet végrehajtási) legfeljebb 30 percig is eltarthat.

### <a name="set-up-batch-ai-resources"></a>Kötegelt AI erőforrások beállítása

Várakozás közben a tárolási fiók fájlátvitel és Spark-fürt üzembe befejezését, előkészítheti a kötegelt AI Network File Server (NFS) és a GPU-fürt. Nyisson meg egy Azure CLI 2.0 parancssort, és futtassa a következő parancsot:

```
az --version 
```

Ellenőrizze, hogy `batchai` a telepített modulok között szerepel. Ha nem, előfordulhat, hogy használni a különböző parancssori felület (például egy munkaterület nyitják).

Ezután ellenőrizze, hogy a szolgáltató regisztrációja sikeresen befejeződött. (A szolgáltató regisztrációja akár 15 percet vesz igénybe, és továbbra is lehet a folyamatban lévő Ha nemrég befejeződött a [kötegelt AI telepítési utasításokat](https://github.com/Azure/BatchAI/tree/master/recipes).) Győződjön meg arról, hogy a "Microsoft.Batch" és "Microsoft.BatchAI" is megjelennek a következő parancs "Regisztrált" állapotú:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Ha nem, futtassa a következő szolgáltató regisztrációs parancsokat, és várja meg a regisztráció befejezéséhez ~ 15 perc.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

A következő parancsokat a zárójeles kifejezések lecseréli a korábbi erőforrás csoport és a storage-fiók létrehozása során használt értékek módosítása. Ezt követően tárolja az értékek változók a következő parancsok futtatásával:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Az Azure Machine Learning projektet tartalmazó mappa (például `C:\Users\<your username>\AzureML\aerialimageclassification`). Cserélje le a zárójeles érték a mappa filepath (és nem végződhet fordított perjel), és hajtsa végre a parancsot:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Most már készen áll az ebben az oktatóanyagban szükséges kötegelt AI erőforrásokat létrehozásához.

#### <a name="prepare-the-batch-ai-network-file-server"></a>A kötegelt AI hálózati fájl kiszolgáló előkészítése

A kötegelt AI fürt fér hozzá a betanítási adatok hálózati fájlkiszolgálón. Egy Azure fájlmegosztás vagy Azure Blob Storage és az NFS-fájlok elérésekor adatelérési gyorsabban several-fold lehet.

1. Adja ki a hálózati fájlkiszolgáló létrehozása a következő parancsot:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_D2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. A kiépítési állapotának a hálózati fájlkiszolgáló, a következő parancsot:

    ```
    az batchai file-server list
    ```

    A "provisioningState" a "landuseclassifier" nevű fájl kiszolgáló van "sikeres", esetén használatra kész. Várt kiépítése körülbelül öt percet vesz igénybe.
1. Az IP-címét az NFS található az előző parancs (a "fileServerPublicIp" tulajdonság "mountSettings" alatt). Írás az IP cím, ahol a a következő parancsot, majd futtassa a parancsot a érték tárolására:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. A kedvenc SSH eszközzel (a következő mintát parancs által használt [PuTTY](http://www.putty.org/)), hajtható végre ez a projekt `prep_nfs.sh` a képzés és érvényesítési lemezkép átvitele NFS a parancsfájl nem állítja be.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Nem lehet érintett, ha a letöltés és a kinyerési folyamat adatfrissítések görgessen között a rendszerhéj ablakát, gyorsan, hogy azok olvashatatlanná.

Ha szükséges, úgy ellenőrizheti, hogy az adatátvitel járt-e, mint a fájlkiszolgáló a kedvenc SSH-eszközzel való bejelentkezés, és ellenőrzi a tervezett a `/mnt/data` könyvtár tartalma. Két mappák training_images és validation_images keresse meg, kategóriák szerint föld nevű almappát minden egyes tartalmazó használja.  A képzés és érvényesítési készletek ~ 44 kell tartalmaznia és ~ 11 k képek, illetve.

#### <a name="create-a-batch-ai-cluster"></a>Hozzon létre egy kötegelt AI fürtöt

1. A fürt létrehozása a következő parancsok beírásával:

    ```
    set AZURE_BATCHAI_STORAGE_ACCOUNT=%STORAGE_ACCOUNT_NAME%
    set AZURE_BATCHAI_STORAGE_KEY=%STORAGE_ACCOUNT_KEY%
    az batchai cluster create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 
    ```

1. Az alábbi parancs segítségével ellenőrizze, hogy a fürthöz tartozó előkészítési állapotát:

    ```
    az batchai cluster list
    ```

    Amikor a fürt elosztási állapotának neve "landuseclassifier" módosításokat az állandó átméretezését, akkor lehet feladatok küldéséhez. Azonban feladatok nem indulnak el működését, amíg a fürt összes virtuális gép maradtak-e a "előkészítését" állapotban. Ha a fürt "hiba" tulajdonsága nem null értékű, hiba történt a fürt létrehozása során, és nem lehet megjeleníteni.

#### <a name="record-batch-ai-training-credentials"></a>Rekord kötegelt AI képzési hitelesítő adatok

Várja meg a fürt foglalási befejezéséhez, nyisson meg a `settings.cfg` a fájlt a szövegszerkesztőben az Ön által választott a projekt "Kód" alkönyvtára. Frissítés a következő változókat a saját hitelesítő adataival.
- `bait_subscription_id`(a 36 karakter Azure előfizetés-azonosító)
- `bait_aad_client_id`(az Azure Active Directory application/ügyfél-azonosító az "Előfeltételek" szakaszban említett)
- `bait_aad_secret`(az Azure Active Directory alkalmazás titkos kulcs a "Előfeltételek" szakaszában)
- `bait_aad_tenant`(az Azure Active Directory bérlő azonosítója "Előfeltételek" szakaszban említett)
- `bait_region`(frissítésétől írásának pillanatában Ez az egyetlen választása marad: eastus)
- `bait_resource_group_name`(a korábban kiválasztott erőforráscsoport)

Ezek az értékek rendelte, a módosított sorokat a settings.cfg fájl a következő szöveget kell hasonlítania:

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Mentse és zárja be `settings.cfg`.

Most már bezárhatja a CLI ablakot, ahol a kötegelt AI erőforrás létrehozása parancsok hajtja végre. Ebben az oktatóanyagban minden további lépésben használja a CLI Azure Machine Learning-munkaterület indítani.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Az Azure Machine Learning-munkaterület végrehajtási környezet előkészítése

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>A HDInsight-fürthöz regisztrálja az Azure Machine Learning-munkaterület számítási célként

Ha a HDInsight-fürt létrehozása befejeződött, a projekthez számítási célként regisztrálása az alábbiak szerint a fürt:

1.  Írja meg az Azure Machine Learning parancssori felület a következő parancsot:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Ez a parancs két fájlt ad hozzá `myhdi.runconfig` és `myhdi.compute`, a projekthez `aml_config` mappát.

1. Nyissa meg a `myhdi.compute` fájlt a kedvenc szövegszerkesztőjével. Módosítsa a `yarnDeployMode: cluster` sor olvasására `yarnDeployMode: client`, majd mentse és zárja be a fájlt.
1. A következő parancsot használja a HDInsight környezet előkészítése:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Helyi függőségek telepítése

Nyissa meg a CLI Azure Machine Learning-munkaterület, és telepítse a következő parancsot a helyi végrehajtásához szükséges függőségek:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

Ebben a forgatókönyvben a nyilvánosan elérhető légifelvételes szimbólumokat adatait használja a [nemzeti mezőgazdaság szimbólumokat Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) 1-mérő felbontásban. Azt 224 képpontos x 224 képpontos PNG-fájlok az eredeti NAIP adatokból levágott és föld használata címkéi szerint rendezve készleteinek hozta létre a [nemzeti föld fedik le a adatbázis](https://www.mrlc.gov/nlcd2011.php). A minta rendszerképének címke "Developed" teljes méretben jelenik meg:

![Egy minta csempe fejlett földek](media/scenario-aerial-image-classification/sample-tile-developed.png)

Osztály elosztott terhelésű készletek ~ 44 és 11 k képek a szolgálnak modell betanítási és ellenőrzéséhez. Bemutatjuk a modell telepítési ~ 67-k lemezképen mozaik Middlesex megye, MA – home, a Microsoft új Angliai kutatási és fejlesztési (NERD) center beállítása. Hogyan a kép halmazok számított további információkért tekintse meg a [Embarrassingly párhuzamos kép besorolás git-tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Middlesex megye, Massachusetts helye](media/scenario-aerial-image-classification/middlesex-ma.png)

A telepítés során az ebben a példában használt légifelvételes kép beállítása a létrehozott tárfiók volt át. A képzési, az érvényesítési és a operationalization képek nincs minden 224 képpontos x 224 képpontos PNG-fájlok / négyzetes mérő egy képponttal felbontásban. A képzés és érvényesítési képeket a föld felirat alapján almappák rendelkezik lett rendezve. (A föld használja a operationalization képek feliratai ismeretlen és sok esetben nem egyértelmű; többféle föld néhány ezeket a lemezképeket tartalmaz.) Hogyan a kép halmazok számított további információkért tekintse meg a [Embarrassingly párhuzamos kép besorolás git-tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Példa az Azure Storage képek fiók (nem kötelező) megtekintése:
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Keresse meg a keresési sávon a képernyő felső mentén a tárfiók nevét. Kattintson a tárfiók a keresési eredmények között.
2. A tárfiók fő ablaktáblán "Blobok" hivatkozásra kattintva.
3. Kattintson a tároló "vonat." Használjon megfelelő föld könyvtárainak listáját kell megjelennie.
4. Kattintson bármelyik ezeket a könyvtárakat tartalmaz képek listájának betöltése.
5. Kattintson a képet, és töltse le a képet.
6. Szükség esetén kattintson a "teszt" és "middlesexma2016" megtekintése, valamint azok tartalmát nevű tárolók.

## <a name="modeling"></a>Modellezés

### <a name="training-models-with-azure-batch-ai"></a>Azure Batch AI képzési modellek

A `run_batch_ai.py` egy kötegelt AI képzési feladat elküldésére szolgáló parancsfájl a munkaterületet üzemeltető projekt "Code\02_Modeling" almappájában. Ez a feladat egy kép osztályozó (AlexNet vagy a ImageNet pretrained ResNet 18) a felhasználó által kiválasztott DNN retrains. A mélység értéke átképezési is megadható: csak a hálózati végső rétegét átképezési csökkentheti overfitting Ha néhány képzési minták érhetők el, a teljes hálózati pontosabb beállításra közben (vagy a AlexNet, a teljes csatlakoztatott rétegek) nagyobb modell vezethet Ha a gyakorlókészlethez megfelelően nagy teljesítmény.

Ha a képzés feladat befejeződik, ezt a parancsfájlt a modell (és a modell egész kimeneti és a karakterlánc címkék közötti leképezést leíró fájl) és menti az előrejelzés a blob storage. A CSALÉTEK feladat naplófájl elemzi hiba arány javulás keresztül a képzés alapidőszakkal timecourse kibontásához. A hiba arány fokozása timecourse AML munkaterület futtatási előzmények funkció szeretné későbbi megtekintés van bejelentkezve.

Válassza ki a betanított modell egy pretrained modelltípus és megőrzési mélységet nevét. A beállításokat a következő parancsot, ahol majd lehagyja futtassa a parancsot az az Azure ML parancssori felület átképezési írási:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Az Azure Machine Learning körülbelül fél órát érvénybe befejezéséhez futtassa várt. Azt javasoljuk, hogy néhány hasonló parancsokat (a kimeneti modell neve, a pretrained modelltípus és a megőrzési mélység változó), hogy összehasonlíthatja a modellek betanítása különböző módszerekkel teljesítményét.

### <a name="training-models-with-mmlspark"></a>MMLSpark képzési modellek

A `run_mmlspark.py` parancsfájl a munkaterületet üzemeltető projekt "Code\02_Modeling" almappájában betanításához használandó egy [MMLSpark](https://github.com/Azure/mmlspark) kép besorolási modell. A parancsfájl első featurizes a képzés beállítani egy kép osztályozó DNN pretrained ImageNet adatkészlet (AlexNet vagy egy 18-réteg ResNet) a lemezképeket. A parancsfájl a featurized lemezképet használja (véletlenszerű erdőben vagy logisztikai regresszió modell) a lemezképeket besorolására egy MMLSpark modell betanításához. A kép TesztKészlet majd featurized, és a betanított modell a program pontozza a mennyiségeket. A tesztet állítsa be a modell előrejelzés pontosságát kiszámítása és Azure Machine Learning munkaterület futtatási előzmények funkció a rendszer naplózza. Végezetül a betanított modell MMLSpark és az előrejelzés a TesztKészlet a kerülnek blob-tároló.

Válassza ki a betanított modell egy pretrained modelltípus és egy MMLSpark modelltípus egyedi kimeneti modell nevét. A beállításokat a következő parancs sablonban, a jelzett majd lehagyja futtassa a parancsot az az Azure ML parancssori felület átképezési írási:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Egy további `--sample_frac` paraméter használható képzése és tesztelni a modellt a rendelkezésre álló adatok egy részét. Kis minta tört használata csökkenti a futásidejű és memória követelmények csökkenti a betanított modell pontosságát. (Például a futtató `--sample_frac 0.1` várt nagyjából 20 percet vesz igénybe.) További információt erről és más paramétereket, futtassa az alábbi parancsot `python Code\02_Modeling\run_mmlspark.py -h`.

Tanácsos több alkalommal a parancsfájl futtatásával más bemeneti paraméterekkel. Az eredményül kapott modell teljesítményétől majd hasonlíthatók össze az Azure Machine Learning munkaterület futtatása előzmények funkció.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>A munkaterület futtatása előzmények funkciójával modell teljesítmény összehasonlítása

Után végrehajtott két vagy több képzési mindkét fut, nyissa meg a Futtatás előzmények funkció munkaterület a bal oldali menüsáv mentén óra ikonjára kattint. Válassza ki `run_mmlspark.py` a bal oldali parancsfájlok listájáról. Ablaktábla betölti a vizsgálat beállítása pontossága összes kísérletekhez összehasonlítása. További részletek megtekintéséhez görgessen le, majd kattintson az egyes feladatfuttatáshoz neve.

## <a name="deployment"></a>Környezet

Távoli végrehajtás használata a HDInsight Middlesex megye, MA elrendezéséhez légifelvételes képek egyik a betanított modell alkalmazásához a kívánt modell neve beszúrása a következő parancsot, és végre:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Egy további `--sample_frac` paraméter azok a modell rendelkezésre álló adatok egy részével is használható. Kis minta tört használata csökkenti a rovására előrejelzés teljességét futásidejű és memória követelmények. További információt erről és más paramétereket, futtassa az alábbi parancsot `python Code\03_Deployment\batch_score_spark -h`.

Ezt a parancsfájlt a modell előrejelzéseket ír a tárfiók. Az előrejelzés ellenőrizni lehet a következő szakaszban leírtak szerint.

## <a name="visualization"></a>Megjelenítés

A "Modell előrejelzésének analysis" Jupyter notebook a munkaterületet üzemeltető projekt "Code\04_Result_Analysis" almappájában visualizes egy modell előrejelzéseket. Betölteni, és futtassa a notebook a következőképpen:
1. Nyissa meg a projektet a munkaterület, és kattintson a mappára ("fájlok") ikonra a bal oldali menüből betöltése a könyvtár listázása.
2. Keresse meg a "Code\04_Result_Analysis" másolja, majd kattintson a notebook nevű, "Modell előrejelzésének elemzés." A notebook preview megjelenésének üzenetnek kell megjelennie.
3. Kattintson a "Start Notebook kiszolgáló" a notebook betöltése.
4. Az első cellába adja meg a modell, amelynek szeretné elemzése a jelzett eredmények nevét.
5. Kattintson a "cella Futtatás -> minden" összes cellák hajtson végre a notebook.
6. Olvasás mellett a notebook tudhat meg többet az elemzések és a képi megjelenítések azt mutatja be.

## <a name="cleanup"></a>Tisztítás
Ha befejezte a példa, azt javasoljuk, hogy törölje a következő parancs végrehajtása az Azure parancssori felület által létrehozott összes:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referencia

- [A Embarrassingly párhuzamos kép besorolás tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Ismerteti a dataset konstrukció ingyenesen elérhető szimbólumokat és címkék
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub-adattár
   - A modell betanítása és kiértékelése MMLSpark a további példákat tartalmaz

## <a name="conclusions"></a>Következtetéseket

Az Azure Machine Learning-munkaterület segítségével könnyen telepíthető a távoli számítási célokon kódjukat adatszakértőkön. Ebben a példában a helyi MMLSpark képzési kód telepítve lett egy HDInsight-fürt távoli végrehajtás, és egy helyi parancsfájl elindítja az Azure Batch AI GPU fürt képzési feladatot. Az Azure Machine Learning munkaterület tartozó futtatási előzményei szolgáltatás több modell teljesítményétől követni, és segített a legpontosabb modell azonosításának. A munkaterület a Jupyter notebookok szolgáltatás segített jelenítheti meg a modellek előrejelzéseket egy interaktív, grafikus környezetben.

## <a name="next-steps"></a>Következő lépések
Mélyebben elmerülhet az ebben a példában:
- Az Azure Machine Learning munkaterület futtatása előzmények funkció kattintson a fogaskerék szimbólumok kiválasztásához, mely diagramok és a metrikák jelennek meg.
- Vizsgálja meg a minta parancsfájlok utasításokra hívja a `run_logger`. Ellenőrizze, hogy megértette minden egyes metrika rögzítésének módját.
- Vizsgálja meg a minta parancsfájlok utasításokra hívja a `blob_service`. Ellenőrizze, hogy tudomásul veszi hogyan betanított modellek és előrejelzéseket tárolása és lekérése a felhőből.
- Megismerkedhet a tárolók a blob storage-fiók létrehozása a tartalmát. Győződjön meg arról, hogy tisztában mely parancsfájl vagy parancs felelős az egyes fájlok kiszolgálócsoportok létrehozása.
- A képzési parancsfájl egy másik MMLSpark modelltípus betanítása, vagy módosítsa a modell-hiperparamétereket módosításával. A futtatási előzményei szolgáltatás segítségével határozza meg, hogy a módosítások növelhető vagy csökkenthető, a modell pontosságát.
