---
title: DSVM és HDI a tárolók számítási az Azure Machine Learning létrehozása
description: DSVM és HDI Spark a tárolók az Azure Machine Learning-Kísérletezési számítási fürt létrehozása.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 18cf885cd71822c2c24791f3c6f55835c3204d35
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285763"
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>DSVM és HDI Spark-fürt a tárolók számítási létrehozása

Egyszerűen vertikális vagy horizontális felskálázáshoz a machine learning-kísérlet adjon hozzá további számítási célnak például az Ubuntu-alapú DSVM (adatelemző virtuális gép), és az Apache Spark for Azure HDInsight-fürt. Ez a cikk útmutatást nyújt az ezek létrehozásának lépésein keresztül számítási tárolók az Azure-ban. További információt az Azure ML számítási célnak, [áttekintése az Azure Machine Learning-Kísérletezési szolgáltatás](experimentation-service-configuration.md).

>[!NOTE]
>Győződjön meg arról, rendelkezik megfelelő engedélyekkel az erőforrások, például a virtuális gép és a HDI-fürtök létrehozása az Azure-ban, akkor a folytatás előtt. Mindkét ezeket az erőforrásokat használhatnak is számos számítási magok a konfigurációtól függően. Ellenőrizze, hogy előfizetése rendelkezik-e elegendő kapacitás a virtuális CPU-magok. Mindig megtekintheti felveszi a kapcsolatot az Azure-támogatás mag az előfizetésben megengedett maximális számának növeléséhez.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Hozzon létre egy Ubuntu dsvm-hez az Azure Portalon

Létrehozhat egy adatelemző virtuális GÉPET az Azure Portalról. 

1. Jelentkezzen be az Azure Portalon https://portal.azure.com
2. Kattintson a **+ új** hivatkozásra, és keresse meg a "adatelemző virtuális gép Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Válassza a **adatelemző virtuális gép Linux (Ubuntu)** a listában, és kövesse a képernyőn megjelenő utasításokat követve hozzon létre a dsvm-hez.

>[!IMPORTANT]
>Válasszon **jelszó** , a _hitelesítési típus_.

![jelszó használata](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Hozzon létre egy Ubuntu dsvm-hez azure-cli-vel

Egy Azure resource management-sablon segítségével is telepítheti a dsvm-hez.

>[!NOTE]
>Az összes alábbi parancsok rendszer feltételezi, hogy az Azure Machine Learning-projekt gyökérmappájában megbízik.

Először hozzon létre egy `mydsvm.json` fájlt a kedvenc szövegszerkesztőjével a `docs` mappát. (Ha nem rendelkezik egy `docs` a projekt gyökérmappájában lévő mappának, hozzon létre egyet.) Ez a fájl néhány alapvető paramétert a az Azure resource management-sablon konfigurálásához használjuk. 

Másolja és illessze be a következő JSON-kódrészlet azokat a `mydsvm.json` fájlt, és töltse ki a megfelelő értékeket:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

Az a _vmSize_ mező, használhat bármilyen szereplő suppported Virtuálisgép-méretet a [Ubuntu dsvm-hez az Azure resource management-sablon](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Javasoljuk, hogy valamelyikét használja az alábbi méretek a tárolók számítási az Azure gépi tanulás. 


>[!TIP]
> A [mély tanulási célú számítási feladatokhoz](how-to-use-gpu.md) központilag telepíthető a GPU-alapú virtuális gépeket.

- [Általános célú virtuális gépek](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [GPU-alapú virtuális gépek](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

További információ ezen [az Azure-ban Linux rendszerű virtuális gépek méretei](../../virtual-machines/linux/sizes.md) és azok [díjszabási információk](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Indítsa el a parancssori felület ablakában az Azure ML Workbench alkalmazásból kattintva **fájl** --> **parancssor megnyitása**, vagy **megnyitott PowerShell** menüpont. 

>[!NOTE]
>Emellett érdemes Ez bármilyen parancssori környezetben, ahol az cli telepítve van.

A parancssori ablakban írja be az alábbi parancsokat:

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>A DSVM számítási célnak csatolása
A DSVM-létrehozása után azt most már az Azure ML-projektjéhez is csatlakoztatható.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Most már készen áll a dsvm-hez a kísérleteket futtathat.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Szabadítsa fel a dsvm-hez, és indítsa újra később
Ha befejezte a számítási feladatokat az Azure gépi tanulás, is felszabadítja a dsvm-hez. Ez a művelet leállítja a virtuális gép felszabadítja a számítási erőforrásokat, hanem a virtuális lemezek megőrzi. Nem terheli számítási költségeit, ha a virtuális gép fel van szabadítva.

Virtuális gép felszabadításához:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

A virtuális gép biztonsági keltse életre, használja a `az ml start` parancsot:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>A DSVM operációsrendszer-lemez kibontása
Az Ubuntu DSVM egy 50 GB-os operációsrendszer-lemezt és egy 100 GB méretű adatlemezzel rendelkezik. Docker, több helyre van elérhető tárolja az adatokat lemezen, a képeket. A cél számítási az Azure ML használatakor ezt a lemezt által használható Docker-motor le a Docker-rendszerképek lekérése és megkönnyítése conda-rétegek létrehozását. Szüksége lehet egy nagyobb méretű (például a 200 GB-os) lemez kibontása elkerülése érdekében a "teljes" lemezhiba, amíg Ön egy végrehajtási közepén. Hivatkozás [hogyan bővít ki egy Linux rendszerű virtuális gépen az Azure CLI-vel virtuális merevlemezek](../../virtual-machines/linux/expand-disks.md) megtudhatja, hogyan teheti ezt egyszerűen az azure-cli. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Az Apache Spark for Azure HDInsight-fürt létrehozása az Azure Portalon

Horizontális felskálázás Spark-feladatok futtatása, meg kell egy Apache Spark for Azure HDInsight-fürt létrehozása az Azure Portalon.

1. Jelentkezzen be az Azure Portalon https://portal.azure.com
2. Kattintson a **+ új** hivatkozásra, és keresse meg a "HDInsight".

    ![Keresse meg a hdi](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Válasszon **HDInsight** a listában, és kattintson a **létrehozás** gombra.
4. Az a **alapjai** konfigurációs képernyőjén **fürt típusa** beállításait, győződjön meg arról, hogy úgy dönt, **Spark** , a _fürt típusa_, **Linux** , a _operációs rendszer_, és **Spark 2.1.0 (HDI 3.6)** , a _Version.

    ![hdi konfigurálása](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Figyelje meg a fenti képernyőn a fürt rendelkezik egy _fürt bejelentkezési felhasználóneve_ mezőt és a egy _Secure Shell (SSH-) felhasználónév_ mező. Ezek a két különböző felhasználói identitásokat, annak ellenére, hogy a felhasználók kényelme érdekében adja meg ugyanazt a jelszót mindkét bejelentkezéseket. A _fürt bejelentkezési felhasználóneve_ jelentkezzen be a felügyeleti webes felhasználói felületen a HDI-fürt segítségével. A _SSH-bejelentkezési felhasználónév_ jelentkezzen be a fürt fő csomópontjának szolgál, és ez az Azure gépi tanulás a Spark-feladatok szállítást van szükség.

5. Válassza ki a fürt méretének és csomópontméret szükséges, és a létrehozási varázsló befejezéséhez. A fürt üzembe helyezése akár 30 percet is igénybe vehet. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Csatolhat egy HDI Spark-fürt számítási célnak

A Spark HDI-fürt létrehozása után azt most már az Azure ML-projektjéhez is csatlakoztatható.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Most már készen áll, futtasson kísérleteket a Spark-fürtön.

## <a name="next-steps"></a>További lépések

További információk:
- [Az Azure Machine Learning-kísérletezés szolgáltatás áttekintése](experimentation-service-configuration.md)
- [Az Azure Machine Learning Workbench Kísérletezési szolgáltatás konfigurációs fájlok](experimentation-service-configuration-reference.md)
- [Az Apache Spark for Azure HDInsight-fürt](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Adatelemző virtuális gép](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
