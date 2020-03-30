---
title: Törölt alkalmazások visszaállítása
description: Ismerje meg, hogyan állíthatja vissza a törölt alkalmazásokat az Azure App Service-ben. Kerülje el a véletlenül törölt alkalmazások fejfájását.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689623"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Eltávolított App Service-alkalmazás visszaállítása a PowerShell használatával

Ha véletlenül törölte az alkalmazást az Azure App Service szolgáltatásban, visszaállíthatja azt az [Az PowerShell modul](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)parancsaival.

> [!NOTE]
> A törölt alkalmazások a kezdeti törlés után 30 nappal törlődnek a rendszerből. Miután egy alkalmazást töröltek, nem lehet visszacsinálni.
>

## <a name="re-register-app-service-resource-provider"></a>Újra regisztrálása App Service erőforrás-szolgáltató
Előfordulhat, hogy egyes ügyfelek olyan problémával találkoznak, amely miatt a törölt alkalmazások listájának beolvasása sikertelen. A probléma megoldásához futtassa a következő parancsot:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Törölt alkalmazások listázása

A törölt alkalmazások gyűjteményének begyűjtéséhez használhatja a használatát. `Get-AzDeletedWebApp`

Egy adott törölt alkalmazás részleteiért:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

A részletes információk a következőket tartalmazzák:

- **DeletedSiteId**: Az alkalmazás egyedi azonosítója, olyan esetekben, amikor több azonos nevű alkalmazást töröltek
- **SubscriptionID**: A törölt erőforrást tartalmazó előfizetés
- **Hely**: Az eredeti alkalmazás helye
- **ResourceGroupName**: Az eredeti erőforráscsoport neve
- **Név**: Az eredeti alkalmazás neve.
- **Bővítőhely:** a bővítőhely neve.
- **Törlési idő**: Mikor törölték az alkalmazást?  

## <a name="restore-deleted-app"></a>Törölt alkalmazás visszaállítása

Miután azonosította a visszaállítani kívánt alkalmazást, visszaállíthatja azt a használatával. `Restore-AzDeletedWebApp`

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

A parancs bemenetei a következők:

- **Erőforráscsoport**: Az alkalmazás visszaállításának helyét célzó erőforráscsoport
- **Név**: Az alkalmazás nevének globálisan egyedinek kell lennie.
- **TargetAppServicePlanName**: Az alkalmazáshoz kapcsolódó App Service-csomag

Alapértelmezés `Restore-AzDeletedWebApp` szerint visszaállítja mind az alkalmazás konfigurációját, mind a tartalmat. Ha csak a tartalmat szeretné `-RestoreContentOnly` visszaállítani, használja a jelzőt ezzel a parancsleéssel.

> [!NOTE]
> Ha az alkalmazás üzemeltetve volt, majd törölték egy App Service-környezetből, akkor csak akkor állítható vissza, ha a megfelelő App Service-környezet továbbra is létezik.
>

A teljes parancsfüzet hivatkozásitt található: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
