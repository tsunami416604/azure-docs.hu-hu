---
title: Azure-Instance Metadata Service Linux rendszerhez
description: Ismerkedjen meg az Azure Instance Metadata Serviceával, valamint arról, hogy miként nyújt információt a jelenleg futó virtuálisgép-példányokról a Linux rendszerben.
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
ms.openlocfilehash: c9a4f5697fb667cde2cf3b4ddc3d637ff58149c9
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436589"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)

Az Azure Instance Metadata Service (IMDS) információt nyújt a jelenleg futó virtuálisgép-példányokról. Használhatja a virtuális gépek felügyeletére és konfigurálására.
Ezen információk közé tartozik a SKU, a Storage, a hálózati konfigurációk és a közelgő karbantartási események. Az elérhető adatok teljes listáját lásd: [metadata API](#metadata-apis)-k.

A IMDS virtuális gépek (VM-EK) és virtuálisgép-méretezési csoport példányainak futtatására is használható. Minden API támogatja a [Azure Resource Manager](/rest/api/resources/)használatával létrehozott és kezelt virtuális gépeket. Csak az igazolt és a hálózati végpontok támogatják a klasszikus üzemi modell használatával létrehozott virtuális gépeket. Az igazolt végpont csak korlátozott mértékben működik.

A IMDS egy olyan REST-végpont, amely jól ismert, nem irányítható IP-címen () érhető el `169.254.169.254` . Csak a virtuális gépen belülről férhet hozzá. A virtuális gép és a IMDS közötti kommunikáció soha nem hagyja el a gazdagépet.
A IMDS lekérdezése során a HTTP-ügyfelek megkerülik a webes proxykat a virtuális gépen, és `169.254.169.254` ugyanazokat a-t kell kezelnie [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Biztonság

Az IMDS-végpont csak a futó virtuálisgép-példányon belül érhető el egy nem irányítható IP-címen. Emellett `X-Forwarded-For` a szolgáltatás elutasítja a fejléctel ellátott kérelmeket.
A kéréseknek tartalmaznia kell egy `Metadata: true` fejlécet is, amely biztosítja, hogy a tényleges kérelem közvetlenül a véletlen átirányítás része legyen.

> [!IMPORTANT]
> A IMDS nem egy csatorna a bizalmas adatokhoz. A végpont a virtuális gépen futó összes folyamat számára nyitva van. Tekintse át a szolgáltatáson keresztül közzétett információkat megosztott információként a virtuális gépen futó összes alkalmazás számára.

## <a name="usage"></a>Használat

### <a name="access-azure-instance-metadata-service"></a>Az Azure Instance Metadata Service elérése

A IMDS eléréséhez hozzon létre egy virtuális gépet [Azure Resource Manager](/rest/api/resources/) vagy a [Azure Portalból](https://portal.azure.com), és használja a következő mintákat.
További Példákért lásd az [Azure-példány metaadatainak mintáit](https://github.com/microsoft/azureimds)ismertető témakört.

Az alábbi mintakód egy példány metaadatainak beolvasására használható. Egy adott adatforrás eléréséhez tekintse meg a [metaadatok API](#metadata-apis) című szakaszt. 

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
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
            }],
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
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Adatkimenet

Alapértelmezés szerint a IMDS JSON formátumban () adja vissza az értékeket `Content-Type: application/json` . Bizonyos API-k azonban más formátumokban is visszaadhatják az adattípusokat, ha szükséges.
Az alábbi táblázat az API-k által támogatott egyéb adatformátumokat sorolja fel.

API | Alapértelmezett adatformátum | Egyéb formátumok
--------|---------------------|--------------
/attested | json | nincs
/identity | json | nincs
/instance | json | szöveg
/scheduledevents | json | nincs

A nem alapértelmezett válasz formátumának eléréséhez a kérelemben a kért formátumot lekérdezési karakterlánc paraméterként kell megadni. Például:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> A-ben `/metadata/instance` a levél csomópontjai `format=json` nem működnek. Ezeket a lekérdezéseket `format=text` explicit módon meg kell adni, mert az alapértelmezett formátum a JSON.

### <a name="version"></a>Verzió

A IMDS verziója, és a HTTP-kérelemben szereplő API-verzió megadása kötelező.

A támogatott API-verziók a következők: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Lehetséges, hogy az 2020-10-01-es verzió még nem érhető el minden régióban.

Az újabb verziók hozzáadásakor továbbra is elérheti a régebbi verziókat a kompatibilitás érdekében, ha a parancsfájlok adott adatformátumok függőségeivel rendelkeznek.

Ha nem ad meg verziót, a rendszer hibaüzenetet kap, és felsorolja a legújabb támogatott verziókat.

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa a hiba feltételét jelzi, ha nincs megadva a verzió. A válasz elég kinyomtatva az olvashatóság érdekében.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Válasz**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>Metaadat-API-k

A IMDS több, különböző adatforrásokat jelképező API-t tartalmaz.

API | Leírás | Verzió bevezetése
----|-------------|-----------------------
/attested | [Igazolt](#attested-data) információ | 2018-10-01
/identity | Lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Lásd: [példány API](#instance-api) | 2017-04-02
/scheduledevents | [Ütemezett események](scheduled-events.md) megjelenítése | 2017-08-01

## <a name="instance-api"></a>Példány API

A példány API elérhetővé teszi a virtuálisgép-példányok fontos metaadatait, beleértve a virtuális gépet, a hálózatot és a tárolót. A következő kategóriákat érheti el `instance/compute` :

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
azEnvironment | Az Azure-környezet, amelyben a virtuális gép fut. | 2018-10-01
customData | Ez a funkció jelenleg le van tiltva. | 2019-02-01
isHostCompatibilityLayerVm | Azt határozza meg, hogy a virtuális gép a gazdagép kompatibilitási rétegén fut-e. | 2020-06-01
licenseType | A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)licencének típusa. Vegye figyelembe, hogy ez csak a AHB-kompatibilis virtuális gépek esetében jelent meg. | 2020-09-01
location | Az az Azure-régió, amelyben a virtuális gép fut. | 2017-04-02
name | A virtuális gép neve. | 2017-04-02
offer | Információ a virtuálisgép-rendszerképről. Ez csak az Azure rendszerkép-katalógusból üzembe helyezett rendszerképek esetében jelent meg. | 2017-04-02
osProfile.adminUsername | Megadja a rendszergazdai fiók nevét. | 2020-07-15
osProfile. számítógépnév | Megadja a számítógép nevét. | 2020-07-15
osProfile. disablePasswordAuthentication | Megadja, hogy a jelszó-hitelesítés le van-e tiltva. Vegye figyelembe, hogy ez csak a Linux rendszerű virtuális gépek esetében jelent meg. | 2020-10-01
osType | Linux vagy Windows. | 2017-04-02
placementGroupId | A virtuálisgép-méretezési [csoport elhelyezési csoportja](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) . | 2017-08-01
csomag | [Megtervezheti](/rest/api/compute/virtualmachines/createorupdate#plan) egy virtuális gép nevét, termékét és közzétevőjét, ha az egy Azure Marketplace-rendszerkép. | 2018-04-02
platformUpdateDomain |  [Frissítési tartomány](../manage-availability.md) , amelyben a virtuális gép fut. | 2017-04-02
Platformfaultdomain tulajdonságot | Az a tartalék [tartomány](../manage-availability.md) , amelyben a virtuális gép fut. | 2017-04-02
Szolgáltató | A virtuális gép szolgáltatója. | 2018-10-01
publicKeys | A virtuális géphez és elérési utakhoz rendelt [nyilvános kulcsok gyűjteménye](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) . | 2018-04-02
közzétevő | A virtuális gép rendszerképének közzétevője. | 2017-04-02
resourceGroupName | A virtuális géphez tartozó [erőforráscsoport](../../azure-resource-manager/management/overview.md) . | 2017-08-01
resourceId | Az erőforrás [teljes](/rest/api/resources/resources/getbyid) azonosítója. | 2019-03-11
SKU | A virtuális gép rendszerképének adott SKU-jának. | 2017-04-02
securityProfile. secureBootEnabled | Azt határozza meg, hogy az UEFI biztonságos rendszerindítás engedélyezve van-e a virtuális gépen. | 2020-06-01
securityProfile.virtualTpmEnabled | Meghatározza, hogy a virtuális platformmegbízhatósági modul (TPM) engedélyezve van-e a virtuális GÉPEN. | 2020-06-01
storageProfile | Lásd: [tárolási profil](#storage-metadata). | 2019-06-01
subscriptionId | Azure-előfizetés a virtuális géphez. | 2017-08-01
tags | A virtuális géphez tartozó [címkék](../../azure-resource-manager/management/tag-resources.md) .  | 2017-08-01
tagsList | Az egyszerűbb programozási elemzéshez JSON-tömbként formázott címkék.  | 2019-06-04
version | A virtuálisgép-rendszerkép verziója. | 2017-04-02
vmId | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) . | 2017-04-02
vmScaleSetName | Virtuálisgép-méretezési [csoport neve](../../virtual-machine-scale-sets/overview.md) a virtuálisgép-méretezési csoport számára. | 2017-12-01
vmSize | Lásd a [virtuális gép méretét](../sizes.md). | 2017-04-02
zóna | A virtuális gép [rendelkezésre állási zónája](../../availability-zones/az-overview.md) . | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>1. példa: az Azure-on futó virtuális gépek nyomon követése

Előfordulhat, hogy nyomon kell követnie a szoftvert futtató virtuális gépek számát, vagy olyan ügynökökkel kell rendelkeznie, amelyeknek nyomon kell követniük a virtuális gép egyediségét. Egy virtuális gép egyedi AZONOSÍTÓjának beszerzéséhez használja a `vmId` mezőt a IMDS.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>2. példa: különböző adatreplikák elhelyezése

Bizonyos esetekben a különböző adatreplikák elhelyezése elsődleges fontossággal bír. Előfordulhat például, hogy az [HDFS replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy a tároló elhelyezése egy [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) keresztül megkövetelheti `platformFaultDomain` a `platformUpdateDomain` és a virtuális gép futtatását.
Ezen döntések elvégzéséhez [Availability Zones](../../availability-zones/az-overview.md) is használhatja a példányokhoz.
Ezeket az adatlekérdezéseket közvetlenül a IMDS-on keresztül kérdezheti le.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Válasz**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>3. példa: további információk a virtuális géppel kapcsolatban a támogatási esetekben

Szolgáltatóként olyan támogatási hívást kaphat, ahol további információkat szeretne megtudni a virtuális gépről. Ebben az esetben az ügyfélnek a számítási metaadatok megosztására való kérése hasznos lehet.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
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
        }],
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
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>4. példa: az Azure-környezet beszerzése, amelyen a virtuális gép fut

Az Azure számos szuverén felhővel rendelkezik, mint például a [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Időnként szükség van az Azure-környezetre bizonyos futtatókörnyezeti döntések elvégzéséhez. A következő minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Válasz**

```text
AzurePublicCloud
```

A felhő és az Azure-környezet értékei itt láthatók.

 Felhőbeli   | Azure-környezet
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Hálózati metaadatok 

A hálózati metaadatok a példány API részét képezik. A következő hálózati kategóriák érhetők el a `instance/network` végponton keresztül.

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
IPv4/Privateipaddress tulajdonságot | A virtuális gép helyi IPv4-címe. | 2017-04-02
IPv4/publicIpAddress | A virtuális gép nyilvános IPv4-címe. | 2017-04-02
alhálózat/címe | A virtuális gép alhálózat-címe. | 2017-04-02
alhálózat/előtag | Az alhálózat előtagja. Példa: 24 | 2017-04-02
IPv6/IP-cím | A virtuális gép helyi IPv6-címe. | 2017-04-02
macAddress | A virtuális gép MAC-címe. | 2017-04-02

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az összes alábbi válasz elég kinyomtatva az olvashatóság érdekében.

#### <a name="sample-1-retrieve-network-information"></a>1. példa: hálózati információk beolvasása

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

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

#### <a name="sample-2-retrieve-public-ip-address"></a>2. minta: nyilvános IP-cím lekérése

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Tárolási metaadatok

A tárolási metaadatok a példányok API részét képezik, a `instance/compute/storageProfile` végpont alatt.
A virtuális géphez társított tárolóeszközök adatait tartalmazza. 

A virtuális gépek tárolási profilja három kategóriára oszlik: képhivatkozás, operációsrendszer-lemez és adatlemezek.

A képhivatkozási objektum a következő információkat tartalmazza az operációs rendszer rendszerképével kapcsolatban:

Adatok    | Leírás
--------|-----------------
id      | Erőforrás-azonosító
offer   | A platform vagy a rendszerkép ajánlata
közzétevő | Rendszerkép kiadója
SKU     | Rendszerkép SKU
version | A platform vagy a rendszerkép verziója

Az operációs rendszer lemezének objektuma a következő információkat tartalmazza a virtuális gép által használt operációsrendszer-lemezről:

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
writeAcceleratorEnabled | Függetlenül attól, hogy engedélyezve van-e a `writeAccelerator` lemezen

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
writeAcceleratorEnabled | Függetlenül attól, hogy engedélyezve van-e a `writeAccelerator` lemezen

Az alábbi példa bemutatja, hogyan lehet lekérdezni a virtuális gép tárolási adatait.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

A virtuális gépek címkéi a példányok API-ját tartalmazzák, a `instance/compute/tags` végpont alatt.
Előfordulhat, hogy az Azure-beli virtuális gépen a címkék logikailag rendszerezve lettek. A virtuális géphez rendelt címkéket a következő kérelem használatával kérheti le.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Válasz**

```text
Department:IT;Environment:Test;Role:WebRole
```

A `tags` mező egy olyan karakterlánc, amelynek a címkéi pontosvesszővel vannak elválasztva. Ez a kimenet akkor lehet probléma, ha a címkékben pontosvesszőt használnak. Ha egy elemzőt a címkék programozott kinyeréséhez kell írni, a mezőre kell támaszkodnia `tagsList` . A `tagsList` mező egy olyan JSON-tömb, amely nem határolójeleket mutat be, ezért könnyebben elemezhető.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

A IMDS segít biztosítani, hogy a megadott adatok az Azure-ból érkeznek. A Microsoft aláírja ezen információk egy részét, így meggyőződhet arról, hogy az Azure piactéren az Azure-ban futtatott rendszerkép látható.

### <a name="sample-1-get-attested-data"></a>1. példa: igazolt adatlekérdezés

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az alábbi példákban az olvashatóság érdekében a következő válaszokat kell kinyomtatni.

**Kérés**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

`Api-version` kötelező mező. Tekintse át a támogatott API-verziók [használati szakaszát](#usage) .
`Nonce` egy opcionális, 10 számjegyű karakterlánc. Ha nincs megadva, a IMDS az aktuálisan beállított világidő időbélyeget adja vissza a helyén.

> [!NOTE]
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott `nonce` értéket ad vissza.

**Válasz**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírási blob a dokumentum egy [PKCS7](https://aka.ms/pkcs7)aláírt verziója. Az aláíráshoz használt tanúsítványt, valamint bizonyos virtuálisgép-specifikus részleteket tartalmaz. 

A Azure Resource Manager használatával létrehozott virtuális gépek esetében a `vmId` `sku` `nonce` `subscriptionId` `timeStamp` dokumentum létrehozásához és lejáratához, valamint a rendszerkép információinak megtervezéséhez a, a, a, a. A csomag adatai csak az Azure Marketplace-lemezképek esetében vannak kitöltve. 

A klasszikus üzemi modellel létrehozott virtuális gépek esetében csak a `vmId` kitöltése garantált. Kinyerheti a tanúsítványt a válaszból, és ezzel ellenőrizheti, hogy a válasz érvényes-e, és az Azure-ból érkezik-e.

A dokumentum a következő mezőket tartalmazza:

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
licenseType | A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit)licencének típusa. Vegye figyelembe, hogy ez csak a AHB-kompatibilis virtuális gépek esetében jelent meg. | 2020-09-01
egyszeri | Egy karakterlánc, amely opcionálisan megadható a kérelemben. Ha nem `nonce` lett megadva, a rendszer az aktuálisan koordinált világidő-időbélyeget használja. | 2018-10-01
csomag | Az [Azure Marketplace-rendszerkép terve](/rest/api/compute/virtualmachines/createorupdate#plan). A csomag AZONOSÍTÓját (név), a termék rendszerképét vagy az ajánlatot (terméket) és a közzétevő AZONOSÍTÓját (kiadó) tartalmazza. | 2018-10-01
időbélyeg/createdOn | Az aláírt dokumentum létrehozásának időbélyege az egyezményes világidő szerint. | 2018-20-01
időbélyeg/expiresOn | Az aláírt dokumentum érvényességének lejárati idejének egyezményes világidő. | 2018-10-01
vmId |  A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) . | 2018-10-01
subscriptionId | A virtuális gép Azure-előfizetése. | 2019-04-30
SKU | A virtuális gép rendszerképének adott SKU-jának. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>2. minta: annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

Az Azure piactéren lévő szállítók biztosítják, hogy a szoftverük csak az Azure-ban fusson. Ha valaki átmásolja a virtuális merevlemezt egy helyszíni környezetbe, a gyártónak képesnek kell lennie az észlelésére. A IMDS-en keresztül ezek a szállítók olyan aláírt adatokhoz juthatnak, amelyek csak az Azure-ból választanak.

> [!NOTE]
> Ehhez a mintához telepíteni kell a jQ segédprogramot.

**Kérés**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
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
  "licenseType": "Windows_Client",  
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
> A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott `nonce` értéket ad vissza.

Az `nonce` aláírt dokumentumban szereplő érték összehasonlítható, ha `nonce` a kezdeti kérelemben paramétert adott meg.

> [!NOTE]
> A nyilvános felhőhöz és az egyes szuverén felhőhöz tartozó tanúsítvány eltérő lesz.

Felhőbeli | Tanúsítvány
------|------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Előfordulhat, hogy a tanúsítványok nem egyeznek pontosan a `metadata.azure.com` nyilvános felhővel. Emiatt a minősítés érvényesítésének minden altartományból engedélyeznie kell a köznapi nevet `.metadata.azure.com` .

Azokban az esetekben, amikor a köztes tanúsítvány nem tölthető le, mert az érvényesítés során hálózati korlátozások miatt nem lehet letölteni, rögzítheti a köztes tanúsítványt. Vegye figyelembe, hogy az Azure átveszi a tanúsítványokat, ami a szabványos PKI-gyakorlat. A beváltáskor frissítenie kell a rögzített tanúsítványokat. A köztes tanúsítvány frissítésének megtervezése után az Azure-blog frissül, és az Azure-ügyfelek értesítést kapnak. 

A köztes tanúsítványokat a PKI- [tárházban](https://www.microsoft.com/pki/mscorp/cps/default.htm)találja. Az egyes régiók köztes tanúsítványok különbözőek lehetnek.

> [!NOTE]
> Az Azure China 21Vianet köztes tanúsítvány a Baltimore helyett a DigiCert globális legfelső szintű HITELESÍTÉSSZOLGÁLTATÓTÓL fog származni.
Ha az Azure China köztes tanúsítványokat a legfelső szintű láncbeli hatóság változásának részeként rögzítette, a köztes tanúsítványokat frissíteni kell.

## <a name="managed-identity"></a>Felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás engedélyezhető a virtuális gépen. Egy vagy több felhasználó által hozzárendelt felügyelt identitást is hozzárendelhet a virtuális géphez.
Ezután jogkivonatokat kérhet a felügyelt identitásokhoz a IMDS-ből. Ezekkel a jogkivonatokkal hitelesíthetők más Azure-szolgáltatásokkal, például a Azure Key Vaultokkal.

A szolgáltatás engedélyezésének részletes lépéseiért lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Ütemezett események
Az ütemezett események állapotát a IMDS használatával szerezheti be. Ezután a felhasználó megadhatja az eseményeken futtatandó műveletek készletét. További információ: [ütemezett események](scheduled-events.md).

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A szolgáltatás általánosan elérhető az összes Azure-felhőben.

## <a name="sample-code-in-different-languages"></a>Mintakód különböző nyelveken

A következő táblázat a virtuális gépen belüli különböző nyelvek használatával mutatja be a hívó IMDS mintáit:

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

## <a name="errors-and-debugging"></a>Hibák és hibakeresés

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service szabványos HTTP-hibákat ad vissza. Például:

HTTP-állapotkód | Ok
-----------------|-------
200 OK |
400 Hibás kérés | Hiányzó `Metadata: true` fejléc vagy hiányzó paraméter `format=json` a levél csomópontjainak lekérdezése során.
404 Nem található  | A kért elem nem létezik.
405 metódus nem engedélyezett | Csak `GET` a kérelmek támogatottak.
410 elment | Próbálkozzon újra egy kis idő elteltével, legfeljebb 70 másodpercig.
429 – Túl sok kérelem | Az API jelenleg legfeljebb 5 lekérdezést támogat másodpercenként.
500 szolgáltatási hiba     | Némi idő elteltével próbálkozzon újra.

### <a name="frequently-asked-questions"></a>Gyakori kérdések

**Hibaüzenetet kapok `400 Bad Request, Required metadata header not specified` . Mit jelent ez?**

A IMDS megköveteli a fejléc átadását a `Metadata: true` kérelemben. Ha ezt a fejlécet a REST-hívásban adja át, a IMDS hozzáférése is elérhetővé válik.

**Miért nem kapok számítási információt a virtuális géphez?**

A IMDS jelenleg csak a Azure Resource Manager-vel létrehozott példányokat támogatja.

**Létrehoztam a virtuális gépet Azure Resource Manager néhány évvel ezelőtt. Miért nem jelenik meg a számítási metaadatokkal kapcsolatos információ?**

Ha a virtuális gépet a 2016 szeptember után hozta létre, vegyen fel egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítésének megkezdéséhez. Ha a virtuális gépet 2016 szeptembere előtt hozta létre, akkor a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányhoz.

**Miért nem látok az új verzióhoz feltöltött összes adatot?**

Ha a virtuális gépet a 2016 szeptember után hozta létre, vegyen fel egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítésének megkezdéséhez. Ha a virtuális gépet 2016 szeptembere előtt hozta létre, akkor a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuálisgép-példányhoz.

**Miért kapok hibát `500 Internal Server Error` vagy `410 Resource Gone` ?**

Próbálja megismételni a kérést. További információ: [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults). Ha a probléma továbbra is fennáll, hozzon létre egy támogatási problémát a virtuális gép Azure Portaljában.

**Működne a virtuálisgép-méretezési csoport példányai?**

Igen, a virtuálisgép-méretezési csoport példányaihoz elérhető a IMDS.

**Frissítettem a címkéket a virtuálisgép-méretezési csoportokban, de nem jelennek meg a példányokban (az Egypéldányos virtuális gépektől eltérően). Valami hiba történt?**

Jelenleg a virtuálisgép-méretezési csoportokhoz tartozó címkék csak a virtuális GÉPRE mutatnak a példány újraindításakor, rendszerképének vagy lemezének változásakor.

**Miért van időtúllépés a kérésem a szolgáltatáshoz való meghívásakor?**

A metaadat-hívásokat a virtuális gép elsődleges hálózati kártyához rendelt elsődleges IP-címről kell elvégezni. Emellett, ha módosította az útvonalakat, a virtuális gép helyi útválasztási táblázatában a 169.254.169.254/32-címnek is szerepelnie kell egy útvonalnak.

   * <details>
        <summary>Az útválasztási táblázat ellenőrzése</summary>

        1. Írja le a helyi útválasztási táblázatot, és keresse meg a IMDS bejegyzést. Például:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Ellenőrizze, hogy létezik-e útvonal a következőhöz `169.254.169.254` , és jegyezze fel a megfelelő hálózati adaptert (például `172.16.69.7` ).
        1. Az illesztőfelület konfigurációjának kiírása, valamint az útválasztási táblázatban hivatkozott, a MAC (fizikai) címek megadásával megegyező felület megkeresése.
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Győződjön meg arról, hogy a csatoló megfelel a virtuális gép elsődleges hálózati adapterének és elsődleges IP-címének. Az elsődleges hálózati adapter és az IP-cím megkereséséhez tekintse meg a Azure Portal hálózati konfigurációját, vagy az Azure CLI-vel megtekintve. Jegyezze fel a nyilvános és a privát IP-címeket (és a MAC-címet, ha a CLI-t használja). Íme egy PowerShell CLI-példa:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Ha nem egyeznek, frissítse az útválasztási táblázatot úgy, hogy az elsődleges NIC és az IP-címet célozza meg.
    </details>

## <a name="support"></a>Támogatás

Ha több kísérlet után nem tud metaadat-választ kapni, létrehozhat egy támogatási problémát a Azure Portal.
A **probléma típusa** beállításnál válassza a **felügyelet** lehetőséget. A **Kategória** mezőben válassza a **instance metadata Service** lehetőséget.

![Képernyőkép a Instance Metadata Service-támogatásról](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>További lépések

[Hozzáférési jogkivonat beszerzése a virtuális géphez](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Ütemezett események](scheduled-events.md)

