---
title: Azure-Instance Metadata Service | Microsoft Docs
description: REST-felület a Windows rendszerű virtuális gépek számítási, hálózati és közelgő karbantartási eseményeivel kapcsolatos információk lekéréséhez.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 658830e37a453075100cd3aaf132bb1d3aedfaea
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240393"
---
# <a name="azure-instance-metadata-service"></a>Azure-példány metaadatainak szolgáltatása

Az Azure Instance Metadata Service információt nyújt a virtuális gépek felügyeletéhez és konfigurálásához használható virtuálisgép-példányok futtatásáról.
Ez olyan információkat tartalmaz, mint például az SKU, a hálózati konfiguráció és a közelgő karbantartási események. További információ arról, hogy milyen típusú információk érhetők el: [metaadat-API](#metadata-apis)-k.

Az Azure Instance Metadata Service egy REST-végpont, amely a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)használatával létrehozott összes IaaS virtuális gép számára elérhető.
A végpont egy jól ismert, nem irányítható IP-címen (`169.254.169.254`) érhető el, amelyet csak a virtuális gépről lehet elérni.

> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** az összes Azure-régióban.  Rendszeresen frissítéseket kap a virtuálisgép-példányokkal kapcsolatos új információk megjelenítéséhez. Ez az oldal az elérhető naprakész [metaadat-API-kat](#metadata-apis) mutatja.

## <a name="service-availability"></a>Elérhető szolgáltatások

A szolgáltatás az általánosan elérhető Azure-régiókban érhető el. Nem minden API-verzió érhető el az összes Azure-régióban.

Regions                                        | Rendelkezésre állási?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure China](https://www.azure.cn/)                                                     | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Ez a tábla akkor frissül, amikor szolgáltatási frissítések vannak, vagy új támogatott verziók érhetők el.

A Instance Metadata Service kipróbálásához hozzon létre egy virtuális gépet [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [Azure Portal](https://portal.azure.com) a fenti régiókban, és kövesse az alábbi példákat.

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

Amikor lekérdezi a instance metadata Service, meg kell adnia a `Metadata: true` fejlécet annak biztosítására, hogy a kérést nem szándékosan átirányítsák.

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
/instance | JSON | text
/scheduledevents | JSON | nincs
/attested | JSON | nincs

A nem alapértelmezett válasz formátumának eléréséhez a kérelemben a kért formátumot lekérdezési karakterlánc paraméterként kell megadni. Példa:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> A `format=json` nem működik a levél csomópontjain. A lekérdezéseket `format=text` explicit módon meg kell adni, ha az alapértelmezett formátum a JSON.

### <a name="security"></a>Biztonság

Az Instance Metadata Service végpont csak a futó virtuálisgép-példányból érhető el egy nem irányítható IP-címen. Emellett a szolgáltatás elutasítja a `X-Forwarded-For` fejléctel ellátott kérelmeket.
A kérelmeknek tartalmaznia kell `Metadata: true` egy fejlécet is, amely biztosítja, hogy a tényleges kérelem közvetlenül a véletlen átirányítás része legyen.

### <a name="error"></a>Hiba

Ha nem található adatelem vagy helytelenül formázott kérelem, a Instance Metadata Service szabványos HTTP-hibákat ad vissza. Példa:

HTTP-állapotkód | Reason
----------------|-------
200 OK |
400 hibás kérelem | Hiányzik `Metadata: true` a fejléc, vagy hiányzik a formátum a levél csomópontjainak lekérdezése során
404 Nem található | A kért elem nem létezik
405 metódus nem engedélyezett | Csak `GET` a `POST` és a kérelmek támogatottak
429 túl sok kérés | Az API jelenleg legfeljebb 5 lekérdezést támogat másodpercenként
500 Service Error     | Némi idő elteltével próbálkozzon újra

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
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
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

A Windows-példány metaadatai a `curl` program segítségével kérhetők le:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmagon keresztül:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
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

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>A metaadatok végpontján a következő API-k érhetők el:

Data | Leírás | Verzió bevezetése
-----|-------------|-----------------------
igazolt | [Igazolt](#attested-data) információ | 2018-10-01
identity | Felügyelt identitások az Azure-erőforrásokhoz. Lásd: [hozzáférési jogkivonat](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) beszerzése | 2018-02-01
instance | Lásd: [példány API](#instance-api) | 2017-04-02
scheduledevents | Lásd: [Scheduled Events](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Példány API
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>A következő számítási kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadat-végponton keresztül a következő kategóriák érhetők el példányon/számításon keresztül.

Data | Leírás | Verzió bevezetése
-----|-------------|-----------------------
azEnvironment | Az Azure-környezet, amelyben a virtuális gép fut | 2018-10-01
customData | [Egyéni](#custom-data) információ | 2019-02-01
location | Az Azure-régió, amelyen a virtuális gép fut | 2017-04-02
name | A virtuális gép neve | 2017-04-02
ajánlat | A virtuálisgép-lemezképre vonatkozó információkat nyújtja, és csak az Azure rendszerkép-katalógusból üzembe helyezett rendszerképekhez érhető el | 2017-04-02
osType | Linux vagy Windows | 2017-04-02
placementGroupId | A virtuálisgép-méretezési [csoport elhelyezési csoportja](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
csomag | Egy virtuális gép nevét, termékét és közzétevőjét tartalmazó [csomag](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) megtervezése, ha az Azure Marketplace-rendszerkép | 2018-04-02
platformUpdateDomain |  A virtuális gépet futtató [tartomány frissítése](manage-availability.md) | 2017-04-02
platformFaultDomain | A virtuális gép által futtatott tartalék [tartomány](manage-availability.md) | 2017-04-02
szolgáltató | A virtuális gép szolgáltatója | 2018-10-01
publicKeys | A virtuális géphez és elérési utakhoz rendelt [nyilvános kulcsok gyűjteménye](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | A virtuális gép rendszerképének közzétevője | 2017-04-02
resourceGroupName | A virtuális géphez tartozó [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
resourceId | Az erőforrás [teljes](https://docs.microsoft.com/rest/api/resources/resources/getbyid) azonosítója | 2019-03-11
sku | A virtuális gép rendszerképének adott SKU-jának | 2017-04-02
subscriptionId | Azure-előfizetés a virtuális géphez | 2017-08-01
címkék | [](../../azure-resource-manager/resource-group-using-tags.md) A virtuális gép címkéi  | 2017-08-01
tagsList | Az egyszerűbb programozási elemzéshez JSON-tömbként formázott Címkék  | 2019-06-04
version | A VM-rendszerkép verziója | 2017-04-02
vmId | A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | A virtuálisgép-méretezési csoport [virtuális gép méretezési csoport neve](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
zóna | A virtuális gép [rendelkezésre állási zónája](../../availability-zones/az-overview.md) | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>A következő hálózati kategóriák érhetők el a példány API-n keresztül:

> [!NOTE]
> A metaadatok végpontján a következő kategóriák érhetők el a instance/Network/Interface használatával

Data | Leírás | Verzió bevezetése
-----|-------------|-----------------------
ipv4/privateIpAddress | A virtuális gép helyi IPv4-címe | 2017-04-02
ipv4/publicIpAddress | A virtuális gép nyilvános IPv4-címe | 2017-04-02
subnet/address | A virtuális gép alhálózati címe | 2017-04-02
alhálózat/előtag | Alhálózat előtagja, 24. példa | 2017-04-02
ipv6/ipAddress | A virtuális gép helyi IPv6-címe | 2017-04-02
macAddress | VM MAC-címe | 2017-04-02

## <a name="attested-data"></a>Igazolt adatértékek

A példány metaadatai a http-végponton válaszolnak a 169.254.169.254. A Instance Metadata Service által kiszolgált forgatókönyv része a garancia arra, hogy az adatok az Azure-ból érkeznek. Aláírjuk ezen információk egy részét, hogy a piactér lemezképei biztosak legyenek abban, hogy az Azure-ban futnak.

### <a name="example-attested-data"></a>Hitelesítő adatként példa

> [!NOTE]
> Minden API-válasz JSON-karakterlánc. Az alábbi példákban az olvashatóság érdekében a következő válaszokat kell kinyomtatni.

 **Kérés**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Az API-Version kötelező mező. A támogatott API-verziókhoz tekintse meg a [szolgáltatás elérhetőségét ismertető szakaszt](#service-availability) .
Az alkalom egy opcionális, 10 számjegyű karakterláncot is biztosít. A kérelem nyomon követésére használható, és ha nincs megadva, a válasz kódolású sztringben a rendszer az aktuális UTC-időbélyeget adja vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Az aláírási blob a dokumentum [PKCS7](https://aka.ms/pkcs7) aláírt verziója. Tartalmazza az aláíráshoz használt tanúsítványt, valamint a virtuális gép részleteit, például a vmId, az alkalom, a subscriptionId, az időbélyegzőt a dokumentum létrehozásához és lejáratához, valamint a rendszerkép információinak megtervezéséhez. A csomag adatai csak az Azure Market Place-lemezképek esetében tölthetők fel. A tanúsítvány kinyerhető a válaszból, és annak ellenőrzésére szolgál, hogy a válasz érvényes-e, és az Azure-ból származik-e.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Igazolt metaadatok beolvasása a Windows rendszerű virtuális gépen

 **Kérés**

A példány metaadatainak a Windows PowerShell segédprogram `curl`használatával kérhetők le:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Vagy a `Invoke-RestMethod` parancsmagon keresztül:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Az API-Version kötelező mező. A támogatott API-verziókhoz tekintse meg a szolgáltatás elérhetőségét ismertető szakaszt.
Az alkalom egy opcionális, 10 számjegyű karakterláncot is biztosít. A kérelem nyomon követésére használható, és ha nincs megadva, a válasz kódolású sztringben a rendszer az aktuális UTC-időbélyeget adja vissza.

 **Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Az aláírási blob a dokumentum [PKCS7](https://aka.ms/pkcs7) aláírt verziója. Tartalmazza az aláíráshoz használt tanúsítványt, valamint a virtuális gép részleteit, például a vmId, az alkalom, a subscriptionId, az időbélyegzőt a dokumentum létrehozásához és lejáratához, valamint a rendszerkép információinak megtervezéséhez. A csomag adatai csak az Azure Market Place-lemezképek esetében tölthetők fel. A tanúsítvány kinyerhető a válaszból, és annak ellenőrzésére szolgál, hogy a válasz érvényes-e, és az Azure-ból származik-e.


## <a name="example-scenarios-for-usage"></a>Használati példák a használathoz  

### <a name="tracking-vm-running-on-azure"></a>Az Azure-ban futó virtuális gép nyomon követése

Szolgáltatóként szükség lehet a szoftvert futtató virtuális gépek számának nyomon követésére, vagy olyan ügynökökkel, amelyeknek nyomon kell követniük a virtuális gép egyediségét. A virtuális gép egyedi azonosítójának beszerzéséhez használja a `vmId` mezőt instance metadata Service.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezése, adatpartíciókon alapuló hibák/frissítési tartomány 

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

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információk beszerzése a virtuális gépről a támogatási eset során

Szolgáltatóként olyan támogatási hívást kaphat, amelyben további információkat szeretne megtudni a virtuális gépről. Ha arra kéri az ügyfelet, hogy ossza meg a számítási metaadatokat, alapvető információkat biztosíthat a támogatási szakember számára az Azure-beli virtuális gépekről. 

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Válasz**

> [!NOTE]
> A válasz egy JSON-karakterlánc. Az alábbi példában szereplő válasz elég kinyomtatva az olvashatóság érdekében.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
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

 Regions | Azure-környezet
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China](https://azure.microsoft.com/global-infrastructure/china)                   | AzureChinaCloud
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

A `tags` mező egy olyan karakterlánc, amelynek a címkéi pontosvesszővel vannak elválasztva. Ez akkor lehet probléma, ha a címkékben pontosvesszőt használnak. Ha egy elemzőt úgy kell megírni, hogy programozott módon kibontsa a címkéket, olyan `tagsList` mezőre kell támaszkodnia, amely egy határolójel nélküli JSON-tömb, és így könnyebben elemezhető.

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

A piactér-szállítók biztosítani szeretnék, hogy a szoftverük csak az Azure-ban fusson. Ha valaki átmásolja a VHD-t a helyszíni rendszerbe, akkor képesnek kell lennie a felderítésére. A Instance Metadata Serviceba való behívásával a piactér-szállítók olyan aláírt adatokhoz juthatnak, amelyek csak az Azure-ból választanak.

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
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Data | Leírás
-----|------------
nonce | A felhasználó nem kötelező karakterláncot adott meg a kérelemmel. Ha a kérelemben nem adtak meg egy adott időpontot, a rendszer az aktuális UTC-időbélyeget adja vissza
csomag | [Tervezze](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) meg, hogy a virtuális gép egy Azure Marketplace-rendszerkép, amely tartalmazza a nevet, a terméket és a közzétevőt
timestamp/createdOn | Az első aláírt dokumentum létrehozásának időbélyege
időbélyeg/expiresOn | Az aláírt dokumentum érvényességi időbélyegzője
vmId |  A virtuális gép [egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | Azure-előfizetés a virtuális géphez, amely a következő címen található:`2019-04-30`

#### <a name="verifying-the-signature"></a>Az aláírás ellenőrzése

Miután megszerezte a fenti aláírást, ellenőrizheti, hogy az aláírás a Microsofttól származik-e. Emellett ellenőrizheti a köztes tanúsítványt és a tanúsítványláncot is. Végül ellenőrizheti, hogy helyes-e az előfizetés-azonosító.

> [!NOTE]
> A nyilvános felhő és a szuverén felhő tanúsítványa eltérő lesz.

 Felhő | Tanúsítvány
---------|-----------------
[Az összes általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
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

2. Futtassa az alábbi parancsot, és jegyezze fel az IPv4-útválasztási táblázatban található hálózati`0.0.0.0`célhely () adapterének a címeit.

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

1. Futtassa a következő parancsot, és használja a Hálózati célhely (`0.0.0.0`) interfészének címe (), amely ebben a példában található (`10.0.1.10`).

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Egyéni adatértékek
A Instance Metadata Service lehetővé teszi, hogy a virtuális gép hozzáférhessen az egyéni adatszolgáltatásokhoz. A bináris adatmennyiségnek 64 KB-nál kisebbnek kell lennie, és a virtuális gép Base64 kódolású formában van megadva.

Az egyéni Azure-adatok a REST API-k, a PowerShell-parancsmagok, az Azure parancssori felület (CLI) vagy egy ARM-sablon segítségével illeszthetők be a virtuális gépre.

Az Azure parancssori felületén példaként tekintse [meg az egyéni és a Cloud-Init Microsoft Azureon](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/)című témakört.

Egy ARM-sablonra példa: [virtuális gép telepítése a CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Az egyéni adatértékek a virtuális gépen futó összes folyamat számára elérhetők. Azt javasoljuk, hogy az ügyfelek ne helyezzen be titkos adatokat egyéni adatokba.

Jelenleg az egyéni adatmennyiség garantáltan elérhető a virtuális gép rendszerindításakor. Ha a virtuális gép frissítéseit, például lemezek hozzáadását vagy a virtuális gép átméretezését végzi, Instance Metadata Service nem biztosít egyéni adattárolást. Az egyéni adatértékek folyamatos biztosítása a Instance Metadata Serviceon keresztül jelenleg folyamatban van.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Egyéni adatok beolvasása a virtuális gépen
A Instance Metadata Service Base64 kódolású formában biztosít egyéni adattípusokat a virtuális géphez. A következő példa dekódolja a Base64 kódolású karakterláncot.

> [!NOTE]
> Az ebben a példában szereplő egyéni adatokat ASCII-karakterláncként értelmezi a rendszer, amely "saját egyéni adatokat" olvas be.

**Kérés**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Válasz**

```text
My custom data.
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

## <a name="faq"></a>GYIK

1. Hibaüzenetet `400 Bad Request, Required metadata header not specified`kapok. Ez mit jelent?
   * A instance metadata Service megköveteli a fejléc `Metadata: true` átadását a kérelemben. Ha ezt a fejlécet a REST-hívásban adja át, akkor a Instance Metadata Servicehoz való hozzáférést is lehetővé teszi.
2. Miért nem kapok számítási információt a virtuális géphez?
   * A Instance Metadata Service jelenleg csak Azure Resource Manager által létrehozott példányokat támogatja. A jövőben a Cloud Service virtuális gépek támogatása is felvehető.
3. Létrehoztam a virtuális gépet Azure Resource Manager egy kicsit vissza. Miért nem jelenik meg a számítási metaadatok adatai?
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá [](../../azure-resource-manager/resource-group-using-tags.md) egy címkét a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuális géphez.
4. Nem látom az új verzióhoz feltöltött összes adatot
   * A Sep 2016 után létrehozott virtuális gépekhez adjon hozzá [](../../azure-resource-manager/resource-group-using-tags.md) egy címkét a számítási metaadatok megjelenítéséhez. Régebbi virtuális gépek esetében (a Sep 2016 előtt létrehozva) a metaadatok frissítéséhez adjon hozzá vagy távolítson el bővítményeket vagy adatlemezeket a virtuális géphez.
5. Miért kapok hibaüzenetet `500 Internal Server Error`?
   * Próbálja megismételni a kérést az exponenciális visszalépési rendszer alapján. Ha a probléma továbbra is fennáll, forduljon az Azure ügyfélszolgálatához.
6. Hol oszthatok meg további kérdéseket/megjegyzéseket?
   * Küldje el megjegyzéseit https://feedback.azure.com.
7. Működne a virtuálisgép-méretezési csoport példányai?
   * Az igen metaadat-szolgáltatás elérhető a méretezési csoport példányaihoz.
8. Hogyan kap támogatást a szolgáltatáshoz?
   * A szolgáltatás támogatásának megszerzéséhez hozzon létre egy támogatási problémát a virtuális gép Azure Portaljában, ahol a hosszú újrapróbálkozás után nem tud metaadatokat kapni.
9. Időtúllépést kapok a kérésem a szolgáltatáshoz való meghívásakor?
   * A metaadatokat a virtuális gép hálózati kártyához rendelt elsődleges IP-címről kell megadnia, továbbá arra az esetre, ha módosította az útvonalakat, a hálózati kártyán kívüli 169.254.0.0/16 cím elérési útvonalát is el kell végezni.
10. Frissítettem a címkéket a virtuálisgép-méretezési csoportokban, de nem jelennek meg a példányokban a virtuális gépektől eltérően?
    * A ScaleSets-címkék esetében jelenleg csak a virtuális gép jelenik meg újraindítás/rendszerkép/lemezkép/vagy a példány lemezre váltásakor.

    ![Példány metaadatainak támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>További lépések

- További információ a [Scheduled Events](scheduled-events.md)
