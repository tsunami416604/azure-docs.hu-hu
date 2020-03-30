---
title: Azure hálózati felület létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogy mi az a hálózati illesztő, és hogyan hozhat létre, módosíthatja és törölheti a hálózati adaptereket.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 11e6285ef70ffde5344add951801997f8541eaad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244939"
---
# <a name="create-change-or-delete-a-network-interface"></a>Hálózati adapter létrehozása, módosítása és törlése

Megtudhatja, hogy miként hozhat létre, módosíthat és törölhet hálózati adaptereket. A hálózati felület lehetővé teszi, hogy az Azure virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. Virtuális gép létrehozásakor az Azure Portalon, a portál létrehoz egy hálózati felület alapértelmezett beállításokat az Ön számára. Ehelyett dönthet úgy, hogy egyéni beállításokkal rendelkező hálózati csatolőket hoz létre, és létrehozáskor egy vagy több hálózati csatolót ad hozzá a virtuális géphez. Előfordulhat, hogy módosítani szeretné egy meglévő hálózati csatoló alapértelmezett hálózati illesztőbeállításait is. Ez a cikk bemutatja, hogyan hozhat létre hálózati adaptert egyéni beállításokkal, hogyan módosíthatja a meglévő beállításokat, például a hálózati szűrő (hálózati biztonsági csoport) hozzárendelését, az alhálózati hozzárendelést, a DNS-kiszolgáló beállításait és az IP-továbbítást, és hogyan törölheti a hálózati adaptert.

Ha hálózati adapter IP-címeket kell hozzáadnia, módosítania vagy eltávolítania, olvassa el [az IP-címek kezelése című témakört.](virtual-network-network-interface-addresses.md) Ha hálózati csatolőket kell hozzáadnia a virtuális gépekhez, vagy hálózati adaptereket kell eltávolítania, olvassa el a [Hálózati adapterek hozzáadása és eltávolítása témakört.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.28-as vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

A fiók, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-network-interface"></a>Hálózati adapter létrehozása

Virtuális gép létrehozásakor az Azure Portalon, a portál létrehoz egy hálózati felületet az alapértelmezett beállításokat az Ön számára. Ha inkább adja meg az összes hálózati illesztő beállításait, létrehozhat egy egyéni beállításokat, és csatolja a hálózati adaptert egy virtuális géphez a virtuális gép létrehozásakor (a PowerShell vagy az Azure CLI használatával). Hálózati adaptert is létrehozhat, és hozzáadhatja egy meglévő virtuális géphez (a PowerShell vagy az Azure CLI használatával). Ha meg szeretné tudni, hogyan hozhat létre meglévő hálózati adapterrel rendelkező virtuális gépet, illetve hogyan adhat hozzá hálózati adaptereket a meglévő virtuális gépekhez, illetve hogyan távolíthatja el azokat, olvassa el a Hálózati adapterek hozzáadása és eltávolítása című [témakört.](virtual-network-network-interface-vm.md) A hálózati csatoló létrehozása előtt rendelkeznie kell egy meglévő [virtuális hálózattal](manage-virtual-network.md) ugyanazon a helyen és előfizetéssel, amelyben hálózati illesztőt hoz létre.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Válassza **a + Hozzáadás** lehetőséget a **Hálózati adapterek csoportban.**
3. Írja be vagy jelölje ki az értékeket a következő beállításokhoz, majd válassza a **Létrehozás**lehetőséget:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Név|Igen|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül. Idővel valószínűleg több hálózati felületis is található az Azure-előfizetésében. Ha több hálózati csatoló kezelését megkönnyítené a több hálózati csatoló kezelésének megkönnyítését szolgáló elnevezési konvenciók létrehozása korlátjait, olvassa el [az Elnevezési konvenciók (Elnevezési konvenciók) (Elnevezési konvenciók) (Elnevezési konvenciók) (Elnevezési konvenciók) (Elnevezési konvenciók) (Elnevezési](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) A név nem módosítható a hálózati adapter létrehozása után.|
    |Virtuális hálózat|Igen|Válassza ki a hálózati adapter virtuális hálózatát. Hálózati adaptert csak olyan virtuális hálózathoz rendelhet, amely ugyanabban az előfizetésben és helyen található, mint a hálózati csatoló. A hálózati adapter létrehozása után nem módosíthatja azt a virtuális hálózatot, amelyhez hozzá van rendelve. Annak a virtuális gépnek, amelyhez hozzáadja a hálózati adaptert, ugyanebben a helyen és előfizetésben kell léteznie, mint a hálózati adapternek.|
    |Alhálózat|Igen|Válasszon egy alhálózatot a kiválasztott virtuális hálózaton belül. Módosíthatja azt az alhálózatot, amelyhez a hálózati adapter hozzá van rendelve a létrehozása után.|
    |Privát IP-cím hozzárendelése|Igen| Ebben a beállításban az IPv4-cím hozzárendelési módját választja. A következő hozzárendelési módszerek közül választhat: **Dinamikus:** Ha ezt a beállítást választja, az Azure automatikusan hozzárendeli a következő elérhető címet a kiválasztott alhálózat címteréből. **Statikus:** Ha ezt a beállítást választja, manuálisan kell hozzárendelnie egy rendelkezésre álló IP-címet a kiválasztott alhálózat címteréből. A statikus és a dinamikus címek nem változnak, amíg nem módosítja őket, vagy nem törli a hálózati adaptert. A hozzárendelési módszert a hálózati csatoló létrehozása után módosíthatja. Az Azure DHCP-kiszolgáló ezt a címet rendeli hozzá a virtuális gép operációs rendszerén belüli hálózati adapterhez.|
    |Hálózati biztonsági csoport|Nem| Hagyja a **Nincs**beállítást , jelöljön ki egy meglévő [hálózati biztonsági csoportot](security-overview.md), vagy [hozzon létre egy hálózati biztonsági csoportot](tutorial-filter-network-traffic.md). A hálózati biztonsági csoportok lehetővé teszik a hálózati kapcsolatok hálózati forgalmának szűrését. A hálózati adapterre nulla vagy egy hálózati biztonsági csoportot alkalmazhat. Nulla vagy egy hálózati biztonsági csoport is alkalmazható arra az alhálózatra, amelyhez a hálózati adapter hozzá van rendelve. Ha egy hálózati biztonsági csoportot alkalmaznak egy hálózati adapterre, és azt az alhálózatot, amelyhez a hálózati adapter hozzá van rendelve, néha váratlan eredmények lépnek fel. A hálózati adapterekre és alhálózatokra alkalmazott hálózati biztonsági csoportok hibaelhárításáról a [Hálózati biztonsági csoportok – Problémaeltség – problémamegoldás](diagnose-network-traffic-filter-problem.md)című témakörben a|
    |Előfizetés|Igen|Válasszon egyet [azure-előfizetései közül.](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Annak a virtuális gépnek, amelyhez hálózati illesztést csatol, és annak a virtuális hálózatnak, amelyhez csatlakoztatja, ugyanabban az előfizetésben kell léteznie.|
    |Privát IP-cím (IPv6)|Nem| Ha bejelöli ezt a jelölőnégyzetet, a hálózati adapterhez egy IPv6-cím lesz hozzárendelve, a hálózati adapterhez rendelt IPv4-cím mellett. Az IPv6 hálózati adapterekkel való használatáról a cikk IPv6-szakaszában olvashat. Az IPv6-címhez nem lehet hozzárendelési metódust választani. Ha úgy dönt, hogy iPv6-címet rendel hozzá, az a dinamikus módszerrel lesz hozzárendelve.
    |IPv6-név (csak akkor jelenik meg, ha a **privát IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **privát IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ez a név a hálózati adapter másodlagos IP-konfigurációjához van rendelve. Az IP-konfigurációkról a Hálózati adapter beállításainak megtekintése című témakörben olvashat [bővebben.](#view-network-interface-settings)|
    |Erőforráscsoport|Igen|Jelöljön ki egy meglévő [erőforráscsoportot,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vagy hozzon létre egyet. A hálózati adapter létezhet ugyanabban a vagy más erőforráscsoportban, mint az a virtuális gép, amelyhez csatlakoztatja, vagy a virtuális hálózat, amelyhez csatlakoztatja.|
    |Hely|Igen|Annak a virtuális gépnek, amelyhez hálózati illesztést csatol, és annak a virtuális hálózatnak, amelyhez csatlakoztatja, ugyanazon a [helyen](https://azure.microsoft.com/regions)kell léteznie, más néven régióban.|

A portál nem biztosítja azt a lehetőséget, hogy a nyilvános IP-címet a hálózati adapter, amikor létrehozza, bár a portál nem hoz létre egy nyilvános IP-címet, és hozzárendeli a hálózati adapter, amikor létrehoz egy virtuális gép a portál használatával. Ha tudni szeretné, hogyan adhat hozzá nyilvános IP-címet a hálózati adapterhez a létrehozása után, olvassa el az [IP-címek kezelése című témakört.](virtual-network-network-interface-addresses.md) Ha nyilvános IP-címmel rendelkező hálózati adaptert szeretne létrehozni, a CLI vagy a PowerShell segítségével kell létrehoznia a hálózati adaptert.

A portál nem biztosítja a lehetőséget, hogy a hálózati adapter t alkalmazásbiztonsági csoportok hoz létre egy hálózati adapter, de az Azure CLI és a PowerShell nem. A portál használatával azonban meglévő hálózati adaptert rendelhet egy alkalmazásbiztonsági csoporthoz, feltéve, hogy a hálózati adapter egy virtuális géphez van csatlakoztatva. Ha tudni szeretné, hogyan rendelhet hálózati adaptert egy alkalmazásbiztonsági csoporthoz, olvassa el a [Hozzáadás alkalmazásbiztonsági csoportokhoz vagy az alkalmazásbiztonsági csoportokból való eltávolítását.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Az Azure csak akkor rendel HOZZÁ MAC-címet a hálózati adapterhez, ha a hálózati adaptert egy virtuális géphez csatlakoztatja, és a virtuális gép első indításakor elindul. Az Azure által a hálózati adapterhez rendelt MAC-cím nem adható meg. A MAC-cím mindaddig hozzá van rendelve a hálózati adapterhez, amíg a hálózati adaptert meg nem törlik, vagy meg nem változtatja az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt magánhálózati IP-címet. Az IP-címekről és az IP-konfigurációkról az [IP-címek kezelése című](virtual-network-network-interface-addresses.md) témakörben olvashat bővebben.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[Új-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Hálózati adapter beállításainak megtekintése

A hálózati adapter ek létrehozása után megtekintheti és módosíthatja a hálózati adapterek legtöbb beállítását. A portál nem jeleníti meg a hálózati adapter DNS-utótagot vagy alkalmazásbiztonsági csoporttagságát. A PowerShell vagy az Azure [CLI-parancsok](#view-settings-commands) segítségével megtekintheti a DNS-utótagot és az alkalmazásbiztonsági csoporttagságot.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki a listából a megtekinteni kívánt hálózati adaptert, vagy módosítsa a beállításokat.
3. A kiválasztott hálózati adapterhez a következő elemek jelennek meg:
   - **Áttekintés:** Tájékoztatást nyújt a hálózati adapterről, például a hozzá rendelt IP-címekről, a hálózati adapterhez rendelt virtuális hálózatról/alhálózatról, valamint arról a virtuális gépről, amelyhez a hálózati illesztő csatlakozik (ha az egyikhez van csatolva). Az alábbi képen a **mywebserver256**nevű hálózati ![adapter áttekintő beállításai láthatók: A hálózati adapter áttekintése](./media/virtual-network-network-interface/nic-overview.png)

     A hálózati adaptert áthelyezheti egy másik erőforráscsoportba vagy előfizetésbe, ha az **Erőforrás csoport** vagy az Előfizetés neve elem mellett kiválasztja (**módosítja**) a hálózati **adaptert.** Ha áthelyezi a hálózati illesztőt, a hálózati adapterhez kapcsolódó összes erőforrást át kell helyeznie vele. Ha például a hálózati adapter egy virtuális géphez van csatlakoztatva, akkor a virtuális gépet és más virtuális géppel kapcsolatos erőforrásokat is át kell helyeznie. Hálózati adapter áthelyezéséről az [Erőforrás áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrás áthelyezése)](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal)témakörben található. A cikk az előfeltételeket, valamint az Erőforrások áthelyezését az Azure Portalon, a PowerShell és az Azure CLI használatával sorolja fel.
   - **IP-konfigurációk:** Az IP-konfigurációkhoz rendelt nyilvános és privát IPv4-címek és IPv6-címek itt találhatók. Ha egy IP-konfigurációhoz IPv6-cím van rendelve, a cím nem jelenik meg. Az IP-konfigurációkról, valamint az IP-címek hozzáadásáról és eltávolításáról az [IP-címek konfigurálása Azure-hálózati adapterhez című témakörben olvashat bővebben.](virtual-network-network-interface-addresses.md) Ebben a szakaszban az IP-továbbítás és az alhálózati hozzárendelés is konfigurálva van. Ezekről a beállításokról az [IP-továbbítás engedélyezése és letiltása](#enable-or-disable-ip-forwarding) és [az Alhálózati hozzárendelés módosítása](#change-subnet-assignment)című témakörben olvashat bővebben.
   - **DNS-kiszolgálók:** Megadhatja, hogy az Azure DHCP-kiszolgálók melyik DNS-kiszolgálóhoz rendelik a hálózati illesztőt. A hálózati adapter örökölheti azt a beállítást attól a virtuális hálózattól, amelyhez a hálózati adapter hozzá van rendelve, vagy olyan egyéni beállítással rendelkezik, amely felülbírálja annak a virtuális hálózatnak a beállítását, amelyhez hozzá van rendelve. A megjelenített értékek módosításáról a [DNS-kiszolgálók módosítása](#change-dns-servers)című témakörben található.
   - **Hálózati biztonsági csoport (NSG):** Megjeleníti, hogy melyik NSG van társítva a hálózati adapterhez (ha van ilyen). Az NSG bejövő és kimenő szabályokat tartalmaz a hálózati adapter hálózati forgalmának szűrésére. Ha nsg van társítva a hálózati adapterhez, megjelenik a társított NSG neve. A megjelenített értékek módosításáról a [Hálózati biztonsági csoport társítása vagy szétválasztása](#associate-or-dissociate-a-network-security-group).
   - **Tulajdonságok:** Megjeleníti a hálózati adapter kulcsbeállításait, beleértve a MAC-címét (üres, ha a hálózati adapter nincs virtuális géphez csatlakoztatva), és azt az előfizetést, amelyben létezik.
   - **Hatékony biztonsági szabályok:**  A biztonsági szabályok akkor jelennek meg, ha a hálózati adapter egy futó virtuális géphez van csatlakoztatva, és egy NSG van társítva a hálózati adapterhez, az alhálózathoz, amelyhez hozzá van rendelve, vagy mindkettőhöz. Ha többet szeretne tudni a megjelenített értékekről, olvassa el [a Hatályos biztonsági szabályok megtekintése (Érvényességi biztonsági szabályok megtekintése) (Érvényességi biztonsági szabályok megtekintése) (Érvényességi biztonsági szabályok megtekintése) (Érvényességi biztonsági szabályok megtekintése) témakört.](#view-effective-security-rules) Az NSG-kről a [Hálózati biztonsági csoportok](security-overview.md)ról olvashat bővebben.
   - **Hatékony útvonalak:** Az útvonalak akkor jelennek meg, ha a hálózati adapter egy futó virtuális géphez van csatlakoztatva. Az útvonalak az Azure alapértelmezett útvonalainak, a felhasználó által definiált útvonalaknak és a hálózati adapterhez rendelt alhálózathoz esetleg létező BGP-útvonalak kombinációja. Ha többet szeretne tudni a megjelenített értékekről, olvassa el [a Hatékony útvonalak megtekintése (Hatékony útvonalak megtekintése) (Hatékony útvonalak megtekintése) témakört.](#view-effective-routes) Ha többet szeretne tudni az Azure alapértelmezett útvonalairól és a felhasználó által definiált útvonalakról, olvassa [el az Útválasztás áttekintése című témakört.](virtual-networks-udr-overview.md)
Általános Azure Resource Manager-beállítások: Ha többet szeretne megtudni az Azure Resource Manager általános beállításairól, olvassa el [a Tevékenységnapló](../azure-monitor/platform/platform-logs-overview.md), [a hozzáférés-vezérlés (IAM),](../role-based-access-control/overview.md) [a Címkék,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [a Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)és [az Automation-parancsfájl című témakört.](../azure-resource-manager/templates/export-template-portal.md)

<a name="view-settings-commands"></a>**Parancsok**

Ha egy Hálózati adapterhez IPv6-cím van hozzárendelve, a PowerShell-kimenet azt a tényt adja vissza, hogy a cím hozzá van rendelve, de nem adja vissza a hozzárendelt címet. Hasonlóképpen a CLI azt a tényt adja vissza, hogy a cím hozzá van rendelve, de *null értéket* ad vissza a cím kimenetében.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nic lista](/cli/azure/network/nic) az előfizetés hálózati interfészeimegtekintéséhez; [az network nic show](/cli/azure/network/nic) a hálózati adapter beállításainak megtekintéséhez|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) a hálózati adapterek előfizetési vagy megtekintési beállításainak megtekintéséhez|

## <a name="change-dns-servers"></a>DNS-kiszolgálók módosítása

A DNS-kiszolgálót az Azure DHCP-kiszolgáló rendeli hozzá a virtuális gép operációs rendszerén belüli hálózati adapterhez. A hozzárendelt DNS-kiszolgáló attól független, hogy a DNS-kiszolgáló beállítása hálózati kapcsolathoz van-e. A hálózati adapternév-feloldási beállításairól a [virtuális gépek névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md)című témakörben olvashat bővebben. A hálózati adapter örökölheti a beállításokat a virtuális hálózattól, vagy használhatja a saját egyedi beállításait, amelyek felülbírálják a virtuális hálózat beállításait.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. A listából válassza ki azt a hálózati illesztőt, amelynek DNS-kiszolgálóját módosítani szeretné.
3. Válassza a **DNS-kiszolgálók lehetőséget** a **BEÁLLÍTÁSOK csoportban.**
4. Válasszon a következők közül:
   - **Öröklés virtuális hálózatról**: Válassza ezt a lehetőséget, ha örökölni szeretné azt a DNS-kiszolgálói beállítást, amely ahhoz a virtuális hálózathoz van definiálva, amelyhez a hálózati illesztő hozzá van rendelve. A virtuális hálózat szintjén vagy egy egyéni DNS-kiszolgáló, vagy az Azure által biztosított DNS-kiszolgáló van definiálva. Az Azure által biztosított DNS-kiszolgáló fel tudja oldani az ugyanahhoz a virtuális hálózathoz rendelt erőforrások állomásneveit. A különböző virtuális hálózatokhoz rendelt erőforrások feloldásához fqdn-t kell használni.
   - **Egyéni**: Beállíthatja, hogy a saját DNS-kiszolgálója több virtuális hálózaton is feloldja a neveket. Adja meg a DNS-kiszolgálóként használni kívánt kiszolgáló IP-címét. A megadott DNS-kiszolgálócím csak ehhez a hálózati adapterhez van rendelve, és felülírja annak a virtuális hálózatnak a DNS-beállítását, amelyhez a hálózati adapter hozzá van rendelve.
     >[!Note]
     >Ha a virtuális gép egy rendelkezésre állási csoport részét használó hálózati adaptert használ, a rendelkezésre állási csoport részét képezi hálózati adapterek minden olyan virtuális géphez megadott DNS-kiszolgáló kóca lesz öröklődve.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nic frissítés](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>IP-továbbítás engedélyezése vagy letiltása

Az IP-továbbítás lehetővé teszi, hogy a virtuális géphez hálózati adapter csatlakozik:
- A hálózati adapterhez rendelt IP-konfigurációk egyikéhez rendelt IP-címek egyikéhez nem rendelt hálózati forgalom fogadása.
- A hálózati adapter IP-konfigurációihoz rendelt hálózati forgalomtól eltérő forrásIP-címmel küldhet hálózati forgalmat.

A beállítást engedélyezni kell minden olyan hálózati adapterhez, amely a virtuális géphez csatlakozik, és amely a virtuális gép által továbbítandó forgalmat fogad. A virtuális gépek továbbíthatják a forgalmat, függetlenül attól, hogy több hálózati csatolmánya vagy egyetlen hálózati csatolója van-e hozzácsatolva. Míg az IP-továbbítás egy Azure-beállítás, a virtuális gépnek is futtatnia kell egy olyan alkalmazást, amely képes a forgalom továbbítására, például a tűzfal, a WAN-optimalizálás és a terheléselosztási alkalmazások. Amikor egy virtuális gép hálózati alkalmazásokat futtat, a virtuális gépet gyakran hálózati virtuális berendezésnek nevezik. Megtekintheti a hálózati virtuális készülékek üzembe helyezésére kész listáját az [Azure Marketplace-en.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) Az IP-továbbítás t általában a felhasználó által definiált útvonalakon használják. A felhasználó által definiált útvonalakról a [Felhasználó által definiált útvonalak](virtual-networks-udr-overview.md)ról olvashat bővebben.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Válassza ki azt a hálózati illesztőt, amelyhez engedélyezni szeretné vagy le tiltani az IP-továbbítást.
3. Válassza az **IP-konfigurációk lehetőséget** a **BEÁLLÍTÁSOK** szakaszban.
4. A beállítás módosításához válassza az **Engedélyezve** vagy **a Letiltva** (alapértelmezett beállítás) lehetőséget.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nic frissítés](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alhálózati hozzárendelés módosítása

Módosíthatja azt az alhálózatot, de azt a virtuális hálózatot nem, amelyhez a hálózati adapter hozzá van rendelve.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki azt a hálózati illesztőt, amelyhez módosítani szeretné az alhálózati hozzárendelést.
3. Válassza az **IP-konfigurációk lehetőséget a** **BEÁLLÍTÁSOK csoportban.** Ha a felsorolt IP-konfigurációk bármely privát IP-címe **(Statikus)** mellett van, a következő lépések végrehajtásával dinamikusra kell módosítania az IP-cím-hozzárendelési módszert dinamikusra. A hálózati adapter alhálózati hozzárendelésének módosításához minden privát IP-címet hozzá kell rendelni a dinamikus hozzárendelési módszerrel. Ha a címek a dinamikus módszerrel vannak hozzárendelve, folytassa az ötödik lépéssel. Ha a statikus hozzárendelési módszerrel bármilyen IPv4-cím van hozzárendelve, hajtsa végre az alábbi lépéseket a hozzárendelési módszer dinamikusra való módosításához:
   - Válassza ki azt az IP-konfigurációt, amelyhez módosítani szeretné az IPv4-címhozzárendelési módszert az IP-konfigurációk listájából.
   - Válassza a **Dinamikus** lehetőséget a privát IP-cím **hozzárendelési** módszerhez. A statikus hozzárendelési módszerrel nem rendelhet IPv6-címet.
   - Kattintson a **Mentés** gombra.
4. Jelölje ki azt az alhálózatot, amelybe a hálózati adaptert át szeretné helyezni az **Alhálózat** legördülő listából.
5. Kattintson a **Mentés** gombra. Az új dinamikus címek az új alhálózat alhálózati címtartományából vannak hozzárendelve. Miután a hálózati adaptert új alhálózathoz rendelte, ha úgy dönt, statikus IPv4-címet rendelhet az új alhálózati címtartományból. Ha többet szeretne tudni a hálózati adapter IP-címeinek hozzáadásáról, módosításáról és eltávolításáról, olvassa el [az IP-címek kezelése című témakört.](virtual-network-network-interface-addresses.md)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic ip-config frissítés](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Alkalmazásbiztonsági csoportok hoz való hozzáadás vagy eltávolítás

Hálózati adaptert csak akkor adhat hozzá, illetve távolíthat el egy alkalmazásbiztonsági csoportból a portál használatával, ha a hálózati csatoló egy virtuális géphez van csatlakoztatva. A PowerShell vagy az Azure CLI segítségével hálózati adaptert adhat hozzá, vagy eltávolíthat egy hálózati adaptert egy alkalmazásbiztonsági csoportból, függetlenül attól, hogy a hálózati csatoló egy virtuális géphez van-e csatlakoztatva vagy sem. További információ [az alkalmazásbiztonsági csoportokról](security-overview.md#application-security-groups) és az [alkalmazásbiztonsági csoportok létrehozásáról.](manage-network-security-group.md)

1. A portál tetején található *Keresés, erőforrások, szolgáltatások és dokumentumok* mezőben kezdje el beírni egy olyan virtuális gép nevét, amely rendelkezik egy olyan hálózati adapterrel, amelyet hozzá szeretne adni egy alkalmazásbiztonsági csoporthoz, vagy onnan szeretne eltávolítani. Amikor a virtuális gép neve megjelenik a keresési eredmények között, jelölje ki azt.
2. A **BEÁLLÍTÁSOK** területen válassza a **Hálózatkezelés** elemet.  Válassza **az Alkalmazásbiztonsági csoportok** lehetőséget, majd az **Alkalmazásbiztonsági csoportok konfigurálása**lehetőséget választja azokat az alkalmazásbiztonsági csoportokat, amelyekhez hozzá szeretné adni a hálózati illesztőt, vagy törölje a hálózati adaptert eltávolítani kívánt alkalmazásbiztonsági csoportok kijelölésének kijelölését, majd válassza a **Mentés gombot.** Ugyanahhoz a virtuális hálózathoz csak az azonos virtuális hálózatban található hálózati csatolók adhatók hozzá. Az alkalmazásbiztonsági csoportnak a hálózati adaptersel azonos helyen kell léteznie.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az hálózati nic frissítés](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Hálózati biztonsági csoport társítása vagy szétválasztása

1. A portál tetején lévő keresőmezőbe írja be a *hálózati adaptereket* a keresőmezőbe. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki azt a hálózati illesztőt a listában, amelyhez hálózati biztonsági csoportot szeretne társítani, vagy válassza le a hálózati biztonsági csoportot.
3. Válassza **a Hálózati biztonság csoport** beállítások **csoportjában**lehetőséget.
4. Válassza a **Szerkesztés** elemet.
5. Válassza a **Hálózati biztonsági csoport** lehetőséget, majd jelölje ki a hálózati adapterhez társítani kívánt hálózati biztonsági csoportot, vagy válassza a **Nincs**lehetőséget a hálózati biztonsági csoport szétválaszthatóvá.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [az hálózati nic frissítés](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Hálózati adapter törlése

A hálózati adapter törölhető, feltéve, hogy nincs a virtuális géphez csatolva. Ha egy hálózati adapter egy virtuális géphez van csatlakoztatva, először a virtuális gépet leállított (felszabadított) állapotba kell helyeznie, majd le kell választania a hálózati adaptert a virtuális gépről. Ha hálózati adaptert szeretne leválasztani egy virtuális gépről, hajtsa végre a [Hálózati adapter leválasztása a virtuális gépről](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm)című lépéseit. A hálózati adapter nem választható le a virtuális gépről, ha ez az egyetlen hálózati csatoló, amely azonban a virtuális géphez csatlakozik. A virtuális géphez mindig legalább egy hálózati adapternek kell hozzá kapcsolódnia. A virtuális gép törlése leválasztja a hozzá csatolt összes hálózati adaptert, de nem törli a hálózati adaptereket.

1. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a hálózati *csatolók*kifejezést. Amikor **hálózati adapterek** jelennek meg a keresési eredmények között, jelölje ki azt.
2. Jelölje ki a törölni kívánt hálózati adaptert a listában.
3. Az **Áttekintés csoportban** Válassza a **Törlés**lehetőséget.
4. A hálózati adapter törlésének megerősítéséhez válassza az **Igen** lehetőséget.

Hálózati adapter törlésekor a rendszer felszabadítja a hozzá rendelt MAC- vagy IP-címeket.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic törlés](/cli/azure/network/nic)|
|PowerShell|[Eltávolítás-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldása

Ha nem tud kommunikálni egy virtuális géppel vagy egy virtuális gépről, akkor a hálózati biztonsági csoport biztonsági szabályai vagy a hálózati adapterek számára hatályos útvonalak okozhatják a problémát. A probléma megoldásához a következő lehetőségek segíthetnek:

### <a name="view-effective-security-rules"></a>Hatékony biztonsági szabályok megtekintése

A virtuális gépekhez csatlakoztatott minden egyes hálózati adapterre vonatkozó hatályos biztonsági szabályok a hálózati biztonsági csoportban létrehozott szabályok és az [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules)kombinációját képezik. A hálózati adapter ek érvényes biztonsági szabályainak ismertetése segíthet annak meghatározásában, hogy miért nem tud kommunikálni a virtuális gépekkel vagy a virtuális gépről. Megtekintheti a futó virtuális géphez csatlakoztatott hálózati adapterek hatályos szabályait.

1. A portál tetején lévő keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynek tényleges biztonsági szabályait meg szeretné tekinteni. Ha nem tudja a virtuális gép nevét, írja be a *virtuális gépeket* a keresőmezőbe. Amikor **a virtuális gépek** megjelennek a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. Válassza **a Hálózat lehetőséget a** BEÁLLÍTÁSOK **csoportban.**
3. Válassza ki a hálózati adapter nevét.
4. Válassza **a Hatékony biztonsági szabályok** lehetőséget a TÁMOGATÁS + **HIBAELHÁRÍTÁS csoportban.**
5. Tekintse át a hatékony biztonsági szabályok listáját, és állapítsa meg, hogy léteznek-e a megfelelő szabályok a szükséges bejövő és kimenő kommunikációhoz. További információ a [Hálózatbiztonsági csoport áttekintése](security-overview.md)című listában látható tudnivalókról.

Az Azure Network Watcher IP-folyamat-ellenőrzési szolgáltatása is segíthet meghatározni, hogy a biztonsági szabályok megakadályozzák-e a virtuális gépek és a végpont közötti kommunikációt. További információ: [IP-folyamat ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Azure CLI: [az hálózati nic lista-hatékony-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Hatékony útvonalak megtekintése

A virtuális géphez csatlakoztatott hálózati adapterek hatékony útvonalai az alapértelmezett útvonalak, a létrehozott útvonalak és a helyszíni hálózatokról a BGP-n keresztül egy Azure virtuális hálózati átjárón keresztül propagált útvonalak kombinációja. A hálózati adapter hatékony útvonalainak megértése segíthet annak meghatározásában, hogy miért nem tud kommunikálni egy virtuális géppel vagy egy virtuális gépről. Megtekintheti a futó virtuális géphez csatlakoztatott hálózati adapterek érvényes útvonalait.

1. A portál tetején lévő keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynek tényleges biztonsági szabályait meg szeretné tekinteni. Ha nem tudja a virtuális gép nevét, írja be a *virtuális gépeket* a keresőmezőbe. Amikor **a virtuális gépek** megjelennek a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. Válassza **a Hálózat lehetőséget a** BEÁLLÍTÁSOK **csoportban.**
3. Válassza ki a hálózati adapter nevét.
4. Válassza **a Hatékony útvonalak lehetőséget** a TÁMOGATÁS + **HIBAELHÁRÍTÁS csoportban.**
5. Tekintse át a hatékony útvonalak listáját, és állapítsa meg, hogy a szükséges bejövő és kimenő kommunikációhoz léteznek-e megfelelő útvonalak. További információ arról, hogy mit láthat az [Útválasztás áttekintés című](virtual-networks-udr-overview.md)listában.

Az Azure Network Watcher következő ugrási szolgáltatása is segíthet meghatározni, hogy az útvonalak megakadályozzák-e a virtuális gép és a végpont közötti kommunikációt. További információ: [Tovább ugrás](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Azure CLI: [az hálózati nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Engedélyek

A hálózati adaptereken végzett feladatok végrehajtásához a fiókot hozzá kell rendelni a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő engedélyekkel van elrendelve:

| Műveletek                                                                     | Név                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Hálózati adapter bekéselése                                     |
| Microsoft.Network/networkInterfaces/write                                  | Hálózati adapter létrehozása vagy frissítése                        |
| Microsoft.Network/networkInterfaces/join/action                            | Hálózati adapter csatolása virtuális géphez           |
| Microsoft.Network/networkInterfaces/delete                                 | Hálózati adapter törlése                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Csatlakozzon egy erőforrás a hálózati interfész keresztül Servi ...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | A hálózati adapter hatékony útvonaltáblájának beszereznie               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Hatékony hálózati adapterbiztonsági csoportok beszerezni           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Hálózati illesztő terheléselosztóinak betöltése                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Szolgáltatástársítás bekéselése                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Szolgáltatástársítás létrehozása vagy frissítése                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Szolgáltatástársítás törlése                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Szolgáltatástársítás ellenőrzése                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Hálózati adapter IP-konfigurációjának beszereznie                    |

## <a name="next-steps"></a>További lépések

- Virtuális gép létrehozása több hálózati adapterrel az [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával
- Egyetlen hálózati adapter virtuális gép létrehozása több IPv4-címmel az [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) vagy a [PowerShell](virtual-network-multiple-ip-addresses-powershell.md) használatával
- Egyetlen hálózati adapter virtuális gép létrehozása privát IPv6-címmel (egy Azure-terheléselosztó mögött) az [Azure CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával
- Hálózati felület létrehozása [PowerShell-](powershell-samples.md) vagy [Azure CLI-mintaparancsfájlokkal](cli-samples.md) vagy Az Azure [Resource Manager-sablon](template-samples.md) használatával
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
