---
title: Létrehozása, módosítása vagy egy Azure-beli hálózati adapter törlése |} A Microsoft Docs
description: További hálózati adaptereket, és hogyan hozhat létre, módosítsa a beállításokat, és töröljön egy.
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
ms.openlocfilehash: 78578197c5f764c8e197d3426506cb1eb13b838f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956796"
---
# <a name="create-change-or-delete-a-network-interface"></a>Létrehozása, módosítása vagy egy hálózati adapter törlése

Ismerje meg, hogyan hozzon létre, módosítsa a beállításokat és egy hálózati adapter törlése. Hálózati adapter lehetővé teszi, hogy kommunikáljanak az internettel, az Azure és helyszíni erőforrások egy Azure virtuális gép. Az Azure portal használatával a virtuális gép létrehozásakor a portálon az alapértelmezett beállításokkal, hoz létre egy hálózati adapter. Ehelyett választhatja az egyéni beállításokkal rendelkező hálózati adapterek létrehozása és a egy vagy több hálózati adapter hozzáadása egy virtuális gép létrehozásakor. Érdemes módosítani az alapértelmezett hálózati kapcsolati beállítások egy meglévő hálózati adapter is. Ez a cikk bemutatja, hogyan lehet egyéni beállításokkal rendelkező hálózati adaptert létrehozni, a meglévő beállításokat, például a hálózati szűrés (hálózati biztonsági csoport) hozzárendelés, alhálózat-hozzárendelés, DNS-kiszolgáló beállításai és IP-továbbítást, és a egy hálózati adapter törlése.

Ha meg kell hozzáadása, módosítása vagy eltávolítása a hálózati illesztő IP-címeket, tekintse meg [kezelése IP-címek](virtual-network-network-interface-addresses.md). Ha a hálózati adaptereket, vagy távolítsa el a hálózati adapterek virtuális gépeiről, lásd: hozzá kell adnia [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az Azure PowerShell-modul 5.4.1-es vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-network-interface"></a>Hozzon létre egy hálózati adaptert

Az Azure portal használatával a virtuális gép létrehozásakor a portálon az alapértelmezett beállításokkal, létrehoz egy hálózati adaptert. Ha szeretné inkább adja meg a hálózati kapcsolati beállítások, egyéni beállításokkal rendelkező hálózati adaptert létrehozni, és a hálózati adaptert egy virtuális géphez csatolni a virtuális gép (a PowerShell vagy az Azure CLI) létrehozásakor. Hozzon létre egy hálózati adaptert, és adja hozzá egy meglévő virtuális gépre (a PowerShell vagy az Azure CLI) is. Megtudhatja, hogyan hozzon létre egy virtuális gépet egy meglévő hálózati adapter vagy a hozzá, vagy távolítsa el a hálózati adapterek a meglévő virtuális gépekről, lásd: [hozzáadása vagy eltávolítása a hálózati adapterek](virtual-network-network-interface-vm.md). Mielőtt létrehozna egy hálózati adapter, rendelkeznie kell egy meglévő [virtuális hálózat](manage-virtual-network.md#create-a-virtual-network) azonos helyen és előfizetést hoz létre egy hálózati adapterrel.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki **+ Hozzáadás** alatt **hálózati adapterek**.
3. Adja meg, vagy válassza ki a következő beállítások értékeit, majd válassza ki **létrehozás**:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name (Név)|Igen|A nevét, válassza ki az erőforráscsoporton belül egyedinek kell lennie. Idővel valószínűleg, akkor több hálózati adapterek az Azure-előfizetésében. Javaslatok elnevezési létrehozásakor, hogy több hálózati adapterrel egyszerűbb felügyelete, lásd: [elnevezési konvenciók](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). A név nem lehet módosítani, a hálózati adapter létrehozása után.|
    |Virtuális hálózat|Igen|Válassza ki a virtuális hálózatot a hálózati adapter. Hozzárendelhet egy hálózati adapter csak egy virtuális hálózathoz, hogy az ugyanazon előfizetésben és helyen, a hálózati adapter létezik. Hálózati adapter létrehozása után nem módosítható a virtuális hálózathoz van rendelve. A virtuális gép hozzáadása a hálózati adapter is szerepelnie kell az azonos helyen és előfizetésen a hálózati adaptert.|
    |Alhálózat|Igen|Válassza ki egy alhálózatot a kiválasztott virtuális hálózaton belül. Az alhálózatot a hálózati adapter van rendelve a létrehozást követően módosítható.|
    |Magánhálózati IP-cím hozzárendelése|Igen| Ebben a beállításban a IPv4-cím a hozzárendelési módszer választhassa. A következő hozzárendelési módszer közül választhat: **dinamikus:** ezen beállítás kiválasztásakor az Azure automatikusan rendeli hozzá a következő elérhető címe a kiválasztott alhálózat címterét. **Statikus:** amikor ezzel a beállítással manuálisan kell rendelnie egy elérhető IP-cím a kiválasztott alhálózat címterében. Statikus és dinamikus címek nem változtatja meg, amíg nem módosítja őket, vagy a hálózati adapter törlése. A hozzárendelési módszer a hálózati adapter létrehozása után módosíthatja. Az Azure DHCP-kiszolgáló ezt a címet a virtuális gép operációs rendszerén belül a hálózati adapterhez rendeli.|
    |Hálózati biztonsági csoport|Nem| Hagyja beállítása **nincs**, válasszon egy meglévő [hálózati biztonsági csoport](security-overview.md), vagy [hozzon létre egy hálózati biztonsági csoport](tutorial-filter-network-traffic.md). Hálózati biztonsági csoportok lehetővé teszik egy hálózati adapter kívüli hálózati forgalom szűrésére. Hálózati adapter nulla vagy egy hálózati biztonsági csoport alkalmazhat. Nulla vagy egy hálózati biztonsági csoportot az alhálózathoz, a hálózati adapterhez hozzárendelt is alkalmazható. Néha nem várt eredmények fordulhat elő, amikor egy hálózati adaptert, és az alhálózatot a hálózati adapterhez rendelt hálózati biztonsági csoport alkalmazza. Hálózati adapterek és alhálózatok alkalmazott hálózati biztonsági csoportok hibaelhárítása: [hálózati biztonsági csoportok hibaelhárítása](diagnose-network-traffic-filter-problem.md).|
    |Előfizetés|Igen|Válassza ki az Azure egyik [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). A virtuális gépet, csatlakoztassa a hálózati adaptert, és kösse össze a virtuális hálózat ugyanabban az előfizetésben léteznie kell.|
    |Magánhálózati IP-cím (IPv6)|Nem| Ha bejelöli ezt a jelölőnégyzetet, IPv6-címet a hálózati kapcsolat mellett a hálózati adapterhez rendelt IPv4-cím van hozzárendelve. Tekintse meg a [IPv6](#IPv6) fontos információkhoz juthat használata az IPv6 hálózati adapterrel rendelkező című szakaszban. Az IPv6-cím-hozzárendelési módszer nem választhatja ki. Ha IPv6-címet hozzárendelni, a dinamikus módszerrel van hozzárendelve.
    |IPv6-név (csak jelenik meg, amikor a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ez a név egy másodlagos IP-konfigurációt a hálózati adapter van hozzárendelve. IP-konfigurációval kapcsolatos további információkért lásd: [hálózatiadapter-beállítások megtekintése](#view-network-interface-settings).|
    |Erőforráscsoport|Igen|Válasszon egy meglévő [erőforráscsoport](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vagy hozzon létre egyet. Hálózati adapter az azonos vagy eltérő erőforráscsoportban, mint a virtuális gépet, csatlakoztassa is létezik, vagy a virtuális hálózathoz csatlakoztatja a.|
    |Hely|Igen|A hálózati adaptert a virtuális géphez csatolni, és kösse össze a virtuális hálózat léteznie kell az azonos [hely](https://azure.microsoft.com/regions), más néven egy régiót.|

A portál nem biztosít a nyilvános IP-cím hozzárendelése a hálózati adapter létrehozásakor, bár a portálon hozzon létre egy nyilvános IP-címet, és rendelje hozzá egy hálózati adaptert egy virtuális gépet a portálon létrehozott lehetőséget. Nyilvános IP-cím hozzáadása a hálózati adapter létrehozása után kapcsolatban lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md). Ha szeretne egy nyilvános IP-címmel rendelkező hálózati adaptert létrehozni, a hálózati adapter létrehozása a parancssori felület vagy PowerShell kell használnia.

A portál nem biztosít arra, hogy egy hálózati adapter létrehozásakor rendelje hozzá a hálózati adapter az alkalmazásbiztonsági csoportok, de az Azure CLI és PowerShell. Mindaddig, amíg egy virtuális géphez csatlakoztatott hálózati adapter van, egy meglévő hálózati adaptert rendelhet egy alkalmazásbiztonsági csoportot azonban a portál használatával. Hálózati adapter hozzárendelése egy alkalmazásbiztonsági csoportot kapcsolatban lásd: [való hozzáadása vagy eltávolítása az alkalmazásbiztonsági csoportok](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Az Azure csak egy virtuális géphez csatlakoztatott hálózati adapter van, és először elindul a virtuális gép után a hálózati adaptert rendel egy MAC-címet. Az Azure rendeli hozzá a hálózati adapter MAC-cím nem adható meg. A MAC-címet a hálózati adapterhez hozzárendelt maradnak, addig, amíg a hálózati adapter törlése vagy az elsődleges hálózati adapter elsődleges IP-konfigurációhoz rendelt magánhálózati IP-cím megváltozik. IP-címek és IP-konfigurációval kapcsolatos további információkért lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface#create)|

## <a name="view-network-interface-settings"></a>Hálózatiadapter-beállítások megtekintése

Megtekintheti és módosíthatja egy hálózati adapter beállításait a legtöbb létrehozása után. A portál nem jelenik meg a DNS utótagja vagy az alkalmazás biztonsági csoport tagsága a hálózati adapter. Használhatja a PowerShell vagy az Azure CLI [parancsok](#view-settings-commands) a DNS-utótagot, és az alkalmazás biztonsági csoport tagsága megtekintéséhez.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adaptert szeretne megtekinteni vagy módosítani a beállításokat a listából.
3. A következő elemek szerepelnek a kiválasztott hálózati adapter:
    - **Áttekintés:** ismerteti a hálózati adaptert, például azt, a virtuális hálózatot/alhálózatot a hálózati adapterhez van hozzárendelve, és a virtuális gép (ha csatlakoztatva van a hálózati adapterhez csatolt IP-címek egy). Az alábbi képen látható nevű hálózati adapter beállításainak áttekintése **mywebserver256**: ![hálózati adapter áttekintése](./media/virtual-network-network-interface/nic-overview.png) áthelyezheti egy hálózati adaptert egy másik erőforráscsoportban vagy előfizetés kiválasztásával (**módosítása**) mellett a **erőforráscsoport** vagy **előfizetésnevet**. Ha áthelyezi a hálózati adaptert, át kell helyeznie azt a hálózati adapterhez kapcsolódó összes erőforrást. A hálózati adapter egy virtuális géphez van csatlakoztatva, például akkor is át kell helyezni a virtuális gép és a kapcsolódó virtuális gép erőforrását. Hálózati adapter áthelyezése: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). A cikk felsorolja az előfeltételekről, és át kell helyeznie az erőforrásokat az Azure portal, PowerShell és az Azure CLI használatával.
    - **IP-konfiguráció:** nyilvános és magánhálózati IPv4 és IPv6-címek rendelt IP-konfigurációk láthatók. Ha IPv6-cím hozzá van rendelve egy IP-konfigurációt, a cím nem jelenik meg. IP-konfigurációk, és hogyan adhat hozzá, és távolítsa el az IP-címek kapcsolatos további információkért lásd: [konfigurálja az IP-címeket az Azure hálózati adapter](virtual-network-network-interface-addresses.md). IP-továbbítás és alhálózat-hozzárendelés ebben a szakaszban is vannak konfigurálva. Ezekkel a beállításokkal kapcsolatos további tudnivalókért lásd: [engedélyezheti vagy tilthatja le az IP-továbbítás](#enable-or-disable-ip-forwarding) és [alhálózat-hozzárendelés módosítása](#change-subnet-assignment).
    - **DNS-kiszolgálók:** megadhatja, hogy melyik DNS-kiszolgálót az Azure DHCP-kiszolgálók által hozzárendelt hálózati adapter. A hálózati adapter örökli a beállításokat a virtuális hálózatot a hálózati adapterhez van hozzárendelve, vagy egy egyéni beállítás, amely hozzá van rendelve a virtuális hálózathoz a beállítás felülbírálja. Mi jelenjen meg módosításához lásd [módosítása DNS-kiszolgálók](#change-dns-servers).
    - **Hálózati biztonsági csoport (NSG):** jeleníti meg, amelyre NSG-t hozzárendelik a hálózati adapterhez (ha van). Az NSG-KET a hálózati adapter hálózati forgalom szűrése bejövő és kimenő szabályokat tartalmaz. Ha a hálózati adapterhez társított NSG-t, a társított NSG neve jelenik meg. Mi jelenjen meg módosításához lásd [társítása vagy egy hálózati biztonsági csoport társításának megszüntetése](#associate-or-dissociate-a-network-security-group).
    - **Tulajdonságok:** jeleníti meg a hálózati adaptert, beleértve a MAC-cím (Ha egy virtuális géphez csatlakoztatott hálózati adapter nem üres), és az előfizetés meglévő kapcsolatos beállítások kulcs.
    - **Érvényes biztonsági szabályok:** biztonsági szabályok láthatók, ha egy futó virtuális géphez csatlakoztatott hálózati adapter van, és a egy NSG-t hozzárendelik a hálózati adaptert, az alhálózathoz van hozzárendelve, vagy mindkét. Mi jelenjen meg kapcsolatos további információkért lásd: [érvényes biztonsági szabályok megtekintése](#view-effective-security-rules). NSG-kkel kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](security-overview.md).
    - **Érvényes útvonalak:** útvonalak láthatók, ha egy futó virtuális géphez csatlakoztatott hálózati adapter van. Az útvonalak az Azure alapértelmezett útvonalakat, minden olyan felhasználó által megadott útvonalak és minden BGP-útvonalak, előfordulhat, hogy a hálózati adapter van rendelve az alhálózat létezik kombinációját is. Mi jelenjen meg kapcsolatos további információkért lásd: [érvényes útvonalak megtekintése](#view-effective-routes). Az Azure alapértelmezett útvonalakat és a felhasználó által megadott útvonalakkal kapcsolatos további tudnivalókért lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).
    - **Közös Azure Resource Manager-beállítások:** gyakori Azure Resource Manager-beállításokkal kapcsolatos további tudnivalókért lásd: [tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Zárolja](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), és [Automation-szkript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Parancsok**

Ha IPv6-cím van rendelve egy hálózati adaptert, a PowerShell kimenete az a tény, hogy a cím hozzá van rendelve, de azt nem ad vissza a hozzárendelt cím adja vissza. Hasonlóképpen, a parancssori felület adja vissza a tény, hogy a cím hozzá van rendelve, de ad vissza *null* a kimenetét a cím.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic list](/cli/azure/network/nic#az_network_nic_list) ; előfizetésben található hálózati adapterek megtekintése [az network nic show](/cli/azure/network/nic#az_network_nic_show) egy hálózati adapter beállításainak megtekintése|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) hálózati illesztők megtekintése egy hálózati adapter az előfizetés vagy a nézet beállításait|

## <a name="change-dns-servers"></a>Módosítsa a DNS-kiszolgálók

A DNS-kiszolgáló által az Azure DHCP-kiszolgáló a virtuális gép operációs rendszeren belül a hálózati adapterhez hozzárendelt. A hozzárendelt DNS-kiszolgáló, függetlenül a DNS-kiszolgáló beállításainak egy hálózati adapter van. Hálózati adapter neve feloldási beállításaitól kapcsolatos további információkért lásd: [virtuális gépek névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md). A hálózati adapter örökli a beállításokat a virtuális hálózatból, vagy használja a virtuális hálózatra vonatkozó beállításainak felülbírálása a saját egyedi beállításait.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adaptert, amely módosítja a DNS-kiszolgáló a listából.
3. Válassza ki **DNS-kiszolgálók** alatt **beállítások**.
4. A következők közül választhat:
    - **Öröklés a virtuális hálózattól**: válassza ezt a beállítást, a DNS-kiszolgáló beállítása a hálózati adapter be van-e rendelve a virtuális hálózathoz meghatározott öröklik. Egyéni DNS-kiszolgálót vagy az Azure által biztosított DNS-kiszolgáló a virtuális hálózat szintjén van meghatározva. Az Azure által biztosított DNS-kiszolgáló fel tudja oldani az erőforrások ugyanahhoz a virtuális hálózathoz rendelt gazdagépnevek. Az erőforrásokhoz a hozzárendelt különböző virtuális hálózatokhoz megoldásához teljes Tartománynevét kell használni.
    - **Egyéni**: beállíthatja a saját DNS-kiszolgáló több virtuális hálózaton a nevek feloldásához. Adja meg a kiszolgáló egy DNS-kiszolgálóként használni kívánt IP-címét. A DNS-kiszolgáló címét adja meg, hogy csak a hálózati adapterhez van hozzárendelve, és felülbírálások bármilyen DNS-beállításainak a hálózati adapter be van-e rendelve a virtuális hálózathoz.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Engedélyezi vagy letiltja az IP-továbbítás

IP-továbbítás lehetővé teszi, hogy a virtuális gép hálózati adapter csatlakozik:
- A hálózati adapterhez hozzárendelt IP-konfigurációk bármelyike rendelt IP-címek egyike nem tartó hálózati forgalom fogadására.
- Egy hálózati adapter IP-konfigurációk hierarchiától különböző forrás IP-címmel rendelkező hálózati adatforgalom elküldésére.

A beállítást engedélyezni kell minden, amelyet a virtuális gép továbbítják a forgalmat fogadó virtuális géphez csatolt hálózati adapter. Egy virtuális gépet is továbbítja a forgalmat, hogy több hálózati adapterrel rendelkezik, vagy egyetlen hálózati adapter csatlakozik. Noha az IP-továbbítás egy Azure beállítás, a virtuális gép is futtatnia kell egy alkalmazás képes továbbítani a forgalmat, például tűzfal, WAN-optimalizálás és terheléselosztási alkalmazások. Ha a virtuális gép hálózati alkalmazások fut, a virtuális gép gyakran nevezik egy hálózati virtuális berendezésre. A hálózati virtuális berendezések üzembe listáját megtekintheti a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). IP-továbbítás jellemzően a felhasználó által megadott útvonalakat. Felhasználó által megadott útvonalakkal kapcsolatos további tudnivalókért lásd: [felhasználó által megadott útvonalak](virtual-networks-udr-overview.md).

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki, hogy engedélyezi vagy letiltja az IP-továbbítási a kívánt hálózati adapter.
3. Válassza ki **IP-konfigurációk** a a **beállítások** szakaszban.
4. Válassza ki **engedélyezve** vagy **letiltott** (alapértelmezett beállítás), módosítsa a beállítást.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Alhálózat-hozzárendelés módosítása

Módosíthatja az alhálózaton, de nem a virtuális hálózat, amely egy hálózati adapter van rendelve.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati adaptert, amely meg szeretné változtatni az alhálózat-hozzárendelés.
3. Válassza ki **IP-konfigurációk** alatt **beállítások**. Ha bármely bármely IP-konfigurációk magánhálózati IP-címek felsorolt **(statikus)** mellettük, módosítania kell az IP-cím hozzárendelési módszer a dinamikus az alábbi lépéseket követve. Magánhálózati IP-címek a hálózati adapter alhálózat-hozzárendelés módosítása a dinamikus hozzárendelési módszer a kell hozzárendelni. Ha a cím hozzá van rendelve, a dinamikus módszerrel, folytassa a öt. Ha minden IPv4-cím hozzá van rendelve, a statikus hozzárendelés módszerrel, a következő lépéseket a hozzárendelési módszer módosításához dinamikus:
    - Válassza ki az IP-konfigurációhoz meg szeretné változtatni az IPv4-cím hozzárendelés a módszer az IP-konfigurációk listájából.
    - Válassza ki **dinamikus** a magánhálózati IP-cím **hozzárendelés** metódust. Nem rendelhető hozzá egy IPv6-címet a statikus hozzárendelés módszerrel oszthatók ki.
    - Kattintson a **Mentés** gombra.
4. Válassza ki az alhálózatot a hálózati adaptert az áthelyezni kívánt a **alhálózati** legördülő listából.
5. Kattintson a **Mentés** gombra. Új dinamikus címek vannak hozzárendelve az új alhálózat alhálózat címtartományából. Miután egy új alhálózatot a hálózati adapterhez rendeli, hozzárendelheti egy statikus IPv4-címet az új alhálózat címtartományából Ha úgy dönt. További információk hozzáadása, módosítása és eltávolítása a hálózati illesztő IP-címek kapcsolatban lásd: [kezelése IP-címek](virtual-network-network-interface-addresses.md).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Ad hozzá, vagy távolítsa el az alkalmazásbiztonsági csoportok

Csak adja hozzá a hálózati adaptert, vagy egy hálózati adapter eltávolítása a portál használatával, ha egy virtuális géphez csatlakoztatott hálózati adapter van egy biztonsági csoportot. A hálózati adapter hozzáadása PowerShell vagy az Azure CLI használatával, vagy egy hálózati adapter eltávolítása egy biztonsági csoportot, hogy a virtuális géphez csatlakoztatott hálózati adapter, vagy nem. Tudjon meg többet [az alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups) és annak [hozzon létre egy alkalmazásbiztonsági csoportot](manage-network-security-group.md#create-an-application-security-group).

1. Az a *erőforrások, szolgáltatások és dokumentumok keresése* a portál tetején lévő mezőbe írja be a nevét, amely rendelkezik egy hálózati adapter, amelyet szeretne hozzáadni, vagy távolítsa el, egy biztonsági csoportot a virtuális gép. Amikor a virtuális gép neve megjelenik a keresési eredmények között, kattintson rá.
2. A **BEÁLLÍTÁSOK** területen válassza a **Hálózatkezelés** elemet.  Válassza ki **konfigurálása az alkalmazásbiztonsági csoportok**az alkalmazásbiztonsági csoportok hozzáadása a hálózati adapter kívánt jelölje be, és törölje az alkalmazásbiztonsági csoportok, amelyek a el kívánja távolítani, a hálózati adapter majd **mentése**. Csak az azonos virtuális hálózatban található hálózati adapterek az ugyanahhoz az alkalmazásbiztonsági csoporthoz lehet hozzáadni. Az alkalmazásbiztonsági csoporthoz léteznie kell a hálózati adapter ugyanazon a helyen.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic update](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>A hálózati biztonsági csoport társításának megszüntetése vagy társítása

1. Írja be a keresőmezőbe, a portál tetején, *hálózati adapterek* kifejezést a keresőmezőbe. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. A hálózati adaptert, amely hozzá szeretne rendelni egy hálózati biztonsági csoport listában jelölje ki, vagy a hálózati biztonsági csoport társításának megszüntetése.
3. Válassza ki **hálózati biztonsági csoport** alatt **beállítások**.
4. Válassza a **Szerkesztés** elemet.
5. Válassza ki **hálózati biztonsági csoport** , és válassza ki a hálózati biztonsági csoport a hálózati adapterhez társítani, vagy válassza ki a kívánt **None**, leválasztja a hálózati biztonsági csoport.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>Egy hálózati adapter törlése

Egy hálózati adapter törölheti, amíg nincs csatolva egy virtuális géphez. Ha egy hálózati adapter egy virtuális géphez van csatlakoztatva, kell először helyezze a virtuális gép leállított (felszabadított) állapotba, majd leválasztja a hálózati adaptert a virtuális gépről. Válassza le a hálózati illesztő a virtuális gépről, hajtsa végre a lépéseket a [leválasztani a virtuális gépről egy hálózati adaptert](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Egy hálózati adaptert a virtuális gépről nem választható le, ha a virtuális géphez csatolt azonban egyetlen hálózati adapter. Virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter csatlakozik. A virtuális gép törlésének leválasztja az összes hálózati adapter csatlakozik, de nem törli a hálózati adaptereket.

1. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *hálózati adapterek*. Amikor **hálózati adapterek** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki **...**  jobb oldalán található a hálózati adapter törli a hálózati adapterek listájából.
3. Válassza a **Törlés** elemet.
4. Válassza ki **Igen** a hálózati adapter törlésének megerősítéséhez.

Ha töröl egy hálózati adapter, bármely hozzárendelt MAC vagy IP-címek jelennek meg.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic delete](/cli/azure/network/nic#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Kapcsolati problémák elhárításához

Ha Ön nem lehet kommunikálni a, vagy egy virtuális gépet, a hálózati biztonsági csoport biztonsági szabályai és a egy hálózati adapter érvényes útvonalak, okozza a problémát. A probléma megoldása érdekében a következő lehetőségek állnak rendelkezésére:

### <a name="view-effective-security-rules"></a>Érvényes biztonsági szabályok megtekintése

Mindegyik hálózati interfész egy virtuális géphez csatolt érvényes biztonsági szabályokat a létrehozott hálózati biztonsági csoport a szabályok kombinációját kell és [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules). A hálózati adapter érvényes biztonsági szabályainak ismertetése segítségével meghatározhatja, miért vagyunk való kommunikációra, vagy a virtuális gépről. A hatékony szabályok bármely hálózati interfész egy futó virtuális géphez csatolt tekintheti meg.

1. A keresőmezőbe, a portál tetején adja meg egy érvényes biztonsági szabályok a megtekinteni kívánt virtuális gép nevét. Ha nem ismeri a virtuális gép nevét, adja meg a *virtuális gépek* kifejezést a keresőmezőbe. Amikor **virtuális gépek** jelennek meg a keresési eredmények között, válassza ki azt, majd válasszon ki egy virtuális gépet a listából.
2. Válassza ki **hálózatkezelés** alatt **beállítások**.
3. Válassza ki a hálózati adapter nevét.
4. Válassza ki **érvényes biztonsági szabályok** alatt **támogatás + hibaelhárítás**.
5. Tekintse át a hatékony biztonsági szabályok használatával határozza meg, hogy a megfelelő szabályok vannak-e a szükséges bejövő és kimenő kommunikáció a listáját. További információ a listájához lásd [hálózati biztonsági csoportok áttekintése](security-overview.md).

Az IP-folyamat ellenőrzése, hogy az Azure Network Watcher szolgáltatása is segítségével eldöntheti, hogy ha a biztonsági szabályok megakadályozza, hogy a virtuális gépek és végpontok közötti kommunikáció. További tudnivalókért lásd: [IP-folyamat ellenőrzésével](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Az Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

Az érvényes útvonalak a virtuális géphez csatlakoztatott hálózati adapterek alapértelmezett útvonalak kombinációját, a bármely létrehozott útvonalakat és a egy Azure virtuális hálózati átjárón keresztül BGP-n keresztül a helyszíni hálózatok propagálni hozzáférésvezérlési. Hálózati adapter érvényes útvonalaihoz megismerése segíthet a megállapíthatja, hogy miért nem lehet kommunikálni a, vagy a virtuális gépről. Minden olyan futó virtuális géphez csatolt hálózati adapter érvényes útvonalaihoz tekintheti meg.

1. A keresőmezőbe, a portál tetején adja meg egy érvényes biztonsági szabályok a megtekinteni kívánt virtuális gép nevét. Ha nem ismeri a virtuális gép nevét, adja meg a *virtuális gépek* kifejezést a keresőmezőbe. Amikor **virtuális gépek** jelennek meg a keresési eredmények között, válassza ki azt, majd válasszon ki egy virtuális gépet a listából.
2. Válassza ki **hálózatkezelés** alatt **beállítások**.
3. Válassza ki a hálózati adapter nevét.
4. Válassza ki **érvényes útvonalak** alatt **támogatás + hibaelhárítás**.
5. Tekintse át a meghatározásához, hogy vannak-e a megfelelő útvonalak a szükséges bejövő és kimenő kommunikáció a tényleges útvonalakat. További információ a listájához lásd [Útválasztás áttekintése](virtual-networks-udr-overview.md).

Az Azure Network Watcher következő ugrás funkcióját is segítségével meghatározhatja, hogy ha útvonalak megakadályozza, hogy a virtuális gépek és végpontok közötti kommunikáció. További tudnivalókért lásd: [a következő Ugrás](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Az Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Engedélyek

A hálózati adaptereken feladatait, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő engedélyeket az alábbi táblázatban felsorolt:

| Műveletek                                                                     | Name (Név)                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hálózati adapter lekérése                                     |
| Microsoft.Network/networkInterfaces/write                                  | Létrehozni vagy frissíteni a hálózati adapter                        |
| Microsoft.Network/networkInterfaces/join/action                            | Hálózati adapter csatlakoztatása egy virtuális gép           |
| Microsoft.Network/networkInterfaces/delete                                 | Hálózati adapter törlése                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Egy hálózati adapter egy szerviz-n keresztül egy erőforrás csatlakoztatása...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Hálózati adapter effektív útvonaltábla beolvasása               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hálózati adapter érvényes biztonsági csoportjainak lekérése           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hálózati adapter terheléselosztók beolvasása                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Szolgáltatás-társítás beolvasása                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Létrehozni vagy frissíteni a szolgáltatás-társítás                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Szolgáltatás-társítás törlése                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Ellenőrizze a szolgáltatás társítása                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hálózati adapter IP-konfiguráció beolvasása                    |

## <a name="next-steps"></a>További lépések

- Használatával több hálózati adapterrel rendelkező virtuális gép létrehozása a [Azure CLI-vel](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Hozzon létre egy egyetlen hálózati adapterrel rendelkező virtuális több IPv4-címek segítségével a [Azure CLI-vel](virtual-network-multiple-ip-addresses-cli.md) vagy [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Hálózati adapter egyetlen virtuális gép létrehozása egy privát IPv6 cím (mögött az Azure Load Balancerhez) használatával a [Azure CLI-vel](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Hozzon létre egy hálózati adapter [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablon](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok