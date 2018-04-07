---
title: Azure-példányokon metaadat-szolgáltatás |} Microsoft Docs
description: Windows virtuális gép számítási, hálózati, és a közelgő karbantartásokról események adatainak beolvasása rESTful felületet.
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
ms.openlocfilehash: b3ecddbfdc37bc58294dff41b62e0505b90ca940
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-instance-metadata-service"></a>Az Azure példány metaadat-szolgáltatás


Az Azure példány metaadat-szolgáltatás kezelése és konfigurálása a virtuális gépek használható virtuálisgép-példányok futtatásával kapcsolatos információkat biztosít.
Ide tartoznak például Termékváltozat, a hálózati konfigurációs és a közelgő karbantartásokról események. Milyen típusú információkat érhető el a további információkért lásd: [metaadat-kategóriák](#instance-metadata-data-categories).

Azure-példány metaadat-szolgáltatás segítségével létrehozott összes infrastruktúra-szolgáltatási virtuális gép számára elérhető REST-végpont a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). A végpont egy jól ismert nem irányítható IP-címen érhető el (`169.254.169.254`), amely elérhető csak a virtuális Gépen belül.

> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** minden Azure-régióban.  Rendszeresen teszi közzé a virtuálisgép-példányok új információ a frissítések kap. Ezen a lapon tükrözi a legfrissebb [az adatkategóriákat](#instance-metadata-data-categories) érhető el.

## <a name="service-availability"></a>Elérhető szolgáltatások
A szolgáltatás általánosan elérhető Azure-régiók érhető el. Nem minden API-verzió érhetők el minden Azure-régióban.

Régiók                                        | Rendelkezésre állási?                                 | Támogatott verziók
-----------------------------------------------|-----------------------------------------------|-----------------
[Minden általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | Általánosan elérhető   | 2017-04-02, 2017-08-01, 2017-12-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Általánosan elérhető | 2017-04-02,2017-08-01
[Azure China](https://www.azure.cn/)                                                           | Általánosan elérhető | 2017-04-02,2017-08-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Általánosan elérhető | 2017-04-02,2017-08-01

Ez a táblázat frissülni fog, amikor a szolgáltatás frissítések érhetők el, és vagy új verziók érhetők el

A példány metaadat-szolgáltatás kipróbálására, a virtuális gép létrehozása az [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [Azure-portálon](http://portal.azure.com) a fenti régiókban, és kövesse az alábbi példák.

## <a name="usage"></a>Használat

### <a name="versioning"></a>Verziókezelés
A példány metaadat-szolgáltatás nem rendszerverzióval ellátott. Verziók kötelező, és az aktuális verzió globális Azure `2017-12-01`. Aktuális verziók a következők (2017-04-02, 2017-08-01,2017-12-01)

> [!NOTE] 
> Előző előzetes kiadásaiban ütemezett események {legújabb} támogatott api-verzióval. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

Növelése újabb verziók hozzáadásakor, régebbi verziók továbbra is elérhető kompatibilitás, ha a parancsfájlok tartalmazhat függőségeket, meghatározott formátumban. Előfordulhat azonban, a korábbi előzetes verzióval (2017, 03, 01) nem érhető el a szolgáltatás általánosan elérhetővé válik.

### <a name="using-headers"></a>Fejlécek használata
Ha a példány metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata: true` annak érdekében, hogy nem szándékos átirányítja a kérést.

### <a name="retrieving-metadata"></a>Metaadatainak lekérése során

Példány metaadatai nem érhető el a futó virtuális gépek létrehozása/segítségével kezelt [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Elérhető összes az adatkategóriákat a virtuális gép példány használatával a következő kérelmet:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Minden példány metaadatok lekérdezés-és nagybetűk.

### <a name="data-output"></a>Kimeneti adatok
Alapértelmezés szerint a példány metaadat-szolgáltatás adatokat adja vissza JSON formátumban (`Content-Type: application/json`). Azonban más API-k adja vissza, adatok különböző formátumokban.
A következő táblázat a más adatok formátumok API-k támogathatja a hivatkozás.

API | Alapértelmezett adatformátum | Eltérő formátumban
--------|---------------------|--------------
/instance | json | Szöveg
/scheduledevents | json | nincs

Egy nem alapértelmezett válaszformátum szeretne használni, adja meg a kért formátumát a kérelem lekérdezési karakterlánc paraméterként. Példa:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Biztonság
A példány metaadatok szolgáltatás végpontjának értéke csak a futó virtuális gép példány nem irányítható IP-címnek a belülről érhetők el. Emellett a kérelmet egy `X-Forwarded-For` fejléc elutasította a szolgáltatást.
Kérelmek is tartalmaznia kell egy `Metadata: true` fejlécének győződjön meg arról, hogy a tényleges kérelem volt közvetlenül tervezett és nem szándékos átirányítási része. 

### <a name="error"></a>Hiba
Ha nem található egy adatelem vagy hibás formátumú kérelem, a példány metaadat-szolgáltatás szabványos HTTP-hibák adja vissza. Példa:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 Hibás kérés | Hiányzó `Metadata: true` fejléc
404 – Nem található | A kért elem nem létezik. 
405 metódus nem engedélyezett | Csak `GET` és `POST` kérelmek támogatottak.
429 túl sok kérelem | Az API-t jelenleg legfeljebb 5 lekérdezések száma másodpercenként
500 Service Error     | Némi várakozás után próbálja meg újra

### <a name="examples"></a>Példák

> [!NOTE] 
> Minden API-válasz olyan JSON-karakterláncok. Minden, a következő példa válaszok pretty nyomtatott olvashatóság érdekében.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

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

#### <a name="retrieving-all-metadata-for-an-instance"></a>Egy példányát minden metaadatainak beolvasása

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>A Windows rendszerű virtuális gép metaadatainak lekérése során

**Kérés**

Példány metaadatok Windows keresztül lehet beolvasni a `curl` program: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` PowerShell-parancsmagot:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

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

## <a name="instance-metadata-data-categories"></a>Példány metaadat az adatkategóriákat
A következő az adatkategóriákat a példány metaadatok szolgáltatáson keresztül érhetők el:

Adatok | Leírás | Bevezetett verziója 
-----|-------------|-----------------------
location | Azure-régió, a virtuális gép fut. | 2017-04-02 
név | A virtuális gép neve | 2017-04-02
ajánlat | A VM-lemezkép információkat nyújtanak. Kép: Azure-galériából telepített lemezképek nincs csak ezt az értéket. | 2017-04-02
publisher | A Virtuálisgép-lemezkép kiadója | 2017-04-02
sku | A VM-lemezkép adott Termékváltozat | 2017-04-02
verzió: | A Virtuálisgép-lemezkép verziója | 2017-04-02
osType | Linux- vagy Windows | 2017-04-02
platformUpdateDomain |  [Frissítési tartományok](manage-availability.md) a virtuális gép fut. | 2017-04-02
platformFaultDomain | [Tartalék tartomány](manage-availability.md) a virtuális gép fut. | 2017-04-02
vmId | [Egyedi azonosító](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) a virtuális géphez | 2017-04-02
vmSize | [Virtuálisgép-mérettel](sizes.md) | 2017-04-02
subscriptionId | A virtuális gép Azure-előfizetés | 2017-08-01
tags | [Címkék](../../azure-resource-manager/resource-group-using-tags.md) a virtuális gép  | 2017-08-01
resourceGroupName | [Erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) a virtuális gép | 2017-08-01
placementGroupId | [Elhelyezési csoport](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) a virtuálisgép-méretezési állítva | 2017-08-01
vmScaleSetName | [A virtuális gép ScaleSet neve] (.. /.. / virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) a virtuálisgép-méretezési állítva | 2017-12-01
zóna | [Rendelkezésre állási zóna](../../availability-zones/az-overview.md) a virtuális gép | 2017-12-01 
ipv4/privateIpAddress | A virtuális gép helyi IPv4-címe | 2017-04-02
ipv4/publicIpAddress | A virtuális gép nyilvános IPv4-cím | 2017-04-02
subnet/address | A virtuális gép alhálózati cím | 2017-04-02 
subnet/prefix | Példa 24 alhálózati előtag | 2017-04-02 
ipv6/ipAddress | A virtuális gép helyi IPv6-cím | 2017-04-02 
MacAddress | Virtuális gép mac-cím | 2017-04-02 
scheduledevents | Lásd: [ütemezett események](scheduled-events.md) | 2017-08-01

## <a name="example-scenarios-for-usage"></a>Példa használati forgatókönyvek  

### <a name="tracking-vm-running-on-azure"></a>Azure-on futó virtuális gép nyomon követése

Szolgáltató akkor szükség lehet a nyomon követheti a szoftvert futtató virtuális gépek számát, vagy ügynököket, amelyeket a virtuális gép egyediségi nyomon kell rendelkeznie. Kell helyeznie egy egyedi Azonosítót a virtuális gépek, használja a `vmId` példány metaadat-szolgáltatásának mezőjét.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Válasz**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezését, adat-partíciók alapú tartalék/frissítési tartomány 

Az egyes forgatókönyvek, más adatok replikák elhelyezését van elsődleges fontosságú. Például [HDFS replika elhelyezésének](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy tároló elhelyezési keresztül egy [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) , szükség lehet tudni, hogy a `platformFaultDomain` és `platformUpdateDomain` fut a virtuális Gépet.
Kihasználhatja [rendelkezésre állási zónák](../../availability-zones/az-overview.md) a példányok ezeket a döntéseket.
Ezek az adatok közvetlenül a példány metaadatok szolgáltatásával lekérdezheti.

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Válasz**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információ a virtuális gép támogatási esetet során beolvasása 

Szolgáltató akkor jelenhet meg a támogatási hívás hol szeretné tudni, hogy a virtuális gép további információt. Az ügyfél a számítási metaadatok megosztásához kérni a támogatási szakember, milyen típusú Azure virtuális gép tudnia alapvető információkat nyújtanak. 

**Kérés**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Példák a virtuális Gépen belül különböző nyelveken használó metaadatok szolgáltatás hívása 

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
    

## <a name="faq"></a>GYIK
1. A hiba azért kapom `400 Bad Request, Required metadata header not specified`. Ez mit jelent?
   * A példány metaadat-szolgáltatás a fejlécet igényel `Metadata: true` átadni a kérelemben. Ezt a fejlécet a REST-hívást benyújtása lehetővé teszi, hogy a példány metaadat-szolgáltatás eléréséhez. 
2. Miért nem jelenik meg a virtuális gép számítási adatai?
   * A példány metaadat-szolgáltatás jelenleg csak az Azure Resource Manager eszközzel létrehozott példányokat támogatja. A jövőben támogatása, a Cloud Service virtuális gépeken lehet, hogy lehet hozzáadni.
3. A virtuális gép Azure Resource Manager használatával létrehozott egy while vissza. Miért nem tudok lásd számítási metaadat-információ?
   * A 2016. szeptember után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) jelenítse meg a számítási metaadatok. (Szeptember 2016 előtt létrehozott) régebbi virtuális gépek hozzáadása a virtuális gép metaadatait extensions vagy adatok lemezét.
4. Nem látható az összes adat feltöltve az olyan 2017-08-01 új verziója
   * A 2016. szeptember után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) jelenítse meg a számítási metaadatok. (Szeptember 2016 előtt létrehozott) régebbi virtuális gépek hozzáadása a virtuális gép metaadatait extensions vagy adatok lemezét.
5. Miért jelenik meg a hiba `500 Internal Server Error`?
   * Próbálja megismételni a kérést a rendszer ki exponenciális vissza alapján. Ha a probléma továbbra is fennáll, forduljon az Azure támogatási.
6. Ha ugyanazt a további kérdéseit vagy megjegyzéseit?
   * A Megjegyzések küldése a http://feedback.azure.com.
7. Ez használható lenne a virtuálisgép-méretezési beállítása példányt?
   * Igen metaadat-szolgáltatás esetén méretezési beállítása példányok érhető el. 
8. Hogyan kérhet támogatást a szolgáltatás?
   * Segítségre van szüksége a szolgáltatáshoz, hozzon létre egy támogatási probléma Azure-portálon a virtuális gép, amelyen még nem kérhető le a metaadatokat válasz hosszú újrapróbálkozás után 

   ![Példány metaadatok támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>További lépések

- További információ [ütemezett események](scheduled-events.md)
