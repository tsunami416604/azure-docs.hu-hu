---
title: Állomás névfeloldási DNS-keresési zónái az Azure DNS-ben
description: Megtudhatja, hogy miként üzemeltetheti az Azure DNS-t az IP-tartományok névkeresési zónáinak üzemeltetésére
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454261"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Állomás névfeloldási DNS-keresési zónái az Azure DNS-ben

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan üzemeltetheti a névfeloldási DNS-keresési zónák at a hozzárendelt IP-tartományok az Azure DNS-ben. A névkeresési zónák által képviselt IP-tartományokat hozzá kell rendelni a szervezethez, általában az isp-nek.

Az Azure-szolgáltatáshoz rendelt Azure-tulajdonú IP-cím fordított DNS-címének konfigurálásához olvassa el a [Fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz című témakört.](dns-reverse-dns-for-azure-services.md)

A cikk elolvasása előtt ismernie kell a fordított DNS és támogatás áttekintését az [Azure-ban.](dns-reverse-dns-overview.md)

Ez a cikk végigvezeti az első névkeresési DNS-zóna létrehozásának lépésein, és az Azure Portalon, az Azure PowerShellen, az Azure Classic CLI-n vagy az Azure CLI-n keresztül rögzítheti az első névkeresési DNS-zónát.

## <a name="create-a-reverse-lookup-dns-zone"></a>Névkeresési DNS-zóna létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A **Központ** menüben válassza az **Új** > **hálózat**lehetőséget, majd válassza a **DNS-zóna**lehetőséget.

   !["DNS-zóna" kiválasztása](./media/dns-reverse-dns-hosting/figure1.png)

1. A **DNS-zóna létrehozása** ablaktáblán nevezze el a DNS-zónát. A zóna neve másképp van kikészítve az IPv4- és IPv6-előtagokhoz. Az [IPv4](#ipv4) vagy az [IPv6](#ipv6) utasítások segítségével nevezze el a zónát. Ha végzett, válassza a **Létrehozás** gombot a zóna létrehozásához.

### <a name="ipv4"></a>IPv4

Az IPv4 névkeresési zóna neve az általa képviselt IP-tartományon alapul. Meg kell a következő `<IPv4 network prefix in reverse order>.in-addr.arpa`formátumban: . Példák: [A fordított DNS és támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md#ipv4)című témakörben talál.

> [!NOTE]
> Amikor osztály nélküli névfeloldási DNS-keresési zónákat hoz létre az Azure`-`DNS-ben, kötőjelet ( )`/`kell használnia a zóna nevében.
>
> A 192.0.2.128/26 IP-tartományban például `128-26.2.0.192.in-addr.arpa` a helyett a `128/26.2.0.192.in-addr.arpa`zónanevet kell használnia.
>
> Bár a DNS-szabványok mindkét módszert támogatják, az Azure DNS nem`/`támogatja a DNS-zónaneveket, amelyek perjel ( ) karaktert tartalmaznak.

A következő példa bemutatja, hogyan hozhat `2.0.192.in-addr.arpa` létre az Azure DNS-ben az Azure PORTALon keresztül elnevezett C osztályú fordított DNS-zónát:

 !["DNS-zóna létrehozása" ablaktábla, kitöltött mezőkkel](./media/dns-reverse-dns-hosting/figure2.png)

**Az erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

Az alábbi példák bemutatják, hogyan hajtsa végre ezt a feladatot az Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Az IPv6 névkeresési zóna nevének a következő `<IPv6 network prefix in reverse order>.ip6.arpa`formában kell lennie: .  Példák: [A fordított DNS és támogatás áttekintése az Azure-ban](dns-reverse-dns-overview.md#ipv6)című témakörben talál.


A következő példa bemutatja, hogyan hozhat létre egy `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` IPv6-névvel ellátott DNS-keresési zónát az Azure DNS-ben az Azure Portalon keresztül:

 !["DNS-zóna létrehozása" ablaktábla, kitöltött mezőkkel](./media/dns-reverse-dns-hosting/figure3.png)

**Az erőforráscsoport helye** határozza meg az erőforráscsoport helyét. Nincs hatással a DNS-zónára. A DNS-zóna helye mindig „globális”, és nem jelenik meg.

Az alábbi példák bemutatják, hogyan hajtsa végre ezt a feladatot az Azure PowerShell és az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>NÉV-DNS keresési zóna delegálása

Most, hogy létrehozta a névfeloldási DNS-keresési zónát, gondoskodnia kell arról, hogy a zóna delegálása a szülőzónából legyen. A DNS-delegálás lehetővé teszi, hogy a DNS-névfeloldási folyamat megtalálja a névkiszolgálókat, amelyek a névfeloldási DNS-keresési zónát üzemeltetik. Ezek a névkiszolgálók ezután válaszolhatnak a címtartományban lévő IP-címek DNS-sztornírozási lekérdezéseire.

A névkeresési zónák esetében a DNS-zóna delegálásának folyamatát a [Tartomány delegálása az Azure DNS-re](dns-delegate-domain-azure-dns.md)című részben ismerteti. A névkeresési zónák delegálása ugyanúgy működik. Az egyetlen különbség az, hogy a névkiszolgálókat a tartománynév-regisztráló helyett az IP-tartományt megadva iston szeretné konfigurálni.

## <a name="create-a-dns-ptr-record"></a>DNS PTR rekord létrehozása

### <a name="ipv4"></a>IPv4

A következő példa végigvezeti a PTR-rekord létrehozásának folyamatán egy fordított DNS-zónában az Azure DNS-ben. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. A **DNS-zóna** ablaktábla tetején válassza a **+ Rekordkészlet** lehetőséget a **Rekordkészlet hozzáadása** ablaktábla megnyitásához.

   ![Rekordkészlet létrehozására szolgáló gomb](./media/dns-reverse-dns-hosting/figure4.png)

1. A PTR-rekord rekordkészletének névnek fordított sorrendben kell lennie az IPv4-cím többi részének. 

   Ebben a példában az első három oktett már fel van töltve a zónanév részeként (.2.0.192). Ezért csak az utolsó oktett van megadva a **Név** mezőben. Elnevezheti például a **15-ös** rekordkészletet egy 192.0.2.15 IP-címmel tartozó erőforráshoz.  
1. A **Típus mezőben**válassza a **PTR**lehetőséget.  
1. A **TARTOMÁNYNÉV**mezőbe írja be az IP-címet használó erőforrás teljesen minősített tartománynevét (FQDN).
1. A DNS-rekord létrehozásához válassza az **OK** gombot az ablaktábla alján.

   !["Rekordkészlet hozzáadása" ablaktábla, kitöltött mezőkkel](./media/dns-reverse-dns-hosting/figure5.png)

Az alábbi példák bemutatják, hogyan hajtsa végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

A következő példa végigvezeti az új PTR-rekord létrehozásának folyamatán. Más rekordtípusok és meglévő rekordok módosítása esetén lásd [a DNS-rekordok és -rekordhalmazok az Azure Portallal való kezelésével kapcsolatos](dns-operations-recordsets-portal.md) témakört.

1. A **DNS-zóna** ablaktábla tetején válassza a **+ Rekordkészlet** lehetőséget a **Rekordkészlet hozzáadása** ablaktábla megnyitásához.

   ![Rekordkészlet létrehozására szolgáló gomb](./media/dns-reverse-dns-hosting/figure6.png)

2. A PTR-rekord rekordkészletének névnek fordított sorrendben kell lennie az IPv6-cím többi részének. Nem tartalmazhat nulla tömörítést. 

   Ebben a példában az IPv6 első 64 bitje már a zónanév részeként van feltöltve (0.0.0.0.c.d.b.8.b.d.0.0.0.2.ip6.arpa). Ezért csak az utolsó 64 bit et adja meg a **Név** mező. Az IP-cím utolsó 64 bitje fordított sorrendben kerül beírásra, az egyes hexadecimális számok közötti elhatároló ponttal. Elnevezheti például a rekordkészletét **e.5.0.9.9.f.a.c.b.0.1.4.2.5.f** néven egy 2001:0db8:abdc:0000:f524:10bc:1af9:405e IP-című erőforrásesetében.  
3. A **Típus mezőben**válassza a **PTR**lehetőséget.  
4. A **TARTOMÁNYNÉV**mezőbe írja be az IP-címet használó erőforrás teljes tartománynevét.
5. A DNS-rekord létrehozásához válassza az **OK** gombot az ablaktábla alján.

!["Rekordkészlet hozzáadása" ablaktábla, kitöltött mezőkkel](./media/dns-reverse-dns-hosting/figure7.png)

Az alábbi példák bemutatják, hogyan hajtsa végre ezt a feladatot a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>A rekordok megtekintése

A létrehozott rekordok megtekintéséhez keresse meg a DNS-zónát az Azure Portalon. A **DNS-zóna** ablaktábla alsó részén a DNS-zóna rekordjai láthatók. Ekkor megjelennek az alapértelmezett NS- és SOA-rekordok, valamint a létrehozott új rekordok. Az NS és SOA rekordok minden zónában létrejönnek. 

### <a name="ipv4"></a>IPv4

A **DNS-zóna** ablaktábla az IPv4 PTR rekordokat jeleníti meg:

!["DNS-zóna" ablaktábla IPv4-rekordokkal](./media/dns-reverse-dns-hosting/figure8.png)

Az alábbi példák bemutatják, hogyan tekintheti meg a PTR-rekordokat a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

A **DNS-zóna** ablaktábla az IPv6 PTR rekordokat jeleníti meg:

!["DNS-zóna" ablaktábla IPv6-rekordokkal](./media/dns-reverse-dns-hosting/figure9.png)

Az alábbi példák bemutatják, hogyan tekintheti meg a rekordokat a PowerShell vagy az Azure CLI használatával.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>GYIK

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Üzemeltethetek névfeloldási DNS-keresési zónákat az internetszolgáltatóhoz rendelt IP-blokkokhoz az Azure DNS-ben?

Igen. Az Azure DNS-ben a saját IP-tartományaihoz tartozó névkeresési (ARPA) zónák üzemeltetése teljes mértékben támogatott.

Hozza létre a névkeresési zónát az Azure DNS-ben a jelen cikkben leírtak szerint, majd az internetszolgáltatóval együttműködve [delegálja a zónát.](dns-domain-delegation.md) Ezután az egyes névlekérdezések PTR rekordjait ugyanúgy kezelheti, mint a többi bejegyzéstípust.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Mennyibe kerül a névfeloldási DNS-keresési zónám üzemeltetése?

Az ISP által hozzárendelt IP-blokk névkeresési zónájának üzemeltetése az Azure DNS-ben [a szokásos Azure DNS-díjak mellett](https://azure.microsoft.com/pricing/details/dns/)kerül felszámításra.

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Üzemeltethetek névfeloldási DNS-keresési zónákat az Azure DNS-ben iPv4- és IPv6-címekhez is?

Igen. Ez a cikk bemutatja, hogyan hozhat létre IPv4- és IPv6-névkeresési zónákat az Azure DNS-ben.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Importálhatok meglévő névfeloldási DNS-keresési zónát?

Igen. Az Azure CLI segítségével importálhatja a meglévő DNS-zónákat az Azure DNS-be. Ez a módszer mind a célkeresési zónákban, mind a névkeresési zónákban működik.

További információt a [DNS-zónafájl importálása és exportálása az Azure CLI használatával című](dns-import-export.md)témakörben talál.

## <a name="next-steps"></a>További lépések

A fordított DNS-ről további információt a [Wikipédia névfeloldási DNS-keresése című témakörben talál.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Ismerje meg, hogyan kezelheti az [Azure-szolgáltatások fordított DNS-rekordjait.](dns-reverse-dns-for-azure-services.md)
