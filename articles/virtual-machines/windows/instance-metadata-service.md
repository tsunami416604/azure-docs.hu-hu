---
title: Az Azure Instance Metadata szolgáltatás |} A Microsoft Docs
description: RESTful interfészről Windows virtuális gép számítási, hálózati és események a közelgő karbantartásokról információt szeretne kapni.
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: a3db99de2fffa19a98fa306477b30fdccf2290ec
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733161"
---
# <a name="azure-instance-metadata-service"></a>Az Azure Instance Metadata szolgáltatás


Az Azure Instance Metadata szolgáltatás fut, amelyek segítségével kezelheti és konfigurálhatja a virtuális gépek virtuálisgép-példányok információkat biztosít.
Ide tartoznak a Termékváltozat, a hálózati konfiguráció vagy az események a közelgő karbantartásokról. Milyen típusú információkat érhető el a további információkért lásd: [metaadat-kategóriák](#instance-metadata-data-categories).

Az Azure Instance Metadata szolgáltatás REST-végponton keresztül létrehozott IaaS virtuális gépek számára elérhető a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). A végpont egy jól ismert nem átirányítható IP-címen érhető el (`169.254.169.254`), amelyek elérhetők csak a virtuális gépen.

> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** az összes Azure-régióban.  Rendszeresen megkapja az új információ a virtuálisgép-példányok elérhetővé a frissítéseket. Ezen a lapon tükrözi a legfrissebb [adatkategóriák](#instance-metadata-data-categories) érhető el.

## <a name="service-availability"></a>Elérhető szolgáltatások
A szolgáltatás általánosan elérhető Azure-régióban érhető el. Lehet, hogy nem minden API-verzió az összes Azure-régióban érhető el.

Régiók                                        | Rendelkezésre állási?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Az összes általánosan elérhető globális Azure-régiók](https://azure.microsoft.com/regions/)     | Mindenki számára elérhető   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure China](https://www.azure.cn/)                                                           | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Mindenki számára elérhető | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01

Ez a táblázat frissül, ha nincsenek a szolgáltatásfrissítések, és vagy új támogatott verziók érhetők el

Próbálja ki a Instance Metadata szolgáltatás, hozzon létre egy virtuális gépről [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [az Azure portal](http://portal.azure.com) az újabb régiókban, és kövesse az alábbi példák.

## <a name="usage"></a>Használat

### <a name="versioning"></a>Verziókezelés
A Instance Metadata szolgáltatás verziószámmal. Verziók a következők kötelező, és a globális Azure-ban a jelenlegi verzió `2018-04-02`. Aktuális verziók a következők (2017-04-02, 2018-04-02 2018-02-01, 2017-12-01, 2017-08-01)

> [!NOTE] 
> {Legújabb} api-verzió is támogatott, az ütemezett események korábbi előzetes kiadásokat. Ez a formátum már nem támogatott, és később elavulttá válik.

Növelése újabb verziók hozzáadásakor, régebbi verzióit továbbra is elérhető kompatibilitás, ha a parancsfájlok adott adatformátumok a célnyelven függőségekkel rendelkezik. Előfordulhat azonban, a korábbi előzetes verzió (2017-03-01) nem érhető el a szolgáltatás általánosan elérhetővé válik.

### <a name="using-headers"></a>Fejlécek használata
Előfordulhat, hogy a Instance Metadata szolgáltatás, meg kell adnia a fejléc `Metadata: true` annak érdekében, hogy a kérés nem volt szándékos átirányítva.

### <a name="retrieving-metadata"></a>Metaadatok beolvasása

Példány-metaadat érhető el a futó virtuális gépek létrehozása és kezelése az alábbi [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Hozzáférés az összes adatkategóriákat a virtuálisgép-példányt használja a következő kérelmet:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Az összes példány metaadatok lekérdezés-és nagybetűk.

### <a name="data-output"></a>Kimeneti adatok
Alapértelmezés szerint a Instance Metadata szolgáltatás adatokat ad vissza JSON formátumban (`Content-Type: application/json`). Azonban más API-k adja vissza, adatok különböző formátumokban.
Az alábbi táblázat a más API-kkal támogathatják adatformátumok a célnyelven eszköztáblára.

API | Alapértelmezett adatformátum | További formátumok
--------|---------------------|--------------
/instance | JSON | szöveg
/scheduledevents | JSON | nincs

Egy nem alapértelmezett válaszformátum eléréséhez, a kérelem lekérdezési karakterlánc paraméterként adja meg a kért formátumát. Példa:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Biztonság
Instance Metadata szolgáltatás végpont elérésére csak a futó virtuálisgép-példány nem átirányítható IP-címen belül. Emellett a kérelmet egy `X-Forwarded-For` fejléc elutasította a szolgáltatást.
Kérelmek is tartalmaznia kell egy `Metadata: true` fejléc, győződjön meg arról, hogy a tényleges kérelmező közvetlenül: tervezett és nem véletlen átirányítás része. 

### <a name="error"></a>Hiba
Ha nem található egy adatelemre, vagy hibás kérés, a Instance Metadata szolgáltatás standard HTTP-hibák adja vissza. Példa:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 Hibás kérés | Hiányzó `Metadata: true` fejléc
404 – Nem található | A kért elem nem létezik. 
405 Metoda není Povolena | Csak `GET` és `POST` kérelmek támogatottak.
429 túl sok kérelem | Az API jelenleg legfeljebb 5 lekérdezések másodpercenként
500 Service Error     | Némi várakozás után próbálkozzon újra

### <a name="examples"></a>Példák

> [!NOTE] 
> Minden API-válasz JSON-sztringek. Az összes, a következő példa válaszok olyan pretty nyomtatott az olvashatóság érdekében.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

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

#### <a name="retrieving-all-metadata-for-an-instance"></a>Minden metaadat-példány beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows virtuális gép metaadatainak beolvasása

**Kérés**

Példány-metaadat beolvasható a Windows-n keresztül a `curl` program: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmagot:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
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
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Példány-metaadat adatkategóriák
Az alábbi adatkategóriákat a Instance Metadata szolgáltatás keresztül érhetők el:

Adatok | Leírás | Bevezetett verzió 
-----|-------------|-----------------------
location | Azure-régió, a virtuális gép fut. | 2017-04-02 
név | A virtuális gép neve | 2017-04-02
az ajánlat | A Virtuálisgép-lemezkép információkat kínálnak. Ez az érték csak megtalálható az Azure lemezkép-katalógus-ről üzembe helyezett rendszerképeket. | 2017-04-02
publisher | A Virtuálisgép-lemezkép közzétevője | 2017-04-02
termékváltozat | Adott Termékváltozat a Virtuálisgép-lemezkép | 2017-04-02
version | A Virtuálisgép-lemezkép verziója | 2017-04-02
osType | Linux vagy Windows | 2017-04-02
platformUpdateDomain |  [Frissítési tartomány](manage-availability.md) a virtuális gép fut. | 2017-04-02
platformFaultDomain | [Tartalék tartomány](manage-availability.md) a virtuális gép fut. | 2017-04-02
vmId | [Egyedi azonosító](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) a virtuális gép | 2017-04-02
vmSize | [Virtuális gép mérete](sizes.md) | 2017-04-02
subscriptionId | A virtuális gép Azure-előfizetés | 2017-08-01
tags | [A címkék](../../azure-resource-manager/resource-group-using-tags.md) a virtuális gép  | 2017-08-01
resourceGroupName | [Erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) a virtuális gép | 2017-08-01
placementGroupId | [Elhelyezési csoport](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) a virtuálisgép-méretezési csoport beállítása | 2017-08-01
csomag | [Csomag](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) azt a virtuális gép van egy Azure Marketplace-beli rendszerképét, tartalmazza a neve, a termékek és a közzétevő | 2018-04-02
publicKeys | Nyilvános kulcsok gyűjteményét [https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey] rendelve a virtuális gép és az elérési út | 2018-04-02
vmScaleSetName | [Virtuálisgép-méretezési csoport neve](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) a virtuálisgép-méretezési csoport beállítása | 2017-12-01
zóna | [Rendelkezésre állási zónában](../../availability-zones/az-overview.md) a virtuális gép | 2017-12-01 
ipv4/privateIpAddress | A virtuális gép helyi IPv4-cím | 2017-04-02
ipv4/publicIpAddress | A virtuális gép nyilvános IPv4-cím | 2017-04-02
subnet/address | Alhálózati cím a virtuális gép | 2017-04-02 
alhálózat/előtag | Alhálózati előtag, például 24 | 2017-04-02 
ipv6/ipAddress | A virtuális gép helyi IPv6-cím | 2017-04-02 
macAddress | Virtuális gép mac-cím | 2017-04-02 
scheduledevents | Lásd: [ütemezett események](scheduled-events.md) | 2017-08-01
identity | (Előzetes verzió) Felügyelt identitások az Azure-erőforrásokhoz. Lásd: [hozzáférési jogkivonat beszerzése](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01

## <a name="example-scenarios-for-usage"></a>Példa használati forgatókönyvek  

### <a name="tracking-vm-running-on-azure"></a>Az Azure-ban futó virtuális gép nyomon követése

Szolgáltatóként nyomon követheti a szoftvereket futtató virtuális gépek számát, illetve szeretne nyomon követni a virtuális gép egyedisége ügynökök lehet szükség. Hogy egyedi azonosító beszerzése egy virtuális géphez, használja a `vmId` Instance Metadata szolgáltatás mezőt.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezését adatpartíciókra alapú tartalék és frissítési tartomány 

Az egyes forgatókönyvek, különböző adatok replikáit elhelyezésére van elsődleges fontosságú. Például [HDFS replika elhelyezésének](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy keresztül tároló elhelyezése egy [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) előfordulhat, hogy szükséges tudni a `platformFaultDomain` és `platformUpdateDomain` a virtuális gép fut-e.
Is [rendelkezésre állási zónák](../../availability-zones/az-overview.md) a példányok ezen döntések.
Ezeket az adatokat közvetlenül a Instance Metadata szolgáltatás használatával kérdezheti le.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Válasz**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információ a virtuális gép során támogatási esetet beolvasása 

Szolgáltatóként előfordulhat, hogy kap egy támogatási hívás hol szeretné tudni, hogy a virtuális gép további információt. A számítási metaadatok megosztani az ügyfél kéri alapvető tudnivalók az Azure-beli virtuális gép milyen professzionális támogatási információkat nyújtanak. 

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterlánc. A következő példa választ, Igen nyomtatott az olvashatóság érdekében.

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


### <a name="getting-azure-environment-where-the-vm-is-running"></a>Bevezetés az Azure-környezetben, ahol a virtuális gép fut. 

Az Azure rendelkezik a különböző szuverén felhők például [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Néha szüksége a Azure-környezet néhány futásidejű döntéseket hozhat. A következő minta bemutatja, hogyan lehet ezt elérni.

**Kérés**

```
  $metadataResponse = Invoke-WebRequest "http://169.254.169.254/metadata/instance/compute?api-version=2018-02-01" -H @{"Metadata"="true"} -UseBasicParsing
  $metadata = ConvertFrom-Json ($metadataResponse.Content)
 
  $endpointsResponse = Invoke-WebRequest "https://management.azure.com/metadata/endpoints?api-version=2017-12-01" -UseBasicParsing
  $endpoints = ConvertFrom-Json ($endpointsResponse.Content)
 
  foreach ($cloud in $endpoints.cloudEndpoint.PSObject.Properties) {
    $matchingLocation = $cloud.Value.locations | Where-Object {$_ -match $metadata.location}
    if ($matchingLocation) {
      $cloudName = $cloud.name
      break
    }
  }
 
  $environment = "Unknown"
  switch ($cloudName) {
    "public" { $environment = "AzureCloud"}
    "usGovCloud" { $environment = "AzureUSGovernment"}
    "chinaCloud" { $environment = "AzureChinaCloud"}
    "germanCloud" { $environment = "AzureGermanCloud"}
  }
 
  Write-Host $environment
```

### <a name="failover-clustering-in-windows-server"></a>A Feladatátvételi fürtszolgáltatás a Windows Server

Az egyes forgatókönyvek esetén Instance Metadata szolgáltatás feladatátvételi fürtszolgáltatás, a lekérdezés esetén szükséges útvonal hozzáadása az útválasztási táblázathoz.

1. Nyissa meg a parancssort rendszergazdai jogosultságokkal.

2. Futtassa a következő parancsot, és jegyezze fel a címet a cél hálózati adapter (`0.0.0.0`) IPv4-útválasztási táblázatához.

```bat
route print
```

> [!NOTE] 
> Az alábbi példa kimenetében a feladatátvevő fürt engedélyezve van a Windows Server virtuális gép csak az IPv4 útválasztási táblázatot az egyszerűség kedvéért tartalmazza.

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

3. Futtassa a következő parancsot, és használja a címet a felület hálózati cél (`0.0.0.0`) amely (`10.0.1.10`) ebben a példában.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Példák a virtuális gép több különböző nyelvet használó metadata szolgáltatás hívása 

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
1. A hibaüzenetet kapok `400 Bad Request, Required metadata header not specified`. Ez mit jelent?
   * A Instance Metadata szolgáltatás szükséges a fejléc `Metadata: true` átadni a kérelmet. Ezt a fejlécet ad át a REST-hívást lehetővé teszi a Instance Metadata szolgáltatás elérését. 
2. Miért nem jelenik meg a virtuális gépem számítási adatait?
   * A Instance Metadata szolgáltatás jelenleg csak az Azure Resource Managerrel létrehozott példányok támogatják. A Cloud Service virtuális gépeken hozzá lehet adva a jövőben támogatása.
3. A virtuális gép az Azure Resource Manageren keresztül létrehozott egy while vissza. Miért nem tudok lásd: számítási metaadat-információkat?
   * Minden 2016. szeptember. után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) , azokat a számítási metaadatait. A régebbi virtuális gépek (2016. szeptember előtt létrehozott) hozzáadása/eltávolítása adatok és a teljes lemezt a virtuális Gépet a metaadatok frissítése.
4. Nem látok feltöltve az új verzió minden adat
   * Minden 2016. szeptember. után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) , azokat a számítási metaadatait. A régebbi virtuális gépek (2016. szeptember előtt létrehozott) hozzáadása/eltávolítása adatok és a teljes lemezt a virtuális Gépet a metaadatok frissítése.
5. Miért jelenik meg a hiba `500 Internal Server Error`?
   * Ismételje meg a kérelmet, a rendszer exponenciális visszatartási alapján. Ha a probléma továbbra is fennáll, forduljon az Azure ügyfélszolgálatához.
6. Ha meg a további kérdéseimmel/megjegyzéseimmel?
   * A Megjegyzések küldése http://feedback.azure.com.
7. Működne ez virtuálisgép-méretezési csoport beállítása példányt?
   * Igen Metadata szolgáltatás érhető el a méretezési csoport beállítása példányokhoz. 
8. Hogyan kérhet támogatást a szolgáltatás?
   * Segítségre van szüksége a szolgáltatáshoz, hozzon létre egy támogatási probléma az Azure Portalon a virtuális gép, ahol Ön nem sikerült beolvasni a metaadatok válasz hosszú újrapróbálkozás után 
9. Kérelem túllépte az időkorlátot a szolgáltatás a hívások kapok?
   * Metaadatok hívások kell tenni a hálózati kártyát a virtuális gép hozzárendelt elsődleges IP-címről, emellett abban az esetben, ha módosította az ott kell lenniük egy útvonalat a hálózati kártya ki 169.254.0.0/16 cím.
10. Saját virtuálisgép-méretezési csoportban lévő címkék frissítése, de azok nem jelennek meg a virtuális gépek ellentétben a példányok?
   * Jelenleg a ScaleSets címkék csak akkor látható, a virtuális Gépet egy újraindítás vagy rendszerképét alaphelyzetbe állítani/egy lemezt, vagy módosítsa a példány a. 

   ![Példány metaadatok támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>További lépések

- Tudjon meg többet [ütemezett események](scheduled-events.md)
