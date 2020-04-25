---
title: Azure nyilvános IP-cím létrehozása, módosítása vagy törlése | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, módosíthat vagy törölhet nyilvános IP-címeket.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: a03ace1553bd845d8a221e458fd47f3d8aa3d611
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146542"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Nyilvános IP-címek létrehozása, módosítása és törlése

Ismerje meg a nyilvános IP-címet, valamint azt, hogyan hozhat létre, módosíthat és törölhet egyet. A nyilvános IP-cím a saját konfigurálható beállításaival rendelkező erőforrás. Nyilvános IP-cím a nyilvános IP-címeket támogató Azure-erőforráshoz való hozzárendelésének engedélyezése:
- Az internetről az erőforrás felé irányuló bejövő kommunikáció, például Azure Virtual Machines (VM), Azure Application Gateway, Azure Load Balancer, Azure VPN Gateway és mások. Továbbra is kommunikálhat bizonyos erőforrásokkal, például a virtuális gépekkel, ha a virtuális gép nem rendelkezik nyilvános IP-címmel, feltéve, hogy a virtuális gép egy terheléselosztó háttér-készlet része, és a terheléselosztó egy nyilvános IP-címet kap. Annak megállapításához, hogy egy adott Azure-szolgáltatáshoz tartozó erőforrás hozzárendelhető-e nyilvános IP-címhez, vagy egy másik Azure-erőforrás nyilvános IP-címén keresztül kommunikálhat-e, tekintse meg a szolgáltatás dokumentációját.
- Az internet felé irányuló kimenő kapcsolat kiszámítható IP-cím használatával. Egy virtuális gép például a hozzá tartozó nyilvános IP-cím nélkül tud kommunikálni az internet felé, de a címe az Azure által fordított hálózati cím, amely alapértelmezés szerint nem előre jelezhető nyilvános cím. Egy nyilvános IP-cím erőforráshoz való hozzárendelésével megtudhatja, melyik IP-címet használja a kimenő kapcsolat. A megadott hozzárendelési módszertől függően előfordulhat, hogy a címe megváltozhat. További információt a [nyilvános IP-cím létrehozása](#create-a-public-ip-address)című témakörben talál. Ha többet szeretne megtudni az Azure-erőforrások kimenő kapcsolatairól, tekintse meg a [Kimenő kapcsolatok ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, https://portal.azure.comnyissa meg, majd jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia `az login` kell, hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

A nyilvános IP-címekhez névleges díj tartozik. A díjszabás megtekintéséhez olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. A *Keresés a piactéren* mezőbe írja be a *nyilvános IP-címet* . Ha a **nyilvános IP-cím** megjelenik a keresési eredmények között, válassza ki.
3. A **nyilvános IP-cím**területen válassza a **Létrehozás**lehetőséget.
4. Adja meg vagy válassza ki a következő beállítások értékeit a **nyilvános IP-cím létrehozása**területen, majd válassza a **Létrehozás**lehetőséget:

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |IP-verzió|Igen| Válassza az IPv4 vagy az IPv6 lehetőséget, vagy mindkettőt. Ha mindkettőt választja, 2 nyilvános IP-címet hoz létre – 1 IPv4-címet és 1 IPv6-címet. További információ az [IPv6-ról az Azure virtuális hálózatok](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Igen|A SKU-bevezetés előtt létrehozott összes nyilvános IP-cím **alapszintű** SKU nyilvános IP-címek. A nyilvános IP-cím létrehozása után az SKU nem módosítható. Az önálló virtuális gépek, a rendelkezésre állási csoporton belüli virtuális gépek, illetve a virtuálisgép-méretezési csoportok alapszintű vagy standard SKU-ket használhatnak. A rendelkezésre állási csoportokban vagy a méretezési csoportokban, illetve az önálló virtuális gépeken belüli virtuális gépek közötti adatkészletek nem engedélyezettek. **Alapszintű** SKU: Ha nyilvános IP-címet hoz létre a rendelkezésre állási zónákat támogató régióban, a **rendelkezésre állási zóna** beállítása alapértelmezés szerint *nincs* . Az alapszintű nyilvános IP-címek nem támogatják a rendelkezésre állási zónákat. **Standard szintű** SKU: A standard SKU nyilvános IP-címe egy virtuális géphez vagy egy terheléselosztó kezelőfelületéhez társítható. Ha olyan régióban hoz létre nyilvános IP-címet, amely támogatja a rendelkezésre állási zónákat, a **rendelkezésre állási zóna** beállítása alapértelmezés szerint a *zóna-redundáns* értékre van állítva. A rendelkezésre állási zónákról további információt a **rendelkezésre állási zóna** beállításában talál. A standard SKU megadása kötelező, ha a címeket standard Load Balancerhez rendeli. A standard Load balancerekkel kapcsolatos további tudnivalókért lásd: [Azure Load Balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
   |Name (Név)|Igen|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporthoz.|
   |IP-cím hozzárendelése|Igen|**Dinamikus:** A dinamikus címek hozzárendelése csak akkor történik meg, ha egy nyilvános IP-cím egy Azure-erőforráshoz van társítva, és az erőforrás első alkalommal indul el. A dinamikus címek megváltozhatnak, ha egy erőforráshoz (például virtuális géphez) vannak rendelve, és a virtuális gép le van állítva (felszabadítva), majd újraindul. A címnek a virtuális gép újraindításakor vagy leállításakor (de nem lefoglalt) marad. A dinamikus címek akkor jelennek meg, ha egy nyilvános IP-cím erőforrás le van rendelve egy hozzá társított erőforrásból. **Statikus:** A statikus címeket a rendszer a nyilvános IP-cím létrehozásakor rendeli hozzá. A statikus címek csak akkor jelennek meg, ha a nyilvános IP-cím erőforrás törlődik. Ha a címnek nincs erőforráshoz társítva, akkor a hozzárendelési módszert a létrehozás után módosíthatja. Ha a címe egy erőforráshoz van társítva, előfordulhat, hogy nem tudja módosítani a hozzárendelési módszert. Ha az *IPv6-ot* választja az **IP-verzióhoz**, a hozzárendelési módszernek *dinamikusnak* kell lennie az alapszintű SKU esetében.  A standard SKU-címek az IPv4 és az IPv6 esetében egyaránt *statikusak* . |
   |Üresjárati időkorlát (perc)|Nem|Hány percet tart a TCP-vagy HTTP-kapcsolatok megnyitása anélkül, hogy az ügyfelek a Keep-Alive üzenetek küldésére támaszkodnak. Ha az IPv6-ot választja az **IP-verzióhoz**, ez az érték nem módosítható. |
   |DNS-névcímke|Nem|Egyedinek kell lennie az Azure-helyen belül (az összes előfizetésben és minden ügyfélen) a nevet kell létrehoznia. Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, hogy csatlakozni tudjanak a névvel rendelkező erőforráshoz. Az Azure a teljes DNS-név létrehozásához hozzáfűz egy alapértelmezett alhálózatot, például a *Location.cloudapp.Azure.com* (ahol a hely az Ön által kiválasztott hely). Ha úgy dönt, hogy mindkét címet létrehozza, ugyanazt a DNS-nevet rendeli hozzá az IPv4-és IPv6-címekhez is. Az Azure alapértelmezett DNS-je mind a IPv4, mind az IPv6 AAAA-név rekordokat tartalmazza, és a DNS-név megkeresésekor mindkét rekordra válaszol. Az ügyfél kiválasztja, hogy melyik címnek (IPv4 vagy IPv6) kell kommunikálnia. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
   |Név (csak akkor látható, ha **mindkettő**IP-verzióját választja)|Igen, ha a **mindkettő** IP-verzióját választja|A névnek eltérőnek kell lennie, mint a lista első **neveként** megadott név. Ha IPv4-és IPv6-címet is létrehoz, a portál két különálló nyilvános IP-cím-erőforrást hoz létre, egyet a hozzá rendelt összes IP-címmel.|
   |IP-cím hozzárendelése (csak akkor látható, ha **mindkettő**IP-verzióját választja)|Igen, ha a **mindkettő** IP-verzióját választja|Ugyanazok a korlátozások, mint az IP-címhozzárendelés felett|
   |Előfizetés|Igen|Ugyanabban az [előfizetésben](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) kell lennie, mint a nyilvános IP-cím hozzárendeléséhez használt erőforrásnak.|
   |Erőforráscsoport|Igen|Ugyanabban vagy más [erőforrás-csoportban](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) létezhet, mint az erőforrás, amelyhez a nyilvános IP-címet hozzárendeli.|
   |Hely|Igen|Ugyanazon a [helyen](https://azure.microsoft.com/regions)kell lennie (más néven régió), mint az a nyilvános IP-címet hozzárendelő erőforrás.|
   |Rendelkezésreállási zóna| Nem | Ez a beállítás csak akkor jelenik meg, ha egy támogatott helyet választ ki. A támogatott helyek listáját lásd: a [rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha az **alapszintű** SKU lehetőséget választotta, a *nincs* beállítás automatikusan lesz kiválasztva. Ha inkább egy adott zónát szeretne garantálni, kiválaszthat egy adott zónát. Bármelyik választás nem redundáns. Ha bejelölte a **standard** SKU: Zone-redundáns beállítást, a rendszer automatikusan kijelöli az adatelérési utat a zóna meghibásodása miatt. Ha inkább egy adott zónát szeretne garantálni, amely nem rugalmas a zóna meghibásodása esetén, kiválaszthat egy adott zónát.

**Parancsok**

Bár a portálon két nyilvános IP-cím erőforrás (egy IPv4 és egy IPv6) hozható létre, a következő parancssori felületi és PowerShell-parancsok egyetlen erőforrást hoznak létre egy IP-verzióhoz vagy a másikhoz. Ha két nyilvános IP-cím-erőforrást szeretne használni, egyet az egyes IP-verziókhoz, a parancsot kétszer kell futtatnia, és a nyilvános IP-cím erőforrásaihoz különböző neveket és IP-verziókat kell megadnia.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Nyilvános IP-cím megtekintése, beállításainak módosítása vagy törlése

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőben írja be a *nyilvános IP-címet*. Ha a **nyilvános IP-címek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a megtekinteni kívánt nyilvános IP-cím nevét, módosítsa a beállításait, vagy törölje a listából.
3. Hajtsa végre a következő lehetőségek egyikét attól függően, hogy meg kívánja-e tekinteni, törölni vagy módosítani szeretné a nyilvános IP-címet.
   - **Megtekintés**: az **Áttekintés** szakasz a nyilvános IP-cím főbb beállításait jeleníti meg, például a hozzárendelt hálózati adaptert (ha a cím hálózati adapterhez van társítva). A portál nem jeleníti meg a cím verzióját (IPv4 vagy IPv6). A verzióra vonatkozó információk megtekintéséhez a PowerShell vagy a CLI parancs használatával tekintse meg a nyilvános IP-címet. Ha az IP-cím IPv6-alapú, a hozzárendelt címet a portál, a PowerShell vagy a parancssori felület nem jeleníti meg.
   - **Törlés**: a nyilvános IP-cím törléséhez válassza a **Törlés** lehetőséget az **Áttekintés** szakaszban. Ha a cím jelenleg IP-konfigurációhoz van társítva, akkor nem törölhető. Ha a cím jelenleg egy konfigurációhoz van társítva, **válassza a társítás lehetőséget a cím** IP-konfigurációból való leválasztásához.
   - **Módosítás**: válassza a **konfiguráció**lehetőséget. Módosítsa a beállításokat a [nyilvános IP-cím létrehozása](#create-a-public-ip-address)szakasz 4. lépésében található információk alapján. Ha egy IPv4-cím hozzárendelését statikusról dinamikusra szeretné módosítani, először el kell távolítania a nyilvános IPv4-címet a társított IP-konfigurációból. Ezt követően módosíthatja a hozzárendelési módszert a dinamikus értékre, és a **társítás** lehetőség kiválasztásával hozzárendelheti az IP-címet UGYANAHHOZ az IP-konfigurációhoz, egy másik konfigurációhoz, vagy meghagyhatja azt. Egy nyilvános IP-cím leválasztásához az **Áttekintés** **szakaszban válassza a**leválasztás lehetőséget.

   >[!WARNING]
   >Ha a hozzárendelési módszert statikusról dinamikusra módosítja, elveszíti a nyilvános IP-címhez hozzárendelt IP-címet. Míg az Azure nyilvános DNS-kiszolgálók fenntartanak egy leképezést a statikus és a dinamikus címek és a DNS-nevek címkéje között (ha Ön definiált ilyet), a dinamikus IP-címek megváltozhatnak, amikor a virtuális gép elindul a leállított (megszüntetett) állapot után. Ha meg szeretné akadályozni, hogy a cím megváltozzon, rendeljen hozzá egy statikus IP-címet.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network Public-IP List](/cli/azure/network/public-ip#az-network-public-ip-list) a nyilvános IP-címek listázásához [az az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) a beállítások megjelenítéséhez; [az Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) to Update; [az Network Public-IP delete](/cli/azure/network/public-ip#az-network-public-ip-delete) to delete|
|PowerShell|A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) egy nyilvános IP-cím objektumot kér le, és megtekinti a beállításait, a [set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) a beállítások frissítéséhez; A DELETE [-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) törlése|

## <a name="assign-a-public-ip-address"></a>Nyilvános IP-cím kiosztása

Ismerje meg, hogyan rendeljen hozzá egy nyilvános IP-címet a következő erőforrásokhoz:

- Windows vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép (létrehozásakor) vagy [meglévő virtuális géphez](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetkapcsolattal rendelkező Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Helyek közötti kapcsolat Azure VPN Gateway használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címekre vonatkozó feladatok végrehajtásához a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                                             | Name (Név)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft. Network/nyilvános IP/READ                           | Nyilvános IP-cím beolvasása                                          |
| Microsoft. Network/nyilvános IP/Write                          | Nyilvános IP-cím létrehozása vagy frissítése                           |
| Microsoft. Network/nyilvános IP/delete                         | Nyilvános IP-cím törlése                                     |
| Microsoft. Network/nyilvános IP/csatlakozás/művelet                    | Nyilvános IP-cím hozzárendelése erőforráshoz                    |

## <a name="next-steps"></a>További lépések

- Nyilvános IP-cím létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájl használatával vagy Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure](policy-samples.md) -szabályzat létrehozása és alkalmazása nyilvános IP-címekhez
