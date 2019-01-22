---
title: Létrehozása, módosítása vagy törlése az Azure nyilvános IP-cím |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, módosítása vagy törlése a nyilvános IP-címet.
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
ms.openlocfilehash: 3e0b664c14cba845c43e3b0202de5fe033bf2186
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434631"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Létrehozása, módosítása vagy egy nyilvános IP-cím törlése

Ismerje meg a nyilvános IP-cím és létrehozása, módosítása és törlése egy. Nyilvános IP-cím egy saját konfigurálható beállításokkal rendelkező erőforrás. Lehetővé teszi a nyilvános IP-cím hozzárendelése egy Azure-erőforrás, amely támogatja a nyilvános IP-címek:
- Az erőforrás, például az Azure Virtual Machines (VM), az Azure Application Gateway átjárók, az Azure Load Balancer Terheléselosztók, Azure VPN Gateway és mások az internetről bejövő kommunikációt. Továbbra is kommunikálhat egyes erőforrásokat, például a virtuális gépek, az internetről, ha a virtuális gép nem rendelkezik egy nyilvános IP-cím rendelhető, mindaddig, amíg a virtuális gép része egy load balancer háttérkészlethez, és a terheléselosztó nyilvános IP-cím van hozzárendelve. Annak megállapításához, hogy egy adott Azure-szolgáltatás erőforrás rendelhető nyilvános IP-cím, vagy e azt továbbíthatók a különböző Azure-erőforrás nyilvános IP-címén keresztül, az a szolgáltatás dokumentációjában talál. 
- Kimenő kapcsolódás az internethez, kiszámítható IP-címet használ. Például a virtuális gépek kommunikálhatnak az interneten egy nyilvános IP-cím nélkül kimenő rendelve, de a cím alapértelmezés szerint egy előre nem látható, nyilvános cím, az Azure által lefordított hálózati cím. Nyilvános IP-cím hozzárendelése egy erőforrás lehetővé teszi, hogy tudja, melyik IP-címet használja a kimenő kapcsolat. Előre jelezhető, bár a cím változhat, a választott hozzárendelési módszertől függően. További információkért lásd: [hozzon létre egy nyilvános IP-cím](#create-a-public-ip-address). Az Azure-erőforrások kimenő kapcsolatok kapcsolatos további információkért lásd: [kimenő kapcsolatainak ismertetése](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

Nyilvános IP-címek egy névleges díj rendelkezik. A díjszabás megtekintéséhez olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. 

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. A portál bal felső sarokban, válassza **+ erőforrás létrehozása**.
2. Adja meg *nyilvános ip-cím* a a *keresés a piactéren* mezőbe. Amikor **nyilvános IP-cím** megjelenik a keresési eredmények között, válassza ki.
3. A **nyilvános IP-cím**válassza **létrehozás**.
4. Adja meg, vagy válassza ki a következő beállítások értékeit a **nyilvános IP-cím létrehozása**, majd **létrehozás**:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie.|
    |SKU|Igen|A termékváltozatok bevezetése előtt létrehozott minden nyilvános IP-címek **alapszintű** Termékváltozat nyilvános IP-címeket.  A Termékváltozat nyilvános IP-cím létrehozása után nem módosítható. Különálló virtuális gépek, virtuális gépek rendelkezésre állási vagy virtuálisgép-méretezési csoportok alapszintű vagy Standard termékváltozatok használhatja.  Keverése termékváltozatok között rendelkezésre állási csoportok vagy a méretezési csoportok virtuális gépeit nem engedélyezett. **Alapszintű** Termékváltozat: Egy régióban, amely támogatja a rendelkezésre állási zónák, nyilvános IP-cím létrehozása a **rendelkezésre állási zónában** beállítása *nincs* alapértelmezés szerint. Ha szeretné, válassza ki a nyilvános IP-címnek egy adott zónához biztosításához rendelkezésre állási zónában. **Standard szintű** Termékváltozat: A Standard Termékváltozat nyilvános IP-cím egy virtuális géphez vagy egy terheléselosztó előtérrendszerhez társíthatók. Ha nyilvános IP-cím, amely támogatja a rendelkezésre állási zónák, egy régióban hoz létre a **rendelkezésre állási zónában** beállítása *zónaredundáns* alapértelmezés szerint. Rendelkezésre állási zónákkal kapcsolatos további információkért lásd: a **rendelkezésre állási zónában** beállítás. A standard Termékváltozat megadása kötelező, ha a Standard load balancer-címet társíthatja. Standard load Balancer terheléselosztók kapcsolatos további információkért lásd: [az Azure load balancer standard Termékváltozatú](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
    |IP-verzió|Igen| Válassza ki az IPv4- vagy IPv6. Bár több Azure-erőforrások nyilvános IPv4-címek rendelhetők, egy IPv6-alapú nyilvános IP-cím csak egy internetkapcsolattal rendelkező terheléselosztó rendelhetők. A load balancer IPv6-forgalom az Azure-beli virtuális gépek terheléselosztása betöltheti. Tudjon meg többet [terheléselosztási IPv6-forgalmat a virtuális gépek](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha bejelölte a **Standard Termékváltozatú**, nem rendelkezik a kívánt *IPv6*. Csak hozhat létre egy IPv4-cím használata esetén a **Standard Termékváltozatú**.|
    |IP-cím hozzárendelése|Igen|**Dinamikus:** A dinamikus címek vannak hozzárendelve, csak a nyilvános IP-címmel társítva az Azure-erőforrás, és először elindul az erőforrás után. Dinamikus címek megváltozhatnak, ha hozzá vannak rendelve. egy erőforrást, például egy virtuális gépet, és a virtuális gép van leállítva (felszabadítva), majd újraindul. A cím ugyanaz marad, ha egy virtuális gép újraindítása vagy leállítása (de nincs felszabadítva) is. A dinamikus címek felszabadítása egy nyilvános IP-cím erőforráshoz hozzá rendelve egy erőforráshoz van leválasztása. **Statikus:** Statikus címek vannak hozzárendelve, amikor létrejön egy nyilvános IP-címet. Statikus címek nem szabadulnak fel egy nyilvános IP-cím erőforrás törléséig. Ha a cím nem egy erőforráshoz van társítva, módosíthatja a hozzárendelési módszer, miután létrejött a címet. Ha a cím egy erőforráshoz van társítva, nem lehet tudni megváltoztatni a hozzárendelési módszer. Ha *IPv6* számára a **IP-verzió**, a hozzárendelési módszer *dinamikus*. Ha *Standard* a **Termékváltozat**, a hozzárendelési módszer *statikus*.|
    |Üresjárat időkorlátja (perc)|Nem|Hány perc a TCP- vagy HTTP-kapcsolat nyitva tartása anélkül, hogy az ügyfelek életben tartási üzenetek küldéséhez. Ha az IPv6 **IP-verzió**, ez az érték nem lehet módosítani. |
    |DNS name label|Nem|Az Azure-beli hely, a nevét a hoz létre (között az összes előfizetés és az összes ügyfél) belül egyedinek kell lennie. Az Azure automatikusan regisztrálja a nevét és IP-cím a DNS-ben úgy csatlakozhat a nevű erőforrás. Azure hozzáfűz egy alapértelmezett alhálózat például *location.cloudapp.azure.com* (ahol a helye az választja) nevet ad meg, hozhat létre teljesen minősített DNS-neve. Ha mindkét cím verzió létrehozását választja, a DNS-névvel az IPv4- és IPv6-cím van hozzárendelve. Az Azure alapértelmezett DNS mind az IPv4 és IPv6-alapú AAAA típusú neve rekordokat tartalmaz, és mindkét rekordok reagál, amikor a keresése a DNS-név. Az ügyfél úgy dönt, hogy mely címet (IPv4 vagy IPv6) való kommunikációhoz. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
    |Hozzon létre egy IPv6-alapú (vagy IPv4) címet|Nem| IPv6 és IPv4 megjelenítését az adott nyelvtől függ, válassza a **IP-verzió**. Például, ha kiválasztja **IPv4** a **IP-verzió**, **IPv6** itt jelenik meg. Ha *Standard* a **Termékváltozat**, nem rendelkezik IPv6-cím létrehozásának lehetősége.
    |Név (csak akkor látható, ha bejelölte a **hozzon létre egy IPv6-alapú (vagy IPv4) címet** jelölőnégyzet)|Igen, ha kiválasztja a **hozzon létre egy IPv6-alapú** (vagy IPv4) jelölőnégyzetet.|A névnek kell lennie, adja meg a neve eltér az első **neve** ebben a listában. Ha IPv4- és IPv6-cím létrehozása, a portál létrehoz két külön nyilvános IP-cím erőforrások, az egyes IP-cím rendelve verziója egy.|
    |IP-cím hozzárendelése (csak akkor látható, ha bejelölte a **hozzon létre egy IPv6-alapú (vagy IPv4) címet** jelölőnégyzet)|Igen, ha kiválasztja a **hozzon létre egy IPv6-alapú** (vagy IPv4) jelölőnégyzetet.|Ha a jelölőnégyzet felirat **IPv4-cím létrehozása**, is kiválaszthat egy hozzárendelési módszer. Ha a jelölőnégyzet felirat **IPv6-cím létrehozása**, egy hozzárendelési módszer nem választhat ki kell lennie, **dinamikus**.|
    |Előfizetés|Igen|Léteznie kell az azonos [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és az erőforrásnak a nyilvános IP-címet társítani szeretné.|
    |Erőforráscsoport|Igen|Az azonos vagy eltérő létrejöhet [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) és az erőforrásnak a nyilvános IP-címet társítani szeretné.|
    |Hely|Igen|Léteznie kell az azonos [hely](https://azure.microsoft.com/regions), régió, mint a nyilvános IP-címet hozzárendelni kívánt erőforrásként a cím, emellett hivatkozott.|
    |Rendelkezésre állási zóna| Nem | Ez a beállítás csak akkor jelenik meg, ha egy támogatott helyet. A támogatott helyek listáját lásd: [rendelkezésre állási zónák áttekintésének](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha bejelölte a **alapszintű** SKU, *nincs* automatikusan ki van jelölve az Ön számára. Ha inkább a garantálják egy adott zónához, választ, kiválaszthat egy adott zónához. Vagy a választás nem zónaredundáns. Ha bejelölte a **Standard** Termékváltozat: Zónaredundáns automatikusan ki van jelölve, és lehetővé teszi az adatok elérési útja rugalmas zóna meghiúsulásához. Ha egy adott zónához, amely nem ellenállni a zóna hiba, hogy inkább választ, kiválaszthat egy adott zónához.

**Parancsok**

Bár a portál lehetővé teszi, hogy hozzon létre két nyilvános IP-cím erőforrások (egy IPv4- és egy IPv6), a következő CLI és PowerShell-parancsokat egy IP-verzió vagy a másik címmel hozzon létre egy erőforrást. Ha azt szeretné, hogy két nyilvános IP-cím erőforrások, egy az egyes IP-verzió, futtatnia kell a parancsot kétszer, adja meg a különböző neveket és a nyilvános IP-cím erőforrás-verziók.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Megtekintheti, beállításainak módosítása vagy egy nyilvános IP-cím törlése

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *nyilvános ip-cím*. Amikor **nyilvános IP-címek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a nevét a nyilvános IP-címet szeretne megtekinteni, beállításainak módosítása vagy törlése a listából.
3. Fejezze be a következők egyikét, attól függően, hogy a megtekintése, törlése vagy a nyilvános IP-címének módosítása.
    - **Nézet**: A **áttekintése** szakasz bemutatja a titkosításikulcs-beállítások a nyilvános IP-címet, például a hálózati adapterhez van társítva (Ha a cím egy hálózati adapterhez társított). A portál nem jelenik meg a címet (IPv4 vagy IPv6) verziója. Szeretné megtekinteni a fájlverzió-információkat, a PowerShell vagy parancssori felület parancs segítségével megtekintheti a nyilvános IP-címet. Ha az IP-cím verziója IPv6, a hozzárendelt cím nem a portal, PowerShell vagy a parancssori felület szerint jelenik meg.
    - **Törlés**: A nyilvános IP-cím törléséhez válassza ki **törlése** a a **áttekintése** szakaszban. Ha a cím jelenleg társított IP-konfigurációhoz, nem lehet törölni. Ha a cím jelenleg-konfigurációhoz társítva, jelölje be **szüntesse** leválasztja a címet az IP-konfigurációhoz.
    - **Változás**: válasszon **konfigurációs**. Módosítsa a beállításokat az adatokat a 4. lépésében [hozzon létre egy nyilvános IP-cím](#create-a-public-ip-address). Ha módosítani szeretné a hozzárendelés egy IPv4-cím statikus dinamikus, először társításának megszüntetése a nyilvános IPv4-cím az IP-konfigurációhoz van társítva a. Módosíthatja a hozzárendelési módszer a dinamikus, és válassza ki a **társítása** társítása az IP cím azonos IP-konfigurációja, egy másik konfigurációs, vagy hagyhatja azt leválasztása. A nyilvános IP-címet, leválasztja a **áttekintése** szakaszban jelölje be **szüntesse**.

    >[!WARNING]
    >Amikor módosítja a hozzárendelési módszer statikusról dinamikus, elveszíti az IP-cím, amely a nyilvános IP-címe hozzá lett rendelve. Bár az Azure nyilvános DNS-kiszolgálók statikus vagy dinamikus címek és bármilyen DNS-Névcímke (amennyiben megadott egyet) közötti leképezést fenntartani, dinamikus IP-címet módosíthatja, ha a virtuális gép leállított (felszabadított) állapotba a elindult. A cím módosítása megakadályozni, rendelje hozzá a statikus IP-címet.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) nyilvános IP-címeinek listáját, hogy [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) ; beállítások megjelenítése [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) frissítése; [az network public-IP-törlése](/cli/azure/network/public-ip#az-network-public-ip-delete) törlése|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) és egy nyilvános IP-címobjektumot, beállítások megtekintéséhez [Set-AzureRmPublicIpAddress](/powershell/module/azurerm.network/set-azurermpublicipaddress) ;-beállítások frissítése [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) törlése|

## <a name="assign-a-public-ip-address"></a>Nyilvános IP-cím hozzárendelése

Ismerje meg, egy nyilvános IP-cím hozzárendelése a következőket:

- A [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM (létrehozásakor), vagy egy [meglévő virtuális gép](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Internetkapcsolattal rendelkező Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Helyek közötti kapcsolattal az Azure VPN Gateway használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure-beli virtuálisgép-méretezési csoportot](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címek feladatait, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                                             | Name (Név)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Olvassa el a nyilvános IP-cím                                          |
| Microsoft.Network/publicIPAddresses/write                          | Hozzon létre vagy nyilvános IP-cím frissítése                           |
| Microsoft.Network/publicIPAddresses/delete                         | Egy nyilvános IP-cím törlése                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Az erőforrás nyilvános IP-cím társítása                    |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy nyilvános IP cím [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) nyilvános IP-címek
