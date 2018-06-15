---
title: Az Azure DNS-névkeresési DNS-címkeresési zónák gazdagép |} Microsoft Docs
description: A névkeresési DNS-címkeresési zónák az IP-címtartományok tárolásához Azure DNS használata
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: c5d53838ffb77ff8d33fee9d10aea4a74f3c523c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32773680"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Állomás névkeresési DNS-címkeresési zónák Azure DNS-ben

Ez a cikk azt ismerteti, hogyan a hozzárendelt IP-címtartományokhoz az Azure DNS-névkeresési DNS-címkeresési zónák üzemeltetésére. Az IP-címtartományai a névkeresési zónák által képviselt kell rendelni a szervezetben, általában az Internetszolgáltató által.

Egy Azure tulajdonában lévő IP-cím, az Azure service rendelt címfeloldási DNS konfigurálásával kapcsolatban lásd: [konfigurálása az Azure-ban tárolt szolgáltatások fordított irányú DNS](dns-reverse-dns-for-azure-services.md).

A cikk elolvasása előtt meg kell ismernie kell a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).

Ez a cikk végigvezeti a lépéseken, az első névkeresési DNS-zóna-rekord létrehozása az Azure portál, Azure PowerShell, Azure CLI 1.0 vagy Azure CLI 2.0 használatával.

## <a name="create-a-reverse-lookup-dns-zone"></a>A névkeresési DNS-zóna létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a **Hub** menüjében válassza **új** > **hálózati**, majd válassza ki **DNS-zóna**.

   !["DNS-zóna" kiválasztása](./media/dns-reverse-dns-hosting/figure1.png)

1. Az a **hozzon létre DNS-zóna** ablaktáblán, a DNS-zóna neve. A zóna nevét kialakított az IPv4 és IPv6-előtagok másképp van. Kövesse az utasításokat a [IPv4](#ipv4) vagy [IPv6](#ipv6) a zóna nevét. Amikor elkészült, válassza ki a **létrehozása** a zóna létrehozása.

### <a name="ipv4"></a>IPv4

Egy IPv4 névkeresési zóna neve, amely az IP-címtartományt alapul. A következő formátumúnak kell lennie: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Tekintse meg a [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Classless névkeresési DNS-címkeresési zónák létrehozásakor az Azure DNS-használnia kell a kötőjel (`-`) helyett egy perjel (`/`) a zóna nevében.
>
> Például az IP-címtartomány 192.0.2.128/26 kell használnia `128-26.2.0.192.in-addr.arpa` ahelyett, hogy a zóna neveként `128/26.2.0.192.in-addr.arpa`.
>
> Bár a DNS-szabványokból mindkét módszer támogatja, Azure DNS-ben nem támogatja a DNS-zóna nevét, amely tartalmazza a perjel (`/`) karaktert.

A következő példa bemutatja, hogyan hozzon létre egy C osztályú címfeloldási DNS-zóna nevű `2.0.192.in-addr.arpa` az Azure DNS az Azure-portálon:

 !["DNS-zóna létrehozása" ablaktáblán, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure2.png)

**Erőforráscsoport helye** határozza meg az erőforrásnak a helyét. Ne legyen hatással van a DNS-zónát. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

A következő példák bemutatják, hogyan befejezheti a feladatot az Azure PowerShell és az Azure parancssori felület használatával.

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

 !["DNS-zóna létrehozása" ablaktáblán, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure3.png)

**Erőforráscsoport helye** határozza meg az erőforrásnak a helyét. Ne legyen hatással van a DNS-zónát. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

A következő példák bemutatják, hogyan befejezheti a feladatot az Azure PowerShell és az Azure parancssori felület használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>A címfeloldási DNS-zóna delegálása

Most, hogy létrehozta a DNS-névkeresési zóna, győződjön meg arról, hogy a zóna van átadva a szülőzónában. DNS-delegálás lehetővé teszi, hogy a DNS-név feloldása folyamat a névkeresési DNS-címkeresési zónát üzemeltető névkiszolgálókat. Ezeket a kiszolgálókat az IP-címek tartománya a majd válaszolhat névkeresési DNS-lekérdezések.

Címkeresési zónák, DNS-zóna delegálása folyamata ismertetett [tartomány delegálása az Azure DNS-](dns-delegate-domain-azure-dns.md). A delegálás névkeresési zónák ugyanúgy működik. Az egyetlen különbség az, hogy szeretné-e névkiszolgálókat konfigurálása biztosító a tartományregisztráló neve helyett az IP-címtartomány a megadott.

## <a name="create-a-dns-ptr-record"></a>DNS PTR-rekord létrehozása

### <a name="ipv4"></a>IPv4

A következő példa bemutatja, hogyan PTR típusú rekord létrehozása az Azure DNS-névkeresési DNS-zóna folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. Felső részén a **DNS-zóna** ablaktáblán válassza előbb **+ rekordhalmaz** megnyitásához a **adja hozzá a rekordhalmaz** ablaktáblán.

   ![A rekordhalmaz létrehozása gomb](./media/dns-reverse-dns-hosting/figure4.png)

1. A PTR típusú rekord rekordhalmaz nevét kell lennie a IPv4-címét a többi fordított sorrendben. 

   Ebben a példában az első három oktettjének már fel vannak töltve a zóna nevét (.2.0.192) részeként. Ezért csak az utolsó oktett van megadva a **neve** mezőbe. Például előfordulhat, hogy nevezze el a rekordhalmaz **15** egy erőforráshoz, amelynek IP-cím 192.0.2.15.  
1. A **típus**, jelölje be **PTR**.  
1. A **tartománynév**, adja meg a teljesen minősített tartománynevét (FQDN) használja az IP-cím erőforrás.
1. Válassza ki **OK** jegyezze fel a DNS létrehozásához ablaktábla alján.

 !["Rekordhalmaz hozzáadása" ablaktáblán, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure5.png)

A következő példák szemléltetik a PowerShell vagy Azure CLI segítségével befejezheti a feladatot.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

A következő példa bemutatja, hogyan új PTR típusú rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. Felső részén a **DNS-zóna** ablaktáblán válassza előbb **+ rekordhalmaz** megnyitásához a **adja hozzá a rekordhalmaz** ablaktáblán.

   ![A rekordhalmaz létrehozása gomb](./media/dns-reverse-dns-hosting/figure6.png)

2. A PTR típusú rekord rekordhalmaz nevét kell lennie a IPv6-címet a többi fordított sorrendben. Nem minden nulla tömörítési tartalmaznia kell. 

   Ebben a példában az első 64 bit, az IPv6-már fel vannak töltve a zóna nevét (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa) részeként. Csak az utolsó 64 bites megadott, ezért a **neve** mezőbe. Az IP-cím utolsó 64 bites fordított sorrendben az idézőjellel minden hexadecimális szám közötti időtartam lett megadva. Például előfordulhat, hogy nevezze el a rekordhalmaz **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** egy erőforráshoz, amelynek IP-cím 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. A **típus**, jelölje be **PTR**.  
4. A **tartománynév**, adja meg az erőforrást, amely használja az IP-cím teljes Tartománynevét.
5. Válassza ki **OK** jegyezze fel a DNS létrehozásához ablaktábla alján.

!["Rekordhalmaz hozzáadása" ablaktáblán, a mezők kitöltése](./media/dns-reverse-dns-hosting/figure7.png)

A következő példák szemléltetik a PowerShell vagy Azure CLI segítségével befejezheti a feladatot.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>A rekordok megtekintése

A létrehozott rekordok megtekintéséhez keresse meg azt a DNS-zónát az Azure portálon. Az alsó részén a **DNS-zóna** panelen megtekintheti a rekordokat a DNS-zónához. Az alapértelmezett NS és SOA rekordokat, valamint bármely új azt jelzi, hogy a létrehozott kell megjelennie. Az NS és SOA rekordokat minden zóna jönnek létre. 

### <a name="ipv4"></a>IPv4

A **DNS-zóna** ablaktábla megjeleníti azokat a IPv4 PTR-rekordok:

![IPv4-rekordok "DNS-zóna" ablaktábla](./media/dns-reverse-dns-hosting/figure8.png)

A következő példák szemléltetik a PTR rekordok megtekintése a PowerShell vagy az Azure parancssori felület használatával.

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

A **DNS-zóna** ablaktábla megjeleníti azokat a IPv6 PTR-rekordok:

![IPv6-rekordokat "DNS-zóna" ablaktábla](./media/dns-reverse-dns-hosting/figure9.png)

A következő példák szemléltetik a rekordok megtekintése a PowerShell vagy az Azure parancssori felület használatával.

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

A névkeresési zóna létrehozása az Azure DNS, a cikkben leírtak szerint, és majd használata Internetszolgáltató [delegálása a zóna](dns-domain-delegation.md). A PTR rekordok minden névkeresési ugyanúgy, mint más rekordtípusokhoz majd felügyelheti.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>A fordított DNS keresési zónához költségének üzemeltető mennyi használ?

A címfeloldási DNS-zóna üzemeltető, az az Internetszolgáltató által hozzárendelt IP-Címblokk az Azure DNS-rendszer díjakon [Azure DNS normál díjszabás](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>IPv4 és IPv6 címek az Azure DNS-névkeresési DNS-címkeresési zónák is futtatni?

Igen. Ez a cikk ismerteti az IPv4 és IPv6 névkeresési DNS-címkeresési zónák létrehozása az Azure DNS-ben.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Beimportálhatok egy meglévő címfeloldási DNS-zóna?

Igen. Azure CLI segítségével importálja a meglévő DNS-zónákat az Azure DNS-ben. Ez a módszer a címkeresési zónák és a névkeresési zóna listáját.

További információkért lásd: [importálása és exportálása a DNS-zónafájlját az Azure parancssori felület használatával](dns-import-export.md).

## <a name="next-steps"></a>További lépések

A címfeloldási DNS-további információkért lásd: [Wikipedia névkeresési DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Megtudhatja, hogyan [címfeloldási DNS-rekordjait az Azure-szolgáltatások kezelése](dns-reverse-dns-for-azure-services.md).
