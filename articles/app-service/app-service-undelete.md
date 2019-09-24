---
title: Törölt App Service-alkalmazások visszaállítása – Azure App Service
description: Megtudhatja, hogyan állíthatja vissza a törölt App Service alkalmazást a PowerShell használatával.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219537"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Törölt App Service-alkalmazás visszaállítása a PowerShell használatával

Ha véletlenül törölte az alkalmazást Azure App Serviceban, visszaállíthatja az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)parancsainak használatával.

## <a name="list-deleted-apps"></a>Törölt alkalmazások listázása

A törölt alkalmazások gyűjteményének lekéréséhez használhatja `Get-AzDeletedWebApp`a következőt:.

A következő használható egy adott törölt alkalmazás részletei:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

A részletes információk a következők:

- **DeletedSiteId**: Az alkalmazás egyedi azonosítója, amelyet olyan helyzetekben kell használni, amikor már több azonos nevű alkalmazás is törölve lett
- **SubscriptionID**: A törölt erőforrást tartalmazó előfizetés
- **Hely**: Az eredeti alkalmazás helye
- **ResourceGroupName**: Az eredeti erőforráscsoport neve
- **Név**: Az eredeti alkalmazás neve.
- **Tárolóhely**: a tárolóhely neve.
- **Törlés időpontja**: Mikor lett törölve az alkalmazás  

## <a name="restore-deleted-app"></a>Törölt alkalmazás visszaállítása

Miután azonosította a visszaállítani kívánt alkalmazást, visszaállíthatja azt a használatával `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

A következő parancs bemenetei:

- **Erőforráscsoport**: Cél erőforráscsoport, amelyben az alkalmazás vissza lesz állítva
- **Név**: Az alkalmazás nevének globálisan egyedinek kell lennie.
- **TargetAppServicePlanName**: Az alkalmazáshoz kapcsolódó App Service csomag

Alapértelmezés `Restore-AzDeletedWebApp` szerint az alkalmazás konfigurációját és tartalmát is visszaállítja a rendszer. Ha csak a tartalmat szeretné visszaállítani, használja a `-RestoreContentOnly` jelzőt ezzel a parancsmagot.
