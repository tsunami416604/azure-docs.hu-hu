---
title: Rövid útmutató – Azure Analysis Services-kiszolgáló létrehozása a PowerShell-lel | Microsoft Docs
description: Megismerheti, hogyan hozhat létre Azure Analysis Services-kiszolgálót a PowerShell használatával
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8159cc8d0e00e830f52e1814ac770c4a16f6b957
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619342"
---
# <a name="quickstart-create-a-server---powershell"></a>Gyors útmutató: Kiszolgáló létrehozása – PowerShell

Ez a rövid útmutató bemutatja, hogyan használhatja a PowerShellt a parancssorból Azure Analysis Services-kiszolgáló létrehozásához az Azure-előfizetésben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**: Hozzon létre egy fiókot az [Azure ingyenes próbaverziójában](https://azure.microsoft.com/offers/ms-azr-0044p/) .
- **Azure Active Directory**: Az előfizetést egy Azure Active Directory bérlőhöz kell társítani, és rendelkeznie kell egy fiókkal a címtárban. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).
- **Azure PowerShell**. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="import-azanalysisservices-module"></a>Importálás az. AnalysisServices modul

Ha kiszolgálót szeretne létrehozni az előfizetésben, használja az az [. AnalysisServices](/powershell/module/az.analysisservices) modult. Töltse be az az. AnalysisServices modult a PowerShell-munkamenetbe.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancs használatával. Kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A kiszolgáló létrehozásakor meg kell adnia egy erőforráscsoportot az előfizetésén belül. Ha még nem rendelkezik erőforráscsoporthoz, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot az USA nyugati régiójában.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre egy új kiszolgálót a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. A következő példában létrehozunk egy myServer nevű kiszolgálót a myResourceGroup erőforráscsoportban az USA nyugati régiójában, D1 (ingyenes) szinten. A kiszolgáló rendszergazdája philipc@adventureworks.com.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiszolgálót eltávolíthatja az előfizetésből a Remove [-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. Ha a gyűjteménybe tartozó további rövid útmutatókkal és oktatóanyagokkal szeretné folytatni, ne távolítsa el a kiszolgálót. A következő példa eltávolítja az előző lépésben létrehozott kiszolgálót.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót Azure-előfizetésében a PowerShell használatával. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: A kiszolgáló tűzfalának konfigurálása – portál](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Oktatóanyag: Minta modell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
