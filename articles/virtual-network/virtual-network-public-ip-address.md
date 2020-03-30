---
title: Azure nyilvános IP-cím létrehozása, módosítása vagy törlése | Microsoft dokumentumok
description: További információ nyilvános IP-cím létrehozásáról, módosításáról és törléséről.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 38ff40b537730418fe9f0f8295884dae98a2fe0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244913"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Nyilvános IP-címek létrehozása, módosítása és törlése

További információ a nyilvános IP-címről, valamint a nyilvános IP-cím létrehozásáról, módosításáról és törléséről. A nyilvános IP-cím saját konfigurálható beállításokkal rendelkező erőforrás. Nyilvános IP-cím hozzárendelése egy nyilvános IP-címeket támogató Azure-erőforráshoz:
- Bejövő kommunikáció az internetről az erőforrás, például az Azure virtuális gépek (VM), az Azure Application Gateways, az Azure Load Balancers, az Azure VPN-átjárók és mások. Továbbra is kommunikálhat bizonyos erőforrásokkal, például a virtuális gépekkel az internetről, ha egy virtuális gép nem rendelkezik hozzá rendelt nyilvános IP-címmel, feltéve, hogy a virtuális gép egy terheléselosztó háttérkészlet része, és a terheléselosztó nyilvános IP-címet kap. Annak megállapításához, hogy egy adott Azure-szolgáltatás egy erőforrása hozzárendelhető-e egy nyilvános IP-címet, vagy egy másik Azure-erőforrás nyilvános IP-címén keresztül kommunikálható- e, tekintse meg a szolgáltatás dokumentációját.
- Kimenő internetkapcsolat kiszámítható IP-címmel. Például egy virtuális gép kommunikálhat az internetre anélkül, hogy egy nyilvános IP-címet rendelt hozzá, de a cím hálózati cím az Azure által lefordított egy előre nem látható nyilvános címet, alapértelmezés szerint. Nyilvános IP-cím hozzárendelése egy erőforráshoz lehetővé teszi annak ismeretét, hogy melyik IP-címet használja a kimenő kapcsolathoz. Bár kiszámítható, a cím változhat, attól függően, hogy a hozzárendelési módszer választott. További információt a [Nyilvános IP-cím létrehozása](#create-a-public-ip-address)című témakörben talál. Ha többet szeretne tudni az Azure-erőforrásokból érkező kimenő kapcsolatokról, [olvassa el a Kimenő kapcsolatok ismertetése.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.31-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

A fiók, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

A nyilvános IP-címek névleges díja. A díjszabás megtekintéséhez olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Adja meg a *nyilvános IP-címet* a *Keresés a piactéren* mezőbe. Amikor **a nyilvános IP-cím** megjelenik a keresési eredmények között, jelölje ki azt.
3. A **Nyilvános IP-cím csoportban**válassza a **Létrehozás lehetőséget.**
4. Írja be vagy jelölje ki a következő beállítások értékeit a **Nyilvános IP-cím létrehozása**csoportban, majd válassza a **Létrehozás lehetőséget:**

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |IP-verzió|Igen| Válassza az IPv4 vagy az IPv6 vagy a Mindkettő lehetőséget. Ha mindkettőt választja, a 2 nyilvános IP-cím létrehozása lesz- 1 IPv4-cím és 1 IPv6-cím. További információ [az IPv6-ról az Azure VNET-kben.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |SKU|Igen|A termékváltozatok bevezetése előtt létrehozott összes nyilvános IP-cím **alapszintű** termékváltozat nyilvános IP-cím. A nyilvános IP-cím létrehozása után a termékváltozat nem módosítható. Egy önálló virtuális gép, egy rendelkezésre állási csoporton belüli virtuális gépek vagy a virtuálisgép-méretezési csoportok használhatják az alapszintű vagy szabványos termékkészleteket. A virtuális gépek közötti keverés a rendelkezésre állási csoportokon vagy méretezési csoportokon vagy önálló virtuális gépeken belül nem engedélyezett. **Alapszintű** Termékváltozat: Ha nyilvános IP-címet hoz létre egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, a **rendelkezésre állási zóna** beállítása alapértelmezés szerint *Nincs.* Az alapvető nyilvános IP-k nem támogatják a rendelkezésre állási zónákat. **Standard (standard)** Termékváltozat: A szabványos termékváltozat nyilvános IP-cím társítható egy virtuális gép vagy egy terheléselosztó előtér. Ha nyilvános IP-címet hoz létre egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, a **rendelkezésre állási zóna** beállítása alapértelmezés szerint *zónaredundáns.* A rendelkezésre állási zónákról további információt a **Rendelkezésre állási zóna** beállításcímű témakörben talál. A szabványos termékváltozat szükséges, ha a címet egy standard terheléselosztóhoz társítja. Ha többet szeretne megtudni a szabványos terheléselosztókról, olvassa el az [Azure terheléselosztó szabványos termékváltozatát.](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
   |Név|Igen|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |IP-cím hozzárendelése|Igen|**Dinamikus:** A dinamikus címek csak akkor vannak hozzárendelve, ha egy nyilvános IP-cím egy Azure-erőforráshoz van társítva, és az erőforrás először elindul. A dinamikus címek módosíthatók, ha egy erőforráshoz, például egy virtuális géphez vannak rendelve, és a virtuális gép leáll (felvan osztva), majd újraindulnak. A cím ugyanaz marad, ha egy virtuális gépet újraindít vagy leállít (de nem felszabadított). A dinamikus címek akkor szabadulnak fel, ha egy nyilvános IP-címerőforrás takarásban van egy olyan erőforrástól, amelyhez társítva van. **Statikus:** A statikus címek nyilvános IP-cím létrehozásakor lesznek hozzárendelve. A statikus címek csak a nyilvános IP-címerőforrás törlése után adják ki őket. Ha a cím nincs erőforráshoz társítva, a cím létrehozása után módosíthatja a hozzárendelési módszert. Ha a cím egy erőforráshoz van társítva, előfordulhat, hogy nem tudja módosítani a hozzárendelési módszert. Ha az **IP-verzióhoz**az *IPv6 lehetőséget* választja, a hozzárendelési módszernek *dinamikusnak* kell lennie az egyszerű termékváltozathoz.  A szabványos termékváltozat-címek *statikusak* mind az IPv4, mind az IPv6 protokollesetében. |
   |Tétlen időelés (perc)|Nem|Hány perc ig kell nyitva tartani a TCP- vagy HTTP-kapcsolatot anélkül, hogy az ügyfelekre támaszkodna az életben maradó üzenetek küldéséhez. Ha az IPv6 **protokollt választja az IP-verzióhoz,** ez az érték nem módosítható. |
   |DNS-névcímke|Nem|Egyedinek kell lennie az Azure-helyen belül, amelyben létrehozza a nevet (az összes előfizetésben és az összes ügyfélnél). Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, így csatlakozhat egy erőforráshoz a névvel. Az Azure hozzáfűzegy alapértelmezett alhálózatot, például *location.cloudapp.azure.com* (ahol a hely a kiválasztott hely) a megadott névhez, a teljesen minősített DNS-név létrehozásához. Ha úgy dönt, hogy mindkét címverziót létrehozza, ugyanaz a DNS-név lesz hozzárendelve az IPv4- és az IPv6-címhez is. Az Azure alapértelmezett DNS-e IPv4 A és IPv6 AAAA névrekordokat is tartalmaz, és mindkét rekorddal válaszol, amikor a DNS-nevet keresik. Az ügyfél kiválasztja, hogy melyik címmel (IPv4 vagy IPv6) kommunikáljon. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
   |Név (Csak akkor látható, ha **a mindkettő**IP-verzióját választja)|Igen, ha **mindkét** IP-verziót választja|A névnek különböznie kell a listában az első **névhez** megadott névétól. Ha úgy dönt, hogy iPv4-címet és IPv6-címet is létrehoz, a portál két különálló nyilvános IP-címerőforrást hoz létre, amelyek közül az egyikhez minden IP-címverzió hozzá van rendelve.|
   |IP-címhozzárendelés (Csak akkor látható, ha **mindkettő**IP-verzióját választja)|Igen, ha **mindkét** IP-verziót választja|Ugyanazok a korlátozások, mint a fenti IP-címhozzárendelés|
   |Előfizetés|Igen|Ugyanebben az [előfizetésben](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) kell léteznie, mint az erőforrás, amelyhez a nyilvános IP-cím társítja.|
   |Erőforráscsoport|Igen|Létezhet ugyanabban az [erőforráscsoportban,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) mint az az erőforrás, amelyhez a nyilvános IP-címet társítja.|
   |Hely|Igen|Kell léteznie ugyanazon a [helyen](https://azure.microsoft.com/regions), más néven régióban, mint az erőforrás, amelyhez a nyilvános IP-cím társítja.|
   |Rendelkezésreállási zóna| Nem | Ez a beállítás csak akkor jelenik meg, ha támogatott helyet választ. A támogatott helyek listáját a [Rendelkezésre állási zónák – áttekintés című témakörben](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)találja. Ha az **Alaptermékváltozat** ot választotta, a *Nincs* beállítás automatikusan kiválasztásra kerül. Ha egy adott zónát szeretne garantálni, kiválaszthat egy adott zónát. Mindkét választás nem zónaredundáns. Ha a **Szabványos** termékváltozat: Zónaredundáns automatikusan kiválasztva, és az adatelérési út rugalmas zóna hiba. Ha egy adott zónát szeretne garantálni, amely nem felel meg a zónameghibásodásnak, kiválaszthat egy adott zónát.

**Parancsok**

Bár a portál lehetővé teszi két nyilvános IP-cím-erőforrás (egy IPv4 és egy IPv6) létrehozását, a következő CLI- és PowerShell-parancsok egy erőforrást hoznak létre az egyik vagy a másik IP-verzióhoz. Ha két nyilvános IP-címerőforrást szeretne, egyet-egyet minden IP-verzióhoz, kétszer kell futtatnia a parancsot, különböző neveket és IP-verziókat adva meg a nyilvános IP-cím erőforrásokhoz.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[Új-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Nyilvános IP-cím megtekintése, beállításainak módosítása vagy törlése

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a nyilvános *IP-címet.* Ha **nyilvános IP-címek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Válassza ki annak a nyilvános IP-címnek a nevét, amelyet meg szeretne tekinteni, módosítani vagy törölni szeretné a listából.
3. Hajtsa végre az alábbi lehetőségek egyikét, attól függően, hogy meg szeretné-e tekinteni, törölni vagy módosítani szeretné a nyilvános IP-címet.
   - **Nézet**: **Az Áttekintés** szakasz a nyilvános IP-cím kulcsbeállításait jeleníti meg, például azt a hálózati adaptert, amelyhez társítva van (ha a cím hálózati adapterhez van társítva). A portál nem jeleníti meg a cím verzióját (IPv4 vagy IPv6). A verzióadatok megtekintéséhez használja a PowerShell vagy a CLI parancsot a nyilvános IP-cím megtekintéséhez. Ha az IP-cím verziója IPv6, a hozzárendelt cím nem jelenik meg a portálon, a PowerShellben vagy a CLI-ben.
   - **Törlés**: A nyilvános IP-cím törléséhez válassza a **Törlés** lehetőséget az **Áttekintő** szakaszban. Ha a cím jelenleg IP-konfigurációhoz van társítva, nem törölhető. Ha a cím jelenleg egy konfigurációhoz van társítva, válassza a **Disszociálás** lehetőséget a cím IP-konfigurációtól való elhatárolt kiválasztásához.
   - **Módosítás**: válassza a **Konfiguráció**lehetőséget. Módosítsa a beállításokat a [Nyilvános IP-cím létrehozása](#create-a-public-ip-address)című 4. Ha egy IPv4-cím hozzárendelését statikusról dinamikusra szeretné módosítani, először el kell választania a nyilvános IPv4-címet attól az IP-konfigurációtól, amelyhez társítva van. Ezután módosíthatja a hozzárendelési módszert dinamikusra, és a **Társítás** lehetőséget választva az IP-címet ugyanahhoz az IP-konfigurációhoz, egy másik konfigurációhoz társíthatja, vagy elválaszthatóvá teheti. Nyilvános IP-cím szétjelölését az **Áttekintés** szakaszban válassza a **Disszociátika**lehetőséget.

   >[!WARNING]
   >Ha a hozzárendelési módszert statikusról dinamikusra módosítja, elveszíti a nyilvános IP-címhez rendelt IP-címet. Míg az Azure nyilvános DNS-kiszolgálók a statikus vagy dinamikus címek és bármely DNS-névcímke közötti leképezést tartanak fenn (ha definiált egyet), a dinamikus IP-cím a leállított (felszabadított) állapot bekapcsolása után változhat, amikor a virtuális gép elindul. A cím módosításának megakadályozásához rendeljen statikus IP-címet.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nyilvános ip lista](/cli/azure/network/public-ip#az-network-public-ip-list) a nyilvános IP-címek listázásához, az hálózati nyilvános ip [show](/cli/azure/network/public-ip#az-network-public-ip-show) a beállítások megjelenítéséhez; [az hálózati nyilvános ip frissítés](/cli/azure/network/public-ip#az-network-public-ip-update) frissítés; [az hálózati nyilvános ip törlés](/cli/azure/network/public-ip#az-network-public-ip-delete) törölni|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) egy nyilvános IP-címobjektum lekéréséhez és a beállítások megtekintéséhez, [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) a beállítások frissítéséhez; A törölni kívánt [eltávolítás-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)|

## <a name="assign-a-public-ip-address"></a>Nyilvános IP-cím hozzárendelése

Megtudhatja, hogyan rendelhet nyilvános IP-címet a következő forrásokhoz:

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép (létrehozáskor), vagy egy meglévő [virtuális géphez](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetre néző terheléselosztó](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Helyek közötti kapcsolat Azure VPN-átjáró használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure virtuális gép méretezési készlete](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címeken végzett feladatok végrehajtásához a fiókot hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                                             | Név                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Nyilvános IP-cím elolvasása                                          |
| Microsoft.Network/publicIPAddresses/write                          | Nyilvános IP-cím létrehozása vagy frissítése                           |
| Microsoft.Network/publicIPAddresses/delete                         | Nyilvános IP-cím törlése                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Nyilvános IP-cím társítása erőforráshoz                    |

## <a name="next-steps"></a>További lépések

- Nyilvános IP-cím létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaparancsfájlokkal vagy Azure [Resource Manager-sablonok](template-samples.md) használatával [Azure CLI](cli-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása nyilvános IP-címekre
