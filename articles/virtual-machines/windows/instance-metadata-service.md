---
title: "A Windows Azure-példányokon metaadat-szolgáltatás |} Microsoft Docs"
description: "Windows virtuális gép számítási, hálózati, és a közelgő karbantartásokról események adatainak beolvasása rESTful felületet."
services: virtual-machines-windows
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: harijay
ms.openlocfilehash: 55b97b89cb297dc08dc73f6714c5159d4565a97c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-instance-metadata-service-for-windows-vms"></a>Windows virtuális gépek Azure példány metaadat-szolgáltatás


Az Azure példány metaadat-szolgáltatás kezelése és konfigurálása a virtuális gépek használható virtuálisgép-példányok futtatásával kapcsolatos információkat biztosít.
Ide tartoznak például Termékváltozat, a hálózati konfigurációs és a közelgő karbantartásokról események. Milyen típusú információkat érhető el a további információkért lásd: [metaadat-kategóriák](#instance-metadata-data-categories).

Azure-példány metaadat-szolgáltatás segítségével létrehozott összes infrastruktúra-szolgáltatási virtuális gép számára elérhető REST-végpont a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). A végpont egy jól ismert nem irányítható IP-címen érhető el (`169.254.169.254`), amely elérhető csak a virtuális Gépen belül.



> [!IMPORTANT]
> Ez a szolgáltatás **általánosan elérhető** globális Azure-régióban. Nyilvános előzetes kormányzati, Kína és német Azure felhőben van. Rendszeresen teszi közzé a virtuálisgép-példányok új információ a frissítések kap. Ezen a lapon tükrözi a legfrissebb [az adatkategóriákat](#instance-metadata-data-categories) érhető el.



## <a name="service-availability"></a>Szolgáltatás rendelkezésre állása
A szolgáltatás minden általánosan elérhető globális Azure területen érhető el. A szolgáltatás a kormányzati, Kína vagy Németország régiókban nyilvános előzetes verzió van.

Régiók                                        | Rendelkezésre állási?
-----------------------------------------------|-----------------------------------------------
[Minden általánosan elérhető globális Azure-régió](https://azure.microsoft.com/regions/)     | Általánosan elérhető 
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Az előzetes verzió 
[Az Azure Kínában](https://www.azure.cn/)                                                           | Az előzetes verzió
[Az Azure-Németország](https://azure.microsoft.com/overview/clouds/germany/)                    | Az előzetes verzió

Ez a táblázat frissülni fog, amikor a szolgáltatás elérhetővé válnak más Azure felhőben.

A példány metaadat-szolgáltatás kipróbálására, a virtuális gép létrehozása az [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) vagy a [Azure-portálon](http://portal.azure.com) a fenti régiókban, és kövesse az alábbi példák.

## <a name="usage"></a>Használat

### <a name="versioning"></a>Versioning
A példány metaadat-szolgáltatás nem rendszerverzióval ellátott. Verziók kötelező, és a jelenlegi verzió: `2017-04-02`.

> [!NOTE] 
> Előző előzetes kiadásaiban ütemezett események {legújabb} támogatott api-verzióval. Ez a formátum már nem támogatott, és a jövőben elavulttá válik.

Azt hozzáadja az újabb verziók, régebbi verziók továbbra is elérhető kompatibilitás, ha a parancsfájlok tartalmazhat függőségeket, meghatározott formátumban. Azonban előfordulhat, hogy az aktuális előnézeti version(2017-03-01) nem érhető el a szolgáltatás általánosan elérhetővé válik.

### <a name="using-headers"></a>Fejlécek használata
Ha a példány metaadat-szolgáltatás, meg kell adnia a fejléc `Metadata: true` annak érdekében, hogy nem szándékos átirányítja a kérést.

### <a name="retrieving-metadata"></a>Metaadatainak lekérése során

Példány metaadatai nem érhető el a futó virtuális gépek létrehozása/segítségével kezelt [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Elérhető összes az adatkategóriákat a virtuális gép példány használatával a következő kérelmet:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Minden példány metaadatok lekérdezés-és nagybetűk.

### <a name="data-output"></a>Kimeneti adatok
Alapértelmezés szerint a példány metaadat-szolgáltatás adatokat adja vissza JSON formátumban (`Content-Type: application/json`). Azonban más API-k adhatnak vissza adatokat különböző formátumokban kérésre.
A következő táblázat a más adatok formátumok API-k támogathatja a hivatkozás.

API | Alapértelmezett adatformátum | Eltérő formátumban
--------|---------------------|--------------
/instance | JSON-ban | Szöveg
/scheduledevents | JSON-ban | Egyik sem

Egy nem alapértelmezett válaszformátum szeretne használni, adja meg a kért formátumát a kérelem lekérdezési karakterlánc paraméterként. Példa:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Biztonság
A példány metaadatok szolgáltatás végpontjának értéke csak a futó virtuális gép példány nem irányítható IP-címnek a belülről érhetők el. Emellett a kérelmet egy `X-Forwarded-For` fejléc elutasította a szolgáltatást.
Azt is igénylik, magában foglalja a kérelmek egy `Metadata: true` fejlécének győződjön meg arról, hogy a tényleges kérelem volt közvetlenül tervezett és nem szándékos átirányítási része. 

### <a name="error"></a>Hiba
Ha nem található egy adatelem vagy hibás formátumú kérelem, a példány metaadat-szolgáltatás szabványos HTTP-hibák adja vissza. Példa:

HTTP-állapotkód | Ok
----------------|-------
200 OK |
400 Hibás kérés | Hiányzó `Metadata: true` fejléc
404 – Nem található | A kért elem does't létezik 
405 metódus nem engedélyezett | Csak `GET` és `POST` kérelmek támogatottak.
429 túl sok kérelem | Az API-t jelenleg legfeljebb 5 lekérdezések száma másodpercenként
500 szolgáltatási hiba     | Némi várakozás után próbálja meg újra

### <a name="examples"></a>Példák

> [!NOTE] 
> Minden API-válasz olyan JSON-karakterláncok. Minden, a következő példa válaszok pretty nyomtatott olvashatóság érdekében.

#### <a name="retrieving-network-information"></a>Hálózati adatok beolvasása

**Kérés**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

```
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

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Egy példányát minden metaadatainak beolvasása

**Kérés**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>A Windows rendszerű virtuális gép metaadatainak lekérése során

**Kérés**

Példány metaadatok lehet beolvasni a Windows a PowerShell segédprogram segítségével `curl`: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

Vagy a `Invoke-RestMethod` parancsmagot:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

```
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

Adatok | Leírás
-----|------------
location | Azure-régió, a virtuális gép fut.
név | A virtuális gép neve 
az ajánlat | A VM-lemezkép információkat nyújtanak. Kép: Azure-galériából telepített lemezképek nincs csak ezt az értéket.
Közzétevő | A Virtuálisgép-lemezkép kiadója
Termékváltozat | A VM-lemezkép adott Termékváltozat  
Verzió | A Virtuálisgép-lemezkép verziója 
osType | Linux- vagy Windows 
platformUpdateDomain |  [Frissítési tartományok](manage-availability.md) a virtuális gép fut.
platformFaultDomain | [Tartalék tartomány](manage-availability.md) a virtuális gép fut.
vmId | [Egyedi azonosító](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) a virtuális géphez
vmSize | [Virtuálisgép-mérettel](sizes.md)
IPv4/privateipaddress tulajdonságot | A virtuális gép helyi IPv4-címe 
IPv4-vagy nyilvános | A virtuális gép nyilvános IPv4-cím
alhálózat és címét | A virtuális gép alhálózati cím
/ előtagot. | Példa 24 alhálózati előtag
IPv6/IP-cím | A virtuális gép helyi IPv6-cím
MacAddress | Virtuális gép mac-cím 
scheduledevents | Jelenleg a nyilvános előzetes lásd: [scheduledevents](scheduled-events.md)

## <a name="example-scenarios-for-usage"></a>Példa használati forgatókönyvek  

### <a name="tracking-vm-running-on-azure"></a>Azure-on futó virtuális gép nyomon követése

Szolgáltató akkor szükség lehet a nyomon követheti a szoftvert futtató virtuális gépek számát, vagy ügynököket, amelyeket a virtuális gép egyediségi nyomon kell rendelkeznie. Kell helyeznie egy egyedi Azonosítót a virtuális gépek, használja a `vmId` példány metaadat-szolgáltatásának mezőjét.

**Kérés**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Válasz**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Tárolók elhelyezését, adat-partíciók alapú tartalék/frissítési tartomány 

Az egyes forgatókönyvek, más adatok replikák elhelyezését van elsődleges fontosságú. Például [HDFS replika elhelyezésének](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) vagy tároló elhelyezési keresztül egy [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) , szükség lehet tudni, hogy a `platformFaultDomain` és `platformUpdateDomain` fut a virtuális Gépet.
Ezek az adatok közvetlenül a példány metaadatok szolgáltatásával lekérdezheti.

**Kérés**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Válasz**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>További információ a virtuális gép támogatási esetet során beolvasása 

Szolgáltató akkor jelenhet meg a támogatási hívás hol szeretné tudni, hogy a virtuális gép további információt. Az ügyfél a számítási metaadatok megosztásához kérni a támogatási szakember, milyen típusú Azure virtuális gép tudnia alapvető információkat nyújtanak. 

**Kérés**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Válasz**

> [!NOTE] 
> A válasz egy JSON-karakterláncban. A következő példa egy válasz pretty nyomtatott olvashatóság érdekében.

```
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
Ruby     | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.RB
Nyissa meg a helyi hálózat   | https://github.com/Microsoft/azureimds/BLOB/Master/imdssample.go            
python   | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.PY
C++      | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample-Windows.cpp
C#       | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.cs
Javascript | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.SH
    

## <a name="faq"></a>GYIK
1. A hiba azért kapom `400 Bad Request, Required metadata header not specified`. Ez mit jelent?
   * A példány metaadat-szolgáltatás a fejlécet igényel `Metadata: true` átadni a kérelemben. Ezt a fejlécet a REST-hívást benyújtása lehetővé teszi, hogy a példány metaadat-szolgáltatás eléréséhez. 
2. Miért nem jelenik meg a virtuális gép számítási adatai?
   * A példány metaadat-szolgáltatás jelenleg csak az Azure Resource Manager eszközzel létrehozott példányokat támogatja. A jövőben azt előfordulhat, hogy támogassa az Cloud Service virtuális gépeken.
3. A virtuális gép Azure Resource Manager használatával létrehozott egy while vissza. Miért nem tudok lásd számítási metaadat-információ?
   * A 2016. szeptember után létrehozott virtuális gépek hozzáadása egy [címke](../../azure-resource-manager/resource-group-using-tags.md) jelenítse meg a számítási metaadatok. (Szeptember 2016 előtt létrehozott) régebbi virtuális gépek hozzáadása a virtuális gép metaadatait extensions vagy adatok lemezét.
4. Miért jelenik meg a hiba `500 Internal Server Error`?
   * Próbálja megismételni a kérést a rendszer ki exponenciális vissza alapján. Ha a probléma továbbra is fennáll, forduljon az Azure támogatási.
5. Ha ugyanazt a további kérdéseit vagy megjegyzéseit?
   * A Megjegyzések küldése a http://feedback.azure.com.
7. Ez használható lenne a virtuálisgép-méretezési beállítása példányt?
   * Igen metaadat-szolgáltatás esetén méretezési beállítása példányok érhető el. 
6. Hogyan kérhet támogatást a szolgáltatás?
   * Segítségre van szüksége a szolgáltatáshoz, hozzon létre egy támogatási probléma Azure-portálon a virtuális gép, amelyen még nem kérhető le a metaadatokat válasz hosszú újrapróbálkozás után 

   ![Példány metaadatok támogatása](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Következő lépések

- További információ a [ütemezett események](scheduled-events.md) API **nyilvános előzetes verziójában** a példány metaadat-szolgáltatás által biztosított.
