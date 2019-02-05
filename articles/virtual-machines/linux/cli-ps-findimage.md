---
title: Válassza ki a Linuxos Virtuálisgép-rendszerkép az Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan határozza meg, a közzétevő, ajánlat, Termékváltozat és verzió Marketplace Virtuálisgép-rendszerképek az Azure CLI használatával.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 443020bd6ca024cb5a04b2a8be5b7cbe7122efac
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734139"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Az Azure CLI-vel az Azure Marketplace-rendszerképek keresése a Linux rendszerű virtuális gép

Ez a témakör ismerteti, hogyan használhatja az Azure CLI az Azure piactéren Virtuálisgép-rendszerképek keresése. Ezen információk használatával adja meg a Piactéri lemezképet, ha egy virtuális Gépet hoz létre programozott módon a CLI-vel Resource Manager-sablonok vagy más eszközök.

Elérhető rendszerképeket és a használatával ajánlatokkal keresse a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/) kirakat, a [az Azure portal](https://portal.azure.com), vagy [Azure PowerShell-lel](../windows/cli-ps-findimage.md). 

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](/cli/azure/install-azure-cli) és a egy Azure-fiókkal van bejelentkezve (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Népszerű lemezképek listája

Futtassa a [az virtuálisgép-lemezkép lista](/cli/azure/vm/image) nélkül parancsot a `--all` beállítást, az Azure Marketplace-en népszerű VM-rendszerképek listájának megtekintéséhez. Például futtassa a következő parancs táblázatos formában jeleníti meg a népszerű-rendszerképek gyorsítótárazott listáját:

```azurecli
az vm image list --output table
```

A kimenet tartalmazza a kép URN (az értéket a *Urn* oszlop). Az alábbi népszerű Piactérről származó rendszerképek egyik virtuális gép létrehozásakor megadhatja a *UrnAlias*, mint például a rövidített *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Adott rendszerképek keresése

Egy adott Virtuálisgép-lemezkép keresése a piactéren, használja a `az vm image list` parancsot a `--all` lehetőséget. A parancs jelen verziója eltarthat egy ideig, és képes hosszadalmas kimenetet ad vissza, ezért általában a lista szerinti szűréséhez `--publisher` vagy egy másik paraméter. 

Például a következő parancs megjeleníti a minden Debian-ajánlat (ne feledje, hogy nélkül a `--all` vált, azt csak a gyakori rendszerképek helyi gyorsítótárában keres):

```azurecli
az vm image list --offer Debian --all --output table 

```

Részleges kimenet: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Hasonló szűrőket a alkalmazni a `--location`, `--publisher`, és `--sku` beállítások. A szűrést, például keresés, hajthat végre részleges egyezéseket `--offer Deb` található összes Debian rendszerkép.

Ha nem adja meg az adott helyen a `--location` beállítást, az alapértelmezett hely értékeit adja vissza. (Másik alapértelmezett helyének beállítása futtatásával `az configure --defaults location=<location>`.)

Például a következő parancs felsorolja a Nyugat-Európa helyen minden Debian 8 SKU:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Részleges kimenet:

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
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Keresse meg a képek
 
Egy másik helyen megkeresni a rendszerképet módja futtatásához a [az virtuális gép rendszerkép közzétevők listázása](/cli/azure/vm/image), [az vm list-rendszerképajánlatok](/cli/azure/vm/image), és [az virtuális gép rendszerkép list-skus](/cli/azure/vm/image) parancsok sorrendben. A következő parancsokkal határozza meg ezeket az értékeket:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott Termékváltozat választhat egy verzió legyen üzembe.

Ha például a következő parancs felsorolja a rendszerkép-közzétevőket az USA nyugati régiójában:

```azurecli
az vm image list-publishers --location westus --output table
```

Részleges kimenet:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Ezen információk használatával egy adott közzétevő ajánlatok keresésére. Például, ha a *Canonical* közzétevő az USA nyugati régiójában, az ajánlatok található futtatásával `azure vm image list-offers`. Adja át a hely és a közzétevő, az alábbi példában látható módon:

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
```
Láthatja, hogy az USA nyugati régiójában, a Canonical közzéteszi a *UbuntuServer* kínálnak az Azure-ban. De milyen termékváltozatokról van szó? Ezek az értékek lekéréséhez futtassa `azure vm image list-skus` és állítsa be a helyet, közzétevőt és ajánlatot, Ön felderített:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Kimenet:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Végül a `az vm image list` parancs található a termékváltozat adott verzió szeretne, például *18.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Részleges kimenet:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

Most már lehetősége van adott URN értéket tovább használni kívánt rendszerképet. Ezt az értéket adja át a `--image` paraméterrel rendelkező virtuális gép létrehozásakor a [az virtuális gép létrehozása](/cli/azure/vm) parancsot. Ne feledje, hogy igény szerint lecserélheti a verziószámot a URN "legutóbbi". Ez a verzió, mindig a rendszerkép legújabb verzióját. 

Ha telepít egy virtuális Gépet a Resource Manager-sablonnal, egyenként a állítsa be a rendszerkép-paraméterek a `imageReference` tulajdonságait. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Csomag tulajdonságainak megtekintése

Egy rendszerkép beszerzési terv adatainak megtekintéséhez futtassa a [az virtuálisgép-rendszerkép megjelenítése](/cli/azure/image) parancsot. Ha a `plan` a kimenetben tulajdonság nem `null`, a rendszerképre feltételeket el kell fogadnia a programozott telepítés előtt.

Ha például a Canonical Ubuntu Server 18.04 LTS rendszerképet nem rendelkezik további feltételeket, mert a `plan` információk `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Kimenet:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Egy hasonló parancs futtatása a Bitnami-rendszerkép által hitelesített RabbitMQ jeleníti meg a következő `plan` tulajdonságai: `name`, `product`, és `publisher`. (Egyes képeket is, hogy egy `promotion code` tulajdonság.) A lemezkép központi telepítése, lásd a következő szakaszok fogadja el a feltételeket, és engedélyezze a programozott telepítés.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Kimenet:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Feltételek elfogadása

Megtekintheti, és fogadja el a licencfeltételeket, használja a [az virtuális gép kép fogadja el – feltételek](/cli/azure/vm/image?) parancsot. Ha elfogadja a feltételeket, programozott üzembe helyezés engedélyezése az előfizetésében. Csak egyszer előfizetésenként elfogadására a lemezképhez kell. Példa:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A parancs kimenete egy `licenseTextLink` , a licenc feltételeit, és azt jelzi, hogy értékét `accepted` van `true`:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Üzembe helyezés beszerzési terv paraméterei

Miután elfogadja a feltételeket a lemezkép, az előfizetésben található virtuális gép is telepítheti. Párhuzamos üzembe helyezése a rendszerkép a `az vm create` parancshoz, nyújtanak a paraméterek a beszerzési csomagok emellett-és a kép URN. Ha például egy virtuális Gépet a RabbitMQ Certified telepíteni a Bitnami-rendszerkép:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>További lépések
A virtuális gép gyors létrehozásához a kép adatait használatával, lásd: [létrehozása és kezelése a Linux rendszerű virtuális gépek az Azure CLI-vel](tutorial-manage-vm.md).
