---
title: Állomás DNS-névkeresési zónák az Azure DNS-ben |} A Microsoft Docs
description: Ismerje meg az IP-címtartományok fordított DNS-névkeresési zónák üzemeltetése az Azure DNS használatával
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e45b3bde0d5077a5d18369236e81bcd467527940
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990144"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Állomás DNS-névkeresési zónák az Azure DNS-ben

Ez a cikk ismerteti az Azure DNS-ben rendelt IP-címtartományok fordított DNS-névkeresési zónák üzemeltetése. A névkeresési zónák által jelölt IP-címtartományokkal kell rendelni a szervezethez, általában az Internetszolgáltató által.

Fordított DNS az Azure-szolgáltatáshoz rendelt Azure tulajdonában lévő IP-cím beállítása: [– címfeloldási DNS konfigurálása az Azure-ban üzemeltetett szolgáltatások](dns-reverse-dns-for-azure-services.md).

Ez a cikk előtt meg kell ismernie a a [fordított DNS és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).

Ez a cikk végigvezeti a lépéseken az első névkeresési DNS-zóna és -rekord létrehozása az Azure Portalon, az Azure PowerShell, az Azure klasszikus parancssori felület vagy Azure CLI használatával.

## <a name="create-a-reverse-lookup-dns-zone"></a>Névkeresési DNS-zóna létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a **Hub** menüjében válassza **új** > **hálózatkezelés**, majd válassza ki **DNS-zóna**.

   !["DNS-zóna" kiválasztása](./media/dns-reverse-dns-hosting/figure1.png)

1. Az a **DNS-zóna létrehozása** panelen nevezze el a DNS-zónát. A zóna nevét eltérően kialakított az IPv4 és IPv6-előtagok esetében. Kövesse az utasításokat a [IPv4](#ipv4) vagy [IPv6](#ipv6) a zóna nevét. Ha elkészült, válassza ki a **létrehozás** hozhat létre a zónát.

### <a name="ipv4"></a>IPv4

Egy IPv4-névkeresési zóna neve, amely ezt az IP-címtartomány alapján. A következő formátumban kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Példák: [fordított DNS és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Classless DNS-névkeresési zónák létrehozásakor az Azure DNS kötőjellel kell használnia (`-`) ahelyett, hogy előre dőlő perjellel (`/`) a zóna nevét.
>
> Ha például az IP-címtartomány 192.0.2.128/26 kell használnia `128-26.2.0.192.in-addr.arpa` helyett a zóna nevét, `128/26.2.0.192.in-addr.arpa`.
>
> Bár a DNS-szabványok a két módszert támogatja, az Azure DNS perjellel nem támogatja a DNS-zóna nevét, amely tartalmazza (`/`) karaktert.

Az alábbi példa bemutatja, hogyan hozhat létre C osztályú fordított DNS-zóna nevű `2.0.192.in-addr.arpa` az Azure DNS az Azure Portalon:

 !["DNS-zóna létrehozása" panelen, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure2.png)

**Erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Ez nem befolyásolja a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot az Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna neve a következő formátumban kell lennie: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Példák: [fordított DNS és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md#ipv6).


Az alábbi példa bemutatja, hogyan hozhat létre IPv6 névkeresési DNS-keresési zóna nevű `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` az Azure DNS az Azure Portalon:

 !["DNS-zóna létrehozása" panelen, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure3.png)

**Erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Ez nem befolyásolja a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot az Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Fordított DNS-zóna delegálása

Most, hogy létrehozta a fordított irányú DNS-címkeresési zóna, biztosítania kell, hogy a zóna van átadva a szülőzónában. DNS-delegálás, keresse meg a fordított irányú DNS-keresési zónát üzemeltető névkiszolgálókat a DNS névfeloldási folyamatot lehetővé teszi. Ezeket a névkiszolgálókat a-címtartományban szereplő IP-címek majd válaszolhat fordított DNS-lekérdezésekre.

A címkeresési zónák, DNS-zóna delegálása folyamata leírt [tartomány delegálása az Azure DNS](dns-delegate-domain-azure-dns.md). Névkeresési zónák delegálása ugyanúgy működik. Az egyetlen különbség, hogy a megadott, az IP-címtartományt, nem pedig a tartománynév regisztrálójánál az internetszolgáltatóval névkiszolgálókat konfigurálnia kell.

## <a name="create-a-dns-ptr-record"></a>DNS PTR-rekord létrehozása

### <a name="ipv4"></a>IPv4

Az alábbi példa végigvezeti egy fordított DNS-zóna Azure DNS-ben a PTR típusú rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. Felső részén a **DNS-zóna** ablaktáblán válassza **+ rekordhalmaz** megnyitásához a **rekordhalmaz hozzáadása** ablaktáblán.

   ![A rekordhalmaz létrehozása gomb](./media/dns-reverse-dns-hosting/figure4.png)

1. A PTR-rekordot a rekordhalmaz nevét kell lennie a az IPv4-címet a többi fordított sorrendben. 

   Ebben a példában az első három oktettjének van már fel van töltve a zóna nevét (.2.0.192) részeként. Ezért csak az utolsó oktettet van megadva a **neve** mezőbe. Például előfordulhat, hogy nevezze el a rekordhalmazt **15** egy erőforrás, amelynek IP-cím 192.0.2.15.  
1. A **típus**válassza **PTR**.  
1. A **tartománynév**, adja meg a teljesen minősített tartománynevét (FQDN) az erőforrás, amely a IP-címet használ.
1. Válassza ki **OK** jegyezze fel a DNS-ben létrehozni a panel alján.

 !["A rekordhalmaz hozzáadása" panelen, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure5.png)

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Az alábbi példa végigvezeti az új PTR-rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. Felső részén a **DNS-zóna** ablaktáblán válassza **+ rekordhalmaz** megnyitásához a **rekordhalmaz hozzáadása** ablaktáblán.

   ![A rekordhalmaz létrehozása gomb](./media/dns-reverse-dns-hosting/figure6.png)

2. A PTR-rekordot a rekordhalmaz nevét kell lennie a IPv6-címet a többi fordított sorrendben. Nulla a tömörítés nem tartalmazhat. 

   Ebben a példában az első 64 bit, az IPv6-rendszer már fel van töltve a zóna nevét (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa) részeként. Ezért csak a legutóbbi 64 bit megadott a **neve** mezőbe. Az IP-cím utolsó 64 bit fordított sorrendben megadott közötti minden hexadecimális szám elválasztóként időszakkal. Például előfordulhat, hogy nevezze el a rekordhalmazt **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** egy erőforrás, amelynek IP-cím 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. A **típus**válassza **PTR**.  
4. A **tartománynév**, írja be a IP-címet használó az erőforrás teljes Tartománynevét.
5. Válassza ki **OK** jegyezze fel a DNS-ben létrehozni a panel alján.

!["A rekordhalmaz hozzáadása" panelen, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure7.png)

A következő példák bemutatják, hogyan hajthatja végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>A rekordok megtekintése

Az Ön által létrehozott rekordok megtekintéséhez keresse meg a DNS-zónát az Azure Portalon. Az alsó részén a **DNS-zóna** panelen láthatja a DNS-zóna rekordjait. Az alapértelmezett NS és SOA rekordokat, valamint az összes új létrehozott kell megjelennie. Az NS és SOA típusú rekordoknak minden zónában létrejönnek. 

### <a name="ipv4"></a>IPv4

A **DNS-zóna** panelen látható a IPv4 PTR-rekordok:

![IPv4-rekordokat a "DNS-zóna" panel](./media/dns-reverse-dns-hosting/figure8.png)

A következő példák bemutatják a PTR-rekordok megtekintése a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

A **DNS-zóna** panelen látható az IPv6-alapú PTR-rekordok:

![IPv6-rekordokat a "DNS-zóna" panel](./media/dns-reverse-dns-hosting/figure9.png)

Az alábbi példák bemutatják, hogyan a rekordok megtekintéséhez használja a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>GYIK

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Lehet-e üzemeltetni DNS-névkeresési zónák az Azure DNS saját Internetszolgáltató által kiosztott IP-címblokkok?

Igen. Az Azure DNS-ben a saját IP-címtartományok esetében (ARPA) névkeresési zónák üzemeltetése teljes mértékben támogatott.

A névkeresési zóna létrehozása az Azure DNS Szolgáltatásban, ebben a cikkben leírtak szerint, és máris munkához az Internetszolgáltatótól [a zóna delegálása](dns-domain-delegation.md). A PTR-rekordok minden egyes névkeresési ugyanúgy, mint más erőforrásrekord-típusok a ezután kezelheti.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>IP-címek fenntartási nem, saját fordított DNS-keresési zóna költség üzemeltető?

A fordított irányú DNS-címkeresési zóna üzemeltetése az Azure DNS-ben az Internetszolgáltató által kiosztott IP-blokk díja [standard Azure DNS díjszabása](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Üzemeltetheti DNS-névkeresési zónák IPv4 és az IPv6-címek az Azure DNS?

Igen. Ez a cikk bemutatja, hogyan hozhat létre az IPv4- és IPv6 DNS-névkeresési zónák az Azure DNS-ben.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Beimportálhatok egy meglévő fordított DNS-címkeresési zóna?

Igen. Azure CLI segítségével importálja a meglévő DNS-zónák az Azure DNS-ben. Ez a módszer használható a címkeresési zónák és a névkeresési zóna listáját.

További információkért lásd: [importálása és exportálása az Azure CLI-vel egy DNS-zónafájlban](dns-import-export.md).

## <a name="next-steps"></a>További lépések

Fordított DNS további információkért lásd: [Wikipedia DNS-címkeresés](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Ismerje meg, hogyan [fordított DNS-rekordjait az Azure-szolgáltatások kezelése](dns-reverse-dns-for-azure-services.md).
