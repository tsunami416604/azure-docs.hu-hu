---
title: "Azure Analysis Services-kiszolgáló létrehozása a PowerShell használatával | Microsoft Docs"
description: "Megismerheti, hogyan hozhat létre Azure Analysis Services-kiszolgálót a PowerShell használatával"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: cb42fd3ed51364cf478848cc51ebbb2f175e96d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Azure Analysis Services-kiszolgáló létrehozása a PowerShell használatával

Ez a rövid útmutató bemutatja, hogyan használhatja a PowerShellt a parancssorból egy Azure Analysis Services-kiszolgáló létrehozásához egy, az Azure-előfizetéshez tartozó [Azure-erőforráscsoportban](../azure-resource-manager/resource-group-overview.md).

A feladathoz az Azure PowerShell-modul 4.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 

> [!NOTE]
> A kiszolgáló létrehozása új számlázható szolgáltatás létrejöttét eredményezheti. További tudnivalókért lásd: [Analysis Services-díjszabás](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia, és abban a könyvtárban kell fiókkal rendelkeznie. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Az AzureRm.AnalysisServices modul importálása
Ahhoz, hogy kiszolgálót hozzon létre az előfizetésében, használja az [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) összetevő modulját. Töltse be az AzureRm.AnalysisServices modult a PowerShell-munkamenetbe.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be Azure-előfizetésébe az [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) parancs használatával. Kövesse a képernyőn megjelenő utasításokat.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
 
Az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A kiszolgáló létrehozásakor meg kell adnia egy erőforráscsoportot az előfizetésén belül. Ha még nincs erőforráscsoportja, létrehozhat egy újat a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot az USA nyugati régiójában.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Új kiszolgálót a [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) paranccsal hozhat létre. A következő példában létrehozunk egy myServer nevű kiszolgálót a myResourceGroup erőforráscsoportban az USA nyugati régiójában, D1 szinten. A kiszolgáló rendszergazdája philipc@adventureworks.com.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiszolgálót az előfizetéséből a [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) paranccsal távolíthatja el. Ha a gyűjteménybe tartozó további rövid útmutatókkal és oktatóanyagokkal szeretné folytatni, ne távolítsa el a kiszolgálót. A következő példa eltávolítja az előző lépésben létrehozott kiszolgálót.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések
[Az Azure Analysis Services kezelése a PowerShell-lel](analysis-services-powershell.md)   
[Modell üzembe helyezése SSDT-ről](analysis-services-deploy.md)   
[Modell létrehozása az Azure Portalon](analysis-services-create-model-portal.md)