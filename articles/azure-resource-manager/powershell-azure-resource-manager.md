---
title: "Az Azure PowerShell-megoldások kezelése |} Microsoft Docs"
description: "Azure PowerShell és a Resource Manager segítségével kezelheti az erőforrásokat."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Az Azure PowerShell és a Resource Manager-erőforrások kezelése

Ebből a cikkből megismerheti, hogyan kezelheti a megoldások Azure PowerShell és az Azure Resource Manager. Ha nem ismeri a Resource Manager, lásd: [Resource Manager áttekintése](resource-group-overview.md). Ez a cikk foglalkozik a felügyeleti feladatokat. Az alábbiakat fogja elvégezni:

1. Hozzon létre egy erőforráscsoportot
2. Az erőforráscsoport erőforrás hozzáadása
3. Az erőforrás egy címke hozzáadása
4. A nevek vagy címke értékek alapján-erőforrások lekérdezése
5. Alkalmazza, és távolítsa el az erőforrás zárolását
6. Egy erőforráscsoportot

Ez a cikk nem szerepelnek a Resource Manager-sablon üzembe helyezése az előfizetéshez. Ez az információ, lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Ismerkedés az Azure PowerShell

Ha még nem telepítette az Azure PowerShell, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

Ha korábban telepítette az Azure PowerShell, de nem frissítve, legutóbb, javasoljuk, hogy telepítse a legújabb verzióra. A verziófrissítés keresztül ugyanezt a módszert akkor telepítéséhez is használt. Például ha a Webplatform-telepítő, indítsa el újra, és keresse meg frissítés.

Az Azure-erőforrások modul verziójának ellenőrzéséhez használja a következő parancsmagot:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

A cikk frissült 3.3.0 verziójához. Ha egy korábbi, a felhasználói élmény nem egyeznek az ebben a cikkben szereplő lépéseket. Az ebben a verzióban a parancsmagokkal dokumentációjáért lásd: [AzureRM.Resources modul](/powershell/module/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjával
A megoldás dolgozik, mielőtt be kell jelentkezni fiókját.

Jelentkezzen be az Azure-fiókjával, használja a **Login-AzureRmAccount** parancsmag.

```powershell
Login-AzureRmAccount
```

A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatait. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára.

A parancsmag fiókját és a feladatokhoz használhatja, hogy az előfizetés információt ad vissza.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Ha egynél több előfizetéssel rendelkezik, átválthat egy másik előfizetést. Első lépésként Ismerkedjen meg az összes olyan előfizetést, a fiókjához.

```powershell
Get-AzureRmSubscription
```

Engedélyezett és letiltott előfizetésekhez adja vissza.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Váltson át egy másik előfizetésben található, adja meg az előfizetés nevét a **Set-AzureRmContext** parancsmag.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az előfizetéshez erőforrásokat telepítés megkezdése előtt létre kell hoznia az erőforrásokat tartalmazó erőforráscsoport.

Erőforráscsoport létrehozásához használja a **New-AzureRmResourceGroup** parancsmag. A parancs a **neve** paraméter segítségével adjon meg egy nevet az erőforráscsoporthoz tartozó és a **hely** paramétert adja meg a helyet.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

A kimenet a következő formátumban kell megadni:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Ha az erőforráscsoport beolvasni később van szüksége, használja a következő parancsmagot:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Ahhoz, hogy az erőforráscsoportok az előfizetéshez, ne adjon meg egy nevet:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Erőforrások hozzáadása egy erőforráscsoportot

Erőforrás hozzáadása az erőforráscsoporthoz, használhatja a **New-AzureRmResource** létrehozásakor parancsmag vagy az erőforrás típusára vonatkozó parancsmag (például **New-AzureRmStorageAccount**). Előfordulhat, hogy ez egyszerűbbé teszi, hogy csak az erőforrástípus, mert azokat a tulajdonságokat, amelyeket az új erőforrás szükséges paramétereket tartalmazza a parancsmag használatával. Használandó **New-AzureRmResource**, ismernie kell az összes tulajdonság beállítása nélkül meg kell adni őket.

Azonban parancsmagokon keresztül erőforrás hozzáadása okozhat jövőbeli zavart, mert az új erőforrás nem létezik a Resource Manager-sablon. A Microsoft azt javasolja, adjon meg az infrastruktúra az Azure-megoldás a Resource Manager-sablon. Sablonok lehetővé teszik megbízható és ismételten a megoldás üzembe helyezéséhez. Ez a cikk egy PowerShell-parancsmaggal hozzon létre egy tárfiókot, de később az erőforráscsoport a sablon létrehozása.

A következő parancsmag létrehoz egy tárfiókot. A példában látható módon a neve helyett adja meg egy egyedi nevet a tárfiók. A névnek kell 3 és 24 karakter hosszúságúnak kell, és csak számokat és kisbetűket tartalmazhatnak. A példában látható módon a nevet használja, ha egy hibaüzenetet kapja, mert a név már használatban van.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Ha ezt az erőforrást beolvasni később van szüksége, használja a következő parancsmagot:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Címke hozzáadása

Címkék lehetővé teszik az erőforrások más tulajdonságokkal szerint rendezheti. Például előfordulhat, hogy több erőforrás részleghez tartozó különböző erőforrás csoportokban. Ezeket az erőforrásokat megjelölése azonos kategóriába tartozó alkalmazhat egy részleg címke és értéket. Vagy jelölheti meg használja-e egy üzemi és tesztkörnyezetben környezetben egy erőforrást. Ebben a cikkben címkékkel csak egy erőforráshoz, de a környezetben valószínűleg érdemes címkék azokra az erőforrások.

A következő parancsmagot a tárfiók két címkék vonatkozik:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Címkék egyetlen objektumként frissülnek. Egy címke hozzáadása egy erőforrást, amely már tartalmazza a címkék, először kérjen le a meglévő címkéket. Az új címke hozzáadása a meglévő címkék tartalmazó objektumot, és alkalmazza újra a címkék az erőforráshoz.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Erőforrások keresése

Használja a **keresés-AzureRmResource** parancsmag használatával kérhet le az erőforrások más keresési feltételeket.

* Ahhoz, hogy az erőforrás neve, adja meg a **ResourceNameContains** paraméter:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Ahhoz, hogy az összes erőforrást erőforráscsoportban, adja meg a **ResourceGroupNameContains** paraméter:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Ahhoz, hogy a címke nevét és értékét az erőforrások, adja meg a **TagName** és **TagValue** paraméterek:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Az összes erőforrást az adott erőforrástípus, adjon meg a **ResourceType** paraméter:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>Erőforrás-azonosító lekérése

Sok parancsok egy erőforrás-azonosító paramétert igénybe vehet. Az azonosító egy erőforrás és a tároló egy változóban, amelyet:

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>Egy erőforrás zárolása

Győződjön meg arról, hogy a kritikus fontosságú erőforrás nem véletlenül törölték vagy módosították van szüksége, alkalmazza a zárolási az erőforráshoz. Vagy megadhat egy **CanNotDelete** vagy **ReadOnly**.

Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök csak a tulajdonos és a felhasználói hozzáférés adminisztrátora kapnak ezeket a műveleteket.

A zárolási segítségével a következő parancsmagot:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Az előző példában a zárolt erőforrás nem törölhető, amíg a rendszer eltávolítja a zárolást. A zárolási eltávolításához használja:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

A beállítás zárolások kapcsolatos további információkért lásd: [erőforrások az Azure Resource Manager zárolása](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Távolítsa el az erőforrás vagy erőforráscsoport
Egy erőforrás vagy egy erőforráscsoportot is eltávolíthat. Egy erőforráscsoport, akkor is távolítsa el erőforrás csoportba tartozó összes erőforrást.

* Az erőforráscsoport egy erőforrás törléséhez használja a **Remove-AzureRmResource** parancsmag. Ez a parancsmag törli az erőforrást, de nem törli az erőforráscsoportot.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Az erőforráscsoport és az ahhoz tartozó összes erőforrást törölheti a **Remove-AzureRmResourceGroup** parancsmag.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

Mindkét parancsmagokkal a rendszer felkéri győződjön meg arról, hogy szeretne-e az erőforrás vagy erőforráscsoport eltávolítani. Ha a művelet sikeresen törli az erőforrás vagy az erőforráscsoportot, visszatérési **igaz**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Az Azure Automation szolgáltatásban, erőforrás-kezelő parancsfájlok futtatása

Ez a cikk bemutatja, hogyan az erőforrások az Azure PowerShell alapvető műveleteket. Speciális kezelési forgatókönyvek esetén általában kívánt hozzon létre egy parancsfájlt, és újra felhasználhatja a parancsfájlhoz, igény szerint vagy ütemezés szerint. [Azure Automation szolgáltatásbeli](../automation/automation-intro.md) biztosít, amelynek automatizálhatja a gyakran használt parancsfájlok, amelyek az Azure megoldások kezelése.

A következő témakörökben láthatja, hogyan használható az Azure Automation, az erőforrás-kezelő és a PowerShell felügyeleti feladatok hatékony elvégzésére:

- Egy runbook létrehozásával kapcsolatos további információkért lásd: [az első PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md).
- További információ a gyűjtemények, parancsfájlok,: [az Azure Automation forgatókönyv- és gyűjtemények](../automation/automation-runbook-gallery.md).
- A runbookok, amelyek indítása és leállítása a virtuális gépek, lásd: [Azure Automation-forgatókönyv: címkék használatával JSON-formátumú az Azure virtuális gép indítási és leállítási ütemezés létrehozása](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- A runbookok, amelyek indítása és leállítása a virtuális gépek munkaidőn kívüli, lásd: [indítása/leállítása virtuális gépek munkaidőn kívüli megoldás az automatizálás során](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Következő lépések
* Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).
* Sablonok telepítésével kapcsolatos további tudnivalókért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).
* Meglévő erőforrásokat áthelyezheti egy új erőforráscsoportot. Tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

