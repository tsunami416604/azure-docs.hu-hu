---
title: "Létrehozása, módosítása vagy egy Azure hálózati illesztő törlése |} Microsoft Docs"
description: "Ismerje meg a hálózati adaptert létrehozása, beállításainak módosításához és törléséhez."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: da29ecaaa0f694be3e96baebfd80c09069d7c4a8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Létrehozása, módosítása vagy a hálózati illesztő törlése

Megtudhatja, hogyan hozzon létre, beállításainak módosítása és törlése a hálózati adaptert. Egy adott hálózati csatoló lehetővé teszi, hogy az Azure virtuális gép Internet, Azure és a helyszíni erőforrások folytatott kommunikációhoz. Az Azure portál használatával virtuális gép létrehozásakor a portál alapértelmezett beállításokkal, hoz létre egy hálózati adapterrel. Ehelyett választhatja, hogy egyéni beállításokkal hozza létre a hálózati adapterek, és adja hozzá a virtuális gép egy vagy több hálózati illesztőre létrehozásakor. Érdemes lehet is, módosíthatja az alapértelmezett hálózati kapcsolati beállítások egy meglévő hálózati illesztő. Ez a cikk azt ismerteti, hogyan hozzon létre egy adott hálózati csatoló egyéni beállításokkal, módosítsa a meglévő beállítások, például hálózati szűrő (hálózati biztonsági csoport) hozzárendelés alhálózat-hozzárendelés, DNS-kiszolgáló beállításai vagy IP-továbbítást, és a hálózati illesztő törlése.

Ha szeretne hozzáadni, módosítása vagy eltávolítása IP-címek a hálózati illesztő, olvassa el a [kezelése IP-címek](virtual-network-network-interface-addresses.md) cikk. Ha szeretné a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépekről, olvassa el a [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md) cikk.


## <a name="before-you-begin"></a>Előkészületek

Esetlegesen szakasz ebben a cikkben szereplő lépésekkel befejezése előtt hajtsa végre a következő feladatokat:

- Tekintse át a [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) cikkben tájékozódhat az korlátozhatja a hálózati adapterek.
- Jelentkezzen be a Azure [portal](https://portal.azure.com), az Azure parancssori felület (CLI), vagy az Azure PowerShell használata az Azure-fiók. Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha a feladat-ebben a cikkben a PowerShell-parancsokkal [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a legfrissebb telepítve az Azure PowerShell-parancsmagjaival. Ha segítséget szeretne kérni a PowerShell-parancsaihoz, valamint példákkal, írja be a `get-help <command> -full`.
- Ha ebben a cikkben a feladatokat az Azure parancssori felület (CLI) parancsokkal [telepítése és konfigurálása az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a telepített Azure CLI legújabb verziója. Segítség kérése parancssori felület parancsait, írja be a következőt `az <command> --help`. Ahelyett, hogy a parancssori felület és a szükséges előfeltételek telepítése, az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj **> _** gomb tetején a [portal](https://portal.azure.com).

## <a name="create-a-network-interface"></a>A hálózati illesztő létrehozása

Az Azure portál használatával virtuális gép létrehozásakor a portált egy adott hálózati csatoló alapértelmezett beállításokat hoz létre. A hálózati kapcsolati beállítások ahelyett, hogy meg kellene megadnia, ha egyéni beállításokkal hozza létre a hálózati adaptert, és a hálózati adapter csatlakoztatása egy virtuális géphez, a virtuális gép (a PowerShell vagy az Azure parancssori felület használatával) létrehozásakor. Hozzon létre egy adott hálózati csatoló is, és adja hozzá egy meglévő virtuális gép (a PowerShell vagy az Azure parancssori felület használatával). További információt a meglévő hálózati illesztő a virtuális gép létrehozásához, vagy adja hozzá, vagy távolítsa el a hálózati adapterek a meglévő virtuális gépek, olvassa el a [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md) cikk. Mielőtt létrehozna egy adott hálózati csatoló, rendelkeznie kell egy meglévő [virtuális hálózati](virtual-networks-create-vnet-arm-pportal.md) azonos helyen és előfizetést hoz létre a hálózati illesztő.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a **+ Hozzáadás**.
4. Az a **létrehozás hálózati illesztő** panelen megjelenő, adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson **létrehozása**:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie. Adott idő alatt akkor valószínűleg kell több hálózati adapterrel az Azure-előfizetésben. Olvassa el a [elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) vonatkozó javaslatok elnevezési létrehozásakor kezelése több hálózati illesztőt megkönnyíti a következő cikket. A név nem lehet módosítani, a hálózati illesztő létrehozása után.|
    |Virtuális hálózat|Igen|Válassza ki a virtuális hálózatot a hálózati adapter. Csak egy virtuális hálózatot, ugyanazt az előfizetést és a hálózati adapter és a helyen található rendelhet egy adott hálózati csatoló. A hálózati illesztő létrehozása után hozzá van rendelve a virtuális hálózat nem módosítható. A virtuális gépet, akkor adja hozzá a hálózati adaptert is szerepelnie kell az azonos helyen és a hálózati adapterként előfizetés.|
    |Alhálózat|Igen|Válassza ki egy alhálózatot a kiválasztott virtuális hálózaton belül. Az alhálózat, a hálózati adapter van rendelve létrehozása után módosíthatja.|
    |Privát IP-cím hozzárendelése|Igen| Ezt a beállítást, az IPv4-cím hozzárendelés módszer van kiválasztása. A következő hozzárendelési módszer közül választhat: **dinamikus:** Ha ezt a lehetőséget választja, Azure automatikusan rendeli hozzá a következő elérhető címek a kiválasztott alhálózat címtartománya. **Statikus:** Ha ezt a lehetőséget választja, kézzel kell rendelnie egy szabad IP-cím az alhálózat kijelölt belülre. Statikus és dinamikus címek nem megváltoztatni, amíg meg nem módosítja őket, vagy a hálózati illesztő törlése. A hálózati illesztő létrehozása után módosíthatja a hozzárendelési módszert. Az Azure DHCP-kiszolgáló ezt a címet rendel a virtuális gép operációs rendszerében a hálózati illesztőt.|
    |Hálózati biztonsági csoport|Nem| Hagyja beállítása **nincs**, válasszon ki egy létező [hálózati biztonsági csoport](virtual-networks-nsg.md), vagy [hálózati biztonsági csoport létrehozása](virtual-networks-create-nsg-arm-pportal.md). Hálózati biztonsági csoportok lehetővé teszi, hogy egy adott hálózati csatoló mindkét hálózati forgalom szűrésére. Nulla vagy egy hálózati biztonsági csoport egy adott hálózati csatoló alkalmazhatja. Nulla vagy egy hálózati biztonsági csoportot az alhálózathoz van hozzárendelve a hálózati adapter is alkalmazhatók. Néha váratlan eredményekhez fordulhat elő, a hálózati biztonsági csoport alkalmazásakor a hálózati adaptert és az alhálózatot a hálózati adapter van hozzárendelve. Hibaelhárítás a hálózati adapterek és alhálózatok alkalmazott hálózati biztonsági csoportok, olvassa el a [hibaelhárítása a hálózati biztonsági csoportok](virtual-network-nsg-troubleshoot-portal.md#nsg) cikk.|
    |Előfizetés|Igen|Válasszon egyet az Azure [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). A virtuális gép csatlakoztatása a hálózati adaptert, és kösse össze a virtuális hálózat ugyanabban az előfizetésben léteznie kell.|
    |Private IP address (IPv6)|Nem| Ha bejelöli ezt a jelölőnégyzetet, az IPv6-címek a hálózati kapcsolat mellett a hálózati illesztő rendelt IPv4-cím van hozzárendelve. Tekintse meg a [IPv6](#IPv6) című szakaszban a fontos adatokat IPv6 hálózati adapterrel együtt. Nem választhat ki egy hozzárendelési módszert az IPv6-cím használatára. Ha IPv6-címet hozzárendelni, a dinamikus módszerrel van hozzárendelve.
    |IPv6-név (csak akkor jelenik meg, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ez a név egy másodlagos IP-konfiguráció a hálózati adapter van hozzárendelve. További tudnivalók az IP-konfigurációk a [hálózati kapcsolati beállítások megtekintése](#view-network-interface-settings) című szakaszát.|
    |Erőforráscsoport|Igen|Válasszon ki egy létező [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vagy hozzon létre egyet. Egy adott hálózati csatoló létezhet ugyanazon vagy másik erőforráscsoportban, mint a virtuális gépnek csatlakoztassa, vagy a virtuális hálózathoz csatlakoztatja a.|
    |Hely|Igen|A virtuális gép csatlakoztatása a hálózati adaptert, és kösse össze a virtuális hálózat létezhet ugyanazon [hely](https://azure.microsoft.com/regions), más néven egy régiót.|

A portál egy nyilvános IP-cím hozzárendelése a hálózati kapcsolat létrehozásakor, ha a portálon hozzon létre egy nyilvános IP-címet, és rendelje hozzá egy adott hálózati csatoló a portál virtuális gép létrehozásakor a beállítás nem biztosít. A nyilvános IP-cím hozzáadása a hálózati illesztő létrehozását követően, olvassa el a [kezelése IP-címek](virtual-network-network-interface-addresses.md) cikk. Ha szeretne létrehozni egy adott hálózati csatoló nyilvános IP-címmel, a hálózati illesztő létrehozása a parancssori felületen vagy a PowerShell kell használnia.

>[!Note]
> Azure csak azután a hálózati illesztő virtuális géphez csatlakozik, és a virtuális gép indításánál először rendel a hálózati adapter MAC-címet. Azure rendel hozzá a hálózati adapter MAC-címe nem adható meg. A MAC-cím megmarad, a hálózati adapter csak a hálózati illesztő törölték, vagy a magánhálózati IP-címet hozzárendelni az elsődleges hálózati adapter elsődleges IP-konfigurációja módosul. IP-címek és IP-konfigurációk kapcsolatos további tudnivalókért olvassa el a [kezelése IP-címek](virtual-network-network-interface-addresses.md) cikk.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati hálózati adapter létrehozása](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Hálózati kapcsolat beállításainak megjelenítése

Megtekintheti és módosíthatja a legtöbb beállítást egy adott hálózati csatoló létrehozása után.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati kapcsolat a beállításainak megtekintése vagy módosítása.
4. A panel, amelyen megjelenik a kiválasztott hálózati adapter a következő beállítások láthatók:
    - **Áttekintés:** információkat nyújt azokról a hálózati adapter, például az IP-címek hozzárendelve, a virtuális hálózatot/alhálózatot a hálózati adapter van hozzárendelve, és a virtuális gép hálózati illesztő csatlakozik (ha van ellátva. egy). Az alábbi képen látható nevű hálózati illesztő – áttekintés beállításait **mywebserver256**: ![hálózati illesztő – áttekintés](./media/virtual-network-network-interface/nic-overview.png) egy adott hálózati csatoló áthelyezheti egy másik erőforráscsoportban található, vagy előfizetés kattintva (**módosítása**) mellett a **erőforráscsoport** vagy **előfizetés neve**. Ha a hálózati illesztő helyezi át, a hálózati illesztő vele kapcsolatos összes erőforrás kell áthelyeznie. Ha a hálózati illesztő virtuális géphez csatlakozik, például akkor is át kell helyezni a virtuális gép, és egyéb kapcsolódó virtuális gép erőforrásokhoz. Helyezze át a hálózati adaptert, olvassa el a [erőforrás áthelyezése egy új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal) cikk. A cikk felsorolja az előfeltételeket és az Azure-portálon, PowerShell és az Azure parancssori felület használatával erőforrások áthelyezése.
    - **IP-konfiguráció:** nyilvános és magánhálózati IPv4 és IPv6 típusú címek rendelt IP-konfigurációk az itt felsorolt. Ha IPv6-cím hozzá van rendelve egy IP-konfiguráció, a cím nem jelenik meg. További tudnivalók az IP-konfigurációk és címek hozzáadásának és IP-cím eltávolítása a [konfigurálja az IP-címeket az Azure hálózati illesztő](virtual-network-network-interface-addresses.md) cikk. IP-továbbítás és alhálózat-hozzárendelés ebben a szakaszban is vannak konfigurálva. Ezek a beállítások kapcsolatos további tudnivalókért olvassa el a [engedélyezi vagy letiltja az IP-továbbítás](#enable-or-disable-ip-forwarding) és [alhálózat-hozzárendelés módosítása](#change-subnet-assignment) a cikk szakaszaiban.
    - **DNS-kiszolgálók:** melyik Azure DHCP-kiszolgálók által hozzárendelt hálózati illesztő DNS-kiszolgálót is megadhat. A hálózati adapter örökli a beállításokat a virtuális hálózatot a hálózati adapter van hozzárendelve, vagy egyéni beállítása, amely a virtuális hálózat társítva a beállítás felülbírálja. Mi jelenjen meg módosításához lépéseinek végrehajtásához a [módosítás DNS-kiszolgálók](#change-dns-servers) című szakaszát.
    - **Hálózati biztonsági csoport (NSG):** jeleníti meg, amely NSG-t (ha van ilyen) a hálózati adapter társítva. Az NSG-t a hálózati illesztő hálózati forgalom szűrésére bejövő és kimenő szabályokat tartalmazza. Ha a hálózati adapter társítva egy NSG-t, a társított NSG neve jelenik meg. Mi jelenjen meg módosításához lépéseinek végrehajtásához a [kezelése a hálózati biztonsági csoport társítását](virtual-network-manage-nsg-arm-portal.md#manage-associations) cikk.
    - **Tulajdonságok:** jeleníti meg a hálózati felületén, beleértve a MAC-címét (Ha a hálózati adapter nincs csatlakoztatva a virtuális géphez üres), és az előfizetés megtalálható a vonatkozó beállítások kulcsát.
    - **Hatékony biztonsági szabályokat:** biztonsági szabály szerepel a listában, ha a hálózati illesztő csatlakozik egy futó virtuális gépre, és az NSG tartozik a hálózati adapter vagy az alhálózat van hozzárendelve. Mi jelenjen meg kapcsolatos további tudnivalókért olvassa el a [hibaelhárítása a hálózati biztonsági csoportok](virtual-network-nsg-troubleshoot-portal.md#nsg) cikk. Ha többet szeretne megtudni az NSG-k, olvassa el a [hálózati biztonsági csoportok](virtual-networks-nsg.md) cikk.
    - **Hatékony útvonalak:** útvonalak találhatók, ha a hálózati illesztő csatlakozik egy futó virtuális gépre. Az útvonalak is az Azure alapértelmezett útvonalak, bármely felhasználó által definiált útvonalak (UDR) és a BGP-útvonalakat, amelyek az alhálózat, a hálózati illesztő hozzá van rendelve. Előfordulhat, hogy létezik. Mi jelenjen meg kapcsolatos további tudnivalókért olvassa el a [útvonalak hibaelhárítása](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface) cikk. Az Azure alapértelmezett és udr-EK kapcsolatos további tudnivalókért olvassa el a [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md) cikk.
    - **Közös Azure Resource Manager-beállítások:** közös Azure Resource Manager beállításaival kapcsolatos további tudnivalókért olvassa el a [tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [címkék](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Zárolja](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), és [automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) cikkeket.

**Parancsok**

Ha IPv6-cím hozzá van rendelve egy adott hálózati csatoló, a PowerShell kimeneti a tényt, hogy a cím hozzá van rendelve, de nem ad vissza a hozzárendelt címet, akkor adja vissza. Hasonlóképpen, a parancssori felület adja vissza az, hogy a cím hozzá van rendelve, de ad vissza *null* a cím kimenete.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az a hálózati adapter lista](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_list) hálózati illesztők megtekintése az előfizetés; [az hálózati nic megjelenítése](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_show) egy hálózati adapter beállításainak megtekintése|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati illesztők megtekintése az előfizetés vagy nézet beállításait egy adott hálózati csatoló|

## <a name="change-dns-servers"></a>Change DNS servers

A DNS-kiszolgáló a hálózati illesztő a virtuális gép operációs rendszerben a Azure DHCP-kiszolgáló által hozzárendelt. A hozzárendelt DNS-kiszolgáló, függetlenül a DNS-kiszolgáló beállítása a hálózati illesztő van. Egy adott hálózati csatoló neve feloldási beállításaitól kapcsolatos további információkért lásd: [névfeloldás a virtuális gépek](virtual-networks-name-resolution-for-vms-and-role-instances.md). A hálózati illesztő öröklik a virtuális hálózati beállításait, vagy használja a saját egyedi beállításokat, amelyek a virtuális hálózat beállításának felülbírálása.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati kapcsolat a beállításainak megtekintése vagy módosítása.
4. A kiválasztott hálózati adapter paneljén kattintson **DNS-kiszolgálók** alatt **beállítások**.
5. Jelölje be az:
    - **Virtuális hálózat (alapértelmezett) örökölhet**: válassza ezt a beállítást, hogy örökölje a meghatározott hálózati illesztőt be van-e rendelve a virtuális hálózat DNS-kiszolgáló beállítása. A virtuális hálózati szinten vagy egy egyéni DNS-kiszolgáló, vagy az Azure által biztosított DNS-kiszolgáló van definiálva. Az Azure által biztosított DNS-kiszolgáló fel tudja oldani az állomásnevek az erőforrásokhoz hozzárendelt ugyanahhoz a virtuális hálózathoz. FQDN kell használni a különböző virtuális hálózatokon rendelt erőforrások feloldásához.
    - **Egyéni**: beállíthatja a saját DNS-kiszolgáló több virtuális hálózaton keresztül a nevek feloldásához. Adja meg a kiszolgáló egy DNS-kiszolgálóként használni kívánt IP-címét. A DNS-kiszolgáló címét adja meg, hogy csak a hálózati adapter nincs hozzárendelve, és minden hálózati kapcsolat hozzá van rendelve a virtuális hálózat DNS-beállítása felülírja.
6. Kattintson a **Save** (Mentés) gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter frissítése](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Engedélyezi vagy letiltja az IP-továbbítás

IP-továbbítás lehetővé teszi, hogy a virtuális gép egy hálózati adapter csatlakozik:
- Nem a hozzárendelt hálózati illesztő IP-konfigurációk bármelyike rendelt IP-címek egyikére szánt hálózati forgalom fogadására.
- Egy adott hálózati csatoló IP-konfigurációk rendelt egy eltérő forrás IP-címek a hálózati forgalom elküldése.

Minden hálózati adapter, amely a virtuális gép kell továbbítani a forgalmat fogadó virtuális géphez csatolt engedélyezni kell a beállítást. A virtuális gépek is továbbítsa a forgalmat, hogy több hálózati adapterrel rendelkezik, vagy egyetlen hálózati adapter nem csatlakoztatható. Míg IP-továbbítás egy Azure beállítás, a virtuális gép is futtatnia kell egy alkalmazás tudnák továbbítani a forgalmat, például tűzfalat, WAN-optimalizálást és terheléselosztást végző alkalmazások. Amikor egy virtuális gép hálózati alkalmazások fut, a virtuális gép gyakran nevezik egy virtuális hálózati berendezések. A virtuális készülékek hálózati üzembe helyezésére listáját megtekintheti a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-továbbítás jellemzően a felhasználó által definiált útvonalak. A felhasználó által megadott útválasztással kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md) ismertető cikket.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a engedélyezi vagy letiltja az IP-továbbítást a kívánt hálózati adapter.
4. A kiválasztott hálózati adapter paneljén kattintson **IP-konfigurációk** a a **beállítások** szakasz.
5. Kattintson a **engedélyezve** vagy **letiltott** (alapértelmezett beállítás) a beállítást.
6. Kattintson a **Save** (Mentés) gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter frissítése](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Alhálózat-hozzárendelés módosítása

Az alhálózat, de nem a virtuális hálózaton, egy adott hálózati csatoló rendelt módosíthatja.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **hálózati illesztőt** panel, amelyen megjelenik, kattintson a hálózati kapcsolat a beállításainak megtekintése vagy módosítása.
4. Kattintson a **IP-konfigurációk** alatt **beállítások** a kiválasztott hálózati adapter a panelen. Ha az összes IP-konfiguráció magánhálózati IP-címek felsorolt **(statikus)** mellett, módosítania kell a IP-cím-hozzárendelési módszert dinamikus a következő lépések végrehajtásával. Összes magán IP-címet kell rendelni, amelyben a dinamikus hozzárendelési módszert a hálózati illesztő alhálózat-hozzárendelés módosítása. Ha a címek a dinamikus módszer van hozzárendelve, folytassa a öt. Ha minden IPv4-címet a statikus hozzárendelési módszer van hozzárendelve, az alábbi lépésekkel dinamikus a hozzárendelés módszerének módosítása:
    - Kattintson az IPv4 cím-hozzárendelési módszert az IP-konfigurációk közül módosítani kívánt IP-konfigurációja.
    - Kattintson a panel, amely akkor jelenik meg az IP-konfiguráció, **dinamikus** a a **hozzárendelés** metódust. IPv6-cím nem rendelhető hozzá a statikus hozzárendelés metódussal.
    - Kattintson a **Save** (Mentés) gombra.
5. Jelölje ki az alhálózatot, amelyhez csatlakozni a hálózati illesztő a az **alhálózati** legördülő listából.
6. Kattintson a **Save** (Mentés) gombra. Új dinamikus címek vannak hozzárendelve az új alhálózat alhálózat címtartományából. A hálózati illesztő rendel egy új alhálózatot, miután hozzárendelheti egy statikus IPv4-cím új alhálózat címtartományából Ha úgy dönt. További hamarosan hozzáadása, módosítása és eltávolítása a hálózati illesztő IP-címek, olvassa el a [kezelése IP-címek](virtual-network-network-interface-addresses.md) cikk.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter ip-konfiguráció frissítése](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>A hálózati illesztő törlése

Egy adott hálózati csatoló törölheti, amíg a virtuális géphez nincs csatlakoztatva. Ha egy virtuális géphez van csatolva, kell először helyezze a virtuális gép leállított (felszabadított) állapotában, majd válassza le a hálózati illesztő a virtuális gépről, a hálózati illesztő törlése előtt. Leválasztani a hálózati adaptert egy virtuális gépről, lépéseinek végrehajtásához a [leválasztani a hálózati adaptert egy virtuális gép](virtual-network-network-interface-vm.md#vm-remove-nic) szakasza a [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md) cikk. Egy virtuális gép törlése leválasztja az összes hálózati kapcsolat kapcsolódik, de nem törli a hálózati adapterek.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a hálózat közreműködő szerepkört az előfizetés (minimum) hozzárendelt engedélyeit. Olvassa el a [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) cikk tudhat meg többet a szerepköröket és engedélyeket hozzárendelése a fiókokhoz.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelenik meg a keresési eredmények között kattintson rá.
3. Kattintson a jobb gombbal a hálózati illesztő törlése, és kattintson a kívánt **törlése**.
4. Kattintson a **Igen** ellenőrizze a hálózati kapcsolat törlése.

Ha töröl egy adott hálózati csatoló, bármely MAC vagy IP-címek hozzárendelve kiadásakor.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati hálózati delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>További lépések
Hozzon létre egy virtuális gép több hálózati adapterek vagy IP-címek, olvassa el a következő cikkeket:

**Parancsok**

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy hálózati adapter virtuális több IPv4-címekkel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy hálózati adapter virtuális magánhálózati IPv6-cím (mögött egy Azure Load Balancer)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
