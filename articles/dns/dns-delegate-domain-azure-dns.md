---
title: "Tartomány delegálása az Azure DNS-be | Microsoft Docs"
description: "Ismerje meg, hogyan módosíthatja a tartományok delegálását és használhatja tartományszolgáltatóként az Azure DNS-névkiszolgálóit."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 33b3ec24432ff1268860b9a2e9d5098600a8dedc
ms.contentlocale: hu-hu
ms.lasthandoff: 06/16/2017

---

# <a name="delegate-a-domain-to-azure-dns"></a>Tartomány delegálása az Azure DNS-be

Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Egy tartomány DNS-lekérdezései csak akkor érik el az Azure DNS-t, ha a tartomány delegálva van az Azure DNS-be a szülőtartományból. Ne feledje: nem az Azure DNS a tartományregisztráló. Ez a cikk a tartomány delegálását ismerteti az Azure DNS-be.

A tartományregisztrálótól vásárolt tartományokhoz a regisztráló felajánlja, hogy beállítja ezeket a névkiszolgálói rekordokat. Nem kell ahhoz egy tartománnyal rendelkeznie, hogy az adott tartománynévvel létrehozzon egy DNS-zónát az Azure DNS-ben. Azonban a tartományregisztrálóval az Azure DNS-be való delegáláshoz már meg kell vásárolnia a tartományt.

Tegyük fel például, hogy megvette a „contoso.net” tartományt, és létrehozott egy „contoso.net” nevű zónát az Azure DNS-ben. A tartomány tulajdonosaként a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálóinak címeit (azaz a névkiszolgálói rekordokat). A regisztráló ezeket a névkiszolgálói rekordokat a szülőtartományban, ebben az esetben a „.net” tartományban tárolja. A világ különböző pontjain található ügyfelek ekkor az Azure DNS-zónabeli tartományához irányíthatók, amikor megpróbálják feloldani a „contoso.net” DNS-rekordjait.

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
1. A központi menüben kattintson az **Új > Hálózatkezelés >** elemre, majd kattintson a **DNS-zóna** elemre a DNS-zóna létrehozása panel megnyitásához.

    ![DNS-zóna](./media/dns-domain-delegation/dns.png)

1. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|contoso.net|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válasszon ki egy előfizetést, amelyben létrehozza az alkalmazásátjárót.|
   |**Erőforráscsoport**|**Új létrehozása:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

## <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak neveit. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az **Összes erőforrás** panelen kattintson a **contoso.net** DNS-zónára. Ha a kiválasztott előfizetésben már több erőforrás szerepel, az alkalmazásátjáró egyszerű eléréséhez beírhatja a **contoso.net** nevet a Szűrés név alapján... mezőbe. 

1. Kérdezze le a névkiszolgálókat a DNS-zóna panelen. Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: „ns1-01.azure-dns.com”, „ns2-01.azure-dns.net”, „ns3-01.azure-dns.org” és „ns4-01.azure-dns.info”:

 ![DNS-névkiszolgáló](./media/dns-domain-delegation/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.  A névkiszolgálók neveit az Azure PowerShellen vagy az Azure parancssori felületén keresztül is megtekintheti ezeknek a rekordoknak a lekérésével.

Az alábbi példák bemutatják az egyes zónák névkiszolgálói lekérdezésének lépéseit az Azure DNS-ben a PowerShell és az Azure CLI használatával.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

A következő példa a válasz.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set show --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

A következő példa a válasz.

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>A tartomány delegálása

Most, hogy létrehozta a DNS-zónát, és megvannak a névkiszolgálók is, frissítenie kell a szülőtartományt az Azure DNS-névkiszolgálókkal. Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS által létrehozottakra.

Amikor egy tartományt az Azure DNS-be delegál, az Azure DNS által nyújtott névkiszolgálói neveket kell használnia. Ajánlott használni mind a négy névkiszolgálói nevet, a tartomány nevétől függetlenül. A tartománydelegáláshoz nem szükséges, hogy a névkiszolgálói név ugyanazt a legfelső szintű tartományt használja, mint az Ön tartománya.

Ne használjon „összetartó rekordokat” az Azure DNS névkiszolgálói IP-címeire való rámutatáshoz, mert ezek az IP-címek megváltozhatnak a jövőben. A saját zónájában történő, névkiszolgálói neveket használó delegálásokat – más néven „személyes névkiszolgálókat” – az Azure DNS jelenleg nem támogatja.

## <a name="verify-name-resolution-is-working"></a>A névfeloldás működésének ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy a névfeloldás működik-e. Ezt például az „nslookup” vagy egy hasonló eszköz segítségével teheti meg, amely lekérdezi a zónája SOA típusú rekordját (amely szintén automatikusan létrejön a zóna létrehozásakor).

Nem kell megadnia az Azure DNS névkiszolgálóit, mivel ha a delegálást helyesen végezte el, a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat.

```
nslookup -type=SOA contoso.com
```

Az alábbiakban egy példát láthat az előző parancsra adott válaszra:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-sub-domains-in-azure-dns"></a>Altartományok delegálása az Azure DNS-ben

Ha különálló gyermekzónát szeretne létrehozni, azt megteheti egy altartomány Azure DNS-beli delegálásával. Tegyük fel például, hogy a „contoso.net” Azure DNS-beli beállítása és delegálása után szeretne egy különálló gyermekzónát is létrehozni, „partners.contoso.net” néven.

1. Hozza létre a „partners.contoso.net” gyermekzónát az Azure DNS-ben.
2. Keresse meg a mérvadó névkiszolgálói rekordokat a gyermekzónában, így megtalálja a gyermekzónát az Azure DNS-ben üzemeltető névkiszolgálókat.
3. A gyermekzónára mutató szülőzónában delegálja a gyermekzónát a névkiszolgálói rekordok konfigurálásával.

### <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

1. Jelentkezzen be az Azure Portalra
1. A központi menüben kattintson az **Új > Hálózatkezelés >** elemre, majd kattintson a **DNS-zóna** elemre a DNS-zóna létrehozása panel megnyitásához.

    ![DNS-zóna](./media/dns-domain-delegation/dns.png)

1. A **DNS-zóna létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|partners.contoso.net|A DNS-zóna neve|
   |**Előfizetés**|[Az Ön előfizetése]|Válasszon ki egy előfizetést, amelyben létrehozza az alkalmazásátjárót.|
   |**Erőforráscsoport**|**Meglévő használata:** contosoRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

> [!NOTE]
> Az erőforráscsoport az erőforráscsoport helyére vonatkozik, és nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

### <a name="retrieve-name-servers"></a>Névkiszolgálók lekérdezése

1. Ha létrehozta a DNS-zónát, az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az **Összes erőforrás** panelen kattintson a **partners.contoso.net** DNS-zónára. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a DNS-zóna egyszerű eléréséhez beírhatja a **partners.contoso.net** nevet a Szűrés név alapján... mezőbe.

1. Kérdezze le a névkiszolgálókat a DNS-zóna panelen. Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: „ns1-01.azure-dns.com”, „ns2-01.azure-dns.net”, „ns3-01.azure-dns.org” és „ns4-01.azure-dns.info”:

 ![DNS-névkiszolgáló](./media/dns-domain-delegation/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.  A névkiszolgálók neveit az Azure PowerShellen vagy az Azure parancssori felületén keresztül is megtekintheti ezeknek a rekordoknak a lekérésével.

### <a name="create-name-server-record-in-parent-zone"></a>Névkiszolgáló-rekord létrehozása a szülőzónában

1. Az Azure Portalon lépjen a **contoso.net** DNS-zónára.
1. Kattintson a **+ Rekordhalmaz** gombra.
1. A **Rekordhalmaz hozzáadása** panelen adja meg az alábbi értékeket, és kattintson az **OK** gombra:

   | **Beállítás** | **Érték** | **Részletek** |
   |---|---|---|
   |**Name (Név)**|partners|A gyermek DNS-zóna neve|
   |**Típus**|NS|A névkiszolgálók esetében használja az NS típust.|
   |**TTL**|1|Élettartam.|
   |**TTL mértékegysége**|Óra|Az élettartam mértékegységét órára állítja.|
   |**NÉVKISZOLGÁLÓ**|{névkiszolgálók a partners.contoso.net zónából}|Adja meg mind a négy névkiszolgálót a partners.contoso.net zónából. |

   ![DNS-névkiszolgáló](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>Altartományok delegálása az Azure DNS-ben más eszközökkel

Az alábbi példák az altartományok delegálásának lépéseit mutatják be az Azure DNS-ben a PowerShell és a CLI használatával:

#### <a name="powershell"></a>PowerShell

Az alábbi PowerShell-példa bemutatja ennek működését. Ugyanezek a lépések az Azure-portálon vagy az Azure platformfüggetlen parancssori felületén keresztül is végrehajthatók.

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Az `nslookup` használatával a gyermekzóna SOA típusú rekordjának megkeresésével ellenőrizze, hogy minden helyesen van-e beállítva.

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Kérdezze le a `partners.contoso.net` zóna névkiszolgálóit a kimenetből.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Hozza létre a rekordkészletet és a névkiszolgálói rekordokat mindegyik névkiszolgálóhoz.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az Összes erőforrás panelen kattintson a **contosorg** erőforráscsoportra. Ha a kiválasztott előfizetésben már több erőforrás szerepel, az erőforráscsoport egyszerű eléréséhez beírhatja a **contosorg** nevet a **Szűrés név alapján...** mezőbe.
1. A **contosorg** panelen kattintson a **Törlés** gombra.
1. A portál megköveteli, hogy az erőforráscsoport törlésének megerősítéséhez beírja annak nevét. Írja be a *contosorg* nevet az erőforráscsoport nevéhez, majd kattintson a **Törlés** gombra. Az erőforráscsoport törlésével az abban foglalt összes erőforrás törölve lesz, ezért mindenképp ellenőrizze az erőforráscsoportok tartalmát azok törlése előtt. A portál törli az erőforráscsoportban lévő összes erőforrást, majd magát az erőforráscsoportot is. Ez a folyamat több percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

[DNS-zónák kezelése](dns-operations-dnszones.md)

[DNS-rekordok kezelése](dns-operations-recordsets.md)

