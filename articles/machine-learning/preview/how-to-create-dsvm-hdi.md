---
title: "DSVM és a cél az Azure ML számítási HDI létrehozása"
description: "Hozzon létre DSVM és HDI Spark-fürt a számítási célozza az Azure ML kísérletezhet."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 2560ca144f8ce4041aa592554f9945ed546cc49b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>A számítási célok DSVM és HDI Spark fürt létrehozása

Könnyedén növelheti vagy további számítási célok például Ubuntu-alapú DSVM (adatok tudományos virtuális gép), és az Apache Spark on Azure HDInsight-fürthöz hozzáadásával a gépi tanulási kísérlet kiterjesztése. Ez a cikk bemutatja, hogyan célozza az Azure számítási ezek létrehozásának lépéseit. Azure ML számítási célokon további információkért tekintse meg [Azure Machine Learning kísérletezhet szolgáltatás áttekintése](experimentation-service-configuration.md).

>[!NOTE]
>Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel az erőforrások, például a virtuális Gépet és HDI-fürtök létrehozása az Azure, mielőtt továbblép. Mindkét ezeket az erőforrásokat is is felhasználhatnak konfigurációtól függően számos számítási magok. Győződjön meg arról, hogy előfizetése elegendő kapacitása a virtuális CPU-magokat. Akkor is mindig, hogy engedélyezte az Azure támogatási magok az előfizetésben megengedett maximális számának növeléséhez.

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Hozzon létre egy Ubuntu DSVM Azure-portálon

Egy DSVM hozhat létre Azure-portálon. 

1. Jelentkezzen be Azure-portálon a https://portal.azure.com
2. Kattintson a **+ új** hivatkozásra, majd keresse meg a "data tudományos virtuális gép Linux".
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. Válasszon **adatok tudományos virtuális gép Linux (Ubuntu)** elemet a listában, és kövesse a képernyőn megjelenő a DSVM létrehozásához nyújt útmutatást.

>[!IMPORTANT]
>Ellenőrizze, hogy **jelszó** , a _hitelesítési típus_.

![pwd használata](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>Hozzon létre egy Ubuntu DSVM azure-parancssori felület használatával

Egy Azure-erőforrás management-sablon segítségével is telepítheti egy DSVM.

>[!NOTE]
>Az összes következő parancsok rendszer feltételezi, hogy a gyökérmappában található azon Azure ML-projektben állítson ki.

Először hozzon létre egy `mydsvm.json` fájl használata a kedvenc szövegszerkesztőjével a `docs` mappát. (Ha még nem rendelkezik egy `docs` a projekt gyökérmappájában lévő mappának, hozzon létre egyet.) Ez a fájl néhány alapvető paramétert az Azure-erőforrás felügyeleti sablon konfigurálásához használjuk. 

Másolja és illessze be a következő JSON részlet azokat a `mydsvm.json` fájlt, és töltse ki a megfelelő értékeket:

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

Az a _vmSize_ mező, használhatja a felsorolt suppported Virtuálisgép-méretet a [Ubuntu DSVM Azure resource felügyeleti sablon](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json). Javasoljuk, hogy valamelyikét használja az alábbi méretek a cél az Azure ML számítási. 


>[!TIP]
> A [mély munkaterhelések tanulási](how-to-use-gpu.md) központilag telepíthető a GPU kapcsolt virtuális gépek.

- [Általános célú virtuális gépek](/virtual-machines/linux/sizes-general.md)
  - Standard_DS2_v2 
  - Standard_DS3_v2 
  - Standard_DS4_v2 
  - Standard_DS12_v2 
  - Standard_DS13_v2 
  - Standard_DS14_v2 
- [Az alkalmazás bekapcsolja GPU virtuális gépek](/virtual-machines/linux/sizes-gpu.md)
  - Standard_NC6 
  - Standard_NC12 
  - Standard_NC24 
 

További információk a [Azure Linux virtuális gépek méretei](../../virtual-machines/linux/sizes.md) és azok [árakról](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Indítsa el a parancssori ablakban az Azure ML munkaterület alkalmazásból kattintva **fájl** --> **nyissa meg a parancssort**, vagy **nyitott PowerShell** menüpont. 

>[!NOTE]
>Is ehhez minden parancssori környezetben, ahol az cli telepítve van.

Adja meg a parancssori ablakban az alábbi parancsokat:

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
## <a name="attach-a-dsvm-compute-target"></a>DSVM számítási target csatolása
A DSVM létrehozása után most csatolhat az Azure ML-projektet.

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> 

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
Most, készen áll a DSVM kísérletek futtatásához.

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>Egy DSVM felszabadítani, és később
A számítási feladatok Azure ML befejezése után felszabadítani a a DSVM. Ez a művelet leállítja a virtuális gép felszabadítja a számítási erőforrásokat, hanem a virtuális lemezek megőrzi. Van nem szó számítási költségeit, amikor a virtuális gép felszabadítása.

A virtuális gép felszabadítása:

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

A virtuális gép vissza keltse életre, használja a `az ml start` parancs:

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>Bontsa ki a DSVM operációsrendszer-lemez
Linux virtuális gép az Azure-ban általában tartalmaz egy 30 GB-os operációsrendszer-lemez. A cél az Azure ML számítási használatakor azt is fogyasztásra gyorsan Docker-motor húzza lefelé Docker képek és felépítése conda rétegek utasítást. Célszerű bontsa ki az operációsrendszer-lemezképet (például 200 GB) nagyobb méretűre elkerülése érdekében a "teljes" lemezhiba, amíg egy végrehajtását közepén. Hivatkozás [kibontása a Linux virtuális gép és az Azure parancssori felület virtuális merevlemezek](../../virtual-machines/linux/expand-disks.md) ehhez egyszerűen az azure-cli módjáról. 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Az Apache Spark on Azure HDInsight-fürt létrehozása az Azure portálon

Kibővített Spark feladatok futtatása az Apache Spark on Azure HDInsight-fürt létrehozása az Azure portálon kell.

1. Jelentkezzen be Azure-portálon a https://portal.azure.com
2. Kattintson a **+ új** hivatkozásra, majd keresse meg a "HDInsight".

    ![hdi keresése](media/how-to-create-dsvm-hdi/hdi.png)
    
3. Válasszon **HDInsight** elemet a listában, és kattintson a a **létrehozása** gombra.
4. Az a **alapjai** konfigurálására szolgáló képernyőn **típusú fürt** beállításait, ellenőrizze, hogy **Spark** , a _típusú fürt_, **Linux** , a _operációs rendszer_, és **Spark 2.1.0 (HDI 3.6)** , a _Version.

    ![hdi konfigurálása](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >Figyelje meg a fenti képernyőn a fürt rendelkezik egy _fürt bejelentkezési felhasználónevének_ mező és egy _Secure Shell (SSH) felhasználónév_ mező. Ezek a két különböző felhasználói identitásokat, annak ellenére, hogy a felhasználók kényelme érdekében adja meg ugyanazt a jelszót a mindkét bejelentkezések során. A _fürt bejelentkezési felhasználónevének_ jelentkezzen be a felügyeleti webes felhasználói Felületét a HDI-fürtöt szolgál. A _SSH bejelentkezéshez username_ jelentkezzen be a fürt átjárócsomópontjához szolgál, és azt, hogy mire van szükség az Azure ML Spark feladatok átirányítani.

5. Válassza ki a fürt mérete és a csomópont méretének meg kell, és a létrehozási varázsló. A fürt üzembe helyezése akár 30 percet is igénybe vehet. 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>Egy HDI Spark-fürt számítási célhoz csatolása

A Spark HDI-fürtnek a létrehozása után most csatolhat az Azure ML-projektet.

```azurecli
# attach the HDI compute target
$ az ml computetarget attach cluster --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> 

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
Most kell kísérletek futtathatja a Spark-fürtön.

## <a name="next-steps"></a>Következő lépések

További információ:
- [Azure Machine Learning kísérletezhet szolgáltatás áttekintése](experimentation-service-configuration.md)
- [Az Azure Machine Learning-munkaterület kísérletezhet szolgáltatás konfigurációs fájlok](experimentation-service-configuration-reference.md)
- [Az Apache Spark on Azure HDInsight-fürthöz](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [Adatok tudományos virtuális gép](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
