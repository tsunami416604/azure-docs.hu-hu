---
title: Rövid útmutató – Azure Analysis Services létrehozása a PowerShell-Azure Analysis Services használatával | Microsoft Docs
description: Megismerheti, hogyan hozhat létre Azure Analysis Services-kiszolgálót a PowerShell használatával
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 071c44136d917874843bdf080de775ec6739937a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442873"
---
# <a name="quickstart-create-a-server---powershell"></a>Gyors útmutató: Kiszolgáló létrehozása – PowerShell

Ez a rövid útmutató bemutatja, hogyan használhatja a PowerShellt a parancssorból Azure Analysis Services-kiszolgáló létrehozásához az Azure-előfizetésben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p/).
- **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia, és abban a könyvtárban kell fiókkal rendelkeznie. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).
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

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A kiszolgáló létrehozásakor meg kell adnia egy erőforráscsoportot az előfizetésén belül. Ha még nem rendelkezik erőforráscsoporthoz, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot az USA nyugati régiójában.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Kiszolgáló létrehozása

Hozzon létre egy új kiszolgálót a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. A következő példában létrehozunk egy myServer nevű kiszolgálót a myResourceGroup erőforráscsoportban az USA nyugati régiójában, D1 (ingyenes) szinten. A kiszolgáló rendszergazdája philipc@adventureworks.com.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiszolgálót eltávolíthatja az előfizetésből a [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. Ha a gyűjteménybe tartozó további rövid útmutatókkal és oktatóanyagokkal szeretné folytatni, ne távolítsa el a kiszolgálót. A következő példa eltávolítja az előző lépésben létrehozott kiszolgálót.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót Azure-előfizetésében a PowerShell használatával. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgálótűzfal konfigurálása – Portál](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
