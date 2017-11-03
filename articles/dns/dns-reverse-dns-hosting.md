---
title: "Üzemeltetési névkeresési DNS zónák az Azure DNS |} Microsoft Docs"
description: "A névkeresési DNS-címkeresési zónák az IP-címtartományok tárolásához Azure DNS használata"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Az Azure DNS-névkeresési DNS-címkeresési zónák üzemeltetéséhez

Ez a cikk azt ismerteti, hogyan a hozzárendelt IP-címtartományokhoz az Azure DNS-névkeresési DNS-címkeresési zónák üzemeltetésére. Az IP-címtartományai a névkeresési zóna által képviselt kell rendelni a szervezetben, általában az Internetszolgáltató által.

Az Azure service rendelt Azure tulajdonában lévő IP-cím címfeloldási DNS konfigurálásával kapcsolatban lásd: [konfigurálása az Azure service lefoglalt IP-címek névkeresési](dns-reverse-dns-for-azure-services.md).

A cikk elolvasása előtt meg kell ismernie a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).

Ez a cikk végigvezeti a az első névkeresési DNS-zóna létrehozása, és jegyezze fel az Azure portál, Azure PowerShell, az Azure CLI 1.0 vagy az Azure CLI 2.0 használatával.

## <a name="create-a-reverse-lookup-dns-zone"></a>A névkeresési DNS-zóna létrehozása

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
1. A központ menüben kattintson, majd **új** > **hálózati** >, majd **DNS-zóna** megnyitásához a **hozzon létre DNS-zóna** panelen.

   ![DNS-zóna](./media/dns-reverse-dns-hosting/figure1.png)

1. Az a **hozzon létre DNS-zóna** panelen, a DNS-zóna neve. A zóna nevét kialakított az IPv4 és IPv6-előtagok másképp van. Vagy az utasításokat a [IPV4](#ipv4) vagy [IPv6](#ipv6) a zóna nevét. Ha végzett a kattintson **létrehozása** a zóna létrehozása.

### <a name="ipv4"></a>IPv4-alapú

Egy IPv4 névkeresési zóna neve az IP-címtartományt, amely alapul. A következő formátumúnak kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Tekintse meg a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Az Azure DNS-classless névkeresési DNS-címkeresési zónák létrehozásakor használnia kell a kötőjel (`-`) helyett egy perjel ("/") a zóna nevét.
>
> Például az IP-címtartomány 192.0.2.128/26 kell használnia `128-26.2.0.192.in-addr.arpa` ahelyett, hogy a zóna neveként `128/26.2.0.192.in-addr.arpa`.
>
> Ennek az az oka, mind támogatottak, a DNS-szabványokban, DNS-zónák neve a perjel (`/`) karakter használata nem támogatott Azure DNS-ben.

A következő példa bemutatja, hogyan hozzon létre egy C osztályú címfeloldási DNS-zóna nevű `2.0.192.in-addr.arpa` az Azure DNS az Azure-portálon:

 ![DNS-zóna létrehozása](./media/dns-reverse-dns-hosting/figure2.png)

Az "erőforráscsoport helye" határozza meg az erőforrásnak a helyét, és nincs hatással van a DNS-zónát. A DNS-zóna helye mindig "global", és nem jelenik meg.

Az alábbi példák bemutatják, hogyan lehet elvégezni ezt a feladatot az Azure PowerShell és az Azure parancssori felület:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Egy IPv6-névkeresési zóna neve a következő formátumban kell lennie: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Tekintse meg a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md#ipv6).


A következő példa bemutatja, hogyan hozzon létre egy IPv6 névkeresési DNS-zóna nevű `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` az Azure DNS az Azure-portálon:

 ![DNS-zóna létrehozása](./media/dns-reverse-dns-hosting/figure3.png)

Az "erőforráscsoport helye" határozza meg az erőforrásnak a helyét, és nincs hatással van a DNS-zónát. A DNS-zóna helye mindig "global", és nem jelenik meg.

Az alábbi példák bemutatják, hogyan lehet elvégezni ezt a feladatot az Azure PowerShell és az Azure parancssori felület:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>A címfeloldási DNS-zóna delegálása

A címfeloldási DNS-zóna hozunk létre, győződjön meg arról, hogy a zóna van átadva a szülőzónában. DNS-delegálás lehetővé teszi, hogy a DNS-név feloldási folyamat keresése a névkeresési DNS-címkeresési zónát üzemeltető névkiszolgálókat. Ez lehetővé teszi, hogy ezek névkiszolgálók megválaszolni a DNS-névkeresési lekérdezések a címtartomány IP-címek.

Címkeresési zónák, DNS-zóna delegálása folyamata ismertetett [tartomány delegálása az Azure DNS-](dns-delegate-domain-azure-dns.md). A delegálás névkeresési zónák ugyanúgy működik. Az egyetlen különbség, hogy szeretné-e a Névkiszolgálók állítson be a tartományregisztrálójához neve helyett az IP-címtartományt biztosító Internetszolgáltató.

## <a name="create-a-dns-ptr-record"></a>DNS PTR-rekord létrehozása

### <a name="ipv4"></a>IPv4-alapú

A következő példa bemutatja, hogyan PTR típusú rekord létrehozása az Azure DNS-névkeresési DNS-zóna folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1.  A **DNS-zóna** panel tetején válassza a **+ Rekordhalmaz** elemet a **Rekordhalmaz hozzáadása** panel megnyitásához.

 ![DNS-zóna](./media/dns-reverse-dns-hosting/figure4.png)

1. Az a **adja hozzá a rekordhalmaz** panelen. 
1. Válassza ki **PTR** a rekordból "**típus**" menü.  
1. A PTR típusú rekord rekordhalmaz nevét kell lennie a IPv4-címét a többi fordított sorrendben. Ebben a példában az első három oktettjének már fel vannak töltve a zóna nevét (.2.0.192) részeként. Ezért csak az utolsó oktett van megadva a neve mezőben. Például nevezze el az volt a rekordhalmaz "**15**" amelynek IP-cím 192.0.2.15 erőforrás.  
1. Az a "**tartománynév**" mezőbe írja be a teljesen minősített tartománynevét (FQDN) az erőforrás, az IP-cím használatával.
1. Válassza a panel alján található **OK** gombot a DNS-rekord létrehozásához.

 ![rekordhalmaz hozzáadása](./media/dns-reverse-dns-hosting/figure5.png)

A következő példák hogyan befejezheti a feladatot a PowerShell és a AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

A következő példa bemutatja, hogyan új "PTR" rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. Felső részén a **DNS-zóna panel**, jelölje be **+ rekordhalmaz** megnyitásához a **adja hozzá a rekordhalmaz** panelen.

  ![DNS-zónák panel](./media/dns-reverse-dns-hosting/figure6.png)

2. Az a **adja hozzá a rekordhalmaz** panelen. 
3. Válassza ki **PTR** a rekordból "**típus**" menü.  
4. A PTR típusú rekord rekordhalmaz nevét kell lennie a IPv6-címet a többi fordított sorrendben. Nem minden nulla tömörítési tartalmaznia kell. Ebben a példában az első 64 bit, az IPv6-már fel vannak töltve a zóna nevét (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa) részeként. Ezért csak az utolsó 64 bites megadva, a név mezőben. Az IP-cím utolsó 64 bites időszak használja, mint az elválasztó mindegyik hexadecimális szám között fordított sorrendben kerülnek. Például nevezze el az volt a rekordhalmaz "**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**" amelynek IP-cím 2001:0db8:abdc:0000:f524:10bc:1af9:405e erőforrás.  
5. Az a "**tartománynév**" mezőbe írja be a teljesen minősített tartománynevét (FQDN) az erőforrás, az IP-cím használatával.
6. Válassza a panel alján található **OK** gombot a DNS-rekord létrehozásához.

![Adja hozzá a rekordhalmaz panel](./media/dns-reverse-dns-hosting/figure7.png)

A következő példák hogyan befejezheti a feladatot a PowerShell és a AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>A rekordok megtekintése

A létrehozott rekordok megtekintéséhez keresse meg a DNS-zónát az Azure portálon. Az alsó részén a **DNS-zóna** panelen láthatja, hogy a rekordokat a DNS-zónához. Meg kell jelennie az alapértelmezett NS és SOA típusú rekordoknak, amelyek minden zónában létrejönnek, valamint az összes új létrehozott rekordnak.

### <a name="ipv4"></a>IPv4-alapú

DNS zóna panelen IPv4 PTR-rekordok:

![DNS-zónák panel](./media/dns-reverse-dns-hosting/figure8.png)

A következő példák szemléltetik a PowerShell vagy az Azure parancssori felület használatával PTR-rekordok megtekintése:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

DNS zóna panelen IPv6 PTR-rekordok:

![DNS-zónák panel](./media/dns-reverse-dns-hosting/figure9.png)

A következő példák a PowerShell és a AzureCLI a rekordok megtekintése:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>GYIK

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Képes-e üzemeltetni névkeresési DNS-címkeresési zónák az Internetszolgáltató által hozzárendelt IP-címblokkok az Azure DNS szolgáltatásra?

Igen. A névkeresési (ARPA) a saját IP-címtartományok, az Azure DNS-zónákat üzemeltető teljes mértékben támogatott.

A névkeresési zóna létrehozása az Azure DNS-amint ez a cikk azt, akkor az Internetszolgáltatótól működik [delegálása a zóna](dns-domain-delegation.md).  A PTR rekordok minden névkeresési ugyanúgy, mint más rekordtípusokhoz majd felügyelheti.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>A fordított DNS keresési zónához költségének üzemeltető mennyi használ?

A címfeloldási DNS-zóna üzemeltető, az az Internetszolgáltató által hozzárendelt IP-Címblokk az Azure DNS-rendszer díjakon [Azure DNS normál díjszabás](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>IPv4 és IPv6 címek az Azure DNS-névkeresési DNS-címkeresési zónák is futtatni?

Igen. Ez a cikk ismerteti az IPv4 és IPv6 névkeresési DNS-címkeresési zónák létrehozása az Azure DNS-ben.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Beimportálhatok egy meglévő címfeloldási DNS-zóna?

Igen. Az Azure CLI segítségével importálja a meglévő DNS-zónákat az Azure DNS-ben. Ez a módszer a címkeresési zónák és a névkeresési zóna listáját.

További információkért lásd: [importálása és exportálása a DNS-zónafájlját az Azure parancssori felület használatával](dns-import-export.md).

## <a name="next-steps"></a>Következő lépések

A címfeloldási DNS-további információkért lásd: [Wikipedia névkeresési DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [címfeloldási DNS-rekordjait az Azure-szolgáltatások kezelése](dns-reverse-dns-for-azure-services.md).
