---
title: Törölt alkalmazások visszaállítása
description: Megtudhatja, hogyan állíthatja vissza egy törölt alkalmazást a Azure App Serviceban. Kerülje a véletlenül törölt alkalmazások fejfájását.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: a30ac638422f99134ebe9cc26e4b418f5de079b9
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672149"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Törölt App Service-alkalmazás visszaállítása a PowerShell használatával

Ha véletlenül törölte az alkalmazást Azure App Serviceban, visszaállíthatja az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)parancsainak használatával.

## <a name="re-register-app-service-resource-provider"></a>App Service erőforrás-szolgáltató újbóli regisztrálása
Egyes ügyfelek olyan problémákba ütközhet, amikor a törölt alkalmazások listájának beolvasása sikertelen. A probléma megoldásához futtassa a következő parancsot:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Törölt alkalmazások listázása

A törölt alkalmazások gyűjteményének beszerzéséhez használhatja a `Get-AzDeletedWebApp`.

A következő használható egy adott törölt alkalmazás részletei:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

A részletes információk a következők:

- **DeletedSiteId**: az alkalmazás egyedi azonosítója, amelyet olyan helyzetekben kell használni, amikor már több azonos nevű alkalmazás is törölve lett
- **SubscriptionID**: a törölt erőforrást tartalmazó előfizetés
- **Hely**: az eredeti alkalmazás helye
- **ResourceGroupName**: az eredeti erőforráscsoport neve
- **Name (név**): az eredeti alkalmazás neve.
- **Tárolóhely**: a tárolóhely neve.
- **Törlés időpontja**: Mikor lett törölve az alkalmazás  

## <a name="restore-deleted-app"></a>Törölt alkalmazás visszaállítása

Miután azonosította a visszaállítani kívánt alkalmazást, `Restore-AzDeletedWebApp`használatával visszaállíthatja.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

A következő parancs bemenetei:

- **Erőforráscsoport**: cél erőforráscsoport, amelyben az alkalmazás vissza lesz állítva
- **Név**: az alkalmazás nevének globálisan egyedinek kell lennie.
- **TargetAppServicePlanName**: app Service az alkalmazáshoz társított csomag

Alapértelmezés szerint a `Restore-AzDeletedWebApp` az alkalmazás konfigurációját és tartalmát is visszaállítja. Ha csak a tartalmat szeretné visszaállítani, használja a `-RestoreContentOnly` jelzőt ezzel a parancsmagot.

> [!NOTE]
> Ha az alkalmazás futott, majd törölve lett egy App Service Environment, akkor csak akkor állítható vissza, ha a megfelelő App Service Environment továbbra is létezik.
>

A teljes parancsmagot-hivatkozást itt találja: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
