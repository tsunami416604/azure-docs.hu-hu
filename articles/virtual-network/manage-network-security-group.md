---
title: Létrehozása, módosítása vagy egy Azure hálózati biztonsági csoport törlése |} Microsoft Docs
description: Megtudhatja, hogyan létrehozása, módosítása vagy a hálózati biztonsági csoport törlése.
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
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: ba7c0400e59d8c747553479eb5474978a629dfdf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-change-or-delete-a-network-security-group"></a>Létrehozása, módosítása vagy a hálózati biztonsági csoport törlése

Hálózati biztonsági csoport biztonsági szabályai lehetővé teszik a hálózati forgalom típusának megfelelő, amely a virtuális hálózati alhálózat és a hálózati adapterek mindkét áramolhasson szűréséhez. Ha nem ismeri a hálózati biztonsági csoportokkal, lásd: [hálózati biztonsági csoport – áttekintés](security-overview.md) tudhat meg többet őket, majd fejezze be a [hálózati forgalom szűrésére](tutorial-filter-network-traffic.md) ahhoz, hogy némi tapasztalattal a hálózati oktatóanyag a biztonsági csoportokat.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.4.1 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.28 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Létrehozhat, [összes](#view-all-network-security-groups), [részleteinek megtekintése](#view-details-of-a-network-security-group), [módosítása](#change-a-network-security-group), és [törlése](#delete-a-network-security-group) hálózati biztonsági csoport. Emellett [hozzárendelése vagy leválasztani](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) alhálózati vagy hálózati illesztő hálózati biztonsági csoport.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hány hálózati biztonsági csoportokat is létrehozhat egy Azure-beli hely és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Válassza ki a portál bal felső sarkában **+ hozzon létre egy erőforrást**.
2. Válassza ki **hálózati**, majd jelölje be **hálózati biztonsági csoport**.
3. Adjon meg egy **neve** a hálózati biztonsági csoportot, válassza ki a **előfizetés**, hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő erőforráscsoportot, válassza ki a **Hely**, majd válassza ki **létrehozása**. 

**Parancsok**

- Az Azure CLI: [az hálózati nsg létrehozása](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [új AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Minden hálózati biztonsági csoportok megtekintése

Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok*. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt. A hálózati biztonsági csoportok, amely szerepel az előfizetéshez vannak felsorolva.

**Parancsok**

- Az Azure CLI: [az nsg lista](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>A hálózati biztonsági csoportok részleteinek megtekintése

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok*. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Jelölje ki a hálózati biztonsági csoportot a listában a létrehozott részleteinek megtekintése. A **beállítások** megtekintheti a **bejövő biztonsági szabályok** és **kimenő biztonsági szabályok**, a **hálózati illesztőt** és  **Alhálózatok** a hálózati biztonsági csoport társítva. Is engedélyezheti vagy letilthatja **diagnosztikai naplók** és nézet **hatékony biztonsági szabályok**. További tudnivalókért lásd: [diagnosztikai naplók](virtual-network-nsg-manage-log.md) és [hatékony biztonsági szabályok megtekintéséhez](virtual-network-nsg-troubleshoot-portal.md).
3. A közös Azure-beállítások kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
    *   [Tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Címkék](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Zárolások feloldása](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az hálózati nsg megjelenítése](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Hálózati biztonsági csoport módosítása

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok* be a keresőmezőbe. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a módosítani kívánt hálózati biztonsági csoport. A leggyakoribb változások a következők [hozzáadása](#create-a-security-rule) vagy [eltávolítása](#delete-a-security-rule) biztonsági szabályok és [Associating vagy az vagy egy alhálózat vagy a hálózati illesztő hálózati biztonsági csoport törléséhez](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource).

**Parancsok**

- Az Azure CLI: [az hálózat nsg frissítése](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Hozzárendelése vagy leválasztani az vagy egy alhálózat vagy a hálózati illesztő hálózati biztonsági csoport

Társítson egy hálózati biztonsági csoportot, vagy leválasztani a hálózati illesztő hálózati biztonsági csoportot, lásd: [hozzárendelése egy hálózati biztonsági csoportot, vagy leválasztani a hálózati illesztő hálózati biztonsági csoportot](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Társítson egy hálózati biztonsági csoportot, vagy leválasztani az alhálózatot a hálózati biztonsági csoport, lásd: [alhálózati beállítások módosítása](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Hálózati biztonsági csoport törlése

Ha a hálózati biztonsági csoport alhálózatok vagy hálózati adapter tartozik, nem lehet törölni. [A társítást](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) alhálózatok és a hálózati adapterek, mielőtt megpróbálja törölni a hálózati biztonsági csoport.

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok* be a keresőmezőbe. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati biztonsági csoport törli a listáról.
3. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati nsg törlése](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Biztonsági szabályok használata

Hálózati biztonsági csoport nulla vagy több biztonsági szabályokat tartalmazza. Létrehozhat, [összes](#view-all-security-rules), [részleteinek megtekintése](#view-details-of-a-security-rule), [módosítása](#change-a-security-rule), és [törlése](#delete-a-security-rule) biztonsági szabály.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

A hálózati biztonsági csoport hány szabálynál hozhat létre egy Azure-beli hely és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok* be a keresőmezőbe. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati biztonsági csoportot a listából, amely a biztonsági szabály hozzáadásához.
3. Válassza ki **bejövő biztonsági szabályok** alatt **beállítások**. Több meglévő szabály szerepel a listában. Egyes hozzáadott nem lehetséges, hogy a szabályok. Egy hálózati biztonsági csoport jön létre, amikor több alapértelmezett biztonsági szabályok azt jönnek létre. További tudnivalókért lásd: [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules).  Alapértelmezett biztonsági szabály nem törölhető, de a magasabb prioritású szabályokkal felülbírálhatja.
4. <a name = "security-rule-settings"></a>Válassza ki **+ Hozzáadás**.  Válassza ki vagy adja hozzá a következő beállítások értékei majd **OK**:
    
    |Beállítás  |Érték  |Részletek  |
    |---------|---------|---------|
    |Forrás     | Válassza ki **bármely**, **IP-címek**, vagy **címke szolgáltatás**.        | Ha **IP-címek**, meg kell majd adnia **forrás IP-címek/CIDR tartományok**. Egy egyetlen érték vagy vesszővel elválasztva több értéket is megadhat. Több érték például 10.0.0.0/16, 192.188.1.1. Nincsenek korlátozások megadható értékek száma. Lásd: [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) részleteiről. Ha **szolgáltatás címke**, majd ki kell választania egy szolgáltatás címke. A szolgáltatás címke egy előre meghatározott IP-címek egy kategóriáját azonosítója. Rendelkezésre álló szolgáltatás címkéket, és minden címke jelöli kapcsolatos további információkért lásd: [címkék szolgáltatás](security-overview.md#service-tags)        |
    |Forrás porttartományok     | Adjon meg egyetlen port, pl. 80-as, egy porttartomány, pl. 1024-65535, vagy egyetlen portok és/vagy porttartományok, például a 80-as, vesszővel elválasztott listája 1024-65535. Írjon be csillag forgalom a portokon engedélyezéséhez. | A portok és címtartományok adja meg, mely portok forgalom engedélyezett vagy megtagadott a szabály által. Nincsenek korlátozások is megadhat portok száma. Lásd: [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) részleteiről.  |
    |Cél     | Válassza ki **bármely**, **IP-címek**, vagy **virtuális hálózati**.        | Ha **IP-címek**, meg kell majd adnia **cél IP-címek/CIDR tartományok**. Hasonló **forrás** és **forrás IP-címek/CIDR tartományok**, egyetlen vagy több címet vagy tartományt is megadhat, és korlátozott számát is megadhat. Kiválasztása **virtuális hálózati**, ez a szolgáltatás címkével, azt jelenti, hogy a forgalom számára engedélyezett a virtuális hálózat a címtér összes IP-címet.        |
    |Cél porttartományok     | Adjon meg egyetlen értéket, vagy értékek vesszővel tagolt listája. | Hasonló **porttartományok forrás**, egyetlen vagy több portok és címtartományok is megadhat, és korlátozott számát is megadhat. |
    |Protokoll     | Válassza ki **bármely**, **TCP**, vagy **UDP**.        |         |
    |Műveletek     | Válassza ki **engedélyezése** vagy **megtagadása**.        |         |
    |Prioritás     | A hálózati biztonsági csoporton belül az összes biztonsági szabályok egyedi 100-4096 közötti értéket kell megadni. |Szabályok feldolgozása prioritási sorrendben. Az alacsonyabb a szám, annál magasabb a prioritás. Javasoljuk, hogy hagyja bejelölve hiány közötti prioritás számokat, például a 100, 200, 300-szabályok létrehozása során. Helyet hagyni a segítségével könnyebben szabályok hozzáadása a jövőben, előfordulhat, hogy módosítania magasabb vagy alacsonyabb, mint a meglévő szabályokat.         |
    |Name (Név)     | A szabály a hálózati biztonsági csoporton belül egyedi nevét.        |  A név legfeljebb 80 karakter lehet. Kell kezdődnie, betűvel vagy számmal végződhet egy betű, szám vagy aláhúzásjel, és csak betűket, számokat, aláhúzásjeleket, pontokat, vagy kötőjelet tartalmazhat.       |
    |Leírás     | Megadhat egy leírást.        |         |

    Nem adható meg egy [alkalmazáscsoport biztonsági](#work-with-application-security-groups) a a **forrás** vagy **cél** beállításait, a portál használatával. Lehet azonban az Azure parancssori felület vagy a PowerShell használatával. A beállítások **kimenő biztonsági szabályok** hasonlóak, ezért nem tartoznak külön-külön.

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Az összes biztonsági szabályok megtekintése

Hálózati biztonsági csoport nulla vagy egynél több szabályokat tartalmazza. Szabályok megtekintésekor felsorolt információkkal kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoport – áttekintés](security-overview.md).

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok*. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati biztonsági csoportot a listából, hogy meg szeretné tekinteni a szabályokat.
3. Válassza ki **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok** alatt **beállítások**.

A listában a létrehozott szabályok és a hálózati biztonsági csoport [alapértelmezett biztonsági szabályok](security-overview.md#default-security-rules).

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabályok listája](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>A biztonsági szabály részleteinek megtekintése

1. Adja meg a keresési mezőbe, a portál felső, *hálózati biztonsági csoportok*. Ha **hálózati biztonsági csoportok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a hálózati biztonsági csoport biztonsági szabály részleteinek megtekintéséhez.
3. Válassza ki **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok** alatt **beállítások**.
4. Jelölje ki a szabályt, a részletek megtekintéséhez. Minden beállítás részletes ismertetése, [biztonsági szabály beállítások](#security-rule-settings).

**Parancsok**

- Az Azure CLI: [az hálózati nsg szabály megjelenítése](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule).
2. Módosítsa a beállításokat, szükség szerint, majd válassza ki **mentése**. Minden beállítás részletes ismertetése, [biztonsági szabály beállítások](#security-rule-settings).

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabály frissítése](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Hajtsa végre a [biztonsági szabály részleteinek megtekintése](#view-details-of-a-security-rule).
2. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabály törlése](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Biztonsági csoportok használata

Egy alkalmazás biztonsági csoport nulla vagy több hálózati illesztőre tartalmaz. További tudnivalókért lásd: [biztonsági csoportok](security-overview.md#application-security-groups). Nem dolgozhat biztonsági csoportok a portálon, de használhatja a PowerShell vagy az Azure parancssori felület. Az alkalmazás biztonsági csoportban lévő minden hálózati interfészen léteznie kell az azonos virtuális hálózatban. Az alkalmazás biztonsági csoporthoz hozzáadott első hálózati adapter további hálózati adaptereken kell lennie a virtuális hálózati határozza meg. Egy adott hálózati csatoló hozzáadása egy alkalmazás biztonsági csoporthoz, lásd: [vegye fel a hálózati adaptert egy alkalmazás biztonsági csoportba](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Alkalmazás biztonsági csoport létrehozása

- Az Azure CLI: [az hálózati asg létrehozása](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [új AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Minden alkalmazás biztonsági csoportoknak a megtekintése

- Az Azure CLI: [az hálózati asg listája](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Egy adott alkalmazás biztonsági csoport részleteinek megtekintése

- Az Azure CLI: [az hálózati asg megjelenítése](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Az alkalmazás biztonsági csoport módosítása

Módosíthatja az egyes beállítások, például a címkék és a meglévő alkalmazás biztonsági csoporthoz, amíg nem módosíthatja a nevet vagy helyet.

- Az Azure CLI: [az hálózat asg frissítése](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Nincs PowerShell-parancsmagot.

### <a name="delete-an-application-security-group"></a>Az alkalmazás biztonsági csoport törlése

Egy alkalmazás biztonsági csoport nem törölhető, azt a hálózati illesztők e azt. El kell távolítani az összes hálózati illesztő az alkalmazáscsoport biztonsági vagy hálózati kapcsolati beállítások módosításával, vagy a hálózati adapterek törlését. További információkért lásd: [való hozzáadását vagy eltávolítását a hálózati illesztő alkalmazás a biztonsági csoportokból](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) vagy [hálózati illesztő törlése](virtual-network-network-interface.md#delete-a-network-interface).

**Parancsok**

- Az Azure CLI: [az hálózati asg törlése](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportok, a biztonsági szabályok és a biztonsági csoportok feladatait, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő engedélyek a következő táblázatban:

|Művelet                                                       |   Művelet neve                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/ruleTables/read                              |   Hálózati biztonsági csoport lekérése                              |
|Microsoft.Network/ruleTables/write                             |   Létrehozni vagy frissíteni a hálózati biztonsági csoport                 |
|Microsoft.Network/ruleTables/delete                            |   Hálózati biztonsági csoport törlése                           |
|Microsoft.Network/ruleTables/join/action                       |   Csatlakoztassa a hálózati biztonsági csoport                             |
|Microsoft.Network/ruleTables/rules/read                       |   -Szabályának beolvasása                                    |
|Microsoft.Network/ruleTables/rules/write                      |   Szabályának létrehozása vagy frissítése                       |
|Microsoft.Network/ruleTables/rules/delete                     |   A szabály törlése                                 |
|Microsoft.Network/networkInterfaces/effectiveruleTable/action  |   Hálózati illesztő hatékony hálózati biztonsági csoport lekérése  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   A következő ugrás a virtuális gép beolvasása                  |

A *hálózati biztonsági csoport illesztése* kell társítani a hálózati biztonsági csoport alhálózathoz.