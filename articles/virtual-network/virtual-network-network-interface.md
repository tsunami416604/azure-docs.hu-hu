---
title: Létrehozása, módosítása vagy egy Azure hálózati illesztő törlése |} Microsoft Docs
description: Ismerje meg a hálózati adaptert létrehozása, beállításainak módosításához és törléséhez.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 65e461eaebaafab6f8a95bed333928d017c540d4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Létrehozása, módosítása vagy a hálózati illesztő törlése

Megtudhatja, hogyan hozzon létre, beállításainak módosítása és törlése a hálózati adaptert. Egy adott hálózati csatoló lehetővé teszi, hogy az Azure virtuális gép internet, Azure és a helyszíni erőforrások folytatott kommunikációhoz. Az Azure portál használatával virtuális gép létrehozásakor a portál alapértelmezett beállításokkal, hoz létre egy hálózati adapterrel. Ehelyett választhatja, hogy egyéni beállításokkal hozza létre a hálózati adapterek, és adja hozzá a virtuális gép egy vagy több hálózati illesztőre létrehozásakor. Érdemes lehet is, módosíthatja az alapértelmezett hálózati kapcsolati beállítások egy meglévő hálózati illesztő. Ez a cikk azt ismerteti, hogyan hozzon létre egy adott hálózati csatoló egyéni beállításokkal, módosítsa a meglévő beállítások, például hálózati szűrő (hálózati biztonsági csoport) hozzárendelés alhálózat-hozzárendelés, DNS-kiszolgáló beállításai vagy IP-továbbítást, és a hálózati illesztő törlése.

Ha kell hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő IP-címek, lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md). Ha hozzá kell adnia, vagy távolítsa el a hálózati adapterek virtuális gépekről, tekintse meg a hálózati adapterek [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.4.1 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.28 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

Hozzá kell rendelni a fiókot, jelentkezzen be, vagy csatlakozzon az Azure-ba, a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-network-interface"></a>A hálózati illesztő létrehozása

Az Azure portál használatával virtuális gép létrehozásakor a portált egy adott hálózati csatoló alapértelmezett beállításokat hoz létre. A hálózati kapcsolati beállítások ahelyett, hogy meg kellene megadnia, ha egyéni beállításokkal hozza létre a hálózati adaptert, és a hálózati adapter csatlakoztatása egy virtuális géphez, a virtuális gép (a PowerShell vagy az Azure parancssori felület használatával) létrehozásakor. Hozzon létre egy adott hálózati csatoló is, és adja hozzá egy meglévő virtuális gép (a PowerShell vagy az Azure parancssori felület használatával). További információt a meglévő hálózati illesztő a virtuális gép létrehozásához, vagy adja hozzá, vagy távolítsa el a hálózati adapterek a meglévő virtuális gépek, lásd: [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md). Mielőtt létrehozna egy adott hálózati csatoló, rendelkeznie kell egy meglévő [virtuális hálózati](manage-virtual-network.md#create-a-virtual-network) azonos helyen és előfizetést hoz létre a hálózati illesztő.

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki **+ Hozzáadás** alatt **hálózati illesztőt**.
3. Adja meg, vagy válassza ki a következő beállítások értékeit, majd válassza ki **létrehozása**:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie. Adott idő alatt akkor valószínűleg kell több hálózati adapterrel az Azure-előfizetésben. Javaslatok elnevezési létrehozásakor több hálózati adapterrel könnyebb kezelése, lásd: [elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). A név nem lehet módosítani, a hálózati illesztő létrehozása után.|
    |Virtuális hálózat|Igen|Válassza ki a virtuális hálózatot a hálózati adapter. Csak egy virtuális hálózatot, ugyanazt az előfizetést és a hálózati adapter és a helyen található rendelhet egy adott hálózati csatoló. A hálózati illesztő létrehozása után hozzá van rendelve a virtuális hálózat nem módosítható. A virtuális gépet, akkor adja hozzá a hálózati adaptert is szerepelnie kell az azonos helyen és a hálózati adapterként előfizetés.|
    |Alhálózat|Igen|Válassza ki egy alhálózatot a kiválasztott virtuális hálózaton belül. Az alhálózat, a hálózati adapter van rendelve létrehozása után módosíthatja.|
    |Privát IP-cím hozzárendelése|Igen| Ezt a beállítást, az IPv4-cím hozzárendelés módszer van kiválasztása. A következő hozzárendelési módszer közül választhat: **dinamikus:** Ha ezt a lehetőséget választja, Azure automatikusan rendeli hozzá a következő elérhető címek a kiválasztott alhálózat címtartománya. **Statikus:** Ha ezt a lehetőséget választja, kézzel kell rendelnie egy szabad IP-cím az alhálózat kijelölt belülre. Statikus és dinamikus címek nem megváltoztatni, amíg meg nem módosítja őket, vagy a hálózati illesztő törlése. A hálózati illesztő létrehozása után módosíthatja a hozzárendelési módszert. Az Azure DHCP-kiszolgáló ezt a címet rendel a virtuális gép operációs rendszerében a hálózati illesztőt.|
    |Hálózati biztonsági csoport|Nem| Hagyja beállítása **nincs**, válasszon ki egy létező [hálózati biztonsági csoport](virtual-networks-nsg.md), vagy [hálózati biztonsági csoport létrehozása](virtual-networks-create-nsg-arm-pportal.md). Hálózati biztonsági csoportok lehetővé teszi, hogy egy adott hálózati csatoló mindkét hálózati forgalom szűrésére. Nulla vagy egy hálózati biztonsági csoport egy adott hálózati csatoló alkalmazhatja. Nulla vagy egy hálózati biztonsági csoportot az alhálózathoz van hozzárendelve a hálózati adapter is alkalmazhatók. Néha váratlan eredményekhez fordulhat elő, a hálózati biztonsági csoport alkalmazásakor a hálózati adaptert és az alhálózatot a hálózati adapter van hozzárendelve. Hálózati adapterek és alhálózatok alkalmazott hálózati biztonsági csoportok elhárításához lásd: [hibaelhárítása a hálózati biztonsági csoportok](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |Előfizetés|Igen|Válasszon egyet az Azure [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). A virtuális gép csatlakoztatása a hálózati adaptert, és kösse össze a virtuális hálózat ugyanabban az előfizetésben léteznie kell.|
    |Magánhálózati IP-cím (IPv6)|Nem| Ha bejelöli ezt a jelölőnégyzetet, az IPv6-címek a hálózati kapcsolat mellett a hálózati illesztő rendelt IPv4-cím van hozzárendelve. Tekintse meg a [IPv6](#IPv6) című szakaszban a fontos adatokat IPv6 hálózati adapterrel együtt. Nem választhat ki egy hozzárendelési módszert az IPv6-cím használatára. Ha IPv6-címet hozzárendelni, a dinamikus módszerrel van hozzárendelve.
    |IPv6-név (csak akkor jelenik meg, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ez a név egy másodlagos IP-konfiguráció a hálózati adapter van hozzárendelve. IP-konfigurációkkal kapcsolatos további tudnivalókért lásd: [hálózati kapcsolati beállítások megtekintése](#view-network-interface-settings).|
    |Erőforráscsoport|Igen|Válasszon ki egy létező [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vagy hozzon létre egyet. Egy adott hálózati csatoló létezhet ugyanazon vagy másik erőforráscsoportban, mint a virtuális gépnek csatlakoztassa, vagy a virtuális hálózathoz csatlakoztatja a.|
    |Hely|Igen|A virtuális gép csatlakoztatása a hálózati adaptert, és kösse össze a virtuális hálózat létezhet ugyanazon [hely](https://azure.microsoft.com/regions), más néven egy régiót.|

A portál egy nyilvános IP-cím hozzárendelése a hálózati kapcsolat létrehozásakor, ha a portálon hozzon létre egy nyilvános IP-címet, és rendelje hozzá egy adott hálózati csatoló a portál virtuális gép létrehozásakor a beállítás nem biztosít. A nyilvános IP-cím hozzáadása a hálózati illesztő létrehozását követően további tudnivalókért lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md). Ha szeretne létrehozni egy adott hálózati csatoló nyilvános IP-címmel, a hálózati illesztő létrehozása a parancssori felületen vagy a PowerShell kell használnia.

A portál nem biztosít lehetőséget a hálózati illesztő hozzárendelése a biztonsági csoportok, de az Azure CLI és a PowerShell. Biztonsági csoportok kapcsolatos további információkért lásd: [biztonsági csoportok](security-overview.md#application-security-groups).

>[!Note]
> Azure csak azután a hálózati illesztő virtuális géphez csatlakozik, és a virtuális gép indításánál először rendel a hálózati adapter MAC-címet. Azure rendel hozzá a hálózati adapter MAC-címe nem adható meg. A MAC-cím megmarad, a hálózati adapter csak a hálózati illesztő törölték, vagy a magánhálózati IP-címet hozzárendelni az elsődleges hálózati adapter elsődleges IP-konfigurációja módosul. IP-címek és IP-konfigurációk kapcsolatos további információkért lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface#create)|

## <a name="view-network-interface-settings"></a>Hálózati kapcsolat beállításainak megjelenítése

Megtekintheti és módosíthatja a legtöbb beállítást egy adott hálózati csatoló létrehozása után. A portál nem jelenik meg a DNS utótagot vagy az alkalmazás biztonsági csoport tagsága a hálózati adapter. Használhatja a PowerShell vagy Azure CLI [parancsok](#view-settings-commands) DNS utótagot és az alkalmazás biztonsági csoport tagságát.

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati kapcsolat beállításainak megtekintése vagy módosítása a listából.
3. A kiválasztott hálózati adapter a következő elemek listáját:
    - **Áttekintés:** információkat nyújt azokról a hálózati adapter, például az IP-címek hozzárendelve, a virtuális hálózatot/alhálózatot a hálózati adapter van hozzárendelve, és a virtuális gép hálózati illesztő csatlakozik (ha van ellátva. egy). Az alábbi képen látható nevű hálózati illesztő – áttekintés beállításait **mywebserver256**: ![hálózati illesztő – áttekintés](./media/virtual-network-network-interface/nic-overview.png) egy adott hálózati csatoló áthelyezheti egy másik erőforráscsoportban található, vagy előfizetés kiválasztásával (**módosítása**) mellett a **erőforráscsoport** vagy **előfizetés neve**. Ha a hálózati illesztő helyezi át, a hálózati illesztő vele kapcsolatos összes erőforrás kell áthelyeznie. Ha a hálózati illesztő virtuális géphez csatlakozik, például akkor is át kell helyezni a virtuális gép, és egyéb kapcsolódó virtuális gép erőforrásokhoz. Egy adott hálózati csatoló áthelyezésével kapcsolatban lásd: [erőforrás áthelyezése egy új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). A cikk felsorolja az előfeltételeket és az Azure-portálon, PowerShell és az Azure parancssori felület használatával erőforrások áthelyezése.
    - **IP-konfiguráció:** nyilvános és magánhálózati IPv4 és IPv6 típusú címek rendelt IP-konfigurációk az itt felsorolt. Ha IPv6-cím hozzá van rendelve egy IP-konfiguráció, a cím nem jelenik meg. IP-konfigurációk és IP-címek hozzáadásának és eltávolításának módját kapcsolatos további információkért lásd: [konfigurálja az IP-címeket az Azure hálózati illesztő](virtual-network-network-interface-addresses.md). IP-továbbítás és alhálózat-hozzárendelés ebben a szakaszban is vannak konfigurálva. Ezek a beállítások kapcsolatos további információkért lásd: [engedélyezi vagy letiltja az IP-továbbítás](#enable-or-disable-ip-forwarding) és [alhálózat-hozzárendelés módosítása](#change-subnet-assignment).
    - **DNS-kiszolgálók:** melyik Azure DHCP-kiszolgálók által hozzárendelt hálózati illesztő DNS-kiszolgálót is megadhat. A hálózati adapter örökli a beállításokat a virtuális hálózatot a hálózati adapter van hozzárendelve, vagy egyéni beállítása, amely a virtuális hálózat társítva a beállítás felülbírálja. Mi jelenjen meg módosításához lásd [módosítás DNS-kiszolgálók](#change-dns-servers).
    - **Hálózati biztonsági csoport (NSG):** jeleníti meg, amely NSG-t (ha van ilyen) a hálózati adapter társítva. Az NSG-t a hálózati illesztő hálózati forgalom szűrésére bejövő és kimenő szabályokat tartalmazza. Ha a hálózati adapter társítva egy NSG-t, a társított NSG neve jelenik meg. Mi jelenjen meg módosításához lásd [társítása vagy leválasztani a hálózati biztonsági csoport](#associate-or-dissociate-a-network-security-group).
    - **Tulajdonságok:** jeleníti meg a hálózati felületén, beleértve a MAC-címét (Ha a hálózati adapter nincs csatlakoztatva a virtuális géphez üres), és az előfizetés megtalálható a vonatkozó beállítások kulcsát.
    - **Hatékony biztonsági szabályokat:** biztonsági szabály szerepel a listában, ha a hálózati illesztő csatlakozik egy futó virtuális gépre, és az NSG tartozik a hálózati adapter vagy az alhálózat van hozzárendelve. Mi jelenjen meg kapcsolatos további információkért lásd: [hatékony biztonsági szabályok megtekintéséhez](#view-effective-security-rules). Az NSG-k kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](security-overview.md).
    - **Hatékony útvonalak:** útvonalak találhatók, ha a hálózati illesztő csatlakozik egy futó virtuális gépre. Az útvonalak is az Azure alapértelmezett útvonalak, bármely felhasználó által definiált útvonalak és a BGP-útvonalakat, amelyek az alhálózat, a hálózati illesztő hozzá van rendelve. Előfordulhat, hogy létezik. Mi jelenjen meg kapcsolatos további információkért lásd: [hatékony útvonalak megtekintése](#view-effective-routes). Az Azure alapértelmezett útvonalak és a felhasználó által definiált útvonalak kapcsolatos további információkért lásd: [Útválasztás – áttekintés](virtual-networks-udr-overview.md).
    - **Közös Azure Resource Manager-beállítások:** közös Azure Resource Manager beállításaival kapcsolatos további tudnivalókért lásd: [tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Zárolja](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), és [automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Parancsok**

Ha IPv6-cím hozzá van rendelve egy adott hálózati csatoló, a PowerShell kimeneti a tényt, hogy a cím hozzá van rendelve, de nem ad vissza a hozzárendelt címet, akkor adja vissza. Hasonlóképpen, a parancssori felület adja vissza az, hogy a cím hozzá van rendelve, de ad vissza *null* a cím kimenete.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az a hálózati adapter lista](/cli/azure/network/nic#az_network_nic_list) hálózati illesztők megtekintése az előfizetés; [az hálózati nic megjelenítése](/cli/azure/network/nic#az_network_nic_show) egy hálózati adapter beállításainak megtekintése|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) hálózati illesztők megtekintése az előfizetés vagy nézet beállításait egy adott hálózati csatoló|

## <a name="change-dns-servers"></a>Módosítsa a DNS-kiszolgálók

A DNS-kiszolgáló a hálózati illesztő a virtuális gép operációs rendszerben a Azure DHCP-kiszolgáló által hozzárendelt. A hozzárendelt DNS-kiszolgáló, függetlenül a DNS-kiszolgáló beállítása a hálózati illesztő van. Egy adott hálózati csatoló neve feloldási beállításaitól kapcsolatos további információkért lásd: [névfeloldás a virtuális gépek](virtual-networks-name-resolution-for-vms-and-role-instances.md). A hálózati illesztő öröklik a virtuális hálózati beállításait, vagy használja a saját egyedi beállításokat, amelyek a virtuális hálózat beállításának felülbírálása.

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati adapter, amely módosítja a DNS-kiszolgálót a listából.
3. Válassza ki **DNS-kiszolgálók** alatt **beállítások**.
4. A következők közül választhat:
    - **Virtuális hálózati örökölhet**: válassza ezt a beállítást, hogy örökölje a meghatározott hálózati illesztőt be van-e rendelve a virtuális hálózat DNS-kiszolgáló beállítása. A virtuális hálózati szinten vagy egy egyéni DNS-kiszolgáló, vagy az Azure által biztosított DNS-kiszolgáló van definiálva. Az Azure által biztosított DNS-kiszolgáló fel tudja oldani az állomásnevek az erőforrásokhoz hozzárendelt ugyanahhoz a virtuális hálózathoz. FQDN kell használni a különböző virtuális hálózatokon rendelt erőforrások feloldásához.
    - **Egyéni**: beállíthatja a saját DNS-kiszolgáló több virtuális hálózaton keresztül a nevek feloldásához. Adja meg a kiszolgáló egy DNS-kiszolgálóként használni kívánt IP-címét. A DNS-kiszolgáló címét adja meg, hogy csak a hálózati adapter nincs hozzárendelve, és minden hálózati kapcsolat hozzá van rendelve a virtuális hálózat DNS-beállítása felülírja.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter frissítése](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Engedélyezi vagy letiltja az IP-továbbítás

IP-továbbítás lehetővé teszi, hogy a virtuális gép egy hálózati adapter csatlakozik:
- Nem a hozzárendelt hálózati illesztő IP-konfigurációk bármelyike rendelt IP-címek egyikére szánt hálózati forgalom fogadására.
- Egy adott hálózati csatoló IP-konfigurációk rendelt egy eltérő forrás IP-címek a hálózati forgalom elküldése.

Minden hálózati adapter, amely a virtuális gép kell továbbítani a forgalmat fogadó virtuális géphez csatolt engedélyezni kell a beállítást. A virtuális gépek is továbbítsa a forgalmat, hogy több hálózati adapterrel rendelkezik, vagy egyetlen hálózati adapter nem csatlakoztatható. Míg IP-továbbítás egy Azure beállítás, a virtuális gép is futtatnia kell egy alkalmazás tudnák továbbítani a forgalmat, például tűzfalat, WAN-optimalizálást és terheléselosztást végző alkalmazások. Amikor egy virtuális gép hálózati alkalmazások fut, a virtuális gép gyakran nevezik egy virtuális hálózati berendezések. A virtuális készülékek hálózati üzembe helyezésére listáját megtekintheti a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-továbbítás jellemzően a felhasználó által definiált útvonalak. Felhasználó által definiált útvonalak kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md).

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a használni kívánt engedélyezi vagy letiltja az IP-továbbítást a hálózati kapcsolat.
3. Válassza ki **IP-konfigurációk** a a **beállítások** szakasz.
4. Válassza ki **engedélyezve** vagy **letiltott** (alapértelmezett beállítás) a beállítást.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter frissítése](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Alhálózat-hozzárendelés módosítása

Az alhálózat, de nem a virtuális hálózaton, egy adott hálózati csatoló rendelt módosíthatja.

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati adapter, amely módosítja az alhálózat-hozzárendelés.
3. Válassza ki **IP-konfigurációk** alatt **beállítások**. Ha az összes IP-konfiguráció magánhálózati IP-címek felsorolt **(statikus)** mellett, módosítania kell a IP-cím-hozzárendelési módszert dinamikus a következő lépések végrehajtásával. Összes magán IP-címet kell rendelni, amelyben a dinamikus hozzárendelési módszert a hálózati illesztő alhálózat-hozzárendelés módosítása. Ha a címek a dinamikus módszer van hozzárendelve, folytassa a öt. Ha minden IPv4-címet a statikus hozzárendelési módszer van hozzárendelve, az alábbi lépésekkel dinamikus a hozzárendelés módszerének módosítása:
    - Válassza ki az IPv4 cím-hozzárendelési módszert az IP-konfigurációk közül módosítani kívánt IP-konfigurációja.
    - Válassza ki **dinamikus** a magánhálózati IP-cím **hozzárendelés** metódust. IPv6-cím nem rendelhető hozzá a statikus hozzárendelés metódussal.
    - Kattintson a **Mentés** gombra.
4. Jelölje ki az alhálózatot a hálózati illesztő a át szeretné helyezni a **alhálózati** legördülő listából.
5. Kattintson a **Mentés** gombra. Új dinamikus címek vannak hozzárendelve az új alhálózat alhálózat címtartományából. A hálózati illesztő rendel egy új alhálózatot, miután hozzárendelheti egy statikus IPv4-cím új alhálózat címtartományából Ha úgy dönt. További hamarosan hozzáadása, módosítása és eltávolítása a hálózati illesztő IP-címek, lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter ip-konfiguráció frissítése](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Vegyen fel vagy távolítsa el a biztonsági csoportok

A portál nem rendelhető hozzá a hálózati adaptert a beállítást, vagy távolítsa el a hálózati adaptert a biztonsági csoportok, de az Azure CLI és a PowerShell. Biztonsági csoportok kapcsolatos további információkért lásd: [biztonsági csoportok](security-overview.md#application-security-groups) és [alkalmazás biztonsági csoport létrehozása](#create-an-application-security-group).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózat hálózati adapter frissítése](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Leválasztja a hálózati biztonsági csoport vagy társítása

1. Adja meg a keresési mezőbe, a portál felső, *hálózati illesztőt* be a keresőmezőbe. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Jelölje be a hálózati adaptert szeretne hozzárendelni a hálózati biztonsági csoport listában szereplő, vagy leválasztani a hálózati biztonsági csoportot.
3. Válassza ki **hálózati biztonsági csoport** alatt **beállítások**.
4. Válassza a **Szerkesztés** elemet.
5. Válassza ki **hálózati biztonsági csoport** , és válassza ki a hálózati biztonsági csoport szeretné társítani a hálózati adapter, vagy válasszon **nincs**, leválasztani a hálózati biztonsági csoport.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az hálózat hálózati adapter frissítése](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>A hálózati illesztő törlése

Egy adott hálózati csatoló törölheti, amíg a virtuális géphez nincs csatlakoztatva. Ha a hálózati adaptert egy virtuális géphez van csatolva, kell először helyezze a virtuális gép leállított (felszabadított) állapotában, majd leválasztja a virtuális gépről a hálózati illesztőt. Válassza le a hálózati adaptert egy virtuális gépről, hajtsa végre a lépéseket a [leválasztani a hálózati adaptert egy virtuális gép](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). A hálózati adaptert egy virtuális gép nem választható le, ha a virtuális géphez csatolt azonban egyetlen hálózati illesztőt. A virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható. Egy virtuális gép törlése leválasztja az összes hálózati kapcsolat kapcsolódik, de nem törli a hálózati adapterek.

1. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *hálózati illesztőt*. Ha **hálózati illesztőt** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki **...**  a jobb oldalon, a hálózati illesztők listája a törölni kívánt hálózati adapter.
3. Válassza a **Törlés** elemet.
4. Válassza ki **Igen** ellenőrizze a hálózati kapcsolat törlése.

Ha töröl egy adott hálózati csatoló, bármely MAC vagy IP-címek hozzárendelve kiadásakor.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati hálózati delete](/cli/azure/network/nic#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldásához

Ha nem lehet kommunikálni az vagy egy virtuális gép, hálózati biztonsági csoport biztonsági szabályai vagy egy adott hálózati csatoló hatékony útvonalakat, okozza a problémát. Lehetősége van a következő a probléma megoldása érdekében:

### <a name="view-effective-security-rules"></a>Hatékony biztonsági szabályok megtekintése

Mindegyik hálózati interfész egy virtuális géphez csatolt a hatékony biztonsági szabályok a hálózati biztonsági csoport a létrehozott szabályok kombinációját és [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules). Az egy adott hálózati csatoló a hatékony biztonsági szabályainak ismertetése segítségével meghatározhatja, miért nem lehet kommunikálni a hálózati adapterre vagy a virtuális gép van. A hatékony szabályokat, amely csatolva van egy futó virtuális gép hálózati csatolóhoz tekintheti meg.

1. A keresési mezőbe, a portál felső adja meg meg szeretné tekinteni a hatékony biztonsági szabályait a virtuális gép nevét. Ha nem tudja a virtuális gép nevét, adja meg a *virtuális gépek* be a keresőmezőbe. Ha **virtuális gépek** jelennek meg a keresési eredmények között, válassza ki azt, majd válassza ki egy virtuális gépet a listából.
2. Válassza ki **hálózati** alatt **beállítások**.
3. Válassza ki a hálózati kapcsolat neve.
4. Válassza ki **hatékony biztonsági szabályok** alatt **támogatási + hibaelhárítás**.
5. Tekintse át a hatékony biztonsági szabályok használatával határozza meg, ha a szükséges bejövő és kimenő kommunikáció szerepel a megfelelő szabály listáját. További információk láthatók a lista [hálózati biztonsági csoport – áttekintés](security-overview.md).

Az IP-adatfolyam ellenőrizze, hogy az Azure hálózati figyelőt szolgáltatása is segítségével meghatározhatja, hogy ha a biztonsági szabályok akadályozzák a virtuális gép és a végpontok közötti kommunikáció. További tudnivalókért lásd: [IP folyamata ellenőrizze](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Az Azure CLI: [az hálózati lista-hatályos-nsg hálózati adapter](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Hatékony útvonalak megtekintése

A hálózati illesztő a virtuális géphez csatolt hatékony útvonalak bármely útvonalakat a helyszíni hálózatokban BGP keresztül egy Azure virtuális hálózati átjárón keresztül érvényesítve, bármely létrehozott útvonalak és alapértelmezett útvonalak kombinációját. Egy adott hálózati csatoló hatékony útvonalak ismertetése segítségével meghatározhatja, miért nem lehet kommunikálni a hálózati adapterre vagy a virtuális gép még. A futó virtuális gép csatlakoztatott hálózati csatolóhoz hatékony útvonalait tekintheti meg.

1. A keresési mezőbe, a portál felső adja meg meg szeretné tekinteni a hatékony biztonsági szabályait a virtuális gép nevét. Ha nem tudja a virtuális gép nevét, adja meg a *virtuális gépek* be a keresőmezőbe. Ha **virtuális gépek** jelennek meg a keresési eredmények között, válassza ki azt, majd válassza ki egy virtuális gépet a listából.
2. Válassza ki **hálózati** alatt **beállítások**.
3. Válassza ki a hálózati kapcsolat neve.
4. Válassza ki **hatékony útvonalak** alatt **támogatási + hibaelhárítás**.
5. Tekintse át a meghatározásához, hogy a helyes útvonalak vannak-e a szükséges bejövő és kimenő kommunikáció a hatékony útvonalak listáját. További információk láthatók a lista [Útválasztás – áttekintés](virtual-networks-udr-overview.md).

Az Azure hálózati figyelőt a következő ugrás szolgáltatása is segítségével meghatározhatja, hogy ha útvonalak akadályozzák a virtuális gép és a végpontok közötti kommunikáció. További tudnivalókért lásd: [a következő Ugrás](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Az Azure CLI: [az hálózati nic megjelenítése-hatályos-útvonal-tábla](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Engedélyek

A hálózati adaptereken feladatait, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő engedélyekkel a következő táblázatban felsorolt:

| Műveletek                                                                     | Name (Név)                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hálózati illesztő beolvasása                                     |
| Microsoft.Network/networkInterfaces/write                                  | Létrehozni vagy frissíteni a hálózati illesztőt                        |
| Microsoft.Network/networkInterfaces/join/action                            | Egy hálózati adapter csatlakoztatása egy virtuális géphez           |
| Microsoft.Network/networkInterfaces/delete                                 | A hálózati illesztő törlése                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Egy erőforrás csatlakoztatása egy hálózati adapteren keresztül egy szerviz...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Hálózati illesztő effektív útvonaltábla beolvasása               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hálózati illesztő hatékony biztonsági csoport lekérése           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hálózati illesztő terheléselosztóinak beolvasása                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Szolgáltatás-társítás beolvasása                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Létrehozni vagy frissíteni a szolgáltatás társítása                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Szolgáltatás-társítás törlése                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Ellenőrizze a szolgáltatás társítása                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hálózati illesztő IP-konfiguráció beolvasása                    |

## <a name="next-steps"></a>További lépések

- Segítségével több hálózati adapterrel rendelkező virtuális gép létrehozása a [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Hozzon létre egy hálózati adapter virtuális gép, és több IPv4-címek használata a [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) vagy [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Hozzon létre egy hálózati adapter virtuális egy titkos IPv6 cím (mögött egy Azure Load Balancer) használatával a [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
- Hozzon létre egy hálózati illesztő használatával [PowerShell](powershell-samples.md) vagy [Azure CLI](cli-samples.md) parancsfájlok, vagy az Azure használatával [Resource Manager-sablonok](template-samples.md)
- Létrehozása és alkalmazása [Azure házirend](policy-samples.md) virtuális hálózatok