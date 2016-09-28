<properties
    pageTitle="Linux virtuális gépek létrehozásának különböző módszerei | Microsoft Azure"
    description="Ez a cikk a Linux virtuális gépek Azure-ban való létrehozásának különböző módszereit ismerteti, és minden módszer esetén további eszközökre és oktatóanyagokra mutató hivatkozásokat tartalmaz."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="iainfou"/>


# Linux virtuális gépek létrehozásának különböző módszerei a Resource Manager használatával

Az Azure különböző módszereket kínál a virtuális gépek létrehozásához a Resource Manager üzembe helyezési modelljével a különböző felhasználóknak és céloknak megfelelően. Ez a cikk ezeket a különböző módszereket, illetve a Linux virtuális gépek létrehozásához választható lehetőségeket összegzi.

## Azure CLI 

Az Azure CLI több platformon elérhető egy npm-csomagon, disztribúció által biztosított csomagokon vagy a Docker-tárolón keresztül. Az [Azure CLI telepítésével és konfigurálásával kapcsolatban itt](../xplat-cli-install.md) olvashat további információt. Az alábbi oktatóanyagok az Azure parancssori felület használatával kapcsolatos példákat biztosítanak. Olvassa el az alábbi cikkeket a bemutatott CLI gyors üzembe helyezési parancsok további részleteivel kapcsolatban:

* [Linux virtuális gép létrehozása az Azure parancssori felületen fejlesztéshez és teszteléshez](virtual-machines-linux-quick-create-cli.md)

    ```bash
    azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
    ```

* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

* [Create a Linux VM from the ground up using the Azure CLI (Linux virtuális gép létrehozása az alapoktól az Azure parancssori felület használatával)](virtual-machines-linux-create-cli-complete.md)

* [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](virtual-machines-linux-add-disk.md)

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
    ```

## Azure Portal

Az [Azure Portal](https://portal.azure.com) grafikus felhasználói felülete egyszerű módszert biztosít a virtuális gépek kipróbálására, különösen, ha nemrég kezdte meg az Azure használatát, mert semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure Portallal:

* [Linux virtuális gép létrehozása az Azure Portal használatával](virtual-machines-linux-quick-create-portal.md) 
* [Lemez csatolása az Azure Portal használatával](virtual-machines-linux-attach-disk-portal.md)

## Választható operációs rendszerek és rendszerképek
A virtuális gépek létrehozásakor egy rendszerképet választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is (lásd alább).

### Azure-rendszerképek
Az `azure vm image` CLI-parancsok használatával megtekintheti az elérhető elemeket közzétevő, disztribúciós kiadás, illetve build szerint.

Az elérhető közzétevők listázása:

```bash
azure vm image list-publishers --location WestUS
```

Egy adott közzétevő elérhető termékeinek (ajánlatainak) listázása:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Egy adott ajánlat elérhető termékváltozatainak (disztribúciós kiadásainak) listázása:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Egy adott kiadás összes elérhető rendszerképének listázása:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Az elérhető rendszerképek tallózásával és használatával kapcsolatos további példák: [Navigate and select Azure virtual machine images with the Azure CLI](virtual-machines-linux-cli-ps-findimage.md) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

Az `azure vm quick-create` és az `azure vm create` parancs is rendelkezik néhány aliassal, amelyek segítségével gyorsan hozzáférhet a leggyakoribb disztribúciókhoz és azok legújabb kiadásaihoz. Ez egyszerűbb, mintha meg kellene adnia a közzétevőt, ajánlatot, termékváltozatot és verziót, valahányszor létrehoz egy virtuális gépet:

| Alias     | Közzétevő | Ajánlat        | SKU         | Verzió |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | legújabb  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | legújabb  |
| Debian    | credativ  | Debian       | 8           | legújabb  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | legújabb  |
| RHEL      | Redhat    | RHEL         | 7.2         | legújabb  |
| SLES      | SLES      | SLES         | 12-SP1      | legújabb  |
| UbuntuLTS | Canonical | UbuntuServer | 14.04.4-LTS | legújabb  |

### Saját rendszerkép használata

Ha speciális egyéni beállításokra van szüksége, meglévő Azure virtuális gépen alapuló rendszerképet használhat a virtuális gép *rögzítésével*, vagy saját, virtuális merevlemezen (VHD-n) tárolt rendszerkép feltöltésével. A támogatott disztribúciókkal és a saját rendszerképek használatával kapcsolatban az alábbi cikkekben tekinthet meg további információt:

* [Azure által támogatott disztribúciók](virtual-machines-linux-endorsed-distros.md)

* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](virtual-machines-linux-create-upload-generic.md)

* [How to capture a Linux virtual machine as a Resource Manager template](virtual-machines-linux-capture-image.md) (Linux virtuális gép rögzítése Resource Manager-sablonként). Gyors üzembe helyezési parancsok:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## Következő lépések

* Próbálja ki az oktatóprogramok valamelyikét Linux virtuális gép létrehozásához a [portálon](virtual-machines-linux-quick-create-portal.md), a [parancssori felülettel](virtual-machines-linux-quick-create-cli.md) vagy Azure Resource Manager-[sablonnal](virtual-machines-linux-cli-deploy-templates.md).

* Linux virtuális gép létrehozása után egyszerűen [adhat hozzá adatlemezt](virtual-machines-linux-add-disk.md).

* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Sep16_HO4-->


