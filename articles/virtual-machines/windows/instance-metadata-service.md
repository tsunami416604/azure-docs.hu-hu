---
title: Azure-Instance Metadata Service
description: REST-felület a Windows rendszerű virtuális gépek számítási, hálózati és közelgő karbantartási eseményeivel kapcsolatos információk lekéréséhez.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 25b61b7e21e70c1cd4d27f88a0f5ce965c01c5a5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964651"
---
# <a name="azure-instance-metadata-service"></a>Azure-példány metaadatainak szolgáltatása

Az Azure Instance Metadata Service (IMDS) információt nyújt a jelenleg futó virtuálisgép-példányokról, és a virtuális gépek felügyeletéhez és konfigurálásához használható.
A megadott információk közé tartozik az SKU, a hálózati konfiguráció és a közelgő karbantartási események. Az elérhető adatok teljes listáját lásd: [metadata API](#metadata-apis)-k.

Az Azure Instance Metadata Service egy REST-végpont, amely a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)használatával létrehozott összes IaaS virtuális gép számára elérhető.
A végpont egy jól ismert, nem irányítható IP-címen (`169.254.169.254`) érhető el, amely csak a virtuális gépről érhető el.

> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** az összes Azure-régióban.  Rendszeresen frissítéseket kap a virtuálisgép-példányokkal kapcsolatos új információk megjelenítéséhez. Ez az oldal az elérhető naprakész [metaadat-API-kat](#metadata-apis) mutatja.

## <a name="service-availability"></a>Elérhető szolgáltatások

A szolgáltatás az általánosan elérhető Azure-régiókban érhető el. Nem minden API-verzió érhető el az összes Azure-régióban.

Térségek                                        | Rendelkezésre állási?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure China 21Vianet](https://www.azure.cn/)                                            | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01

Ez a tábla akkor frissül, ha a szolgáltatás frissítései és/vagy új támogatott verziók érhetők el.

A Instance Metadata Service kipróbálásához hozzon létre egy virtuális gépet [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [Azure Portal](https://portal.azure.com) a fenti régiókban, és kövesse az alábbi példákat.
További példák a IMDS lekérdezésére az [Azure-példány metaadatainak mintái](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Használat

### <a name="versioning"></a>Verziókezelés

A Instance Metadata Service verziója, és a HTTP-kérelemben szereplő API-verzió megadása kötelező.

A [rendelkezésre állási táblában](#service-availability)felsorolt legújabb verziók láthatók.

Ahogy újabb verziók lettek hozzáadva, a régebbi verziók továbbra is elérhetők a kompatibilitás érdekében, ha a parancsfájlok adott adatformátumok függőségeivel rendelkeznek.

Ha nincs megadva verzió, a rendszer egy hibaüzenetet ad vissza a legújabb támogatott verziók listájával.

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

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

### <a name="using-headers"></a>Fejlécek használata

Amikor lekérdezi a Instance Metadata Service, meg kell adnia a fejlécet `Metadata: true` annak biztosítására, hogy a kérést nem szándékosan átirányítsák.

### <a name="retrieving-metadata"></a>Metaadatok beolvasása

A példány metaadatainak elérhetők a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)használatával létrehozott/kezelt virtuális gépek futtatásához. Egy virtuálisgép-példány összes adatkategóriájának elérése a következő kérelem használatával:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Az összes példány metaadat-lekérdezése megkülönbözteti a kis-és nagybetűket.

### <a name="data-output"></a>Adatkimenet

Alapértelmezés szerint a Instance Metadata Service JSON formátumú (`Content-Type: application/json`) adatokkal tér vissza. A különböző API-k azonban eltérő formátumban adják vissza az adattípusokat, ha szükséges.
Az alábbi táblázat más adatformátum-API-kat is támogat.

API | Alapértelmezett adatformátum | Egyéb formátumok
--------|---------------------|--------------
/instance | JSON | szöveg
/scheduledevents | JSON | Nincs
/attested | JSON | Nincs

A nem alapértelmezett válasz formátumának eléréséhez a kérelemben a kért formátumot lekérdezési karakterlánc paraméterként kell megadni. Példa:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> A `format=json` nem működik a levél csomópontjain. Ezeknek a lekérdezéseknek a `format=text` explicit módon meg kell adni, ha az alapértelmezett formátum a JSON.

### <a name="security"></a>Biztonság

Az Instance Metadata Service végpont csak a futó virtuálisgép-példányból érhető el egy nem irányítható IP-címen. Emellett a szolgáltatás visszautasítja a `X-Forwarded-For` fejléctel rendelkező kérelmeket.
A kéréseknek tartalmazniuk kell egy `Metadata: true` fejlécet is, amely biztosítja, hogy a tényleges kérelem közvetlenül legyen kiképezve, és ne a véletlen átirányítás része legyen.

### <a name="error"></a>Hiba

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service szabványos HTTP-hibákat ad vissza. Példa:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 hibás kérelem | Hiányzó `Metadata: true` fejléc, vagy hiányzik a formátum a levél csomópontjainak lekérdezése során
404 – Nem található | A kért elem nem létezik
405 metódus nem engedélyezett | Csak `GET` kérelmek támogatottak
429 túl sok kérés | Az API jelenleg legfeljebb 5 lekérdezést támogat másodpercenként
500 szolgáltatási hiba     | Némi idő elteltével próbálkozzon újra

### <a name="examples"></a>Példák

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az összes alábbi válasz elég kinyomtatva az olvashatóság érdekében.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

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

#### <a name="retrieving-public-ip-address"></a>Nyilvános IP-cím lekérése

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Példány összes metaadatának beolvasása

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
    "tags": "Department:IT;Environment:Test;Role:WebRole",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Metaadatok beolvasása a Windows rendszerű virtuális gépen

**Kérés**

A Windows a `curl` program segítségével kérheti le a példány metaadatainak beolvasását:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmaggal:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get
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
    "tags": "Department:IT;Environment:Test;Role:WebRole",
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
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Metaadat-API-k

A metaadatok végpontján a következő API-k érhetők el:

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
igazolt | [Igazolt](#attested-data) információ | 2018-10-01
identitáskezelés | Felügyelt identitások az Azure-erőforrásokhoz. Lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
például | Lásd: [példány API](#instance-api) | 2017-04-02
scheduledevents | Lásd: [Scheduled Events](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Példány API

A következő számítási kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadat-végponton keresztül a következő kategóriák érhetők el példányon/számításon keresztül.

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
azEnvironment | Az Azure-környezet, amelyben a virtuális gép fut | 2018-10-01
customData | Ez a funkció jelenleg le van tiltva, és az elérhetővé válás után frissítjük a dokumentációt | 2019-02-01
location | Az Azure-régió, amelyen a virtuális gép fut | 2017-04-02
név | A virtuális gép neve | 2017-04-02
offer | A virtuálisgép-lemezképre vonatkozó információkat nyújtja, és csak az Azure rendszerkép-katalógusból üzembe helyezett rendszerképekhez érhető el | 2017-04-02
osType | Linux vagy Windows | 2017-04-02
placementGroupId | A virtuálisgép-méretezési [csoport elhelyezési csoportja](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
csomag | Egy virtuális gép nevét, termékét és közzétevőjét tartalmazó [csomag megtervezése](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) , ha az Azure Marketplace-rendszerkép | 2018-04-02
platformUpdateDomain |  A virtuális gépet futtató [tartomány frissítése](manage-availability.md) | 2017-04-02
Platformfaultdomain tulajdonságot | A virtuális gép által futtatott tartalék [tartomány](manage-availability.md) | 2017-04-02
Szolgáltató | A virtuális gép szolgáltatója | 2018-10-01
publicKeys | A virtuális géphez és elérési utakhoz rendelt [nyilvános kulcsok gyűjteménye](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
Publisher | A virtuális gép rendszerképének közzétevője | 2017-04-02
resourceGroupName | A virtuális géphez tartozó [erőforráscsoport](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | Az erőforrás [teljes](https://docs.microsoft.com/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
SKU | A virtuális gép rendszerképének adott SKU-jának | 2017-04-02
storageProfile | Lásd: [tárolási profil](#storage-profile) | 2019-06-01
subscriptionId | Azure-előfizetés a virtuális géphez | 2017-08-01
tags | A virtuális gép [címkéi](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | Az egyszerűbb programozási elemzéshez JSON-tömbként formázott Címkék  | 2019-06-04
version | A VM-rendszerkép verziója | 2017-04-02
vmId | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | A virtuálisgép-méretezési csoport virtuálisgép-méretezési [készletének neve](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
VmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
zóna | A virtuális gép [rendelkezésre állási zónája](../../availability-zones/az-overview.md) | 2017-12-01

A következő hálózati kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadatok végpontján a következő kategóriák érhetők el a instance/Network/Interface használatával

Adatok | Leírás | Verzió bevezetése
-----|-------------|-----------------------
IPv4/Privateipaddress tulajdonságot | A virtuális gép helyi IPv4-címe | 2017-04-02
IPv4/publicIpAddress | A virtuális gép nyilvános IPv4-címe | 2017-04-02
alhálózat/címe | A virtuális gép alhálózati címe | 2017-04-02
alhálózat/előtag | Alhálózat előtagja, 24. példa | 2017-04-02
IPv6/IP-cím | A virtuális gép helyi IPv6-címe | 2017-04-02
macAddress | VM MAC-címe | 2017-04-02

## <a name="attested-data"></a>Igazolt adatértékek

Instance Metadata Service által kiszolgált forgatókönyv része a garancia arra, hogy a megadott adatok az Azure-ból érkeznek. Aláírjuk ezen információk egy részét, hogy a piactér lemezképei biztosak legyenek abban, hogy az Azure-ban futnak.

### <a name="example-attested-data"></a>Hitelesítő adatként példa

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az alábbi példákban az olvashatóság érdekében a következő válaszokat kell kinyomtatni.

 **Kérés**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Az API-Version kötelező mező. A támogatott API-verziókhoz tekintse meg a [szolgáltatás elérhetőségét ismertető szakaszt](#service-availability) .
Az alkalom egy opcionális 10 számjegyű karakterlánc. Ha nincs megadva, a IMDS az aktuális UTC időbélyeget adja vissza a helyén. A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott egyszeres értéket ad vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírási blob a dokumentum [PKCS7](https://aka.ms/pkcs7) aláírt verziója. Tartalmazza az aláíráshoz használt tanúsítványt, valamint a virtuális gép részleteit, például a vmId, az SKU, az alkalom, a subscriptionId, az időbélyeg a dokumentum létrehozásához és lejáratához, valamint a rendszerképre vonatkozó terv információit. A csomag adatai csak az Azure Market Place-lemezképek esetében tölthetők fel. A tanúsítvány kinyerhető a válaszból, és annak ellenőrzésére szolgál, hogy a válasz érvényes-e, és az Azure-ból származik-e.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Igazolt metaadatok beolvasása a Windows rendszerű virtuális gépen

 **Kérés**

A Windows PowerShell-segédprogram `curl`segítségével lekérheti a példány metaadatainak beolvasását:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Vagy a `Invoke-RestMethod` parancsmaggal:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Az API-Version kötelező mező. A támogatott API-verziókhoz tekintse meg a szolgáltatás elérhetőségét ismertető szakaszt.
Az alkalom egy opcionális 10 számjegyű karakterlánc. Ha nincs megadva, a IMDS az aktuális UTC időbélyeget adja vissza a helyén. A IMDS gyorsítótárazási mechanizmusa miatt előfordulhat, hogy a rendszer egy korábban gyorsítótárazott egyszeres értéket ad vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírási blob a dokumentum [PKCS7](https://aka.ms/pkcs7) aláírt verziója. Tartalmazza az aláíráshoz használt tanúsítványt, valamint a virtuális gép részleteit, például a vmId, az SKU, az alkalom, a subscriptionId, az időbélyeg a dokumentum létrehozásához és lejáratához, valamint a rendszerképre vonatkozó terv információit. A csomag adatai csak az Azure Market Place-lemezképek esetében tölthetők fel. A tanúsítvány kinyerhető a válaszból, és annak ellenőrzésére szolgál, hogy a válasz érvényes-e, és az Azure-ból származik-e.


## <a name="example-scenarios-for-usage"></a>Használati példák a használathoz  

### <a name="tracking-vm-running-on-azure"></a>Az Azure-ban futó virtuális gép nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökkel, amelyeknek nyomon kell követniük a virtuális gép egyediségét. A virtuális gép egyedi AZONOSÍTÓjának beszerzéséhez használja a `vmId` mezőt Instance Metadata Service.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezése, adatpartíciókon alapuló hibák/frissítési tartomány 

Bizonyos esetekben a különböző adatreplikák elhelyezése elsődleges fontossággal bír. Előfordulhat például, hogy az [HDFS replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy a tároló elhelyezése egy [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) keresztül lehetséges, hogy tudnia kell a `platformFaultDomain` és `platformUpdateDomain` a virtuális gép fut.
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

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információk beszerzése a virtuális gépről a támogatási eset során

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Azon Azure-környezet lekérése, amelyben a virtuális gép fut

Az Azure számos szuverén felhővel rendelkezik, mint például a [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Időnként szükség van az Azure-környezetre bizonyos futtatókörnyezeti döntések elvégzéséhez. A következő minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Válasz**
```bash
AzurePublicCloud
```

Az alábbi lista a régiókat és az Azure-környezet értékeit sorolja fel.

 Térségek | Azure-környezet
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china)          | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>A virtuális gép címkéinak beolvasása

Előfordulhat, hogy az Azure-beli virtuális gépen a címkék logikailag rendszerezve lettek. A virtuális géphez rendelt címkék az alábbi kérelem használatával kérhetők le.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Válasz**

```text
Department:IT;Environment:Test;Role:WebRole
```

A `tags` mező egy olyan karakterlánc, amelynek a címkéi pontosvesszővel tagolva vannak. Ez akkor lehet probléma, ha a címkékben pontosvesszőt használnak. Ha egy elemzőt a címkék programozott kinyeréséhez kell írni, érdemes a `tagsList` mezőre támaszkodni, amely egy határolójel nélküli JSON-tömb, és így könnyebben elemezhető.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=JSON"
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

### <a name="validating-that-the-vm-is-running-in-azure"></a>Annak ellenőrzése, hogy a virtuális gép fut-e az Azure-ban

A piactér-szállítók biztosítani szeretnék, hogy a szoftverük csak az Azure-ban fusson. Ha valaki a helyileg másolja a virtuális merevlemezt, akkor képesnek kell lennie az észlelésére. A Instance Metadata Serviceba való behívásával a piactér-szállítók olyan aláírt adatokhoz juthatnak, amelyek csak az Azure-ból választanak.

> [!NOTE]
> A jQ telepítéséhez szükséges.

**Kérés**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Válasz**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Adatok | Leírás
-----|------------
egyszeri | A felhasználó nem kötelező karakterláncot adott meg a kérelemmel. Ha a kérelemben nem adtak meg egy adott időpontot, a rendszer az aktuális UTC-időbélyeget adja vissza
csomag | [Tervezze](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) meg, hogy a virtuális gép egy Azure Marketplace-rendszerkép, amely tartalmazza a nevet, a terméket és a közzétevőt
időbélyeg/createdOn | Az első aláírt dokumentum létrehozásának UTC-időbélyege
időbélyeg/expiresOn | Az aláírt dokumentum érvényességi időpontjának UTC-időbélyege
vmId |  A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | Azure-előfizetés a virtuális géphez, amely `2019-04-30`
SKU | A virtuálisgép-rendszerkép adott SKU-jának `2019-11-01`ban bemutatott

#### <a name="verifying-the-signature"></a>Az aláírás ellenőrzése

Miután megszerezte a fenti aláírást, ellenőrizheti, hogy az aláírás a Microsofttól származik-e. Emellett ellenőrizheti a köztes tanúsítványt és a tanúsítványláncot is. Végül ellenőrizheti, hogy helyes-e az előfizetés-azonosító.

> [!NOTE]
> A nyilvános felhő és a szuverén felhő tanúsítványa eltérő lesz.

 Felhőbeli | Tanúsítvány
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Azokban az esetekben, amikor a közbenső tanúsítvány nem tölthető le, mert az érvényesítés során hálózati korlátozások miatt nem lehet letölteni, a köztes tanúsítvány rögzíthető. Az Azure azonban a szabványos PKI-gyakorlatnak megfelelően átadja a tanúsítványokat. A rögzített tanúsítványokat frissíteni kell, ha a folyamat bekövetkezik. A köztes tanúsítvány frissítésének megtervezése után az Azure-blog frissül, és az Azure-ügyfelek értesítést kapnak. A köztes tanúsítványokat [itt](https://www.microsoft.com/pki/mscorp/cps/default.htm)találja. Az egyes régiók köztes tanúsítványok különbözőek lehetnek.

### <a name="failover-clustering-in-windows-server"></a>Feladatátvételi fürtszolgáltatás a Windows Server rendszerben

Bizonyos forgatókönyvek esetén, amikor feladatátvételi fürtszolgáltatással Instance Metadata Service lekérdezést hajt végre, hozzá kell adnia egy útvonalat az útválasztási táblához.

1. Nyisson meg egy parancssort rendszergazdai jogosultságokkal.

2. Futtassa a következő parancsot, és jegyezze fel a Hálózati célhely (`0.0.0.0`) interfészének címe az IPv4-útválasztási táblázatban.

```bat
route print
```

> [!NOTE]
> A feladatátvevő fürttel rendelkező Windows Server rendszerű virtuális gépek következő példájának kimenete csak az egyszerűség IPv4-útválasztási táblázatát tartalmazza.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Futtassa a következő parancsot, és használja a Hálózati célhely (`0.0.0.0`) interfészének címe (`10.0.1.10`) ebben a példában.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Tárolóprofil

A Instance Metadata Service a virtuális géphez társított tárolóeszközök adatait is megadhatja. Ezek az adatok a példány/számítás/storageProfile végponton találhatók.

A virtuális gépek tárolási profilja három kategóriára oszlik – a Képhivatkozás, az operációsrendszer-lemez és az adatlemezek.

A képhivatkozási objektum a következő információkat tartalmazza az operációsrendszer-lemezképpel kapcsolatban:

Adatok    | Leírás
--------|-----------------
id      | Erőforrás-azonosító
offer   | A platform vagy a piactér rendszerképének ajánlata
Publisher | Rendszerkép kiadója
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
név    | Lemez neve
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
név    | Lemez neve
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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Példák a metaadat-szolgáltatás hívására különböző nyelveken a virtuális gépen belül 

Nyelv | Példa
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Indítás  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Gyakori kérdések

1. `400 Bad Request, Required metadata header not specified`hibaüzenetet kapok. Ez mit jelent?
   * A Instance Metadata Service megköveteli, hogy a fejléc `Metadata: true` átadja a kérést. Ha ezt a fejlécet a REST-hívásban adja át, akkor a Instance Metadata Servicehoz való hozzáférést is lehetővé teszi.
2. Miért nem kapok számítási információt a virtuális géphez?
   * A Instance Metadata Service jelenleg csak Azure Resource Manager által létrehozott példányokat támogatja. A jövőben a Cloud Service virtuális gépek támogatása is felvehető.
3. Létrehoztam a virtuális gépet Azure Resource Manager egy kicsit vissza. Miért nem jelenik meg a számítási metaadatok adatai?
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuális géphez.
4. Nem látom az új verzióhoz feltöltött összes adatot
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuális géphez.
5. Miért kapok hibaüzenetet `500 Internal Server Error`?
   * Próbálja megismételni a kérést az exponenciális visszalépési rendszer alapján. Ha a probléma továbbra is fennáll, forduljon az Azure ügyfélszolgálatához.
6. Hol oszthatok meg további kérdéseket/megjegyzéseket?
   * Küldje el megjegyzéseit https://feedback.azure.com.
7. Működne a virtuálisgép-méretezési csoport példányai?
   * Az igen metaadat-szolgáltatás elérhető a méretezési csoport példányaihoz.
8. Hogyan kap támogatást a szolgáltatáshoz?
   * A szolgáltatás támogatásának megszerzéséhez hozzon létre egy támogatási problémát a virtuális gép Azure Portaljában, ahol a hosszú újrapróbálkozás után nem tud metaadatokat kapni.
9. Időtúllépést kapok a kérésem a szolgáltatáshoz való meghívásakor?
   * A metaadatokat a virtuális gép elsődleges hálózati kártyához rendelt elsődleges IP-címről kell végrehajtani, továbbá abban az esetben, ha módosította az útvonalakat, a hálózati kártyán kívüli útvonalnak kell lennie a 169.254.0.0/16-címhez.
10. Frissítettem a címkéket a virtuálisgép-méretezési csoportokban, de nem jelennek meg a példányokban a virtuális gépektől eltérően?
    * A ScaleSets-címkék esetében jelenleg csak a virtuális gép jelenik meg újraindítás/rendszerkép/lemezkép/vagy a példány lemezre váltásakor.

    ![Példány metaadatainak támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Következő lépések

- További információ a [Scheduled Events](scheduled-events.md)
