---
title: Rövid útmutató – Azure Analysis Services-kiszolgáló létrehozása a PowerShell-lel | Microsoft Docs
description: Megismerheti, hogyan hozhat létre Azure Analysis Services-kiszolgálót a PowerShell használatával
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 01bd8b5d1f8ed0d78f3331b4150df37ef0a1049e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426780"
---
# <a name="quickstart-create-a-server---powershell"></a>Gyors útmutató: Kiszolgáló létrehozása – PowerShell

Ez a rövid útmutató bemutatja, hogyan használhatja a PowerShellt a parancssorból Azure Analysis Services-kiszolgáló létrehozásához az Azure-előfizetésben.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Látogasson el [Azure ingyenes próbaverziója](https://azure.microsoft.com/offers/ms-azr-0044p/) hozhat létre fiókot.
- **Azure Active Directory**: Az előfizetés Azure Active Directory-bérlő társítva kell lennie, és ebben a könyvtárban kell fiókkal rendelkeznie. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).
- **Az Azure PowerShell-modul 4.0-s vagy újabb verziója**. A verzió megkereséséhez futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.

## <a name="import-azurermanalysisservices-module"></a>Az AzureRm.AnalysisServices modul importálása

Ahhoz, hogy kiszolgálót hozzon létre az előfizetésében, használja az [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) összetevő modulját. Töltse be az AzureRm.AnalysisServices modult a PowerShell-munkamenetbe.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetéshez a [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsot. Kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A kiszolgáló létrehozásakor meg kell adnia egy erőforráscsoportot az előfizetésén belül. Ha még nincs erőforráscsoportja, létrehozhat egy újat a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot az USA nyugati régiójában.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Új kiszolgálót a [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) paranccsal hozhat létre. A következő példában létrehozunk egy myServer nevű kiszolgálót a myResourceGroup erőforráscsoportban az USA nyugati régiójában, D1 (ingyenes) szinten. A kiszolgáló rendszergazdája philipc@adventureworks.com.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiszolgálót az előfizetéséből a [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) paranccsal távolíthatja el. Ha a gyűjteménybe tartozó további rövid útmutatókkal és oktatóanyagokkal szeretné folytatni, ne távolítsa el a kiszolgálót. A következő példa eltávolítja az előző lépésben létrehozott kiszolgálót.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót Azure-előfizetésében a PowerShell használatával. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgálótűzfal - portál konfigurálása](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)