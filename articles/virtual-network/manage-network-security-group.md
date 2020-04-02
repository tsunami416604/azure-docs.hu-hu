---
title: Azure hálózati biztonsági csoport létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: További információ a hálózati biztonsági csoportok létrehozásáról, módosításáról és törléséről.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521031"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása, módosítása vagy törlése

A hálózati biztonsági csoportok biztonsági szabályai lehetővé teszik a virtuális hálózati alhálózatokba és hálózati adapterekbe be- és kiáramló hálózati forgalom típusának szűrését. Ha többet szeretne tudni a hálózati biztonsági csoportokról, olvassa el a [Hálózati biztonsági csoport – áttekintés című témakört.](security-overview.md) Ezután fejezze be a [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyagot, hogy tapasztalatot szerezzen a hálózati biztonsági csoportokkal.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik ilyennel, állítson be egy Aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). A cikk további részének megkezdése előtt hajtsa végre az alábbi feladatok egyikét:

- **Portál felhasználói:** Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók:** Vagy futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/powershell)vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az Azure Cloud Shell böngészőlapján keresse meg a **Környezet kiválasztása** legördülő listát, majd válassza a **PowerShellt,** ha még nincs kiválasztva.

    Ha a PowerShellt helyileg futtatja, használja az Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói:** Vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/bash)vagy a CLI a számítógépről. Az Azure CLI 2.0.28-as vagy újabb verzióját használja, ha helyileg futtatja az Azure CLI-t. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

A fiók, amelybe bejelentkezik, vagy csatlakozik az Azure-hoz, hozzá kell rendelnie a [hálózati közreműködői szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Létrehozhat, [megtekinthet egy](#view-all-network-security-groups)hálózati biztonsági csoportot, megtekintheti az [összeset, megtekintheti a részleteket,](#view-details-of-a-network-security-group) [módosíthatja](#change-a-network-security-group)és [törölheti](#delete-a-network-security-group) a hálózati biztonsági csoportot. A hálózati biztonsági csoportot hálózati adapterről vagy alhálózatról is [társíthatja vagy elválaszthatja.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az egyes Azure-helyeken és -előfizetésekhez létrehozható hálózati biztonsági csoportok at korlátozhatja. További információ: [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

2. Válassza **a Hálózat**lehetőséget, majd a Hálózati biztonsági csoport **lehetőséget.**

3. A **Hálózati biztonsági csoport létrehozása** lap **Alapjai** lapján adja meg az értékeket a következő beállításokhoz:

    | Beállítás | Műveletek |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon egy meglévő erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget** új erőforráscsoport létrehozásához. |
    | **Név** | Írjon be egy erőforráscsoporton belüli egyedi szöveges karakterláncot. |
    | **Régió** | Válassza ki a kívánt helyet. |

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. Miután meglátta az **Érvényesítési átadott** üzenetet, válassza a **Létrehozás gombot.**

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [Új-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Az összes hálózati biztonsági csoport megtekintése

A hálózati biztonsági csoportok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.** Megjelenik az előfizetéshálózati biztonsági csoportok listája.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati nsg-lista](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Hálózati biztonsági csoport részleteinek megtekintése

1. A hálózati biztonsági csoportok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Válassza ki a hálózati biztonsági csoport nevét.

A hálózati biztonsági csoport **Menüsorában**a Beállítások csoportban megtekintheti a **Bejövő biztonsági szabályokat**, **a Kimenő biztonsági szabályokat**, **a Hálózati adaptereket**és az **Alhálózatokat,** amelyekhez a hálózati biztonsági csoport társítva van.

A **Figyelés**csoportban engedélyezheti vagy letilthatja **a Diagnosztikai beállításokat.** A **Támogatás + hibaelhárítás**csoportban megtekintheti **a Hatályos biztonsági szabályokat.** További információ: [Diagnosztikai naplózás egy hálózati biztonsági csoporthoz](virtual-network-nsg-manage-log.md) és [A virtuális gép hálózati forgalomszűrőjének diagnosztizálása](diagnose-network-traffic-filter-problem.md)című témakörben olvashat.

A felsorolt általános Azure-beállításokról az alábbi cikkekben olvashat bővebben:

- [Tevékenységnapló](../azure-monitor/platform/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárak](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózat nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Hálózati biztonsági csoport módosítása

1. A hálózati biztonsági csoportok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Jelölje ki a módosítani kívánt hálózati biztonsági csoport nevét.

A leggyakoribb változások [a biztonsági szabály hozzáadása](#create-a-security-rule), [a szabály eltávolítása](#delete-a-security-rule), valamint a hálózati biztonsági [csoport alhálózathoz vagy hálózati adapterhez való hozzárendelése vagy szétosztása](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati nsg frissítés](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Hálózati biztonsági csoport társítása vagy szétválasztása alhálózathoz vagy hálózati adapterhez

Ha hálózati biztonsági csoportot szeretne társítani a hálózati biztonsági csoporthoz, vagy el szeretne választani egy hálózati biztonsági csoportot a hálózati adaptertől, olvassa el a [Hálózati biztonsági csoport társítása hálózati biztonsági csoporthoz vagy a hálózati adaptertől való leválasztó](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)témakört. Hálózati biztonsági csoport társítása az alhálózathoz, illetve hálózati biztonsági csoport alhálózattól való elhatároltsága, [lásd: Alhálózati beállítások módosítása](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Hálózati biztonsági csoport törlése

Ha egy hálózati biztonsági csoport alhálózatokhoz vagy hálózati adapterekhez van társítva, akkor nem törölhető. A hálózati biztonsági csoport elhatárolt sága az összes alhálózattól és hálózati csatolótól, mielőtt megpróbálna törölni.

1. A hálózati biztonsági csoportok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Jelölje ki a törölni kívánt hálózati biztonsági csoport nevét.

3. A hálózati biztonsági csoport eszköztárán válassza a **Törlés**lehetőséget. Ezután a megerősítést kérő párbeszédpanelen válassza az **Igen** lehetőséget.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg törlés](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Biztonsági szabályok kal való kapcsolat

A hálózati biztonsági csoport nulla vagy több biztonsági szabályt tartalmaz. Létrehozhat, [megtekinthet egy](#view-all-security-rules)biztonsági szabályt, megtekintheti a biztonsági szabály [részleteit,](#view-details-of-a-security-rule) [módosíthatja](#change-a-security-rule)és [törölheti](#delete-a-security-rule) azokat.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

Az egyes Azure-helyeken és -előfizetésekhez hálózati biztonsági csoportonként hány szabályt hozhat létre. További információ: [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. A hálózati biztonsági csoportok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Jelölje ki annak a hálózati biztonsági csoportnak a nevét, amelyhez biztonsági szabályt szeretne hozzáadni.

3. A hálózati biztonsági csoport menüsorában válassza a **Bejövő biztonsági szabályok** vagy a Kimenő biztonsági szabályok **lehetőséget.**

    Több meglévő szabály is szerepel a listán, köztük olyanok is, amelyeket esetleg nem adott hozzá. Hálózati biztonsági csoport létrehozásakor több alapértelmezett biztonsági szabály jön létre benne. További információ: [Alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules).  Az alapértelmezett biztonsági szabályok nem törölhetők, de felülbírálhatók magasabb prioritású szabályokkal.

4. <a name="security-rule-settings"></a>Válassza a **Hozzáadás**lehetőséget. Jelölje ki vagy adja hozzá az értékeket a következő beállításokhoz, majd kattintson az **OK gombra:**

    | Beállítás | Érték | Részletek |
    | ------- | ----- | ------- |
    | **Forrás** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatáscímke** (bejövő biztonsági szabály) vagy **VirtualNetwork** (kimenő biztonsági szabály)</li><li>**Alkalmazás&nbsp;&nbsp;biztonsági csoportja**</li></ul> | <p>Ha **az IP-címek lehetőséget választja,** forrás **IP-címeket/CIDR-tartományokat**is meg kell adnia.</p><p>Ha a **Szervizcímkét választja,** választhat **jaa forrásszolgáltatás-címkét**is.</p><p>Ha **az Alkalmazásbiztonsági csoport**lehetőséget választja, akkor egy meglévő alkalmazásbiztonsági csoportot is ki kell választania. Ha a **Forrás** és a Cél eszköz **alkalmazásbiztonsági csoportját** **választja,** mindkét alkalmazásbiztonsági csoporthálózati összeköttetéseknek ugyanabban a virtuális hálózatban kell lenniük.</p> |
    | **Forrás IP-címek/CIDR-tartományok** | Az IP-címek és az osztálynélküli tartományok közötti útválasztás (CIDR) tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha **a Forrást** **IP-címekre módosítja.** Meg kell adnia egy értéket vagy több értékből álló, vesszővel tagolt listát. Több értékre lehet `10.0.0.0/16, 192.188.1.1`példa. A megadható értékek száma korlátozott. További részletek az [Azure-korlátokban.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Ha a megadott IP-cím egy Azure virtuális géphez van rendelve, adja meg a privát IP-címét, nem a nyilvános IP-címét. Az Azure feldolgozza a biztonsági szabályokat, miután a nyilvános IP-címet egy privát IP-címre fordítja a bejövő biztonsági szabályok hoz, de mielőtt egy privát IP-címet nyilvános IP-címre fordít a kimenő szabályokhoz. Ha többet szeretne tudni az Azure-beli nyilvános és privát IP-címekről, olvassa el az [IP-címtípusok című témakört.](virtual-network-ip-addresses-overview-arm.md)</p> |
    | **Forrásszolgáltatás címkéje** | Szolgáltatáscímke a legördülő listából | Ez a nem kötelező beállítás akkor jelenik meg, ha a **Forrás** beállítást **szolgáltatáscímkét** állít be egy bejövő biztonsági szabályhoz. A szolgáltatáscímke az IP-címek egy kategóriájának előre definiált azonosítója. Ha többet szeretne tudni az elérhető szolgáltatáscímkékről és az egyes címkékről, olvassa el a [Szolgáltatáscímkék ( Szolgáltatáscímkék](security-overview.md#service-tags)) . |
    | **Forrásalkalmazás biztonsági csoportja** | Meglévő alkalmazásbiztonsági csoport | Ez a beállítás akkor jelenik meg, ha a **Forrás** beállítást **alkalmazásbiztonsági csoportra állítja.** Jelöljön ki egy olyan alkalmazásbiztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati csatoló. Megtudhatja, hogy [miként hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group) |
    | **Forrásporttartományok** | Az egyik:<ul><li>Egyetlen port, például`80`</li><li>Számos port, például`1024-65535`</li><li>Az egyes portok és/vagy porttartományok, például a portok és/vagy a porttartományok vesszővel tagolt listája`80, 1024-65535`</li><li>Csillag (`*`) , amely lehetővé teszi a forgalmat bármely kikötőben</li></ul> | Ez a beállítás azokat a portokat adja meg, amelyeken a szabály engedélyezi vagy letiltja a forgalmat. A megadható portok száma korlátozott. További részletek az [Azure-korlátokban.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Cél** | Az egyik:<ul><li>**Bármely**</li><li>**IP-címek**</li><li>**Szolgáltatáscímke** (kimenő biztonsági szabály) vagy **VirtualNetwork** (bejövő biztonsági szabály)</li><li>**Alkalmazás&nbsp;&nbsp;biztonsági csoportja**</li></ul> | <p>Ha **az IP-címeket választja,** adja meg **a cél IP-címeket/CIDR-tartományokat**is.</p><p>Ha a **VirtualNetwork**lehetőséget választja, a forgalom a virtuális hálózat címterén belül lévő összes IP-címre engedélyezett. **A VirtualNetwork** egy szolgáltatáscímke.</p><p>Ha az **Alkalmazásbiztonsági csoportot**választja, ki kell jelölnie egy meglévő alkalmazásbiztonsági csoportot. Megtudhatja, hogy [miként hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group)</p> |
    | **Cél IP-címek/CIDR-tartományok** | Ip-címek és CIDR-tartományok vesszővel tagolt listája | <p>Ez a beállítás akkor jelenik meg, ha a **Cél** címet **IP-címekre módosítja.** A **Forrás** és **Forrás IP-címekhez/CIDR-tartományokhoz**hasonlóan egy vagy több címet vagy tartományt is megadhat. A megadható számnak vannak korlátai. További részletek az [Azure-korlátokban.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Ha a megadott IP-cím egy Azure-beli virtuális géphez van rendelve, győződjön meg arról, hogy a privát IP-címet adja meg, nem pedig a nyilvános IP-címet. Az Azure feldolgozza a biztonsági szabályokat, miután a nyilvános IP-címet egy privát IP-címre fordítja a bejövő biztonsági szabályok hoz, de mielőtt az Azure egy privát IP-címet egy nyilvános IP-címre fordít a kimenő szabályokhoz. Ha többet szeretne tudni az Azure-beli nyilvános és privát IP-címekről, olvassa el az [IP-címtípusok című témakört.](virtual-network-ip-addresses-overview-arm.md)</p> |
    | **Cél szolgáltatáscímkéje** | Szolgáltatáscímke a legördülő listából | Ez a nem kötelező beállítás akkor jelenik meg, ha egy kimenő biztonsági szabály esetében a **Cél** **szolgáltatáscímkét módosítja.** A szolgáltatáscímke az IP-címek egy kategóriájának előre definiált azonosítója. Ha többet szeretne tudni az elérhető szolgáltatáscímkékről és az egyes címkékről, olvassa el a [Szolgáltatáscímkék ( Szolgáltatáscímkék](security-overview.md#service-tags)) . |
    | **Célalkalmazás biztonsági csoportja** | Meglévő alkalmazásbiztonsági csoport | Ez a beállítás akkor jelenik meg, ha a **Cél** beállítást **alkalmazásbiztonsági csoportra állítja.** Jelöljön ki egy olyan alkalmazásbiztonsági csoportot, amely ugyanabban a régióban található, mint a hálózati csatoló. Megtudhatja, hogy [miként hozhat létre alkalmazásbiztonsági csoportot.](#create-an-application-security-group) |
    | **Célporttartományok** | Az egyik:<ul><li>Egyetlen port, például`80`</li><li>Számos port, például`1024-65535`</li><li>Az egyes portok és/vagy porttartományok, például a portok és/vagy a porttartományok vesszővel tagolt listája`80, 1024-65535`</li><li>Csillag (`*`) , amely lehetővé teszi a forgalmat bármely kikötőben</li></ul> | A **Forrásport-tartományokhoz**is megadhat egy vagy több portot és tartományt. A megadható számnak vannak korlátai. További részletek az [Azure-korlátokban.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protocol (Protokoll)** | **Bármely**, **TCP,** **UDP**vagy **ICMP** | A szabályt korlátozhatja a TCP, a User Datagram Protocol (UDP) vagy az Internet Control Message Protocol (ICMP) protokollra. Az alapértelmezett beállítás az, hogy a szabály minden protokollra vonatkozzon. |
    | **Művelet** | **Engedélyezés** vagy **megtagadás** | Ez a beállítás határozza meg, hogy ez a szabály engedélyezi vagy letiltja-e a hozzáférést a megadott forrás- és célkonfigurációhoz. |
    | **Prioritás** | 100 és 4096 közötti érték, amely a hálózati biztonsági csoport on belüli összes biztonsági szabályra vonatkozik | Az Azure prioritási sorrendben dolgozza fel a biztonsági szabályokat. Minél alacsonyabb a szám, annál magasabb a prioritás. Azt javasoljuk, hogy a szabályok létrehozásakor hagyjon rést a prioritási számok között, például 100, 200 és 300. A hézagok elhagyása megkönnyíti a szabályok hozzáadását a jövőben, így a meglévő szabályoknál magasabb vagy alacsonyabb prioritást adhat nekik. |
    | **Név** | A szabály egyedi neve a hálózati biztonsági csoporton belül | A név legfeljebb 80 karakter ből állhat. Betűvel vagy számmal kell kezdődnie, és betűvel, számmal vagy aláhúzásjellel kell végződnie. A név csak betűket, számokat, aláhúzásjeleket, pontokat vagy kötőjeleket tartalmazhat. |
    | **Leírás** | A szöveges leírás | A biztonsági szabályszöveges leírását megadhatja. |

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Az összes biztonsági szabály megtekintése

A hálózati biztonsági csoport nulla vagy több szabályt tartalmaz. Ha többet szeretne tudni a szabályok megtekintésekor felsorolt információkról, olvassa el a Hálózati biztonsági csoport áttekintése című [témakört.](security-overview.md)

1. A hálózati biztonsági csoport szabályainak megtekintéséhez nyissa meg az [Azure Portalon](https://portal.azure.com) a szabályokat. Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelynek szabályait meg szeretné tekinteni.

3. A hálózati biztonsági csoport menüsorában válassza a **Bejövő biztonsági szabályok** vagy a Kimenő biztonsági szabályok **lehetőséget.**

A lista tartalmazza a létrehozott szabályokat és a hálózati biztonsági csoport [alapértelmezett biztonsági szabályait.](security-overview.md#default-security-rules)

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

1. A hálózati biztonsági csoport szabályainak megtekintéséhez nyissa meg az [Azure Portalon](https://portal.azure.com) a szabályokat. Keresse meg és válassza ki **a Hálózati biztonsági csoportokat.**

2. Válassza ki annak a hálózati biztonsági csoportnak a nevét, amelynek a szabály részleteit meg szeretné tekinteni.

3. A hálózati biztonsági csoport menüsorában válassza a **Bejövő biztonsági szabályok** vagy a Kimenő biztonsági szabályok **lehetőséget.**

4. Jelölje ki azt a szabályt, amelynek részleteit meg szeretné tekinteni. Az összes beállítás magyarázatát a [Biztonsági szabály beállításai](#security-rule-settings)ban találja.

    > [!NOTE]
    > Ez az eljárás csak egyéni biztonsági szabályra vonatkozik. Nem működik, ha alapértelmezett biztonsági szabályt választ.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati nsg szabály megjelenítése](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Hajtsa végre a biztonsági szabályok részleteinek megtekintése című [lépéseit.](#view-details-of-a-security-rule)

2. Szükség szerint módosítsa a beállításokat, majd válassza a **Mentés gombot.** Az összes beállítás magyarázatát a [Biztonsági szabály beállításai](#security-rule-settings)ban találja.

    > [!NOTE]
    > Ez az eljárás csak egyéni biztonsági szabályra vonatkozik. Alapértelmezett biztonsági szabály módosítása nem engedélyezett.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Hajtsa végre a biztonsági szabályok részleteinek megtekintése című [lépéseit.](#view-details-of-a-security-rule)

2. Válassza a **Törlés**, majd az **Igen** lehetőséget.

    > [!NOTE]
    > Ez az eljárás csak egyéni biztonsági szabályra vonatkozik. Alapértelmezett biztonsági szabályt nem törölhet.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati nsg szabály törlése](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Alkalmazásbiztonsági csoportok használata

Az alkalmazásbiztonsági csoport nulla vagy több hálózati összeköttetést tartalmaz. További információ: [alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups). Az alkalmazásbiztonsági csoport összes hálózati illesztőjének ugyanabban a virtuális hálózatban kell lennie. Ha tudni szeretné, hogyan vehet fel hálózati adaptert egy alkalmazásbiztonsági csoportba, olvassa el [a Hálózati adapter hozzáadása alkalmazásbiztonsági csoporthoz (Hálózati adapter hozzáadása alkalmazásbiztonsági csoporthoz) témakört.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Alkalmazásbiztonsági csoport létrehozása

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

2. A keresőmezőbe írja be az *Alkalmazás biztonsági csoportját*.

3. Az **Alkalmazás biztonsági csoportlapján** válassza a **Létrehozás gombot.**

4. Az **Alkalmazásbiztonsági csoport létrehozása** lap **Alapjai** lapján adja meg a következő beállítások értékeit:

    | Beállítás | Műveletek |
    | --- | --- |
    | **Előfizetés** | Válassza ki az előfizetését. |
    | **Erőforráscsoport** | Válasszon egy meglévő erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget** új erőforráscsoport létrehozásához. |
    | **Név** | Írjon be egy erőforráscsoporton belüli egyedi szöveges karakterláncot. |
    | **Régió** | Válassza ki a kívánt helyet. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. A **Véleményezés + létrehozás** lap ban, miután megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati asg létrehozása](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Az összes alkalmazásbiztonsági csoport megtekintése

Az [Azure Portalon](https://portal.azure.com) megtekintheti az alkalmazásbiztonsági csoportokat. Keresse meg és válassza **ki az Alkalmazásbiztonsági csoportokat.** Az Azure Portal megjeleníti az alkalmazás biztonsági csoportjainak listáját.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati asg lista](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Adott alkalmazásbiztonsági csoport részleteinek megtekintése

1. Az [Azure Portalon](https://portal.azure.com) megtekintheti az alkalmazásbiztonsági csoportot. Keresse meg és válassza **ki az Alkalmazásbiztonsági csoportokat.**

2. Válassza ki annak az alkalmazásbiztonsági csoportnak a nevét, amelynek részleteit meg szeretné tekinteni.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Alkalmazásbiztonsági csoport módosítása

1. Az [Azure Portalon](https://portal.azure.com) megtekintheti az alkalmazásbiztonsági csoportot. Keresse meg és válassza **ki az Alkalmazásbiztonsági csoportokat.**

2. Jelölje ki a módosítani kívánt alkalmazásbiztonsági csoport nevét.

3. Jelölje be a módosítani kívánt beállítás melletti **módosítás** jelölőnégyzetet. Hozzáadhat vagy eltávolíthat **címkéket,** vagy módosíthatja az **Erőforrás csoportot** vagy az **Előfizetés csoportot.**

    > [!NOTE]
    > Nem változtathatja meg a helyet.

    A menüsorban a **Hozzáférés-vezérlés (IAM)** lehetőséget is választhatja. A **Hozzáférés-vezérlés (IAM)** lapon engedélyeket rendelhet vagy távolíthat el az alkalmazás biztonsági csoportjához.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati asg frissítés](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Nincs PowerShell-parancsmag |

### <a name="delete-an-application-security-group"></a>Alkalmazásbiztonsági csoport törlése

Nem törölhet i. alkalmazásbiztonsági csoport, ha az hálózati csatolókat tartalmaz. Ha az összes hálózati adaptert el szeretné távolítani az alkalmazásbiztonsági csoportból, módosítsa a hálózati csatoló beállításait, vagy törölje a hálózati adaptereket. További információ: [Hozzáadás alkalmazásbiztonsági csoportokhoz vagy](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) [Hálózati adapter törlése](virtual-network-network-interface.md#delete-a-network-interface).

1. Az [Azure Portalon](https://portal.azure.com) kezelheti az alkalmazásbiztonsági csoportokat. Keresse meg és válassza **ki az Alkalmazásbiztonsági csoportokat.**

2. Jelölje ki a törölni kívánt alkalmazásbiztonsági csoport nevét.

3. Válassza **a Törlés**lehetőséget, majd az **Igen** lehetőséget az alkalmazásbiztonsági csoport törléséhez.

#### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati asg törlés](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportokon, biztonsági szabályokon és alkalmazásbiztonsági csoportokon végzett feladatok elvégzéséhez a fiókot hozzá kell rendelni a [Hálózat közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely a következő táblázatokban felsorolt megfelelő engedélyeket rendeli hozzá:

### <a name="network-security-group"></a>Hálózati biztonsági csoport

| Műveletek                                                        |   Név                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hálózati biztonsági csoport bekéselése                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Hálózati biztonsági csoport létrehozása vagy frissítése                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Hálózati biztonsági csoport törlése                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Hálózati biztonsági csoport társítása alhálózathoz vagy hálózati adapterhez 

### <a name="network-security-group-rule"></a>Hálózati biztonsági csoport szabálya

| Műveletek                                                        |   Név                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Szabály beszereznie                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Szabály létrehozása vagy frissítése                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Szabály törlése                                                         |

### <a name="application-security-group"></a>Alkalmazásbiztonsági csoport

| Műveletek                                                                     | Név                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP-konfiguráció összekapcsolódása alkalmazásbiztonsági csoporthoz|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Biztonsági szabály egyesítése alkalmazásbiztonsági csoporthoz    |
| Microsoft.Network/applicationSecurityGroups/read                           | Alkalmazásbiztonsági csoport beszereznie                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Alkalmazásbiztonsági csoport létrehozása vagy frissítése           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Alkalmazásbiztonsági csoport törlése                     |

## <a name="next-steps"></a>További lépések

- Hálózati vagy alkalmazásbiztonsági csoport létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaparancsfájlokkal vagy Azure [Resource Manager-sablonokkal](template-samples.md) [Azure CLI](cli-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
