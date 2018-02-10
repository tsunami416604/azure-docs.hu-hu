---
title: "Válassza ki a Linux rendszerű virtuális lemezképeket az Azure parancssori felülettel |} Microsoft Docs"
description: "Útmutató az Azure CLI segítségével határozhatja meg, a közzétevő, az ajánlat, SKU és verziója a piactér Virtuálisgép-lemezképek."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79eb69b83e4ffc0a4ad7c2631ce4d1306a1e335c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Linux virtuális gép lemezképek megkeresése az Azure piactéren az Azure parancssori felülettel
Ez a témakör ismerteti, hogyan használható az Azure CLI 2.0 Virtuálisgép-lemezképek megkeresése az Azure piactéren. Ezen információk használatával adja meg a Piactéri rendszerkép Linux virtuális gép létrehozásakor.

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és az Azure-fiókkal bejelentkezett (`az login`).

## <a name="terminology"></a>Terminológia

Piactéren elérhető rendszerkép azonosítja a CLI és más Azure-eszközök a hierarchia megfelelően:

* **A Publisher** -szervezet, amely a lemezkép létrehozása. Példa: kanonikus
* **Ajánlat** -közzétevő által létrehozott kapcsolódó képek csoportja. Példa: Ubuntu Server
* **SKU** - ajánlatot, például egy terjesztési jelentős kiadása példányát. Példa: 16.04-es lts verzió
* **Verzió** -SKU lemezkép verziószámát. A kép megadásakor lecserélheti a verziószámot a "legújabb", amely kiválasztja a terjesztési legújabb verzióját.

Adja meg a Piactéri rendszerkép, általában használhatja a kép *URN*. A URN összevonja ezeket az értékeket, a kettőspont (:) elválasztva: *Publisher*:*kínálnak*:*Sku*:*verzió*. 


## <a name="list-popular-images"></a>Népszerű lemezképek felsorolása

Futtassa a [az vm képlistában](/cli/azure/vm/image#az_vm_image_list) nélkül parancsot a `--all` beállítás, az Azure piactéren népszerű Virtuálisgép-rendszerképekről listájának megjelenítéséhez. Például futtassa a következő parancsot, és jelenítse meg a gyorsítótárazott népszerű képek táblázatos formátumú:

```azurecli
az vm image list --output table
```

A kimenet tartalmazza a URN (az értéket a *Urn* oszlop), amely segítségével beállíthatja a lemezképet. Virtuális gép valamelyik, a népszerű piactér lemezképek létrehozásakor megadhatja URN alias például *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Adott rendszerképek keresése

Egy adott VM-lemezkép keresése a piactéren, használja a `az vm image list` parancsot a `--all` lehetőséget. Ez a parancs verziója bizonyos idő, és térjen vissza a hosszadalmas kimeneti, ezért általában szűrheti a listát `--publisher` vagy egy másik paraméter. 

Például a következő parancs megjeleníti az összes Debian ajánlatok (ne feledje, hogy nélkül a `--all` váltáshoz csak keres a gyakori képek a helyi gyorsítótár):

```azurecli
az vm image list --offer Debian --all --output table 

```

Részleges kimenete: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

A hasonló szűrők a `--location`, `--publisher`, és `--sku` beállítások. Részleges egyezések is végezhető egy szűrővel – például keresése `--offer Deb` összes Debian lemezkép található.

Ha nem adja meg a meghatározott helyen a `--location` beállítás, az értékek `westus` alapértelmezett által visszaadott. (Egy másik alapértelmezett hely beállítása futtatásával `az configure --defaults location=<location>`.)

Például a következő parancs megjeleníti az összes Debian 8 termékváltozatok `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Részleges kimenete:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Lépjen a lemezképek 
Lemezkép a helyen található egy másik módszer a [az méretű kép lista-közzétevők](/cli/azure/vm/image#az_vm_image_list_publishers), [az méretű kép lista-ajánlatok](/cli/azure/vm/image#az_vm_image_list_offers), és [az méretű kép lista-SKU](/cli/azure/vm/image#az_vm_image_list_skus) parancsok sorrendben. A következő parancsokkal ezek az értékek határozzák meg:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.


A következő parancs például az USA nyugati régiója helyét a kép közzétevők sorolja fel:

```azurecli
az vm image list-publishers --location westus --output table
```

Részleges kimenete:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Ezen információk használatával egy adott közzétételi ajánlatok található. Például ha Canonical egy kép közzétevőjének, az USA nyugati régiója helyén, keressék a futtatásával `azure vm image list-offers`. A hely és a közzétevő, az alábbi példában látható módon adja át:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Kimenet:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Láthatja, hogy a West US régió Canonical közzéteszi a **UbuntuServer** kínálnak az Azure-on. De milyen termékváltozatokról van szó? Ahhoz, hogy ezeket az értékeket, futtassa `azure vm image list-skus` és a hely, kiadó és ajánlat, amely rendelkezik a felderített:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Kimenet:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Végül a `az vm image list` parancsot egy adott verziójához a Termékváltozat található, például **16.04-es lts verzió**:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Kimenet:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>További lépések
Most választhatja meg pontosan a URN értéket megtételével használni kívánt kép. Ezt az értéket átadni a `--image` paraméter, a virtuális gép létrehozásakor a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. Ne feledje, hogy választhatóan lecserélheti a verziószámot a URN "legutóbbi". Ebben a verzióban az mindig a terjesztési legújabb verziója. Hozzon létre egy virtuális gép gyors URN információk segítségével, lásd: [létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felülettel](tutorial-manage-vm.md).
