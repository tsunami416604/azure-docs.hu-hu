---
title: Töltse le az Azure-verem eszközök a Githubról |} Microsoft Docs
description: Útmutató eszközök Azure verem használata szükséges.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075289"
---
# <a name="download-azure-stack-tools-from-github"></a>Töltse le az Azure-verem eszközök a Githubról

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

**AzureStack-eszközök** van egy GitHub-tárházban, amelyen a PowerShell-modulok kezelése és Azure verem erőforrásokat üzembe helyezi. Ha azt tervezi, hogy a VPN-kapcsolatot létrehozni, letöltheti a PowerShell-modulok, az Azure verem szoftverfejlesztői készlet, vagy egy Windows-alapú külső ügyfél számára. Ezek az eszközök beszerzéséhez klónozza a GitHub-tárházban, vagy töltse le a **AzureStack-eszközök** mappa a következő parancsfájl futtatásával:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>A modulok által biztosított funkciókat

A **AzureStack-eszközök** tárház tartalmaz, amely támogatja a következő funkciók verem Azure PowerShell-modul:  

| Funkció | Leírás | Ez a modul ki tudja használni? |
| --- | --- | --- |
| [Felhőalapú képességek](user/azure-stack-validate-templates.md) | Ez a modul segítségével felhő felhőalapú szolgáltatásokkal. Például ez a modul használatával juthat a felhőalapú képességek, például az API-verzió és az Azure Resource Manager erőforrások. Ez a modul használatával Azure verem és Azure felhők is beszerezheti a Virtuálisgép-bővítmények. | A felhő üzemeltetőinek és a felhasználók |
| [Erőforrás-kezelő házirend Azure verem](user/azure-stack-policy-module.md) | Ez a modul segítségével konfigurálása Azure-előfizetés vagy egy Azure-erőforráscsoportot a azonos versioning és a szolgáltatás rendelkezésre állás mint Azure verem. | A felhő üzemeltetőinek és a felhasználók |
| [Regisztrálja az Azure-ral](azure-stack-register.md) | Ez a modul segítségével regisztrálja a development kit példányát az Azure-ral. A regisztrálás után töltse le a Piactéri elemek az Azure-ból, és azok Azure verem használatát. | A felhő üzemeltetői |
| [Az Azure verem-telepítés](azure-stack-run-powershell-script.md) | Ez a modul segítségével a előkészítéséhez Azure verem gazdagép telepítése, és telepítse újra az Azure-verem virtuális merevlemez (VHD) lemezkép használatával. | A felhő üzemeltetői|
| [Kapcsolódás Azure verem](azure-stack-connect-powershell.md) | Ez a modul segítségével konfigurálhatja a VPN-kapcsolat Azure verem. | A felhő üzemeltetőinek és a felhasználók |
| [Sablon érvényesítő](user/azure-stack-validate-templates.md) | Ez a modul segítségével ellenőrizheti, ha egy meglévő vagy új sablon Azure verem számára telepíthető. | A felhő üzemeltetőinek és a felhasználók|


## <a name="next-steps"></a>További lépések
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](user/azure-stack-powershell-configure-user.md)   
* [Csatlakozás Azure verem szoftverfejlesztői készlet egy VPN-kapcsolaton keresztül](azure-stack-connect-azure-stack.md)  
