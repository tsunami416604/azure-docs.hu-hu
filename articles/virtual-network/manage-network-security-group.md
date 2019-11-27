---
title: Azure-beli hálózati biztonsági csoport létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre, módosíthat vagy törölhet hálózati biztonsági csoportokat.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: afbe4aae0ac1296bfc4b2011069f9d81afed977f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273683"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása, módosítása vagy törlése

A hálózati biztonsági csoportokban található biztonsági szabályok lehetővé teszik a virtuális hálózati alhálózatok és hálózati adapterek közötti és onnan kimenő hálózati forgalom típusának szűrését. Ha nem ismeri a hálózati biztonsági csoportokat, tekintse meg a [hálózati biztonsági csoportok áttekintése](security-overview.md) című cikket, és ismerkedjen meg [a hálózati](tutorial-filter-network-traffic.md) biztonsági csoportokkal.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, a `az login` futtatásával is létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókba, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Létrehozhatja, [megtekintheti az összeset](#view-all-network-security-groups), [megtekintheti](#view-details-of-a-network-security-group), [módosíthatja](#change-a-network-security-group)és [törölheti](#delete-a-network-security-group) a hálózati biztonsági csoportokat. Hálózati biztonsági csoportot is [hozzárendelhet vagy](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) leválaszthat egy hálózati adapterről vagy alhálózatról.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az Azure-helyek és-előfizetések által létrehozott hálózati biztonsági csoportok száma korlátozott. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.
2. Válassza a **hálózatkezelés**, majd a **hálózati biztonsági csoport**elemet.
3. Adja meg a hálózati biztonsági csoport **nevét** , válassza ki az **előfizetést**, hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévő erőforráscsoportot, válasszon ki egy **helyet**, majd válassza a **Létrehozás**lehetőséget.

**Parancsok**

- Azure CLI: [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Az összes hálózati biztonsági csoport megtekintése

A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok*értéket. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki. Megjelenik az előfizetésben található hálózati biztonsági csoportok listája.

**Parancsok**

- Azure CLI: [az Network NSG List](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Hálózati biztonsági csoport részleteinek megtekintése

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok*értéket. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a hálózati biztonsági csoportot azon a listán, amelynek a adatait meg szeretné tekinteni. A **Beállítások** területen megtekintheti a **bejövő biztonsági szabályokat** és a **kimenő biztonsági szabályokat**, valamint a hálózati biztonsági csoporthoz tartozó hálózati **adaptereket** és **alhálózatokat** . Emellett engedélyezheti vagy letilthatja a **diagnosztikai naplókat** , és megtekintheti az **érvényes biztonsági szabályokat**. További információ: [diagnosztikai naplók](virtual-network-nsg-manage-log.md) és [hatékony biztonsági szabályok megtekintése](diagnose-network-traffic-filter-problem.md).
3. Ha többet szeretne megtudni a felsorolt általános Azure-beállításokról, tekintse meg a következő cikkeket:
    *   [Tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md)
    *   [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
    *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zárak](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-parancsfájl](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Parancsok**

- Azure CLI: [az Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Hálózati biztonsági csoport módosítása

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a módosítani kívánt hálózati biztonsági csoportot. A leggyakoribb módosítások a biztonsági szabályok [hozzáadásával](#create-a-security-rule) vagy [eltávolításával](#delete-a-security-rule) , valamint [egy hálózati biztonsági csoport társításával vagy az alhálózatból vagy hálózati adapterből való összevonásával vagy leválasztásával foglalkoznak](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Parancsok**

- Azure CLI: [az Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Hálózati biztonsági csoport társítása vagy leválasztása egy alhálózathoz vagy hálózati adapterhez

Ha hálózati biztonsági csoportot szeretne hozzárendelni a vagy a hálózati biztonsági csoport hálózati adapterből való társításához, tekintse meg a hálózati biztonsági csoport társítása a hálózathoz vagy a hálózati [biztonsági csoport társítása hálózati adapterről](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)című témakört. Ha hálózati biztonsági csoportot szeretne hozzárendelni, vagy a hálózati biztonsági csoportot egy alhálózatról szeretné leválasztani, tekintse meg az [alhálózat beállításainak módosítása](virtual-network-manage-subnet.md#change-subnet-settings)című témakört.

### <a name="delete-a-network-security-group"></a>Hálózati biztonsági csoport törlése

Ha egy hálózati biztonsági csoport bármely alhálózathoz vagy hálózati adapterhez van társítva, nem törölhető. A törlés megkísérlése előtt szüntesse meg a hálózati biztonsági csoportot az összes alhálózatból és hálózati adapterből.

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a listából a törölni kívánt hálózati biztonsági csoportot.
3. Válassza a **Törlés**lehetőséget, majd válassza az **Igen**lehetőséget.

**Parancsok**

- Azure CLI: [az Network NSG delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Biztonsági szabályok használata

A hálózati biztonsági csoport nulla vagy több biztonsági szabályt tartalmaz. Létrehozhat, [megtekintheti az összeset](#view-all-security-rules) [, megtekintheti](#view-details-of-a-security-rule), [módosíthatja](#change-a-security-rule)és [törölheti](#delete-a-security-rule) a biztonsági szabályokat.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

Az Azure-beli hely és előfizetés alapján a hálózati biztonsági csoportokra vonatkozó szabályok száma korlátozva lehet. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati biztonsági csoportot a listából, amelyhez biztonsági szabályt kíván hozzáadni.
3. A **Beállítások**területen válassza a **bejövő biztonsági szabályok** lehetőséget. Számos meglévő szabály szerepel a felsorolásban. Előfordulhat, hogy egyes szabályok nem lettek hozzáadva. Hálózati biztonsági csoport létrehozásakor több alapértelmezett biztonsági szabály jön létre. További információ: [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules).  Az alapértelmezett biztonsági szabályok nem törölhetők, de felülbírálhatja azokat olyan szabályokkal, amelyek magasabb prioritással rendelkeznek.
4. <a name = "security-rule-settings"></a>Válassza a **+ Hozzáadás**lehetőséget.  Válassza ki vagy adja meg a következő beállítások értékeit, majd kattintson **az OK gombra**:
    
    |Beállítás  |Érték  |Részletek  |
    |---------|---------|---------|
    |Forrás     | Válassza a **bármely**, **alkalmazás biztonsági csoport**, **IP-cím**vagy **szolgáltatási címke** lehetőséget a bejövő biztonsági szabályokhoz. Ha kimenő biztonsági szabályt hoz létre, a beállítások ugyanazok, mint a **célhelyen**felsorolt beállítások.       | Ha az **alkalmazás biztonsági csoportja**lehetőséget választja, válasszon ki egy vagy több olyan meglévő alkalmazás biztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Megtudhatja, hogyan [hozhat létre egy alkalmazás biztonsági csoportot](#create-an-application-security-group). Ha a **forráshoz** és a **célhoz**is kiválasztja az **alkalmazás biztonsági csoportot** , akkor az alkalmazás biztonsági csoportjain belüli hálózati adaptereknek ugyanabban a virtuális hálózatban kell lenniük. Ha az **IP-címek**lehetőséget választja, adja meg a **forrás IP-címek/CIDR tartományokat**. Egyetlen értéket vagy vesszővel tagolt listát is megadhat több érték közül. Több érték például a 10.0.0.0/16, a 192.188.1.1. A megadható értékek száma korlátozott. További részletekért lásd az [Azure-korlátokat](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Ha a **szolgáltatás címkét**választja, akkor válasszon ki egy szolgáltatási címkét. A szolgáltatási címke az IP-címek kategóriájának előre meghatározott azonosítója. Ha többet szeretne megtudni az elérhető szolgáltatásokkal kapcsolatos címkékről és az egyes címkékről, tekintse meg a [szolgáltatás címkéi](security-overview.md#service-tags)című témakört. Ha a megadott IP-cím egy Azure-beli virtuális géphez van hozzárendelve, akkor győződjön meg arról, hogy a magánhálózati IP-címet adta meg, nem pedig a virtuális géphez hozzárendelt nyilvános IP-címet. A biztonsági szabályok feldolgozása után az Azure lefordítja a nyilvános IP-címet a bejövő biztonsági szabályok magánhálózati IP-címére, és az Azure előtt lefordít egy magánhálózati IP-címet a kimenő szabályok nyilvános IP-címére. Ha többet szeretne megtudni az Azure nyilvános és magánhálózati IP-címeiről, tekintse meg az [IP-címek típusait](virtual-network-ip-addresses-overview-arm.md).        |
    |Source port ranges     | Egyetlen portot kell megadnia, például 80, több port, például 1024-65535, vagy az önálló portok és/vagy porttartomány vesszővel tagolt listája, például 80, 1024-65535. Adjon meg egy csillagot, amely engedélyezi a forgalmat bármely porton. | A portok és tartományok határozzák meg, hogy a szabály mely portok forgalmát engedélyezi vagy tiltja. A megadható portok száma korlátozott. További részletekért lásd az [Azure-korlátokat](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .  |
    |Cél     | Válassza a **bármely**, **alkalmazás biztonsági csoport**, **IP-cím**vagy **Virtual Network** lehetőséget a kimenő biztonsági szabályokhoz. Ha egy bejövő biztonsági szabályt hoz létre, a beállítások ugyanazok, mint a **forrásként**megadott beállítások.        | Ha az **alkalmazás biztonsági csoport** lehetőséget választja, ki kell választania egy vagy több olyan meglévő alkalmazás biztonsági csoportját, amely ugyanabban a régióban található, mint a hálózati adapter. Megtudhatja, hogyan [hozhat létre egy alkalmazás biztonsági csoportot](#create-an-application-security-group). Ha az **alkalmazás biztonsági csoportja**lehetőséget választja, akkor válasszon egy meglévő alkalmazás biztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati adapter. Ha az **IP-címek**lehetőséget választja, adja meg a **cél IP-címeket/CIDR-tartományokat**. A **forrás** -és **forrás IP-CÍMEKhez/CIDR-tartományokhoz**hasonlóan egyetlen vagy több címet vagy tartományt is megadhat, és a megadható határértékek is meghatározhatók. A **virtuális hálózat**, amely egy szolgáltatás címkéje, azt jelenti, hogy az összes IP-cím engedélyezett a virtuális hálózat címterület területén. Ha a megadott IP-cím egy Azure-beli virtuális géphez van hozzárendelve, akkor győződjön meg arról, hogy a magánhálózati IP-címet adta meg, nem pedig a virtuális géphez hozzárendelt nyilvános IP-címet. A biztonsági szabályok feldolgozása után az Azure lefordítja a nyilvános IP-címet a bejövő biztonsági szabályok magánhálózati IP-címére, és az Azure előtt lefordít egy magánhálózati IP-címet a kimenő szabályok nyilvános IP-címére. Ha többet szeretne megtudni az Azure nyilvános és magánhálózati IP-címeiről, tekintse meg az [IP-címek típusait](virtual-network-ip-addresses-overview-arm.md).        |
    |Célporttartományok     | Egyetlen értéket vagy vesszővel tagolt értékeket kell megadnia. | A **forrásport-tartományokhoz**hasonlóan egyetlen vagy több portot és tartományt is megadhat, és a megadható határértékek is meghatározhatók. |
    |Protokoll     | Válassza **a bármely**, **TCP**, **UDP** vagy **ICMP**lehetőséget.        |         |
    |Műveletek     | Válassza az **Engedélyezés** vagy a **Megtagadás**lehetőséget.        |         |
    |Prioritás     | Adjon meg egy 100-4096 közötti értéket, amely egyedi az összes biztonsági szabály számára a hálózati biztonsági csoporton belül. |A szabályok feldolgozása prioritási sorrendben történik. Minél kisebb a szám, annál magasabb a prioritás. A szabályok létrehozásakor, például a 100, az 200, a 300 a prioritási számok közötti hézagot érdemes hagyni. A hézagok kihagyása megkönnyíti a jövőbeli szabályok hozzáadását, hogy a meglévő szabályoknál nagyobb vagy alacsonyabb legyen.         |
    |Név     | A szabály egyedi neve a hálózati biztonsági csoporton belül.        |  A név legfeljebb 80 karakter hosszú lehet. Betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzással kell végződnie, és csak betűket, számokat, aláhúzást, pontot vagy kötőjelet tartalmazhat.       |
    |Leírás     | A leírás nem kötelező.        |         |

**Parancsok**

- Azure CLI: [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Az összes biztonsági szabály megtekintése

Egy hálózati biztonsági csoport nulla vagy több szabályt tartalmaz. Ha többet szeretne megtudni a szabályok megtekintésekor felsorolt információkkal kapcsolatban, tekintse meg a [hálózati biztonsági csoport áttekintése](security-overview.md)című témakört.

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok*értéket. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a hálózati biztonsági csoportot abból a listából, amelynek a szabályait meg szeretné tekinteni.
3. A **Beállítások**területen válassza a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok** lehetőséget.

A lista tartalmazza a létrehozott szabályokat, valamint a hálózati biztonsági csoport [alapértelmezett biztonsági szabályait](security-overview.md#default-security-rules).

**Parancsok**

- Azure CLI: [az Network NSG Rule List](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

1. A portál felső részén található Keresés mezőbe írja be a *hálózati biztonsági csoportok*értéket. Ha a **hálózati biztonsági csoportok** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati biztonsági csoportot, amelyre vonatkozóan meg szeretné tekinteni a biztonsági szabály részleteit.
3. A **Beállítások**területen válassza a **bejövő biztonsági szabályok** vagy a **kimenő biztonsági szabályok** lehetőséget.
4. Válassza ki azt a szabályt, amelynek adatait meg szeretné tekinteni. Az összes beállítás részletes ismertetését lásd: [biztonsági szabályok beállításai](#security-rule-settings).

**Parancsok**

- Azure CLI: [az Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule)című témakör lépéseit.
2. Módosítsa a beállításokat igény szerint, majd válassza a **Mentés**lehetőséget. Az összes beállítás részletes ismertetését lásd: [biztonsági szabályok beállításai](#security-rule-settings).

**Parancsok**

- Azure CLI: [az Network NSG Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule)című témakör lépéseit.
2. Válassza a **Törlés**lehetőséget, majd válassza az **Igen**lehetőséget.

**Parancsok**

- Azure CLI: [az Network NSG Rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Alkalmazásbiztonsági csoportok használata

Az alkalmazás biztonsági csoportja nulla vagy több hálózati adaptert tartalmaz. További információ: [alkalmazás biztonsági csoportjai](security-overview.md#application-security-groups). Egy alkalmazás biztonsági csoportjában lévő összes hálózati csatolónak ugyanabban a virtuális hálózatban kell lennie. Ha meg szeretné tudni, hogyan adhat hozzá hálózati adaptert egy alkalmazás biztonsági csoportjához, tekintse meg [a hálózati adapter hozzáadása alkalmazás-biztonsági csoporthoz](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)című témakört.

### <a name="create-an-application-security-group"></a>Alkalmazás biztonsági csoportjának létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. A **Keresés a Marketplace-en** mezőbe írja be a következőt: *Alkalmazásbiztonsági csoport*. Amikor az **Alkalmazásbiztonsági csoport** kifejezés megjelenik a keresési eredmények között, válassza ki ismét az **Alkalmazásbiztonsági csoportot** a **Minden** területen, majd válassza a **Létrehozás** elemet.
3. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

    | Beállítás        | Érték                                                   |
    | ---            | ---                                                     |
    | Név           | A névnek egyedinek kell lennie az erőforráscsoporton belül.        |
    | Előfizetés   | Válassza ki előfizetését.                               |
    | Erőforráscsoport | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. |
    | Hely       | Válasszon helyet                                       |

**Parancsok**

- Azure CLI: [az Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Az összes alkalmazás biztonsági csoport megtekintése

1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában.
2. Adja meg az *alkalmazás biztonsági csoportjait* a **minden szolgáltatás szűrő** mezőben, majd válassza az **alkalmazás biztonsági csoportjai** lehetőséget, amikor megjelenik a keresési eredmények között.

**Parancsok**

- Azure CLI: [az Network ASG List](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Egy adott alkalmazás biztonsági csoportjának részleteinek megtekintése

1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában.
2. Adja meg az *alkalmazás biztonsági csoportjait* a **minden szolgáltatás szűrő** mezőben, majd válassza az **alkalmazás biztonsági csoportjai** lehetőséget, amikor megjelenik a keresési eredmények között.
3. Válassza ki azt az alkalmazás-biztonsági csoportot, amelynek adatait meg szeretné tekinteni.

**Parancsok**

- Azure CLI: [az Network ASG show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Alkalmazás biztonsági csoportjának módosítása

1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában.
2. Adja meg az *alkalmazás biztonsági csoportjait* a **minden szolgáltatás szűrő** mezőben, majd válassza az **alkalmazás biztonsági csoportjai** lehetőséget, amikor megjelenik a keresési eredmények között.
3. Válassza ki azt az alkalmazás-biztonsági csoportot, amelynek beállításait módosítani szeretné. Hozzáadhat vagy eltávolíthat címkéket, vagy hozzárendelhet vagy eltávolíthat engedélyeket az alkalmazás biztonsági csoportjához.

- Azure CLI: [az Network ASG Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: nincs PowerShell-parancsmag.

### <a name="delete-an-application-security-group"></a>Alkalmazás biztonsági csoportjának törlése

Az alkalmazás biztonsági csoportja nem törölhető, ha van hálózati adaptere. A hálózati adapter beállításainak módosításával vagy a hálózati adapterek törlésével távolítsa el az összes hálózati adaptert az alkalmazás biztonsági csoportjából. Részletekért lásd: [hálózati adapter hozzáadása vagy eltávolítása az alkalmazás biztonsági csoportjaiból](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) vagy [a hálózati adapter törlése](virtual-network-network-interface.md#delete-a-network-interface).

1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában.
2. Adja meg az *alkalmazás biztonsági csoportjait* a **minden szolgáltatás szűrő** mezőben, majd válassza az **alkalmazás biztonsági csoportjai** lehetőséget, amikor megjelenik a keresési eredmények között.
3. Válassza ki a törölni kívánt alkalmazás biztonsági csoportot.
4. Válassza a **Törlés**lehetőséget, majd válassza az **Igen** lehetőséget az alkalmazás biztonsági csoportjának törléséhez.

**Parancsok**

- Azure CLI: [az Network ASG delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportokkal, a biztonsági szabályokkal és az alkalmazás biztonsági csoportjaival kapcsolatos feladatok elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az alábbi táblázatokban felsorolt megfelelő engedélyekhez van rendelve:

### <a name="network-security-group"></a>Hálózati biztonsági csoport

| Műveletek                                                        |   Név                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hálózati biztonsági csoport beolvasása                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Hálózati biztonsági csoport létrehozása vagy frissítése                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Hálózati biztonsági csoport törlése                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Hálózati biztonsági csoport hozzárendelése alhálózathoz vagy hálózati adapterhez 


### <a name="network-security-group-rule"></a>Hálózati biztonsági csoport szabálya

| Műveletek                                                        |   Név                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Szabály lekérése                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Szabály létrehozása vagy frissítése                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Szabály törlése                                                         |

### <a name="application-security-group"></a>Alkalmazás biztonsági csoportja

| Műveletek                                                                     | Név                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP-konfiguráció csatlakoztatása egy alkalmazás biztonsági csoportjához|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Biztonsági szabály csatlakoztatása egy alkalmazás biztonsági csoportjához    |
| Microsoft.Network/applicationSecurityGroups/read                           | Alkalmazás biztonsági csoportjának beolvasása                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Alkalmazás biztonsági csoportjának létrehozása vagy frissítése           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Alkalmazás biztonsági csoportjának törlése                     |

## <a name="next-steps"></a>További lépések

- Hálózati vagy alkalmazás-biztonsági csoport létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájl használatával vagy Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure](policy-samples.md) -szabályzat létrehozása és alkalmazása virtuális hálózatokhoz
