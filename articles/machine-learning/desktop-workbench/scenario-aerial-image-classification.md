---
title: Légifelvételek besorolása |} A Microsoft Docs
description: Légifelvételek besorolása a valós forgatókönyvekben ad útmutatást
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
ms.component: core
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: eb788f56825166ccaa376d32b07371db0588edc8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923387"
---
# <a name="aerial-image-classification"></a>Légifelvételek besorolása

Ez a példa bemutatja, hogyan összehangolja elosztott képzés és operacionalizálás képbesorolási modellek az Azure Machine Learning Workbench használatával. Két módszer képzéshez használjuk: (i) finomítása, a Neurális hálózat használatával egy [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) GPU-fürtön, és (ii) a [Microsoft Machine Learning, az Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) csomagot képjellemzők imagenet CNTK-modellek és származtatott funkciót osztályozó eszközökkel taníthat be. Ezután alkalmazunk a betanított modellek párhuzamos módon, nagy méretű kép csoportok a felhőben található egy [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) fürt, lehetővé téve a képzés és operacionalizálás sebességével méretezésre hozzáadásával vagy eltávolításával a munkavégző csomópontok.

Ebben a példában két megközelítés vihetők át a tanulást, amely modellek imagenet képzési Neurális hálózatok előzmények a költségek elkerülése érdekében emeli ki. Neurális hálózat általában átképezési GPU számítási igényel, de nagyobb pontosságát is vezethet, ha a betanítási készlet nem elég nagy. Egy egyszerű osztályozó featurized képeken képzési nem igényel a GPU számítási, természetüknél fogva gyors és méretezhető önkényesen, és kevesebb paramétert illeszkedik. Ez a módszer esetén ezért igényük néhány képzési minták érhető el – a helyzet gyakran az egyéni használati eseteket. 

A Spark-fürt ebben a példában használt 40 feldolgozó csomóponttal rendelkezik, és a költségek ~$40/hr működéséhez; a Batch AI-fürt erőforrásainak nyolc gpu-kat tartalmaznak, és optimalizálhatja a költségeket ~$10/hr megfelelően működjenek. Ez az útmutató elvégzése körülbelül három órát vesz igénybe. Ha elkészült, az törlési utasítások távolítsa el az erőforrásokat hozott létre, és állítsa le a felmerülő költségek.

## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás

A nyilvános GitHub-tárházat a valós forgatókönyvekben a tartalmazza az összes anyag – Kódminták ebben a példában a szükséges:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Használati eset leírása

Ebben a forgatókönyvben azt besorolására 224-fogyasztásmérő x 224-fogyasztásmérő grafikon, légi felvételeken képeken látható föld típusú gépi tanulási modellek betanításához. Szárazföldi használata képbesorolási modellek segítségével nyomon követheti a urbanization, erdőirtás, élőhelyek elvesztését, és más jelentős környezeti trendeket, rendszeres időközönként használatával gyűjtött légifelvételes képeken. Azt előkészítette az Amerikai Egyesült Államokból képanyag alapján képzés és érvényesítési kép beállítása Nemzeti mezőgazdaság képanyag Program és a föld az USA által közzétett feliratainak használata National föld Cover adatbázis. A példában képek föld használja az osztályok itt látható:

![Példa régiók az egyes föld címke használata](media/scenario-aerial-image-classification/example-labels.PNG)

Képzés és az osztályozó által igénybe vett modell érvényesítése után kerülnek érvényre, légi felvételeken lemezképek Middlesex megyét, MA – home, a Microsoft új England kutatási és fejlesztési (ÉLÉNKEN) Center – bemutatják, hogyan ezek a modellek segítségével városi trendjei tanulmánya átfedési fejlesztés.

Egy rendszerkép osztályozó tanulással előállításához, az adatszakértők gyakran hozhat létre több modelleket különféle módszereket és a legtöbb nagy teljesítményű modell kiválasztása. Az Azure Machine Learning Workbench a tudósok képzési összehangolják a különböző számítási környezetekben, nyomon követheti és a teljesítmény, a több modell összehasonlítására adatok segítségével, és a alkalmazni egy kiválasztott modell a felhőben lévő nagy adatkészletek.

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

Ebben a példában a rendszerkép-adatok és modellek imagenet részeként az Azure storage-fiók. Egy Azure HDInsight Spark-fürt elolvassa ezeket a fájlokat, és egy kép osztályozási modell MMLSpark használatával. A betanított modell és az előrejelzések majd a tárfiókot, ahol azok elemzése és helyileg futtatja a Jupyter notebook által ábrázolt írja. Az Azure Machine Learning Workbench koordinálja a távoli végrehajtás-szkriptek a Spark-fürtön. Még több, különböző módszerekkel, lehetővé téve a felhasználó kiválaszthat a legtöbb nagy teljesítményű modellt betanított modell pontossága metrikáinak követi nyomon.

![A légifelvételek besorolása valós forgatókönyvekben sematikus nézete](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Ezen részletes utasítások alapján először végigkalauzolja Önt a létrehozása és a egy Azure storage-fiók és a Spark-fürtöt, beleértve az adatok átvitel és a függőségi telepítési előkészítését. Majd indítsa el a betanítási feladatokat, és az eredményül kapott modellek teljesítményének összehasonlítására ismertetik. Végül ezek bemutatják, hogyan alkalmazza a kiválasztott modell számára egy nagy méretű kép a Spark-fürtön, és elemezze az előrejelzési eredményeket helyileg.


## <a name="set-up-the-execution-environment"></a>A végrehajtási környezetének beállítása

Az alábbi utasításokat követve végigvezetik az ebben a példában a végrehajtási környezet beállításának folyamatán.

### <a name="prerequisites"></a>Előfeltételek
- Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el)
    - 40 feldolgozó csomóponttal (168 magok teljes) létrehozhat egy HDInsight Spark-fürt. Győződjön meg arról, hogy a fiók rendelkezik-e elegendő elérhető maggal áttekintésével, a "használat + kvóták" fülre az előfizetés az Azure Portalon.
       - Ha kevesebb elérhető maggal rendelkezik, előfordulhat, hogy módosítja a HDInsight-fürt sablon csökkentheti a kiépített feldolgozók száma. Ehhez útmutatást a "A HDInsight Spark-fürt létrehozása" szakaszban jelennek meg.
    - Ez a példa létrehoz egy Batch AI Training fürt két NC6 (1 GPU, 6 vCPU) virtuális gépek. Győződjön meg arról, hogy a fiók rendelkezik elegendő elérhető maggal az USA keleti régiójában áttekintésével, a "használat + kvóták" fülre az előfizetés az Azure Portalon.
- [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)
    - Kövesse a [telepítési és létrehozási rövid útmutató](../service/quickstart-installation.md) az Azure Machine Learning Workbench telepítése, és a kísérleti és Modellkezelési fiókok létrehozásához.
- [Batch AI](https://github.com/Azure/BatchAI) Python SDK-t és az Azure CLI
    - Hajtsa végre a következő szakaszai a [Batch AI receptek információs](https://github.com/Azure/BatchAI/tree/master/recipes):
        - "Előfeltételek"
        - "Létrehozása és az Azure Active Directory (AAD) alkalmazást"
        - "BatchAI erőforrás-szolgáltatók regisztrálása" (a "futtatási receptek az Azure CLI-vel")
        - "Az Azure Batch AI felügyeleti ügyfél telepítése"
        - "Az Azure Python SDK telepítése"
    - Rekord az ügyfél-azonosító, titkos kulcsot, és az Azure Active Directory-alkalmazás létrehozása átirányítjuk Bérlőazonosítója. Az oktatóanyag későbbi részében fogja használni ezeket a hitelesítő adatokat.
    - Jelen cikk írásakor az Azure Machine Learning Workbench és az Azure Batch AI külön elágazásoknak az Azure CLI használata. Az egyértelműség kedvéért nevezzük a Workbench "egy az Azure Machine Learning Workbench alkalmazásból indított CLI", a parancssori felület és az általános kiadás verziója (amely magában foglalja a Batch AI), "Az Azure CLI."
- [Az AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), egy ingyenes segédprogram az Azure storage-fiókok közötti fájlátvitel összehangolásáért
    - Győződjön meg arról, hogy a mappát, amely tartalmazza az AzCopy végrehajtható fájlt a rendszer PATH környezeti változó be van-e. (Érhetők el a környezeti változók módosításával [Itt](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Egy SSH-ügyfél; Javasoljuk, hogy [PuTTY](http://www.putty.org/).

Ebben a példában a Windows 10 rendszerű; tesztelés futtatható bármely Windows gépről, beleértve az Azure Data Science Virtual Machines kell lennie. Az Azure CLI lett telepítve, a következők szerint az MSI-ből [ezek az utasítások](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Kisebb módosítások segítségére lesz szüksége (például filepaths módosításai) futtatásakor ebben a példában a MacOS-gépeken.

### <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Ebben a példában egy HDInsight Spark-fürt és a egy Azure storage-fiók megfelelő fájlok gazdagépre van szükség. Kövesse ezeket az utasításokat az új Azure-erőforráscsoportot, amelybe az erőforrások létrehozásához:

#### <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Légifelvételes Képbesorolás", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra
 
#### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

1. Után az Azure Machine Learning Workbench a projekt betöltésekor, nyissa meg a parancssori felület (CLI) kattintson a fájl -> parancssor megnyitása.
    A CLI ezen verziójának használata a legtöbb az oktatóanyag. (A felsoroltak, a rendszer felkéri nyissa meg a CLI a Batch AI-erőforrások előkészítése egy másik verziója.)

1. A parancssori felületről jelentkezzen be az Azure-fiókjába a következő parancs futtatásával:

    ```
    az login
    ```

    A rendszer felkéri egy URL-címet, és írja be a megadott ideiglenes kód; felkeresése a webhelyen az Azure-fiók hitelesítő adatait kéri.
    
1. Bejelentkezési befejeződése után térjen vissza a parancssori Felületet, és annak meghatározására, hogy melyik Azure-előfizetéssel elérhető Azure-fiókjába a következő parancsot:

    ```
    az account list
    ```

    Ez a parancs felsorolja az Azure-fiókjával társított előfizetéseket. Keresse meg a használni kívánt előfizetés azonosítója. Az előfizetés-azonosító írási felsoroltak közül az az alábbi parancsot, majd állítsa be az aktív előfizetés parancs végrehajtásával:

    ```
    az account set --subscription [subscription ID]
    ```

1. Ebben a példában létrehozott Azure-erőforrások Azure-erőforráscsoporttal együtt tárolja. Válasszon egy egyedi erőforráscsoport-nevet, és jegyezze fel felsoroltak közül, majd futtassa mindkét parancsot az Azure-erőforráscsoport létrehozásához:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>A tárfiók létrehozása

Most létrehozunk, hogy a gazdagépek projekt, amely szerint a HDInsight Spark érhető el fájlokat a storage-fiókot.

1. Válassza ki a tárfiók egyedi neve, és jegyezze fel a következő felsoroltak `set` parancsot, majd hozzon létre egy Azure storage-fiók mindkét parancsok végrehajtásával:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. A tárfiókkulcsok listázása a következő parancs futtatásával:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Jegyezze fel az értéket a `key1` kulcsaként storage a következő parancsot, majd futtassa a parancsot, az érték tárolásához.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Hozzon létre egy fájlmegosztást, nevű `baitshare` a storage-fiókjába a következő paranccsal:

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. A kedvenc szövegszerkesztőjével, betöltése a `settings.cfg` fájlt a "Code" alkönyvtárat az Azure Machine Learning Workbench-projektet, és helyezze be a tárfiók nevét és a kulcs jelöli. Mentse és zárja be a `settings.cfg` fájlt.
1. Ha még nem tette, töltse le és telepítse a [AzCopy](http://aka.ms/downloadazcopy) segédprogramot. Győződjön meg arról, hogy az AzCopy végrehajtható fájl elérési úton van a rendszer beírja a "AzCopy", és lenyomja az Enter billentyűt a hozzá tartozó dokumentáció megjelenítése.
1. Adja ki a megfelelő helyekre a tárfiókban lévő összes a mintaadatokat, imagenet modelleket és modell betanítási szkriptekhez átmásolása a következő parancsokat:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    A várt fájlátvitel körülbelül egy óra. Amíg várakozik, folytathatja a következő szakaszra: nyisson meg egy másik parancssori felületen keresztül Workbench, és definiálja újra az ideiglenes változók szükség lehet.

#### <a name="create-the-hdinsight-spark-cluster"></a>A HDInsight Spark-fürt létrehozása

Az ajánlott módszer hozhat létre HDInsight-fürtöt használ a HDInsight Spark fürtön resource manager-sablon szerepel a projekt "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" almappájában.

1. A HDInsight Spark-fürt sablon a "template.json" fájlt a projekt "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" almappájában. Alapértelmezés szerint a sablon létrehoz egy Spark-fürt 40 feldolgozó csomópontokat. Ha módosítania kell ezt a számot, tetszőleges szövegszerkesztőben nyissa meg a sablon, és cserélje le a munkavégző csomópont száma tetszőleges "40" példányai.
    - Ha úgy dönt, a feldolgozó csomópontok száma kisebb, később jelentkezhetnek memóriahiány hibák. Memória típusú hibái elleni, előfordulhat, hogy a képzés és operacionalizálás parancsfájlok futtathatók a rendelkezésre álló adatok egy részét ez a dokumentum későbbi szakaszában leírtak szerint.
2. Válasszon egy egyedi nevet és jelszót a HDInsight-fürt, és beírhatók a következő parancsot a felsoroltak közül: majd hozni a fürtöt a parancsok kiadásával:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

A fürt üzembe helyezése (beleértve a kiépítés és a parancsfájl a művelet végrehajtása) akár 30 percet is igénybe vehet.

### <a name="set-up-batch-ai-resources"></a>Állítsa be a Batch AI-erőforrások

Várakozás közben a Storage-fiók fájlátvitel és a Spark-fürt üzembe helyezése végrehajtásához, előkészítheti a Batch AI hálózati File Server (NFS) és a GPU-fürtön. Nyisson meg egy Azure CLI-parancssort, és futtassa a következő parancsot:

```
az --version 
```

Ellenőrizze, hogy `batchai` szerepel a listán a telepített modulok között. Ha nem, akkor előfordulhat, hogy használni egy másik parancssori felületen (például egy Workbench keresztül megnyitva).

Ezután ellenőrizze, hogy a szolgáltató regisztrálása sikeresen befejeződött. (A szolgáltató regisztrációja akár 15 percet vesz igénybe, és akkor még lehetnek folyamatban lévő, ha nemrég fejeződött be a [Batch AI telepítési utasításokat](https://github.com/Azure/BatchAI/tree/master/recipes).) Győződjön meg arról, hogy "Microsoft.Batch" és "Microsoft.BatchAI" is "Regisztrálva" állapot a következő parancs kimenetében megjelennek:

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Ha nem, futtassa a következő szolgáltató regisztrációs parancsokat, és várjon a regisztráció befejezéséhez körülbelül 15 percenként.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Az alábbi parancsokat a korábban erőforrás csoport és a storage-fiók létrehozása során használt értékeket cserélje le a zárójeles kifejezések módosítása Ezután értékeket tárolja változókként a parancsok végrehajtásával:
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Azonosítsa az Azure Machine Learning-projektet tartalmazó mappa (például `C:\Users\<your username>\AzureML\aerialimageclassification`). A zárójeles értékét cserélje le a mappa fájl elérési útja (a nincs záró fordított perjel), és hajtsa végre a parancsot:
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Most már készen áll az ehhez az oktatóanyaghoz szükség a Batch AI-erőforrások létrehozása.

#### <a name="prepare-the-batch-ai-network-file-server"></a>A Batch AI fájl kiszolgáló előkészítése

A Batch AI-fürtöt a betanítási adatok, egy hálózati fájlkiszolgálón fér hozzá. Az NFS és a egy Azure-fájlmegosztás vagy egy Azure Blob Storage-fájlok elérésekor adatelérési gyorsabban several-fold lehet.

1. Egy hálózati fájlkiszolgálókat hozhat létre a következő parancs kiadásával:

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p "Dem0Pa$$w0rd" --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Ellenőrizze a kiépítési állapotot, a hálózati fájlkiszolgáló, a következő paranccsal:

    ```
    az batchai file-server list
    ```

    A "provisioningState" a "landuseclassifier" nevű fájl kiszolgáló van "sikeres", akkor készen áll a használatra. A várt üzembe helyezés nagyjából öt perc alatt.
1. Az NFS IP-címe található az előző parancs (a "fileServerPublicIp" tulajdonság "mountSettings" alatt). Írás az IP-cím a következő parancsot a felsoroltak közül, majd tárolja az értéket úgy, hogy végrehajtja a parancsot:

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. A kedvenc SSH-eszközzel (a következő parancsot használja minta [PuTTY](http://www.putty.org/)), hajtsa végre a projekt `prep_nfs.sh` a képzés és érvényesítési lemezkép átvitele az NFS-parancsprogram beállítja van.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Nem kell aggódnia amiatt, ha az adatok letöltésének és végrehajtási frissítéseket görgessen a rendszerhéj ablakát között ezért gyorsan, hogy azok olvashatatlanná.

Igény szerint, hogy az adatátvitel járt-e a fájlkiszolgáló a kedvenc SSH-eszközzel bejelentkezni, és ellenőrzi a tervek szerint ellenőrizheti a `/mnt/data` könyvtár tartalmát. Két mappa training_images és validation_images keresse meg, akkor minden egyes tartalmazó föld alapján elnevezett almappákban a kategóriák használja.  A képzés és érvényesítési csoportok tartalmaznia kell a ~ 44 k és a ~ 11-k lemezképek jelölik.

#### <a name="create-a-batch-ai-cluster"></a>Hozzon létre egy Batch AI-fürtön

1. A fürt létrehozása a következő parancsot:

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p "Dem0Pa$$w0rd" --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. A következő paranccsal ellenőrizze, hogy a fürt a kiépítési állapot:

    ```
    az batchai cluster list
    ```

    Amikor a lefoglalási állapota a fürt átméretezése az állandó változtatásai "landuseclassifier" nevű, akkor lehet feladatok elküldéséhez. Azonban feladatok nem indulnak el, amíg a fürt összes virtuális gép van hátra az "előkészítés" állapot. Ha a fürt "hibák" tulajdonsága nem null értékű, hiba történt a fürt létrehozása során, és nem lehet.

#### <a name="record-batch-ai-training-credentials"></a>Rögzítse a Batch AI Training hitelesítő adatok

Várja meg a fürt foglalási végrehajtásához, nyisson meg a `settings.cfg` fájlt a projekt tetszőleges szövegszerkesztőben a "Code" alkönyvtárat. Frissítse a következő változókat a saját hitelesítő adataival:
- `bait_subscription_id` (a 36 karakterből álló Azure-előfizetés azonosítója)
- `bait_aad_client_id` (az Azure Active Directory application/ügyfél-azonosító az "Előfeltételek" szakaszban ismertetett)
- `bait_aad_secret` (az Azure Active Directory application secret az "Előfeltételek" szakaszban ismertetett)
- `bait_aad_tenant` (az Azure Active Directory bérlői azonosító az "Előfeltételek" szakaszban ismertetett)
- `bait_region` (a jelen cikk írásakor, az egyetlen lehetőség van: USA keleti régiója)
- `bait_resource_group_name` (a korábban kiválasztott erőforráscsoport)

Miután hozzárendelte a ezeket az értékeket, a módosított sorok settings.cfg fájlt a következő szöveget kell hasonlítania:

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

Most már bezárhatja a parancssori felület ablakában, ahol a Batch AI-erőforrás létrehozása parancsok hajtja végre. Minden további lépésben ebben az oktatóanyagban egy Azure Machine Learning Workbench alkalmazásból indított CLI használatával.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Az Azure Machine Learning Workbench végrehajtási környezet előkészítése

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Regisztrálja a HDInsight-fürt az Azure Machine Learning Workbench számítási célnak

HDInsight-fürt létrehozása után a projekt számítási célként regisztrálása módon a fürt:

1.  Az az Azure Machine Learning parancssori felülete a következő parancs kiadásával:

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Ez a parancs két fájlt ad hozzá `myhdi.runconfig` és `myhdi.compute`, a projekthez `aml_config` mappát.

1. Nyissa meg a `myhdi.compute` fájlt a kedvenc szövegszerkesztőjével. Módosítsa a `yarnDeployMode: cluster` sor olvasható `yarnDeployMode: client`, majd mentse és zárja be a fájlt.
1. Futtassa a következő parancsot a használatra a HDInsight-környezet előkészítése:
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Helyi függőségek telepítése

Nyisson meg egy parancssori felület az Azure Machine Learning Workbench, és a helyi végrehajtásra az alábbi parancsot a szükséges függőségek telepítése:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

Ebben a forgatókönyvben a nyilvánosan elérhető légifelvételes képanyag adatait használja a [nemzeti mezőgazdaság képanyag Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) : 1-fogyasztásmérő. A Microsoft hozta létre, 224 képpontos x 224 képpontos PNG-fájlok, az eredeti NAIP adatok csonkolva és a föld használata címkéit alapján rendezett készleteket a [nemzeti föld lefedje adatbázis](https://www.mrlc.gov/nlcd2011.php). Egy képet "Developed" címkével ellátott teljes méretben jelenik meg:

![Fejlett föld minta csempe](media/scenario-aerial-image-classification/sample-tile-developed.png)

Osztály elosztott terhelésű készletek ~ 44 k és 11 k képeket is használt modellek tanítása és ellenőrzése,. Bemutatjuk, hogyan modell-üzembehelyezés ~ 67-k lemezképen mozaik Middlesex megyét, MA – home, a Microsoft új England kutatási és fejlesztési (ÉLÉNKEN) center beállítása. Hogyan ezen kép készletek is össze a további információkért lásd: a [zavaróan párhuzamos Képbesorolás git-tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Middlesex megyét, a Massachusetts helye](media/scenario-aerial-image-classification/middlesex-ma.png)

A telepítés során a légifelvételek készletek ebben a példában használt átruházták a létrehozott storage-fiókot. Az oktatás, érvényesítése és képeket a működőképessé az összes 224 képpontos x 224 képpontos PNG-fájlok képpontonkénti négyzetes mérni / felbontásban. A képzés és érvényesítési képeket rendelkezik lett rendezve almappákat a föld használata címke alapján. (A föld használja a operacionalizálás rendszerképek címkékhez ismeretlen és sok esetben nem egyértelmű; ezek a lemezképek egyes tartalmaznak többféle föld.) Hogyan ezen kép készletek is össze a további információkért lásd: a [zavaróan párhuzamos Képbesorolás git-tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Rendszerképek az Azure Storage-fiók (nem kötelező) Példa megtekintése:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a keresősávba, a képernyő felső részén a tárfiókja nevére. Kattintson a tárfiók a keresési eredmények között.
2. A tárfiók a fő panelen a "BLOB" hivatkozásra kattintva.
3. Kattintson a "train". tároló Megjelenik a föld szerint nevű könyvtárainak listáját használja.
4. Kattintson bármelyik ezeket a könyvtárakat a benne található rendszerképek listájának betöltése.
5. Kattintson a képet, és töltse le a képet.
6. Ha szükséges, kattintson a tárolók neve a "teszt" és "middlesexma2016" megtekintése, valamint azok tartalmát.

## <a name="modeling"></a>Modellezés

### <a name="training-models-with-azure-batch-ai"></a>Az Azure Batch AI Training modellek

A `run_batch_ai.py` a Workbench-projekt "Code\02_Modeling" almappájában parancsfájl egy Batch AI betanítási feladat kiadására használatos. Ez a feladat egy kép osztályozó (AlexNet vagy ResNet 18-ra épít a imagenet) a felhasználó által kiválasztott DNN retrains. Átképezési mélysége is megadható: a hálózat csak a végső réteg átképezési csökkentheti overfitting Ha néhány képzési minták érhetők el, a teljes hálózat finomhangoló közben (vagy, AlexNet, a teljes körűen csatlakoztatott rétegek) újabb modellre vezethet Ha a betanítási készlet nem elég nagy teljesítmény.

A betanítási feladat befejezését követően a szkript a modell (és a egy fájl a modell egész kimeneti és a karakterlánc-címkéket közötti leíró) és menti az előrejelzés a blob storage. Bontsa ki a timecourse hiba arány javulás a betanítási alapidőszakkal keresztül a BAIT feladat naplófájlt a rendszer elemzi. A hiba arány fokozása timecourse AML Workbench futtatási előzmények funkció szeretné későbbi megtekintés céljából a rendszer naplózza.

Válassza ki a betanított modell, imagenet modelltípus és a egy megőrzési mélységét. Írás a beállításokat a következő parancsot, ahol kezdjük úgy, hogy végrehajtja a parancsot a az Azure ML parancssori felülete átképezési:

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

Az Azure Machine Learning körülbelül fél óra figyelembe befejezéséhez futtassa a várt. Azt javasoljuk, hogy néhány hasonló parancsot (a kimeneti modell neve, a modell imagenet típusa és a megőrzési mélység változó), hogy össze lehessen hasonlítani a különböző módszerekkel betanított modellek teljesítményét.

### <a name="training-models-with-mmlspark"></a>MMLSpark képzési modelljeit

A `run_mmlspark.py` taníthat be a Workbench-projekt "Code\02_Modeling" almappájában parancsfájl szolgál egy [MMLSpark](https://github.com/Azure/mmlspark) képbesorolás modellt. A szkript első featurizes képzés képek egy kép osztályozó DNN imagenet használatával épít adatkészletén (AlexNet vagy -18-réteg ResNet) beállítása. A szkript ezután használja a featurized képek (egy véletlenszerű erdőben vagy egy logisztikai regressziós modell) a képek osztályozásához egy MMLSpark modell betanításához. A teszt kép set majd featurized, és a betanított modell pontozunk. Teszt esetén a modell előrejelzés pontosságát kiszámítása és az Azure Machine Learning Workbench futtatási előzmények funkció a naplóba. Végül a betanított modell MMLSpark és az előrejelzések a teszt beállítása a blob storage-ba kerülnek mentésre.

Válassza ki a betanított modell, imagenet modelltípus és a egy MMLSpark modelltípus egyedi kimeneti modell nevét. Írás a beállításokat a következő parancs sablon felsoroltak kezdeni a átképezési úgy, hogy végrehajtja a parancsot a az Azure ML parancssori felülete:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Egy további `--sample_frac` paraméter használható betanítása és teszteli a modellt, a rendelkezésre álló adatok egy részéből. Egy kis minta töredék használatával csökkenti a betanított modell pontossága rovására futtatókörnyezeti és a memória követelményeinek. (Például a futtató `--sample_frac 0.1` várhatóan körülbelül 20 percet is igénybe vehet.) További információ erről és más paramétereket, futtassa `python Code\02_Modeling\run_mmlspark.py -h`.

Tanácsos több alkalommal a szkript futtatásához más bemeneti paraméterekkel. Az eredményül kapott modellek teljesítményének majd összehasonlíthatók az Azure Machine Learning Workbench futtatási előzmények funkció.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>A Workbench futtatási előzmények szolgáltatással modellek teljesítményének összehasonlítása

Után két végrehajtott, vagy további betanítási mindkét fut, nyissa meg a Workbench futtatási előzmények funkció a bal oldali menüsáv mentén az óra ikonra kattintva. Válassza ki `run_mmlspark.py` parancsfájlok a bal oldalon a listából. A panelen tölti be, minden Futtatás teszt set pontosságának összehasonlítása. További részletek megtekintéséhez görgessen lefelé, és kattintson egy futtatásra nevére.

## <a name="deployment"></a>Környezet

Légifelvételes lemezképek elrendezéséhez Middlesex megyét, MA távoli végrehajtás használata a HDInsight a betanított modell egyike alapján alkalmazni, helyezze be a kívánt modell neve a következő parancsot, és eszközünkkel:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Egy további `--sample_frac` paraméter használható a rendelkezésre álló adatok egy részét a modell üzembe helyezése. Egy kis minta töredék használatával csökkenti a futtatókörnyezeti és a memória követelmények rovására előrejelzési teljességét. További információ erről és más paramétereket, futtassa `python Code\03_Deployment\batch_score_spark -h`.

Ez a szkript a modell-előrejelzéseket storage-fiókjába ír. Az előrejelzés lehet megvizsgálni, a következő szakaszban leírtak szerint.

## <a name="visualization"></a>Megjelenítés

A Workbench-projekt "Code\04_Result_Analysis" almappájában "Előrejelzési elemzési minta" Jupyter notebookot megjeleníti a modell-előrejelzéseket. Betölteni, és futtassa a notebookot az alábbiak szerint:
1. A Workbench alkalmazásban nyissa meg a projektet, és kattintson a mappa ("fájlok") ikonra a bal oldali menüben a könyvtárlistán betöltése során.
2. A "Code\04_Result_Analysis" almappa keresse meg és kattintson a notebook nevű, "Modell előrejelzési elemzési." A notebook előzetes verzió megjelenésének üzenetnek kell megjelennie.
3. Kattintson a "Start Notebook Server" betölteni a notebookot.
4. Az első olyan cellára adja meg a modellt, amelynek szeretné elemezni, a felsoroltak közül eredmények nevét.
5. Kattintson a "cella Futtatás -> minden" a Jegyzetfüzet minden cella végrehajtásához.
6. Olvassa el, további információ az elemzéseket és vizualizációkat jelenítse meg a notebook együtt.

## <a name="cleanup"></a>Felesleges tartalmak törlése
Amikor befejeződött a példa, javasoljuk, hogy végrehajtja a következő parancsot az Azure parancssori felület által létrehozott erőforrásokat törölnie:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Referencia

- [A zavaróan párhuzamos Képbesorolás tárház](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Ismerteti az adatkészlet konstrukció keretrendszerekben képanyag és címkék
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub-adattár
   - További példákat tartalmaz a modell betanítása és kiértékelése az MMLSpark

## <a name="conclusions"></a>Következtetések

Az Azure Machine Learning Workbench egyszerű üzembe helyezése a kódra összpontosítsanak, a távoli számítási célnak adatszakértők számára nyújt segítséget. Ebben a példában helyi MMLSpark képzési kódok távoli végrehajtás egy HDInsight-fürtön üzemel, és a egy helyi szkript elindítja a betanítási feladatot egy Azure Batch AI GPU-fürtön. Az Azure Machine Learning Workbench futtatási előzmények funkció nyomon követi a több modell teljesítményét, és azonosíthatja a legnagyobb pontosságú modellt kommunikációnkhoz. Workbench Jupyter notebookok funkció segítségével jelenítheti meg egy interaktív, grafikus környezetben a modellek előrejelzéseket.

## <a name="next-steps"></a>További lépések
A mélyebb ebben a példában:
- Az Azure Machine Learning Workbench futtatási előzmények funkció kattintson a fogaskerék szimbólum, válassza ki, melyik diagramok és metrikák jelennek meg.
- Vizsgálja meg a minta parancsprogramok hívása utasítások a `run_logger`. Ellenőrizze, hogy megértette minden egyes metrika rögzítésének módját.
- Vizsgálja meg a minta parancsprogramok hívása utasítások a `blob_service`. Ellenőrizze, hogy megismerte, hogyan betanított modellek és előrejelzések tárolása és lekérése a felhőből.
- Fedezze fel a létrehozott blob storage-fiókjában található tárolók tartalmát. Győződjön meg arról, hogy tisztában melyik parancsfájl vagy parancs felelős az egyes fájlok csoportjának létrehozása.
- Módosítsa a tanítási szkriptet betanításához egy másik MMLSpark modell típusa, vagy a modell-hiperparaméterek módosításához. Határozza meg, hogy a módosítások növelhető vagy csökkenthető a modell pontosságát a futtatási előzmények szolgáltatással.
