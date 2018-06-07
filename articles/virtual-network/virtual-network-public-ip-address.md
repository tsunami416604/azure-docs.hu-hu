---
title: Létrehozása, módosítása vagy törlése az Azure nyilvános IP-cím |} Microsoft Docs
description: Megtudhatja, hogyan létrehozása, módosítása vagy a nyilvános IP-cím törlése.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 30b4a7ea0d3f68e48d02e5cb72e70de74dc2addf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658689"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Létrehozása, módosítása vagy a nyilvános IP-cím törlése

Információ a nyilvános IP-cím és létrehozása, módosítása és törlése egy. A nyilvános IP-cím és a saját konfigurálható beállítások erőforrás. Lehetővé teszi egy nyilvános IP-cím hozzárendelése egy Azure-erőforrás, amely támogatja a nyilvános IP-címek:
- Az erőforrás, például az Azure virtuális gépek (VM), Azure Alkalmazásátjárót, Azure Load Balancer Terheléselosztók, Azure VPN Gatewayek és mások számára az internetről bejövő kommunikációt. Továbbra is kommunikálhat egyes erőforrásokat, például virtuális gépek, az internetről, ha egy virtuális gép nem rendelkezik a nyilvános IP-cím hozzárendelve, mindaddig, amíg a virtuális gép terhelés terheléselosztó háttér-készlet része, és a terheléselosztó a nyilvános IP-cím van hozzárendelve. Határozza meg, hogy egy adott Azure szolgáltatás-erőforrást is hozzárendelhető egy nyilvános IP-címet, vagy hogy azt továbbíthatók a keresztül a nyilvános IP-címét egy másik Azure-erőforrás, olvassa el a dokumentációt a szolgáltatáshoz. 
- Kimenő kapcsolódás az internethez, egy előre jelezhető IP-címet használja. Például a virtuális gépek kommunikálhatnak kimenő internetkapcsolat nélkül egy nyilvános IP-címet kap, de a cím az Azure által lefordított egy előre nem látható a nyilvános cím, alapértelmezés szerint hálózati cím. Egy erőforrást egy nyilvános IP-cím hozzárendelése lehetővé teszi, hogy tudja, melyik IP-címet a kimenő kapcsolathoz használt. Bár a előre jelezhető, a cím változhat, attól függően, hogy a választott hozzárendelési módszert. További információkért lásd: [hozzon létre egy nyilvános IP-cím](#create-a-public-ip-address). Az Azure-erőforrások kimenő kapcsolatok kapcsolatos további információkért lásd: [kimenő kapcsolatok megértéséhez](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.31 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

Hozzá kell rendelni a fiókot, jelentkezzen be, vagy csatlakozzon az Azure-ba, a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

Nyilvános IP-címek rendelkezik egy névleges kell fizetni. Az árképzés megtekintéséhez olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. 

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. A portál bal felső sarokban, válassza **+ hozzon létre egy erőforrást**.
2. Adja meg *nyilvános IP-cím* a a *keresése a piactéren* mezőbe. Ha **nyilvános IP-cím** megjelenik a keresési eredmények között, jelölje be.
3. A **nyilvános IP-cím**, jelölje be **létrehozása**.
4. Adja meg, vagy válassza ki az értékeket a következő beállítások **nyilvános IP-cím létrehozása**, majd jelölje be **létrehozása**:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie.|
    |SKU|Igen|Minden nyilvános IP-címek termékváltozatok bevezetése előtt létrehozott **alapvető** SKU nyilvános IP-címeket.  A Termékváltozat a nyilvános IP-cím létrehozása után nem módosítható. Egy különálló virtuális gépet, a virtuális gépek rendelkezésre állási csoportok, vagy a virtuálisgép-méretezési csoportok alapszintű vagy Standard termékváltozat használhatja.  SKU keverése virtuális gépek rendelkezésre állási készletek vagy méretezési csoportok között nem engedélyezett. **Alapszintű** Termékváltozat: létrehozásakor egy nyilvános IP-címet, amely támogatja a rendelkezésre állási zónák régióban a **rendelkezésre állási zóna** beállítása *nincs* alapértelmezés szerint. Ha szeretné, válassza ki egy rendelkezésre állási zóna biztosítása a nyilvános IP-cím a megadott zónában. **Standard** Termékváltozat: A Standard Termékváltozat nyilvános IP-cím egy virtuális gép vagy egy load balancer előtér társíthatók. Ha egy nyilvános IP-címet, amely támogatja a rendelkezésre állási zónák régióban hoz létre a **rendelkezésre állási zóna** beállítása *zónaredundáns* alapértelmezés szerint. Rendelkezésre állási zónák kapcsolatos további információkért tekintse meg a **rendelkezésre állási zóna** beállítást. A standard Termékváltozat szükség, ha a cím, egy szabványos terheléselosztóhoz rendeli. Standard terheléselosztók kapcsolatos további információkért lásd: [Azure terheléselosztó standard Termékváltozat](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
    |IP-verziója|Igen| Válassza ki az IPv4- vagy IPv6. Nyilvános IPv4-címek hozzárendelhetők legyenek több Azure-erőforrások, míg IPv6 nyilvános IP-cím csak egy internetre irányuló terheléselosztót rendelhető. A load balancer is terheléselosztásához IPv6-forgalom Azure virtuális gépekhez. További információ [terheléselosztási IPv6-forgalom a virtuális gépek](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha bejelölte a **Standard Termékváltozat**, nincs lehetőség kijelölésére *IPv6*. Csak hozhat létre egy IPv4-cím használata esetén a **Standard Termékváltozat**.|
    |IP-cím hozzárendelése|Igen|**Dinamikus:** dinamikus címek hozzárendelésének csak után a nyilvános IP-cím társítva egy virtuális gépre, a virtuális gép hálózati kapcsolatát első alkalommal elindul. Dinamikus címet használva módosítható, ha a virtuális gép, a hálózati adapter csatlakozik leállított (felszabadított). A cím változatlan marad, ha a virtuális gép újraindítása vagy leállítása (de nem felszabadítása. lehetséges) is. **Statikus:** statikus címek hozzárendelésének a nyilvános IP-cím létrehozásakor. Statikus címeket ne változtassa meg akkor is, ha a virtuális gép leállított (felszabadított) állapotában kerül. A cím csak kiadott, hálózati kapcsolat törlésekor. A hálózati illesztő létrehozása után módosíthatja a hozzárendelési módszert. Ha *IPv6* a a **verziójú IP**, a hozzárendelés módszer *dinamikus*. Ha *szabványos* a **SKU**, a hozzárendelés módszer *statikus*.|
    |Üresjárati időkorlátja (perc)|Nem|Hány perc megnyitva, a TCP- vagy HTTP-kapcsolat ügyfelek által küldött életben tartási üzenetek nélkül. Ha az IPv6 **verziójú IP**, ez az érték nem módosítható. |
    |DNS-névcímke|Nem|Az Azure-hoz létre a neve (között az összes előfizetést és az összes ügyfél számára) hely belül egyedinek kell lennie. Azure automatikusan regisztrálja a nevét és IP-címet a DNS-ben, csatlakozhat a nevű erőforrás. Azure hozzáfűz egy alapértelmezett alhálózati például *location.cloudapp.azure.com* (amennyiben helye az választja) nevét ad meg, a teljesen minősített DNS-név létrehozásához. Ha mindkét cím verzió létrehozása mellett dönt, a DNS-névvel az IPv4 és IPv6-cím van hozzárendelve. Azure alapértelmezett DNS mind az IPv4 és IPv6 AAAA neve rekordok tartalmazza, és mindkét rekordok válaszol, amikor a DNS-név keresése. Az ügyfél úgy dönt, hogy mely címet (IPv4 vagy IPv6) való kommunikációhoz. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
    |Egy IPv6-(vagy IPv4-) cím létrehozása|Nem| Válassza a függő IPv6 és IPv4-alapú megjelenítését **verziójú IP**. Ha például **IPv4** a **verziójú IP**, **IPv6** itt jelenik meg. Ha *szabványos* a **SKU**, létrehozhat egy IPv6-cím nem rendelkezik.
    |Name (csak akkor látható, ha bejelölte a **hozzon létre egy IPv6-(vagy IPv4-alapú) címet** jelölőnégyzet)|Igen, ha bejelöli a **hozzon létre egy IPv6-alapú** (vagy IPv4-alapú) jelölőnégyzetet.|A neve eltér a nevet kell lennie az első **neve** ezen a listán. Ha IPv4- és IPv6-cím létrehozása mellett dönt, a portál két külön nyilvános IP-cím erőforrás, egyet a minden egyes hozzárendelt IP-cím verziót hoz létre.|
    |IP-cím hozzárendelése (csak akkor látható, ha bejelölte a **hozzon létre egy IPv6-(vagy IPv4-alapú) címet** jelölőnégyzet)|Igen, ha bejelöli a **hozzon létre egy IPv6-alapú** (vagy IPv4-alapú) jelölőnégyzetet.|Ha a jelölőnégyzet felirat **IPv4-cím létrehozása**, egy hozzárendelési módszert. Ha a jelölőnégyzet felirat **IPv6-cím létrehozása**, nem egy hozzárendelési módszert választja, azt kell **dinamikus**.|
    |Előfizetés|Igen|Léteznie kell az azonos [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) a nyilvános IP-címet hozzárendelni kívánt erőforrásként.|
    |Erőforráscsoport|Igen|A azonos vagy eltérő, létezhet [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) a nyilvános IP-címet hozzárendelni kívánt erőforrásként.|
    |Hely|Igen|Léteznie kell az azonos [hely](https://azure.microsoft.com/regions), régió, mint a nyilvános IP-címet hozzárendelni kívánt erőforrásként cím is hivatkozott.|
    |Rendelkezésre állási zóna| Nem | Ez a beállítás csak akkor jelenik meg, ha egy támogatott helyre. Támogatott helyek listáját lásd: [rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha bejelölte a **alapvető** SKU, *nincs* automatikusan ki van jelölve meg. Ha inkább a megadott zónában garantálni, beállíthatja a megadott zónában. Vagy a rendszer nem zónaredundáns. Ha bejelölte a **szabványos** Termékváltozat: zónaredundáns automatikusan ki van jelölve, és lehetővé teszi az adatok elérési útja rugalmas zóna hiba esetén. Ha inkább garantálja a megadott zónában, amely nem esetén is lehetséges legyen zóna, beállíthatja a megadott zónában.

**Parancsok**

Bár a portálon hozzon létre két nyilvános IP-cím erőforrás (egy IPv4- és egy IPv6-) lehetőséget biztosít, a következő parancssori felület és a PowerShell-parancsok egy IP-verziót, vagy a másik címmel hozzon létre egy erőforrást. Ha azt szeretné, hogy két nyilvános IP-cím erőforrás, egy az egyes IP-verziót, futtatnia kell a parancs kétszer, különböző neveket és a nyilvános IP-cím erőforrás-verziók.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Megtekintése, módosítsa a beállításokat, vagy egy nyilvános IP-cím törlése

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *nyilvános IP-cím*. Ha **nyilvános IP-címek** jelennek meg a keresési eredmények között, válassza ki azt.
2. Adjon meg szeretné tekinteni, módosítsa a beállításokat, vagy törli a listáról a nyilvános IP-cím.
3. Fejezze be a következők egyikére, attól függően, hogy megtekintését, törlése és a nyilvános IP-címének módosítása.
    - **Nézet**: A **áttekintése** szakasz bemutatja beállításait a nyilvános IP-címet, például a hálózati illesztő azt hozzá van rendelve (Ha egy hálózati adapter társítva hozzá a cím). A portál nem jelenik meg a címet (IPv4 vagy IPv6) verziója. Szeretné megtekinteni a fájlverzió-információkat, a PowerShell vagy a CLI parancs segítségével megtekintheti a nyilvános IP-cím. Ha az IP-cím verziót IPv6, a hozzárendelt címet nem jelenik meg a portálon, PowerShell vagy a parancssori felület.
    - **Törölje**: törli a nyilvános IP-címet, válassza ki a **törlése** a a **áttekintése** szakasz. Ha a cím jelenleg társított IP-konfigurációt, nem lehet törölni. Ha a cím jelenleg társítva van egy konfigurációhoz, válassza ki a **szüntesse** leválasztja a címet az IP-konfigurációt.
    - **Változás**: válasszon **konfigurációs**. Az információk alapján a 4. lépésben a beállítások módosításához [hozzon létre egy nyilvános IP-cím](#create-a-public-ip-address). Ha módosítani szeretné egy IPv4-cím hozzárendelés statikus dinamikus, meg kell szüntetnie a nyilvános IPv4-cím, amelyekhez társítva vannak az IP-konfigurációja a. Módosíthatja a hozzárendelési módszert dinamikus, és válassza a **társítása** hozzárendelni az IP cím az ugyanazon IP-konfiguráció, egy másik konfigurációt, vagy is hagyhatja leválasztása. Leválasztja a nyilvános IP-cím, az a **áttekintése** szakaszban jelölje be **szüntesse**.

    >[!WARNING]
    >Amikor a hozzárendelés metódus statikus dinamikus módosítjuk, az IP-cím, amely a nyilvános IP-cím lett rendelve elvesznek. Az Azure nyilvános DNS-kiszolgálók statikus vagy dinamikus címek és a DNS-Névcímke (Ha egy meghatározott) közötti leképezést karbantartása, amíg egy dinamikus IP-címet módosíthatja, ha a virtuális gép leállított (felszabadított) állapotában állapota után elindul. A cím megváltoztatása érdekében rendelje hozzá egy statikus IP-címet.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az nyilvános ip-lista](/cli/azure/network/public-ip#az-network-public-ip-list) nyilvános IP-címeinek listáját, hogy [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az-network-public-ip-show) ; beállítások megjelenítése [az hálózati nyilvános ip-frissítés](/cli/azure/network/public-ip#az-network-public-ip-update) frissítése; [az hálózati nyilvános ip-törlése](/cli/azure/network/public-ip#az-network-public-ip-delete) törlése|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) egy nyilvános IP-cím objektum beolvasása és a beállítások megtekintéséhez [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress) frissíteni a beállításait; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) törlése|

## <a name="permissions"></a>Engedélyek

Nyilvános IP-címek feladatok elvégzésekor, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                                             | Name (Név)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Olvassa el a nyilvános IP-cím                                          |
| Microsoft.Network/publicIPAddresses/write                          | Hozzon létre vagy egy nyilvános IP-címének frissítése                           |
| Microsoft.Network/publicIPAddresses/delete                         | A nyilvános IP-cím törlése                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Egy erőforrást egy nyilvános IP-címet társítani                    |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy nyilvános IP cím használatával [PowerShell](powershell-samples.md) vagy [Azure CLI](cli-samples.md) parancsfájlok, vagy az Azure használatával [Resource Manager-sablonok](template-samples.md)
- Létrehozása és alkalmazása [Azure házirend](policy-samples.md) nyilvános IP-címek
- Nyilvános IP-címek hozzárendelése létrehozásakor egy [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure virtuális gép, egy [Azure Internet felé néző Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), egy [Azure Alkalmazásátjáró](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), egy [webhelyek kapcsolat az Azure VPN Gateway használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy egy [Azure virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
