---
title: Azure Instance Metadata szolgáltatás
description: REST-felület a virtuális gépek számítási, hálózati és közelgő karbantartási eseményeivel kapcsolatos információk lekéréséhez.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: f638b332eae5cd85e1cb6aae9c6bd8eb4ad44848
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84886199"
---
# <a name="azure-instance-metadata-service"></a>Azure-példány metaadatainak szolgáltatása

Az Azure Instance Metadata Service (IMDS) információt nyújt a jelenleg futó virtuálisgép-példányokról, és a virtuális gépek felügyeletéhez és konfigurálásához használható.
Ezen információk közé tartozik a SKU, a Storage, a hálózati konfigurációk és a közelgő karbantartási események. Az elérhető adatok teljes listáját lásd: [metadata API](#metadata-apis)-k.
Instance Metadata Service a virtuális gép és a virtuálisgép-méretezési csoport példányai esetében is elérhető. Csak [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)használatával létrehozott/kezelt virtuális gépek futtatására használható.

Az Azure Instance Metadata Service egy jól ismert, nem irányítható IP-címen () elérhető REST-végpont `169.254.169.254` , amely csak a virtuális gépről érhető el.

## <a name="security"></a>Biztonság

Az Instance Metadata Service végpont csak a futó virtuálisgép-példányból érhető el egy nem irányítható IP-címen. Emellett `X-Forwarded-For` a szolgáltatás elutasítja a fejléctel ellátott kérelmeket.
A kérelmeknek tartalmaznia kell egy `Metadata: true` fejlécet is, amely biztosítja, hogy a tényleges kérelem közvetlenül a véletlen átirányítás része legyen.

> [!IMPORTANT]
> A Instance Metadata Service nem a bizalmas adatok csatornája. A végpont minden folyamat számára nyitva van a virtuális gépen. A szolgáltatáson keresztül elérhető információkat a virtuális gépen futó összes alkalmazásnak megosztott információnak kell tekinteni.

## <a name="usage"></a>Használat

### <a name="accessing-azure-instance-metadata-service"></a>Azure-Instance Metadata Service elérése

Instance Metadata Service eléréséhez hozzon létre egy virtuális gépet [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [Azure Portal](https://portal.azure.com), és kövesse az alábbi mintákat.
A IMDS lekérdezésével kapcsolatban további példákat talál az [Azure-példány metaadatait tartalmazó mintákban](https://github.com/microsoft/azureimds).

Az alábbi mintakód egy példány összes metaadatának beolvasására szolgál az adott adatforráshoz való hozzáféréshez: [metadata API](#metadata-apis) szakasz. 

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

### <a name="data-output"></a>Adatkimenet

Alapértelmezés szerint a Instance Metadata Service JSON formátumú () adatokkal tér vissza `Content-Type: application/json` . Bizonyos API-k azonban más formátumokban is képesek visszaadni az adatkérést.
Az alábbi táblázat más adatformátum-API-kat is támogat.

API | Alapértelmezett adatformátum | Egyéb formátumok
--------|---------------------|--------------
/attested | json | Nincs
/identity | json | Nincs
/instance | json | szöveg
/scheduledevents | json | Nincs

A nem alapértelmezett válasz formátumának eléréséhez a kérelemben a kért formátumot lekérdezési karakterlánc paraméterként kell megadni. Például:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> A/metadata/instance lévő Leaf-csomópontok `format=json` nem működnek. Ezeket a lekérdezéseket `format=text` explicit módon meg kell adni, mivel az alapértelmezett formátum a JSON.

### <a name="versioning"></a>Verziókezelés

A Instance Metadata Service verziója, és a HTTP-kérelemben szereplő API-verzió megadása kötelező.

A támogatott szolgáltatási verziók a következők: 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15.

Vegye figyelembe, hogy amikor megjelent az új verzió, eltarthat egy ideig, amíg az összes régióba be nem kerül. A jelenleg 2019-11-01-es verzió még mindig üzembe van helyezve, és előfordulhat, hogy nem érhető el minden régióban.

Ahogy újabb verziók lettek hozzáadva, a régebbi verziók továbbra is elérhetők a kompatibilitás érdekében, ha a parancsfájlok adott adatformátumok függőségeivel rendelkeznek.

Ha nincs megadva verzió, a rendszer egy hibaüzenetet ad vissza a legújabb támogatott verziók listájával.

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa azt jelzi, hogy a hiba feltétele, ha nincs megadva a verzió, a válasz elég kinyomtatva az olvashatóság érdekében.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Válasz**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>Metaadat-API-k

Metadata Service több, különböző adatforrásokat jelképező API-t tartalmaz.

API | Leírás | Verzió bevezetése
----|-------------|-----------------------
/attested | [Igazolt](#attested-data) információ | 2018-10-01
/identity | Lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Lásd: [példány API](#instance-api) | 2017-04-02
/scheduledevents | Lásd: [Scheduled Events](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Példány API

A példány API elérhetővé teszi a virtuálisgép-példányok fontos metaadatait, beleértve a virtuális gépet, a hálózatot és a tárolót. A következő kategóriák érhetők el példányon/számításon keresztül:

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
azEnvironment | Az Azure-környezet, amelyben a virtuális gép fut | 2018-10-01
customData | Ez a funkció jelenleg le van tiltva. Ezt a dokumentációt akkor fogjuk frissíteni, amikor elérhetővé válik | 2019-02-01
location | Az Azure-régió, amelyen a virtuális gép fut | 2017-04-02
name | A virtuális gép neve | 2017-04-02
offer | A virtuálisgép-lemezképre vonatkozó információkat nyújtja, és csak az Azure rendszerkép-katalógusból üzembe helyezett rendszerképekhez érhető el | 2017-04-02
osType | Linux vagy Windows | 2017-04-02
placementGroupId | A virtuálisgép-méretezési [csoport elhelyezési csoportja](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
csomag | Egy virtuális gép nevét, termékét és közzétevőjét tartalmazó [csomag megtervezése](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) , ha az Azure Marketplace-rendszerkép | 2018-04-02
platformUpdateDomain |  A virtuális gépet futtató [tartomány frissítése](manage-availability.md) | 2017-04-02
Platformfaultdomain tulajdonságot | A virtuális gép által futtatott tartalék [tartomány](manage-availability.md) | 2017-04-02
Szolgáltató | A virtuális gép szolgáltatója | 2018-10-01
publicKeys | A virtuális géphez és elérési utakhoz rendelt [nyilvános kulcsok gyűjteménye](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
közzétevő | A virtuális gép rendszerképének közzétevője | 2017-04-02
resourceGroupName | A virtuális géphez tartozó [erőforráscsoport](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | Az erőforrás [teljes](https://docs.microsoft.com/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
SKU | A virtuális gép rendszerképének adott SKU-jának | 2017-04-02
storageProfile | Lásd: [tárolási profil](#storage-metadata) | 2019-06-01
subscriptionId | Azure-előfizetés a virtuális géphez | 2017-08-01
tags | A virtuális gép [címkéi](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | Az egyszerűbb programozási elemzéshez JSON-tömbként formázott Címkék  | 2019-06-04
version | A VM-rendszerkép verziója | 2017-04-02
vmId | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | A virtuálisgép-méretezési csoport virtuálisgép-méretezési [készletének neve](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
zóna | A virtuális gép [rendelkezésre állási zónája](../../availability-zones/az-overview.md) | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>1. példa: az Azure-on futó virtuális gép nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökkel, amelyeknek nyomon kell követniük a virtuális gép egyediségét. A virtuális gép egyedi AZONOSÍTÓjának beszerzéséhez használja a `vmId` mezőt instance metadata Service.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>2. példa: tárolók elhelyezése, adatpartíció-alapú hiba/frissítési tartomány

Bizonyos esetekben a különböző adatreplikák elhelyezése elsődleges fontossággal bír. Előfordulhat például, hogy az [HDFS replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy a tároló elhelyezése egy [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) keresztül megkövetelheti `platformFaultDomain` a `platformUpdateDomain` és a virtuális gép futtatását.
Ezen döntések elvégzéséhez [Availability Zones](../../availability-zones/az-overview.md) is használhatja a példányokhoz.
Ezeket az adatlekérdezéseket közvetlenül a Instance Metadata Service keresztül kérdezheti le.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Válasz**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>3. példa: további információk a virtuális géppel kapcsolatban a támogatási esetekben

Szolgáltatóként olyan támogatási hívást kaphat, amelyben további információkat szeretne megtudni a virtuális gépről. Ha arra kéri az ügyfelet, hogy ossza meg a számítási metaadatokat, alapvető információkat biztosíthat a támogatási szakember számára az Azure-beli virtuális gépekről.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>4. példa: az Azure-környezet beszerzése, ahol a virtuális gép fut

Az Azure számos szuverén felhővel rendelkezik, mint például a [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Időnként szükség van az Azure-környezetre bizonyos futtatókörnyezeti döntések elvégzéséhez. A következő minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Válasz**

```text
AzurePublicCloud
```

A felhő és az Azure-környezet értékei az alábbiakban láthatók.

 Felhő   | Azure-környezet
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Hálózati metaadatok 

A hálózati metaadatok a példány API részét képezik. A következő hálózati kategóriák érhetők el a példány/hálózat végponton keresztül.

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
IPv4/Privateipaddress tulajdonságot | A virtuális gép helyi IPv4-címe | 2017-04-02
IPv4/publicIpAddress | A virtuális gép nyilvános IPv4-címe | 2017-04-02
alhálózat/címe | A virtuális gép alhálózati címe | 2017-04-02
alhálózat/előtag | Alhálózat előtagja, 24. példa | 2017-04-02
IPv6/IP-cím | A virtuális gép helyi IPv6-címe | 2017-04-02
macAddress | VM MAC-címe | 2017-04-02

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az összes alábbi válasz elég kinyomtatva az olvashatóság érdekében.

#### <a name="sample-1-retrieving-network-information"></a>1. példa: hálózati adatok beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieving-public-ip-address"></a>2. minta: nyilvános IP-cím beolvasása

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Tárolási metaadatok

A tárolási metaadatok a példány-API részét képezik a példány/számítás/storageProfile végpont alatt.
A virtuális géphez társított tárolóeszközök adatait tartalmazza. 

A virtuális gépek tárolási profilja három kategóriára oszlik: képhivatkozás, operációsrendszer-lemez és adatlemezek.

A képhivatkozási objektum a következő információkat tartalmazza az operációsrendszer-lemezképpel kapcsolatban:

Adatok    | Leírás
--------|-----------------
id      | Erőforrás-azonosító
offer   | A platform vagy a piactér rendszerképének ajánlata
közzétevő | Rendszerkép kiadója
SKU     | Rendszerkép SKU
version | A platform vagy a piactér rendszerképének verziója

Az operációsrendszer-lemez objektum a következő információkat tartalmazza a virtuális gép által használt operációsrendszer-lemezről:

Adatok    | Leírás
--------|-----------------
gyorsítótárazás | Gyorsítótárazási követelmények
createOption | Információk a virtuális gép létrehozásáról
diffDiskSettings | Ideiglenes lemez beállításai
diskSizeGB | A lemez mérete GB-ban
image   | Forrás felhasználói lemezkép virtuális merevlemeze
LUN     | A lemez logikai egységének száma
managedDisk | Felügyelt lemez paramétereinek
name    | Lemez neve
VHD     | Virtuális merevlemez
writeAcceleratorEnabled | Azt jelzi, hogy engedélyezve van-e a writeAccelerator a lemezen

Az adatlemezek tömb tartalmazza a virtuális géphez csatolt adatlemezek listáját. Minden adatlemez-objektum a következő információkat tartalmazza:

Adatok    | Leírás
--------|-----------------
gyorsítótárazás | Gyorsítótárazási követelmények
createOption | Információk a virtuális gép létrehozásáról
diffDiskSettings | Ideiglenes lemez beállításai
diskSizeGB | A lemez mérete GB-ban
encryptionSettings | A lemez titkosítási beállításai
image   | Forrás felhasználói lemezkép virtuális merevlemeze
managedDisk | Felügyelt lemez paramétereinek
name    | Lemez neve
osType  | A lemezen található operációs rendszer típusa
VHD     | Virtuális merevlemez
writeAcceleratorEnabled | Azt jelzi, hogy engedélyezve van-e a writeAccelerator a lemezen

Az alábbi példa bemutatja, hogyan lehet lekérdezni a virtuális gép tárolási adatait.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM-Címkék

A virtuális gépek címkéi a példány/számítás/címkék végpont alatt találhatók a példány API-val.
Előfordulhat, hogy az Azure-beli virtuális gépen a címkék logikailag rendszerezve lettek. A virtuális géphez rendelt címkék az alábbi kérelem használatával kérhetők le.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Válasz**

```text
Department:IT;Environment:Test;Role:WebRole
```

A `tags` mező egy olyan karakterlánc, amelynek a címkéi pontosvesszővel vannak elválasztva. Ez a kimenet akkor lehet probléma, ha a címkékben pontosvesszőt használnak. Ha egy elemzőt a címkék programozott kinyeréséhez kell írni, a mezőre kell támaszkodnia `tagsList` . A `tagsList` mező egy olyan JSON-tömb, amely nem határolójeleket, és így könnyebben elemezhető.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**Válasz**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Igazolt adatértékek

Instance Metadata Service által kiszolgált forgatókönyv része a garancia arra, hogy a megadott adatok az Azure-ból érkeznek. Aláírjuk ezen információk egy részét, hogy a piactér lemezképei biztosak legyenek abban, hogy az Azure-ban futnak.

### <a name="sample-1-getting-attested-data"></a>1. példa: igazolt adatértékek beszerzése

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az alábbi példákban az olvashatóság érdekében a következő válaszokat kell kinyomtatni.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

Az API-Version kötelező mező. Tekintse át a támogatott API-verziók [használati szakaszát](#usage) .
Az alkalom egy opcionális 10 számjegyű karakterlánc. Ha nincs megadva, a IMDS az aktuális UTC időbélyeget adja vissza a helyén.

> [!NOTE]
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott egyszeres értéket ad vissza.

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírási blob a dokumentum [PKCS7](https://aka.ms/pkcs7) aláírt verziója. Tartalmazza az aláíráshoz használt tanúsítványt, valamint a virtuális gép részleteit, például a vmId, az SKU, az alkalom, a subscriptionId, az időbélyeg a dokumentum létrehozásához és lejáratához, valamint a rendszerképre vonatkozó terv információit. A csomag adatai csak az Azure Marketplace-lemezképek esetében vannak kitöltve. A tanúsítvány kinyerhető a válaszból, és annak ellenőrzésére szolgál, hogy a válasz érvényes-e, és az Azure-ból származik-e.
A dokumentum a következő mezőket tartalmazza:

Adatok | Leírás
-----|------------
egyszeri | Egy karakterlánc, amely opcionálisan megadható a kérelemben. Ha nem adta meg a megadott időpontot, a rendszer az aktuális UTC-időbélyeget használja.
csomag | Az [Azure Marketplace-rendszerkép terve](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan). A csomag azonosítóját (név), a termék rendszerképét vagy az ajánlatot (terméket) és a közzétevő azonosítóját (kiadó) tartalmazza.
időbélyeg/createdOn | Az aláírt dokumentum létrehozási időpontjának UTC-időbélyege
időbélyeg/expiresOn | Az aláírt dokumentum érvényességi időpontjának UTC-időbélyege
vmId |  A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | Azure-előfizetés a virtuális géphez, amely a következő címen található:`2019-04-30`
SKU | A virtuálisgép-rendszerképhez tartozó, a következőben bemutatott SKU`2019-11-01`

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>2. minta: annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

A piactér-szállítók biztosítani szeretnék, hogy a szoftverük csak az Azure-ban fusson. Ha valaki átmásolja a VHD-t a helyszíni rendszerbe, akkor képesnek kell lennie a felderítésére. A Instance Metadata Serviceba való behívásával a piactér-szállítók olyan aláírt adatokhoz juthatnak, amelyek csak az Azure-ból választanak.

> [!NOTE]
> A jQ telepítéséhez szükséges.

**Kérés**

```bash
# Get the signature
curl --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Válasz**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Ellenőrizze, hogy az aláírás Microsoft Azure-e, és ellenőrizze a tanúsítványlánc hibáit.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott egyszeres értéket ad vissza.

Az aláírt dokumentumban szereplő alkalom összehasonlítható, ha a kezdeti kérelemben egy egyalkalomú paramétert adott meg.

> [!NOTE]
> A nyilvános felhő és a szuverén felhő tanúsítványa eltérő lesz.

Felhő | Tanúsítvány
------|------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Az aláíráshoz használt tanúsítvány körül ismert probléma van. Előfordulhat, hogy a tanúsítványok nem rendelkeznek pontos egyezéssel a `metadata.azure.com` nyilvános felhőben. Ezért a minősítés érvényesítésének lehetővé kell tennie egy köznapi név használatát bármely `.metadata.azure.com` altartományból.

Azokban az esetekben, amikor a közbenső tanúsítvány nem tölthető le, mert az érvényesítés során hálózati korlátozások miatt nem lehet letölteni, a köztes tanúsítvány rögzíthető. Az Azure azonban a szabványos PKI-gyakorlatnak megfelelően átadja a tanúsítványokat. A rögzített tanúsítványokat frissíteni kell, ha a váltás történik. A köztes tanúsítvány frissítésének megtervezése után az Azure-blog frissül, és az Azure-ügyfelek értesítést kapnak. A köztes tanúsítványokat [itt](https://www.microsoft.com/pki/mscorp/cps/default.htm)találja. Az egyes régiók köztes tanúsítványok különbözőek lehetnek.

> [!NOTE]
> Az Azure China 21Vianet köztes tanúsítvány a Baltimore helyett a DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓTÓL fog származni.
Ha az Azure China köztes tanúsítványokat is rögzítette a főláncú hatóság változásának részeként, akkor a köztes tanúsítványokat frissíteni kell.

## <a name="managed-identity-via-metadata-service"></a>Felügyelt identitás Metadata Serviceon keresztül

Egy rendszerhez rendelt felügyelt identitás engedélyezhető a virtuális gépen, vagy egy vagy több felhasználóhoz rendelt felügyelt identitás is hozzárendelhető a virtuális géphez.
A felügyelt identitásokhoz tartozó jogkivonatok később is kérhetők Instance Metadata Service. Ezek a tokenek az egyéb Azure-szolgáltatásokkal (például Azure Key Vault) való hitelesítéshez használhatók.

A szolgáltatás engedélyezésének részletes lépéseiért lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Scheduled Events Metadata Service használatával
Az ütemezett események állapotát metaadat-szolgáltatáson keresztül szerezheti be, majd a felhasználó megadhatja, hogy milyen műveletet hajtson végre az eseményeken.  Részletekért tekintse meg [Scheduled Events](scheduled-events.md) . 

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A szolgáltatás **általánosan elérhető** az összes Azure-felhőben.

## <a name="sample-code-in-different-languages"></a>Mintakód különböző nyelveken

Minták a virtuális gépen belüli különböző nyelveket használó hívási metaadat-szolgáltatásban:

Nyelv      | Példa
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Indítás            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Hiba és hibakeresés

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service szabványos HTTP-hibákat ad vissza. Például:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 hibás kérelem | Hiányzó `Metadata: true` fejléc vagy hiányzó paraméter `format=json` a levél csomópontjainak lekérdezése során
404 Nem található | A kért elem nem létezik
405 metódus nem engedélyezett | Csak `GET` a kérelmek támogatottak
410 elment | Némi várakozás után próbálja megismételni a maximális 70 másodpercet
429 – Túl sok kérelem | Az API jelenleg legfeljebb 5 lekérdezést támogat másodpercenként
500 szolgáltatási hiba     | Némi idő elteltével próbálkozzon újra

### <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

1. Hibaüzenetet kapok `400 Bad Request, Required metadata header not specified` . Ez mit jelent?
   * A Instance Metadata Service megköveteli a fejléc átadását a `Metadata: true` kérelemben. Ha ezt a fejlécet a REST-hívásban adja át, akkor a Instance Metadata Servicehoz való hozzáférést is lehetővé teszi.
1. Miért nem kapok számítási információt a virtuális géphez?
   * A Instance Metadata Service jelenleg csak Azure Resource Manager által létrehozott példányokat támogatja. A jövőben a Cloud Service virtuális gépek támogatása is felvehető.
1. Létrehoztam a virtuális gépet Azure Resource Manager egy kicsit vissza. Miért nem jelenik meg a számítási metaadatok adatai?
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez hozzáadhat vagy eltávolíthat bővítményeket vagy adatlemezeket a virtuálisgép-példány (ok) hoz.
1. Nem látom az új verzióhoz feltöltött összes adatot
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez hozzáadhat vagy eltávolíthat bővítményeket vagy adatlemezeket a virtuálisgép-példány (ok) hoz.
1. Miért kapok hibát `500 Internal Server Error` vagy `410 Resource Gone` ?
   * Próbálja megismételni a kérést az exponenciális visszalépési rendszer vagy az [átmeneti hibák kezelésére](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)szolgáló egyéb módszerek alapján. Ha a probléma továbbra is fennáll, hozzon létre egy támogatási problémát Azure Portal a virtuális gép számára.
1. Működne a virtuálisgép-méretezési csoport példányai?
   * Az igen metaadat-szolgáltatás elérhető a méretezési csoport példányaihoz.
1. Frissítettem a címkéket Virtual Machine Scale Sets de nem jelennek meg a példányokban az Egypéldányos virtuális gépektől eltérően?
   * A méretezési csoportok jelenleg csak a virtuális gépre mutatnak be egy újraindítás, rendszerkép vagy lemez módosítása a példányra.
1. Időtúllépést kapok a kérésem a szolgáltatáshoz való meghívásakor?
   * A metaadat-hívásokat a virtuális gép elsődleges hálózati kártyához rendelt elsődleges IP-címről kell elvégezni. Továbbá abban az esetben, ha módosította az útvonalakat, a virtuális gép helyi útválasztási táblázatában található 169.254.169.254/32-címnek útvonalnak kell lennie.
   * <details>
        <summary>Az útválasztási táblázat ellenőrzése</summary>

        1. Írja le a helyi útválasztási táblázatot egy paranccsal, `netstat -r` és keresse meg a IMDS bejegyzést (például):
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Ellenőrizze, hogy létezik-e útvonal a következőhöz `169.254.169.254` , és jegyezze fel a megfelelő hálózati adaptert (például `eth0` ).
        1. Az útválasztási táblázatban található megfelelő csatoló felületi konfigurációjának kiírása (Megjegyzés: a konfigurációs fájl pontos neve eltérő lehet)
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. Dinamikus IP-cím használata esetén jegyezze fel a MAC-címet. Ha statikus IP-címet használ, akkor a felsorolt IP-címek és/vagy a MAC-cím is megjegyezhető.
        1. Győződjön meg arról, hogy a csatoló megfelel a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges NIC/IP-t az Azure Portal hálózati konfigurációjának megtekintésével vagy [Az Azure CLI-vel](https://docs.microsoft.com/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show)keresheti meg. Jegyezze fel a nyilvános és a privát IP-címeket (és a MAC-címet, ha a CLI-t használja). PowerShell CLI-példa:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Ha nem egyeznek, frissítse az útválasztási táblázatot úgy, hogy az elsődleges NIC/IP-címet célozza meg.
    </details>

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Küldje el visszajelzését és megjegyzéseit https://feedback.azure.com .

A szolgáltatás támogatásának megszerzéséhez hozzon létre egy támogatási problémát a virtuális gép Azure Portaljában, ahol a hosszú újrapróbálkozás után nem tud metaadatokat kapni.
Használja a probléma típusát, `Management` és válassza ki `Instance Metadata Service` a kategóriát.

![Példány metaadatainak támogatása](./media/instance-metadata-service/InstanceMetadata-support.png "Képernyőfelvétel: támogatási eset megnyitása a Instance Metadata Serviceával kapcsolatos problémák esetén")

## <a name="next-steps"></a>Következő lépések

További információk:
1. [Szerezze be a virtuális gép hozzáférési jogkivonatát](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Scheduled Events](scheduled-events.md)

