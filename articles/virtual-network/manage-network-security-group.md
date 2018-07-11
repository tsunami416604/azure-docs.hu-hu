---
title: Létrehozása, módosítása vagy törlése az Azure-beli hálózati biztonsági csoportok |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, módosítása vagy egy hálózati biztonsági csoport törlése.
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
ms.openlocfilehash: 5ca70b085b5ac9db4d108966fa695f042c20489d
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920511"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Létrehozása, módosítása vagy egy hálózati biztonsági csoport törlése

A hálózati biztonsági csoportok biztonsági szabályokat lehetővé teszi a hálózati forgalom típusának megfelelő, amelyek virtuális hálózati alhálózatokhoz és hálózati adapterekhez adataikkal is szűrheti. Ha még nem ismeri a hálózati biztonsági csoportok, [hálózati biztonsági csoportok áttekintése](security-overview.md) további információkat olvashat, és végezze el a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag némi tapasztalattal a hálózat eléréséhez biztonsági csoportok.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az Azure PowerShell-modul 5.4.1-es vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.28-as vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Hozzá kell rendelni a fiókot, jelentkezzen be, vagy csatlakozhat az Azure-ban a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy olyan [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="work-with-network-security-groups"></a>Hálózati biztonsági csoportok használata

Hozhat létre, [az összes megtekintése](#view-all-network-security-groups), [részleteinek megtekintése](#view-details-of-a-network-security-group), [módosítása](#change-a-network-security-group), és [törlése](#delete-a-network-security-group) egy hálózati biztonsági csoportot. Emellett [hozzárendelése vagy leválasztani](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) egy hálózati adapter vagy alhálózatot a hálózati biztonsági csoport.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hány hálózati biztonsági csoportokat is létrehozhat egy Azure-helyen és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A portál bal felső sarkában válassza **+ erőforrás létrehozása**.
2. Válassza ki **hálózatkezelés**, majd **hálózati biztonsági csoport**.
3. Adjon meg egy **neve** a hálózati biztonsági csoportot, válassza a **előfizetés**, hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő erőforráscsoportot, válassza ki a **Hely**, majd válassza ki **létrehozás**.

**Parancsok**

- Az Azure CLI: [az network nsg létrehozása](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [új AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Az összes hálózati biztonsági csoportok megtekintése

Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok*. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt. A hálózati biztonsági csoportok, amelyek szerepelnek az előfizetésében szerepelnek.

**Parancsok**

- Az Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>A hálózati biztonsági csoport részleteinek megtekintése

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok*. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati biztonsági csoportot a listában a részleteinek megtekintéséhez. A **beállítások** megtekintheti a **bejövő biztonsági szabályok** és **kimenő biztonsági szabályok**, a **hálózati adapterek** és  **Alhálózatok** a hálózati biztonsági csoport társítva. Emellett engedélyezheti vagy letilthatja **diagnosztikai naplók** és nézet **érvényben lévő biztonsági szabályokat**. További tudnivalókért lásd: [diagnosztikai naplók](virtual-network-nsg-manage-log.md) és [érvényes biztonsági szabályok megtekintése](diagnose-network-traffic-filter-problem.md).
3. A felsorolt gyakori Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
    *   [Tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zárolások](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-szkript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>A hálózati biztonsági csoport módosítása

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a módosítani kívánt hálózati biztonsági csoport. A leggyakoribb változások [hozzáadása](#create-a-security-rule) vagy [eltávolítása](#delete-a-security-rule) biztonsági szabályokat és [Associating vagy leválasztása folyamatban van egy hálózati biztonsági csoportot, vagy egy alhálózathoz vagy hálózati adapterhez](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Parancsok**

- Az Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Egy hálózati biztonsági csoportot, vagy egy alhálózathoz vagy hálózati adapterhez való társítás megszüntetése vagy társítása

A hálózati biztonsági csoport hozzárendelése, vagy a hálózati adapter hálózati biztonsági csoport társításának megszüntetése, lásd: [a hálózati biztonsági csoport hozzárendelése, vagy a hálózati adapter hálózati biztonsági csoport társításának megszüntetése](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). A hálózati biztonsági csoport hozzárendelése, vagy egy alhálózatot a hálózati biztonsági csoport társításának megszüntetése, lásd: [alhálózati beállítások módosítása](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>A hálózati biztonsági csoport törlése

Ha egy hálózati biztonsági csoport bármely alhálózatokhoz vagy a hálózati adapterek társítva, nem lehet törölni. [Leválasztja](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) összes alhálózat és hálózati adapterek, mielőtt megkísérelné törölni a hálózati biztonsági csoport.

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati biztonsági csoport törli a listából.
3. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az network nsg törlése](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Biztonsági szabályok használata

A hálózati biztonsági csoportok nulla vagy több biztonsági szabályokat tartalmaznak. Hozhat létre, [az összes megtekintése](#view-all-security-rules), [részleteinek megtekintése](#view-details-of-a-security-rule), [módosítása](#change-a-security-rule), és [törlése](#delete-a-security-rule) egy biztonsági szabályt.

### <a name="create-a-security-rule"></a>Biztonsági szabály létrehozása

A hálózati biztonsági csoport hány szabályt hozhat létre egy Azure-helyen és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok* kifejezést a keresőmezőbe. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati biztonsági csoport, amely szeretne hozzáadni egy biztonsági szabályt, amely a listából.
3. Válassza ki **bejövő biztonsági szabályok** alatt **beállítások**. Több meglévő szabály szerepel a listában. A szabályokat, akkor nem kell hozzá néhányat. Ha egy hálózati biztonsági csoport jön létre, néhány alapértelmezett biztonsági szabályok, jönnek létre. További tudnivalókért lásd: [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules).  Alapértelmezett biztonsági szabályok nem törölhetők, de a szabályokat, amelyek magasabb prioritást kapnak felülbírálhatók.
4. <a name = "security-rule-settings"></a>Válassza ki **+ Hozzáadás**.  Válassza ki vagy adja meg a következő beállítások adatait, majd **OK**:
    
    |Beállítás  |Érték  |Részletek  |
    |---------|---------|---------|
    |Forrás     | Válassza ki **bármely**, **alkalmazásbiztonsági csoport**, **IP-címek**, vagy **Szolgáltatáscímke** bejövő biztonsági szabályok. Ha egy kimenő biztonsági szabályt hoz létre, a beállítások ugyanazok, mint a felsorolt lehetőségek **cél**.       | Ha **alkalmazásbiztonsági csoport**, majd válasszon ki egy, vagy több meglévő alkalmazás biztonsági csoportokat, amelyeket a hálózati adapter megegyező régióban található. Ismerje meg, hogyan [hozzon létre egy alkalmazásbiztonsági csoportot](#create-an-application-security-group). Ha **alkalmazásbiztonsági csoport** mind a **forrás** és **cél**, a hálózati adapterek, mindkét alkalmazásbiztonsági csoport belül kell lennie az egyazon virtuális hálózat. Ha **IP-címek**, majd adja meg **forrás IP-címek vagy CIDR-tartományok**. Megadhat egy egyetlen érték vagy több értékek vesszővel elválasztott listája. Több érték egy példát a 10.0.0.0/16, 192.188.1.1. Megadhat értékek száma korlátozva van. Lásd: [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) részleteiről. Ha **Szolgáltatáscímke**, majd válassza ki az egyik szolgáltatáscímke. Egy szolgáltatáscímke egy előre meghatározott IP-címek egy kategóriáját azonosítója. Minden címke jelöli, és elérhető szolgáltatáscímkék kapcsolatos további tudnivalókért lásd: [Szolgáltatáscímkéket](security-overview.md#service-tags). Ha a megadott IP-cím hozzá van rendelve egy Azure virtuális gépen, győződjön meg arról, magánhálózati IP, nem a virtuális géphez társított nyilvános IP-címet adjon meg. Biztonsági szabályok feldolgozása után az Azure a rendszer lefordítja a bejövő biztonsági szabályok magánhálózati IP-cím a nyilvános IP-címet, és mielőtt Azure fordítja le egy nyilvános IP-cím kimenő szabályok egy magánhálózati IP-címet. Az Azure nyilvános és privát IP-címek kapcsolatos további információkért lásd: [IP-cím-típusok](virtual-network-ip-addresses-overview-arm.md).        |
    |Forrásporttartományok     | Adja meg egyetlen port, pl. 80-as port, pl. 1024-65535, számos vagy portokat és/vagy alkalmazásport-tartományok, mint például a 80-as, vesszővel elválasztott listája 1024-65535. Adja meg a csillag karakter engedélyezi a forgalmat az összes porton. | A portok és címtartományok adja meg a portokat adatforgalom engedélyezett vagy tiltott a szabály által. Megadhat portok száma korlátozva van. Lásd: [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) részleteiről.  |
    |Cél     | Válassza ki **bármely**, **alkalmazásbiztonsági csoport**, **IP-címek**, vagy **virtuális hálózat** bejövő biztonsági szabályok. Ha egy kimenő biztonsági szabályt hoz létre, a beállítások ugyanazok, mint a felsorolt lehetőségek **forrás**.        | Ha **alkalmazásbiztonsági csoport** majd jelöljön ki egy vagy több meglévő alkalmazásbiztonsági csoportok, amelyek szerepelnek a hálózati adapter és ugyanabban a régióban. Ismerje meg, hogyan [hozzon létre egy alkalmazásbiztonsági csoportot](#create-an-application-security-group). Ha **alkalmazásbiztonsági csoport**, majd válassza ki egy meglévő alkalmazás biztonsági csoportot, amely ugyanabban a régióban, mint a hálózati adapter létezik. Ha **IP-címek**, majd adja meg **cél IP-címek vagy CIDR-tartományok**. Hasonló **forrás** és **forrás IP-címek vagy CIDR-tartományok**, megadhat egy vagy több címet vagy címtartományt és korlátozások száma is megadhat. Kiválasztásával **virtuális hálózati**, azaz egy szolgáltatáscímke, azt jelenti, hogy a forgalom a virtuális hálózat címterén belül minden IP-címek számára engedélyezett. Ha a megadott IP-cím hozzá van rendelve egy Azure virtuális gépen, győződjön meg arról, magánhálózati IP, nem a virtuális géphez társított nyilvános IP-címet adjon meg. Biztonsági szabályok feldolgozása után az Azure a rendszer lefordítja a bejövő biztonsági szabályok magánhálózati IP-cím a nyilvános IP-címet, és mielőtt Azure fordítja le egy nyilvános IP-cím kimenő szabályok egy magánhálózati IP-címet. Az Azure nyilvános és privát IP-címek kapcsolatos további információkért lásd: [IP-cím-típusok](virtual-network-ip-addresses-overview-arm.md).        |
    |Célporttartományok     | Adjon meg egyetlen érték, vagy értékek vesszővel elválasztott listája. | Hasonló **alkalmazásport-tartományok forrás**, megadhatja, egy vagy több portok és címtartományok és korlátozások száma is megadhat. |
    |Protokoll     | Válassza ki **bármely**, **TCP**, vagy **UDP**.        |         |
    |Műveletek     | Válassza ki **engedélyezése** vagy **megtagadása**.        |         |
    |Prioritás     | Adjon meg egy értéket, amely egyedi az összes biztonsági szabályt a hálózati biztonsági csoporton belül 100-4096 között. |Szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb a szám, annál magasabb a prioritás. Javasoljuk, hogy hagyja eseményáramlási kimaradást közötti prioritás számokat, mint 100, 200, 300 szabályok létrehozása során. Hézagokat teszi könnyebben szabályok hozzáadása a jövőben, szükség lehet, hogy magasabb vagy alacsonyabb, mint a meglévő szabályok.         |
    |Name (Név)     | Egy egyedi nevet a szabály a hálózati biztonsági csoporton belül.        |  A név legfeljebb 80 karakter lehet. Kell kezdődnie, betűvel vagy számmal végződhet egy betűvel, számmal vagy aláhúzásjellel, és csak betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat.       |
    |Leírás     | Az opcionális leírást.        |         |

**Parancsok**

- Az Azure CLI: [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Az összes biztonsági szabályok megtekintése

A hálózati biztonsági csoportok nulla vagy egynél több szabályokat tartalmaz. Szabályok megtekintésekor felsorolt információkkal kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md).

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok*. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati biztonsági csoportot a listából, hogy meg szeretné tekinteni a szabályokat.
3. Válassza ki **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok** alatt **beállítások**.

A lista tartalmazza a létrehozott szabályok és a hálózati biztonsági csoport [alapértelmezett biztonsági szabályokat](security-overview.md#default-security-rules).

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabályok listája](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

1. Írja be a keresőmezőbe, a portál tetején, *hálózati biztonsági csoportok*. Amikor **hálózati biztonsági csoportok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a hálózati biztonsági csoport egy biztonsági szabály részleteinek megtekintéséhez.
3. Válassza ki **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok** alatt **beállítások**.
4. Válassza ki a szabály részleteinek megtekintéséhez. Minden beállítás részletes leírását lásd: [biztonsági szabály beállítások](#security-rule-settings).

**Parancsok**

- Az Azure CLI: [az network nsg szabály show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Biztonsági szabály módosítása

1. Hajtsa végre a [egy biztonsági szabály részleteinek megtekintéséhez](#view-details-of-a-security-rule).
2. Módosítsa a beállításokat igény szerint, majd válassza ki **mentése**. Minden beállítás részletes leírását lásd: [biztonsági szabály beállítások](#security-rule-settings).

**Parancsok**

- Az Azure CLI: [az network nsg szabály frissítése](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Biztonsági szabály törlése

1. Hajtsa végre a [egy biztonsági szabály részleteinek megtekintéséhez](#view-details-of-a-security-rule).
2. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati nsg-szabály törlése](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Az alkalmazásbiztonsági csoportok használata

Egy alkalmazásbiztonsági csoportot nulla vagy több hálózati adaptereket tartalmaz. További tudnivalókért lásd: [az alkalmazásbiztonsági csoportok](security-overview.md#application-security-groups). Egy alkalmazásbiztonsági csoportot az összes hálózati adapter léteznie kell az azonos virtuális hálózatban. Hálózati adapter hozzáadása egy alkalmazásbiztonsági csoportot kapcsolatban lásd: [egy hálózati adapter hozzáadása egy alkalmazásbiztonsági csoportot](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Hozzon létre egy alkalmazásbiztonsági csoportot

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Az a **keresés a piactéren** mezőbe írja be *alkalmazásbiztonsági csoport*. Amikor **alkalmazásbiztonsági csoport** megjelenik a keresési eredmények között, válassza ki azt, válassza **alkalmazásbiztonsági csoport** újra alatt **mindent**, majd válassza ki a **Létrehozása**.
3. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Létrehozás** lehetőséget:

    | Beállítás        | Érték                                                   |
    | ---            | ---                                                     |
    | Name (Név)           | A név egy erőforráscsoporton belül egyedinek kell lennie.        |
    | Előfizetés   | Válassza ki előfizetését.                               |
    | Erőforráscsoport | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. |
    | Hely       | Válasszon ki egy helyet                                       |

**Parancsok**

- Az Azure CLI: [az hálózati adaptertől létrehozása](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [új-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Az összes alkalmazásbiztonsági csoportok megtekintése

1. Válassza ki **minden szolgáltatás** bal felső, a az Azure portál felső részén található.
2. Adja meg *az alkalmazásbiztonsági csoportok* a a **minden szolgáltatás szűrő** mezőbe, majd válassza ki **az alkalmazásbiztonsági csoportok** amikor megjelenik a keresési eredmények között.

**Parancsok**

- Az Azure CLI: [az hálózati adaptertől listája](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Egy adott alkalmazásbiztonsági csoport részleteinek megtekintése

1. Válassza ki **minden szolgáltatás** bal felső, a az Azure portál felső részén található.
2. Adja meg *az alkalmazásbiztonsági csoportok* a a **minden szolgáltatás szűrő** mezőbe, majd válassza ki **az alkalmazásbiztonsági csoportok** amikor megjelenik a keresési eredmények között.
3. Válassza ki az alkalmazásbiztonsági csoporthoz, amelyet szeretne részleteinek megtekintéséhez.

**Parancsok**

- Az Azure CLI: [az hálózati adaptertől show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Az alkalmazásbiztonsági csoport módosítása

1. Válassza ki **minden szolgáltatás** bal felső, a az Azure portál felső részén található.
2. Adja meg *az alkalmazásbiztonsági csoportok* a a **minden szolgáltatás szűrő** mezőbe, majd válassza ki **az alkalmazásbiztonsági csoportok** amikor megjelenik a keresési eredmények között.
3. Válassza ki, amelyek beállításait módosítani szeretné az alkalmazásbiztonsági csoporthoz. Adja hozzá vagy távolítsa el az címkéket, vagy rendelje hozzá vagy távolítsa el az engedélyek az alkalmazásbiztonsági csoporthoz.

- Az Azure CLI: [az hálózati adaptertől update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Nincs PowerShell-parancsmagot.

### <a name="delete-an-application-security-group"></a>Egy alkalmazás biztonsági csoport törlése

Egy alkalmazásbiztonsági csoportot nem lehet törölni, ha bármely, a hálózati adapterek. Az alkalmazásbiztonsági csoport összes hálózati adapter eltávolítása hálózatiadapter-beállítások módosítása, vagy a hálózati adapterek törlésével. További információkért lásd: [való telepítése és eltávolítása az alkalmazásbiztonsági csoportok a hálózati adapter](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) vagy [egy hálózati adapter törlése](virtual-network-network-interface.md#delete-a-network-interface).

1. Válassza ki **minden szolgáltatás** bal felső, a az Azure portál felső részén található.
2. Adja meg *az alkalmazásbiztonsági csoportok* a a **minden szolgáltatás szűrő** mezőbe, majd válassza ki **az alkalmazásbiztonsági csoportok** amikor megjelenik a keresési eredmények között.
3. Válassza ki a törölni kívánt az alkalmazásbiztonsági csoporthoz.
4. Válassza ki **törlése**, majd válassza ki **Igen** törli az alkalmazásbiztonsági csoporthoz.

**Parancsok**

- Az Azure CLI: [az hálózati adaptertől törlése](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Engedélyek

A hálózati biztonsági csoportok biztonsági szabályokat és az alkalmazásbiztonsági csoportok feladatait, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely hozzá van rendelve a az alábbi táblázatban felsorolt megfelelő engedélyei:

### <a name="network-security-group"></a>Hálózati biztonsági csoport

| Műveletek                                                        |   Name (Név)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Hálózati biztonsági csoport lekérése                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Létrehozni vagy frissíteni a hálózati biztonsági csoport                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Hálózati biztonsági csoport törlése                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Egy alhálózathoz vagy hálózati adapterhez a hálózati biztonsági csoport hozzárendelése 


### <a name="network-security-group-rule"></a>Hálózatbiztonságicsoport-szabály

| Műveletek                                                        |   Name (Név)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Get szabály                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Hozzon létre vagy szabály frissítése                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Szabály törlése                                                         |

### <a name="application-security-group"></a>Alkalmazásbiztonsági csoportok

| Műveletek                                                                     | Name (Név)                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Egy IP-konfiguráció csatlakoztatása egy alkalmazásbiztonsági csoportot|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Biztonsági szabály csatlakoztatása egy alkalmazásbiztonsági csoportot    |
| Microsoft.Network/applicationSecurityGroups/read                           | Egy alkalmazás biztonsági csoport lekérése                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Létrehozása vagy frissítése egy alkalmazásbiztonsági csoportot           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Egy alkalmazás biztonsági csoport törlése                     |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy hálózati vagy a biztonsági csoport használó [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok
