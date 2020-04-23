---
title: Azure-példány metaadat-szolgáltatása
description: RESTful felület a virtuális gépek számítási, hálózati és közelgő karbantartási eseményekkel kapcsolatos információk beszerezéséhez.
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: cb9453e1a25f4042c45d4e89229b555c996d4c8b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870083"
---
# <a name="azure-instance-metadata-service"></a>Azure-példány metaadat-szolgáltatása

Az Azure Instance metaadat-szolgáltatás (IMDS) nyújt információt a jelenleg futó virtuálisgép-példányok, és a virtuális gépek kezelésére és konfigurálására használható.
A megadott információk közé tartozik a termékváltozat, a hálózati konfiguráció és a közelgő karbantartási események. A rendelkezésre álló adatok teljes listáját a [metaadat-API-k című témakörben tetszés szerint.](#metadata-apis)

Az Azure példánymetaadat-szolgáltatása egy REST-végpont, amely az Azure [Resource Manager](https://docs.microsoft.com/rest/api/resources/)en keresztül létrehozott összes IaaS virtuális gép számára elérhető.
A végpont érhető el egy jól ismert, nem`169.254.169.254`irányítható IP-cím ( ), amely csak a virtuális gépről érhető el.

> [!IMPORTANT]
> Ez a szolgáltatás általában minden Azure-régióban **elérhető.**  Rendszeresen kap frissítéseket, hogy új információkat jelenítsen meg a virtuálisgép-példányokról. Ez az oldal a rendelkezésre álló naprakész [metaadat-API-kat](#metadata-apis) tükrözi.

## <a name="service-availability"></a>A szolgáltatás elérhetősége

A szolgáltatás általánosan elérhető Azure-régiókban érhető el. Előfordulhat, hogy nem minden API-verzió érhető el az összes Azure-régióban.

Régiók                                        | Elérhetőség?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Minden általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | Általánosan elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Általánosan elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure China 21Vianet](https://www.azure.cn/)                                            | Általánosan elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Általánosan elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

A 2019-11-01-es verzió telepítése folyamatban van, és előfordulhat, hogy nem érhető el minden régióban.

Ez a táblázat frissül, ha szolgáltatásfrissítések és/vagy új támogatott verziók érhetők el.

A példány metaadat-szolgáltatás kipróbálásához hozzon létre egy virtuális gép az [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy az Azure [Portal](https://portal.azure.com) a fenti régiókban, és kövesse az alábbi példákat.
További példák az IMDS lekérdezésére az [Azure-példány metaadat-mintáiban](https://github.com/microsoft/azureimds) találhatók

## <a name="usage"></a>Használat

### <a name="versioning"></a>Verziókezelés

A példány metaadat-szolgáltatás verziószámozott, és az API-verzió megadása a HTTP-kérelemben kötelező.

Az [elérhetőségi táblázatban](#service-availability)felsorolt legújabb verziók láthatók.

Az újabb verziók hozzáadásakor a régebbi verziók továbbra is elérhetők a kompatibilitás érdekében, ha a parancsfájlok adott adatformátumoktól függenek.

Ha nincs megadva verzió, a rendszer hibaüzenetet ad vissza a legújabb támogatott verziók listájával.

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

Amikor lekérdezi a példány metaadat-szolgáltatás, `Metadata: true` meg kell adnia a fejlécet, hogy megbizonyosodjon arról, hogy a kérelem nem véletlenül lett átirányítva.

### <a name="retrieving-metadata"></a>Metaadatok beolvasása

A példány metaadatai az Azure Resource [Manager](https://docs.microsoft.com/rest/api/resources/)használatával létrehozott/felügyelt virtuális gépek futtatásához érhetők el. A következő kéréssel elérheti a virtuálisgép-példány összes adatkategóriáját:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Minden példány metaadat-lekérdezése imitátust jelent.

### <a name="data-output"></a>Adatkimenet

Alapértelmezés szerint a Példánymetaadat-szolgáltatás JSON`Content-Type: application/json`formátumú ( ) formátumú adatokat ad vissza. A különböző API-k azonban kérésre különböző formátumokban adnak vissza adatokat.
Az alábbi táblázat az api-k által támogatott egyéb adatformátumok hivatkozását tartalmaz.

API | Alapértelmezett adatformátum | Egyéb formátumok
--------|---------------------|--------------
/példány | json | szöveg
/ütemezett események | json | Nincs
/igazolt | json | Nincs

Ha nem alapértelmezett válaszformátumot szeretne elérni, adja meg a kért formátumot lekérdezési karakterlánc-paraméterként a kérelemben. Például:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> A levél csomópontok `format=json` a nem működik. Ezekhez `format=text` a lekérdezésekhez explicit módon meg kell adni, ha az alapértelmezett formátum json.

### <a name="security"></a>Biztonság

A példány metaadat-szolgáltatás végpontja csak a futó virtuálisgép-példányon belül érhető el egy nem irányítható IP-címen. Ezenkívül a `X-Forwarded-For` szolgáltatás elutasítja a fejléccel rendelkező kéréseket.
A kérelmeknek `Metadata: true` fejlécet is tartalmazniuk kell annak biztosítása érdekében, hogy a tényleges kérelmet közvetlenül szánták, és ne része legyen a nem szándékos átirányításnak.

### <a name="error"></a>Hiba

Ha egy adatelem nem található, vagy hibásan formázott kérelem, a példány metaadat-szolgáltatás szabványos HTTP-hibákat ad vissza. Például:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 rossz kérés | Hiányzó `Metadata: true` fejléc vagy a formátum hiányzása levélcsomópont lekérdezésekor
404 Nem található | A kért elem nem létezik
405 módszer nem engedélyezett | Csak `GET` a kérelmek támogatottak
410 Eltűnt | Próbálkozzon újra egy idő után legfeljebb 70 másodpercig
429 – Túl sok kérelem | Az API jelenleg másodpercenként legfeljebb 5 lekérdezést támogat
500 szervizhiba     | Próbálkozzon újra egy idő után

### <a name="examples"></a>Példák

> [!NOTE]
> Minden API-válasz JSON-karakterláncok. Az összes következő példa válaszok elég nyomtatott olvashatóság.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

#### <a name="retrieving-public-ip-address"></a>Nyilvános IP-cím beolvasása

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Egy példány összes metaadatának beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Metaadatok beolvasása a Windows virtuális gépen

**Kérés**

A példány metaadatai a Windows `curl` rendszerben a programon keresztül olvashatók be:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmagon keresztül:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

A metaadat-végponton keresztül a következő API-k érhetők el:

Adatok | Leírás | Bevezetett verzió
-----|-------------|-----------------------
Igazolt | Lásd [igazolt adatok](#attested-data) | 2018-10-01
identity | Felügyelt identitások az Azure-erőforrásokhoz. Hozzáférési [jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
Példány | Lásd: [Példány API](#instance-api) | 2017-04-02
tervezett események | Lásd [ütemezett események](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Példány API

A következő számítási kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadat-végponton keresztül a következő kategóriák érhetők el a példány/számítási

Adatok | Leírás | Bevezetett verzió
-----|-------------|-----------------------
azKörnyezet | Az Azure-környezetben, ahol a virtuális gép fut | 2018-10-01
egyéni adatok | Ez a funkció jelenleg le van tiltva, és ezt a dokumentációt frissítjük, amint elérhetővé válik | 2019-02-01
location | Az Azure-régió, amelyben a virtuális gép fut | 2017-04-02
név | A virtuális gép neve | 2017-04-02
offer | A virtuális gép lemezképének ajánlata, és csak az Azure képgalériából telepített lemezképekhez érhető el | 2017-04-02
osType típus | Linux vagy Windows | 2017-04-02
elhelyezésCsoportazonosító | A virtuálisgép-méretezési csoport [elhelyezési](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) csoportja | 2017-08-01
Terv | [Tervezze](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) meg a virtuális gép nevét, termékét és közzétevőit, ha az egy Azure Marketplace-lemezkép | 2018-04-02
platformUpdateDomain |  [A virtuális gép](manage-availability.md) által futtatott tartomány frissítése | 2017-04-02
platformFaultDomain | [A](manage-availability.md) virtuális gép által futtatott tartalék tartomány | 2017-04-02
Szolgáltató | A virtuális gép szolgáltatója | 2018-10-01
nyilvános kulcsok | A virtuális [géphez](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) és elérési utakhoz rendelt nyilvános kulcsok gyűjteménye | 2018-04-02
közzétevő | A virtuális gép lemezképének közzétevője | 2017-04-02
resourceGroupName | [Erőforráscsoport](../../azure-resource-manager/management/overview.md) a virtuális géphez | 2017-08-01
resourceId | Az erőforrás [teljesen minősített](https://docs.microsoft.com/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
Sku | A virtuális gép rendszerképének adott termékváltozata | 2017-04-02
storageProfile (storageProfile) | Lásd: [Tárolási profil](#storage-profile) | 2019-06-01
subscriptionId | Azure-előfizetés a virtuális géphez | 2017-08-01
címkét | [Címkék](../../azure-resource-manager/management/tag-resources.md) a virtuális géphez  | 2017-08-01
címkékListája | Címkék formázott JSON tömb könnyebb programozott elemzés  | 2019-06-04
version | A virtuális gép lemezképének verziója | 2017-04-02
vmId | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | [Virtuálisgép-méretezési készlet A](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) virtuálisgép-méretezési csoport neve | 2017-12-01
vmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
zóna | A virtuális gép [rendelkezésre állási zónája](../../availability-zones/az-overview.md) | 2017-12-01

A következő hálózati kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadat-végponton keresztül a következő kategóriák érhetők el a példányon/hálózaton/felületen keresztül

Adatok | Leírás | Bevezetett verzió
-----|-------------|-----------------------
ipv4/privateIpAddress cím | A virtuális gép helyi IPv4-címe | 2017-04-02
ipv4/publicIpAddress cím | A virtuális gép nyilvános IPv4-címe | 2017-04-02
alhálózat/cím | A virtuális gép alhálózati címe | 2017-04-02
alhálózat/előtag | Alhálózati előtag, 24 példa | 2017-04-02
ipv6/ipAddress | A virtuális gép helyi IPv6-címe | 2017-04-02
macAddress | VM mac cím | 2017-04-02

## <a name="attested-data"></a>Igazolt adatok

A példánymetaadat-szolgáltatás által kiszolgált forgatókönyv része, hogy garantálja, hogy a megadott adatok az Azure-ból érkeznek. Ezen információk egy részét aláírjuk, hogy a marketplace-lemezképek biztosak lehessenek abban, hogy az ő lemezképük fut az Azure-ban.

### <a name="example-attested-data"></a>Példa igazolt adatokra

> [!NOTE]
> Minden API-válasz JSON-karakterláncok. A következő példa válaszok elég nyomtatott olvashatóság.

 **Kérés**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Az Api-verzió kötelező mező. A támogatott API-verziók a [szolgáltatás rendelkezésre állása című szakaszban](#service-availability) található.
A Nonce egy választható 10 jegyű karakterlánc. Ha nincs megadva, az IMDS az aktuális UTC-időbélyeget adja vissza a helyén. Az IMDS gyorsítótárazási mechanizmusa miatt egy korábban gyorsítótárazott nonce értéket adhat vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírásblob a dokumentum [pkcs7-es](https://aka.ms/pkcs7) aláírással ellátott verziója. Ez tartalmazza a virtuális gép részleteinek aláírásához használt tanúsítványt, például a vmId, sku, nonce, subscriptionId, timeStamp a dokumentum létrehozásához és lejártához, valamint a rendszerkép tervadatait. A csomaginformációk csak az Azure Market-helylemezképekhez vannak feltöltve. A tanúsítvány kinyerhető a válaszból, és a válasz érvényességének ellenőrzésére használható, és az Azure-ból érkezik.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Igazolt metaadatok beolvasása a Windows virtuális gépen

 **Kérés**

A példány metaadatai a Windows rendszerben a `curl`PowerShell segédprogramon keresztül olvashatók be:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Vagy a `Invoke-RestMethod` parancsmagon keresztül:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Az Api-verzió kötelező mező. A támogatott API-verziók a szolgáltatás rendelkezésre állása című szakaszban található.
A Nonce egy választható 10 jegyű karakterlánc. Ha nincs megadva, az IMDS az aktuális UTC-időbélyeget adja vissza a helyén. Az IMDS gyorsítótárazási mechanizmusa miatt egy korábban gyorsítótárazott nonce értéket adhat vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Az aláírásblob a dokumentum [pkcs7-es](https://aka.ms/pkcs7) aláírással ellátott verziója. Ez tartalmazza a virtuális gép részleteinek aláírásához használt tanúsítványt, például a vmId, sku, nonce, subscriptionId, timeStamp a dokumentum létrehozásához és lejártához, valamint a rendszerkép tervadatait. A csomaginformációk csak az Azure Market-helylemezképekhez vannak feltöltve. A tanúsítvány kinyerhető a válaszból, és a válasz érvényességének ellenőrzésére használható, és az Azure-ból érkezik.

## <a name="example-scenarios-for-usage"></a>Példák használati forgatókönyvekre  

### <a name="tracking-vm-running-on-azure"></a>Az Azure-ban futó virtuális gép nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökre, amelyeknek nyomon kell követniük a virtuális gép egyediségét. Ahhoz, hogy egy virtuális gép egyedi azonosítót `vmId` kapjon, használja a példánymetaadat-szolgáltatás mezőjét.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezése, adatpartíciókon alapuló hibák/frissítési tartomány

Bizonyos esetekben a különböző adatreplikák elhelyezése elsődleges fontosságú. Például [a HDFS replika elhelyezése](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy tároló elhelyezése `platformFaultDomain` `platformUpdateDomain` egy [orchestrator-on](https://kubernetes.io/docs/user-guide/node-selection/) keresztül szükség lehet tudni, hogy a és a virtuális gép fut.
A példányok hozhatja meg ezeket a döntéseket [rendelkezésre állási zónák](../../availability-zones/az-overview.md) is.
Ezeket az adatokat közvetlenül a példány metaadat-szolgáltatáson keresztül lehet lekérdezni.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Válasz**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információk beszerzése a virtuális gépről a támogatási eset során

Szolgáltatóként támogatási hívást kaphat, ahol további információkat szeretne tudni a virtuális gépről. Az ügyfél felkérése a számítási metaadatok megosztására alapvető információkat nyújthat a támogatási szakember számára, hogy tudjon az Azure-beli virtuális gépek típusáról.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

Az Azure különböző szuverén felhők, mint az [Azure Government.](https://azure.microsoft.com/overview/clouds/government/) Néha szükség van az Azure-környezetben, hogy néhány futásidejű döntéseket. Az alábbi minta bemutatja, hogyan érheti el ezt a viselkedést.

**Kérés**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Válasz**
```bash
AzurePublicCloud
```

A régiók és az Azure-környezet értékei az alábbiakban láthatók.

 Régiók | Azure környezet
---------|-----------------
[Minden általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china)          | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>A virtuális gép címkéinek beszerzése

Címkékelőfordulhat, hogy az Azure-beli virtuális gép logikailag rendszerezi őket egy taxonómia. A virtuális géphez rendelt címkék az alábbi kérelem melã-in keresztül lehívhatók.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Válasz**

```text
Department:IT;Environment:Test;Role:WebRole
```

A `tags` mező pontosvesszővel körülhatárolt címkékkel rendelkező karakterlánc. Ez akkor lehet probléma, ha pontosvesszőt használnak maguka címkék. Ha egy elemző íródott programozott kivonat a címkéket, `tagsList` akkor kell támaszkodnia a területen, amely egy JSON tömb nem határolók, és következésképpen, könnyebb elemezni.

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

A Marketplace-szállítók biztosítani szeretnék, hogy a szoftverük csak az Azure-ban fusson. Ha valaki másolja a virtuális merevlemezt a helyszíni, akkor képesnek kell lennie arra, hogy észlelje azt. A Példánymetaadat-szolgáltatás hívásával a Marketplace-szállítók olyan aláírt adatokat kaphatnak, amelyek csak az Azure-ból garantálják a választ.

> [!NOTE]
> A jq-t telepíteni kell.

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
nonce között | A felhasználó opcionális karakterláncot adott meg a kéréshez. Ha a kérelemben nem kapott nonce értéket, az aktuális UTC-időbélyeg et adja vissza a rendszer
Terv | [Tervezze meg](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) a virtuális gép egy Azure Marketplace-rendszer, tartalmazza a nevét, a termék és a kiadó
időbélyeg/létrehozvaOn | Az utc időbélyeg, amelyen az első aláírt dokumentum készült
időbélyeg/lejáratOn | Az aláírt dokumentum lejáratának UTC-időbélyegzője
vmId |  A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | A virtuális géphez szóló Azure-előfizetés, amelyet`2019-04-30`
Sku | A virtuális gép rendszerképének speciális termékváltozata, amelyet`2019-11-01`

#### <a name="verifying-the-signature"></a>Az aláírás ellenőrzése

Miután megkapta a fenti aláírást, ellenőrizheti, hogy az aláírás a Microsofttól származik-e. A köztes tanúsítványt és a tanúsítványláncot is ellenőrizheti. Végül ellenőrizheti, hogy az előfizetés-azonosító helyes-e.

> [!NOTE]
> A nyilvános felhő és a szuverén felhő tanúsítványa eltérő lesz.

 Felhő | Tanúsítvány
---------|-----------------
[Minden általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Ismert probléma van az aláíráshoz használt tanúsítvány körül. Előfordulhat, hogy a tanúsítványok `metadata.azure.com` nem rendelkeznek pontos egyezést a nyilvános felhőben. Ezért a hitelesítési hitelesítésnek lehetővé kell tennie egy közös nevet bármely `.metadata.azure.com` altartományból.

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

Azokban az esetekben, amikor a köztes tanúsítvány az érvényesítés során hálózati megkötések miatt nem tölthető le, a köztes tanúsítvány rögzíthető. Az Azure azonban a szabványos PKI-gyakorlatnak megfelelően átgörgeti a tanúsítványokat. A rögzített tanúsítványokat frissíteni kell, amikor a váltás történik. Amikor a köztes tanúsítvány frissítésének módosítása a tervek szerint történik, az Azure-blog frissül, és az Azure-ügyfelek értesítést kapnak. A köztes tanúsítványok [itt](https://www.microsoft.com/pki/mscorp/cps/default.htm)találhatók. Az egyes régiók köztes tanúsítványai eltérőek lehetnek.

> [!NOTE]
> Az Azure China 21Vianet köztes tanúsítványa baltimore helyett a DigiCert globális gyökérhitelesítési szolgáltatótól származik.
Akkor is, ha az Azure China köztes tanúsítványok at rögzítette a gyökérlánc-jogosultság módosítása részeként, a köztes tanúsítványokat frissíteni kell.

### <a name="failover-clustering-in-windows-server"></a>Feladatátvevő fürtözés a Windows Server rendszerben

Bizonyos esetekben a példány metaadat-szolgáltatás feladatátvevő fürtözéssel történő lekérdezésekénekéneknél hozzá kell adni egy útvonalat az útválasztási táblához.

1. Nyissa meg a parancssort rendszergazdai jogosultságokkal.

2. Futtassa a következő parancsot, és jegyezze fel a hálózati célkapcsolat (`0.0.0.0`) címét az IPv4-útvonaltáblában.

```bat
route print
```

> [!NOTE]
> A következő példa kimenete egy Windows Server virtuális gép feladatátvételi fürt engedélyezve csak az IPv4-útvonaltábla az egyszerűség kedvéért.

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

1. Futtassa a következő parancsot, és használja`0.0.0.0`a hálózati`10.0.1.10`célkapcsolat ( ) címét, amely ebben a példában ( ) .

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Tárolóprofil

Példány metaadat-szolgáltatás a virtuális géphez társított tárolólemezek részleteit. Ezek az adatok megtalálhatók a példány/számítási/storageProfile végpont.

A virtuális gép tárolási profilja három kategóriába sorolható : lemezkép, operációs rendszer lemeze és adatlemezek.

A képreferencia-objektum a következő információkat tartalmazza az operációs rendszer lemezképéről:

Adatok    | Leírás
--------|-----------------
id      | Erőforrás-azonosító
offer   | A platform vagy a piactér imázsának ajánlata
közzétevő | Kép közzétevője
Sku     | Kép sku
version | A platform vagy a piactér lemezképének verziója

Az operációs rendszer lemezobjektuma a következő információkat tartalmazza a virtuális gép által használt operációsrendszer-lemezről:

Adatok    | Leírás
--------|-----------------
Gyorsítótár | Gyorsítótárazási követelmények
createOption | A virtuális gép létrehozásának módjáról szóló információ
diffDiskSettings | Rövid élettartamú lemezbeállítások
lemezméret | A lemez mérete GB-ban
image   | Virtuális forrásfelhasználói kép
Lun     | A lemez logikai egységszáma
managedDisk | Felügyelt lemez paraméterei
név    | Lemez neve
Vhd     | Virtuális merevlemez
writeAcceleratorEnabled | A writeAccelerator engedélyezve van-e a lemezen

Az adatlemezek tömb tartalmazza a virtuális géphez csatolt adatlemezek listáját. Minden adatlemez-objektum a következő információkat tartalmazza:

Adatok    | Leírás
--------|-----------------
Gyorsítótár | Gyorsítótárazási követelmények
createOption | A virtuális gép létrehozásának módjáról szóló információ
diffDiskSettings | Rövid élettartamú lemezbeállítások
lemezméret | A lemez mérete GB-ban
encryptionSettings (titkosítási beállítások) | A lemez titkosítási beállításai
image   | Virtuális forrásfelhasználói kép
managedDisk | Felügyelt lemez paraméterei
név    | Lemez neve
osType típus  | A lemezen található operációs rendszer típusa
Vhd     | Virtuális merevlemez
writeAcceleratorEnabled | A writeAccelerator engedélyezve van-e a lemezen

Az alábbi példa bemutatja, hogyan lehet lekérdezni a virtuális gép tárolási adatait.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. A következő példa válasz elég nyomtatott olvashatóság.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Példák a metaadat-szolgáltatás különböző nyelvek en belüli hívására 

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

## <a name="faq"></a>GYIK

1. Kapok a hiba `400 Bad Request, Required metadata header not specified`. Ez mit jelent?
   * A példány metaadat-szolgáltatás `Metadata: true` megköveteli, hogy a fejléc et át kell adni a kérelemben. A fejléc átadása a REST-hívásban hozzáférést biztosít a példány metaadat-szolgáltatásához.
2. Miért nem kapok számítási adatokat a virtuális gépemhez?
   * Jelenleg a példány metaadat-szolgáltatás csak az Azure Resource Manager rel létrehozott példányokat támogatja. A jövőben előfordulhat, hogy a felhőszolgáltatás virtuális gépei támogatják.
3. Nemrég az Azure Resource Manager en keresztül hoztam létre a virtuális gépet. Miért nem jelennek meg a számítási metaadat-adatok?
   * A 2016 szeptembere után létrehozott virtuális gépek hez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megtekintéséhez. Régebbi virtuális gépek (2016 szeptembere előtt létrehozott) hozzáadása és eltávolítása bővítmények vagy adatlemezek a virtuális gép metaadatok frissítéséhez.
4. Nem látom az összes új verzióhoz kitöltött adatot
   * A 2016 szeptembere után létrehozott virtuális gépek hez adjon hozzá egy [címkét](../../azure-resource-manager/management/tag-resources.md) a számítási metaadatok megtekintéséhez. Régebbi virtuális gépek (2016 szeptembere előtt létrehozott) hozzáadása és eltávolítása bővítmények vagy adatlemezek a virtuális gép metaadatok frissítéséhez.
5. Miért kapok a hibát? `500 Internal Server Error`
   * Próbálja meg újra a kérelmet exponenciális biztonsági kimaradási rendszer alapján. Ha a probléma továbbra is fennáll, forduljon az Azure támogatási szolgálatához.
6. Hol oszthatok meg további kérdéseket/megjegyzéseket?
   * Küldje el https://feedback.azure.comészrevételeit .
7. Ez a virtuális gép méretezése set példány?
   * Igen metaadat-szolgáltatás érhető el méretezési példányok.
8. Hogyan kaphatok támogatást a szolgáltatáshoz?
   * A szolgáltatás támogatásának beszerezni, hozzon létre egy támogatási problémát az Azure Portalon a virtuális gép, ahol nem tudja, hogy a metaadatok válasz hosszú újrapróbálkozások után.
9. Kapok kérés időkinek a hívást a szolgáltatás?
   * A metaadat-hívásokat a virtuális gép elsődleges hálózati kártyájához rendelt elsődleges IP-címről kell kezdeményezni, továbbá abban az esetben, ha megváltoztatta az útvonalakat, a hálózati kártyán kívül a 169.254.0.0/16-os címre kell útvonalat választani.
10. Frissítettem a címkéket a virtuálisgép-méretezési csoportban, de nem jelennek meg a virtuális gépektől eltérő esetekben?
    * ScaleSets címkék jelenleg csak azt mutatják, hogy a virtuális gép egy újraindítás/reimage/vagy a lemez módosítása a példány.

    ![Példány metaadat-támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>További lépések

- További információ [az ütemezett eseményekről](scheduled-events.md)
