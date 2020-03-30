---
title: Az Azure Functions futásidejű verzióinak megcélzása
description: Az Azure Functions a futásidejű több verzióját támogatja. Ismerje meg, hogyan adhatja meg az Azure-ban üzemeltetett függvényalkalmazás futásidejű verzióját.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151955"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions futásidejű verzióinak megcélzása

Egy függvényalkalmazás az Azure Functions futásidejű egy adott verzióján fut. Három fő verzió létezik: [1.x, 2.x és 3.x](functions-versions.md). Alapértelmezés szerint a függvényalkalmazások a futásidejű 2.x-es verzióban jönnek létre. Ez a cikk bemutatja, hogyan konfigurálhat egy függvényalkalmazást az Azure-ban a kiválasztott verzión való futtatáshoz. A helyi fejlesztési környezet konfigurálásáról egy adott verzióhoz a [Code and test in Local in (Helyi verzió)](functions-run-local.md)című témakörben talál további információt.

## <a name="automatic-and-manual-version-updates"></a>Automatikus és manuális verziófrissítések

Az Azure Functions lehetővé teszi, hogy a `FUNCTIONS_EXTENSION_VERSION` futásidejű egy adott verzióját célozza meg az alkalmazásbeállítás használatával egy függvényalkalmazásban. A függvényalkalmazás a megadott főverzión marad, amíg kifejezetten úgy nem dönt, hogy új verzióra lép.

Ha csak a főverziót adja meg, a függvényalkalmazás automatikusan frissül a futásidejű új alverzióira, amikor azok elérhetővé válnak. Az új alverziók nem vezethetnek be törésmódosításokat. Ha egy alverziót (például "2.0.12345") ad meg, a függvényalkalmazás az adott verzióhoz van rögzítve, amíg kifejezetten meg nem változtatja azt.

> [!NOTE]
> Ha az Azure Functions egy adott verziójára rögzít, majd a Visual Studio használatával próbál meg közzétenni az Azure-ban, megjelenik egy párbeszédpanelablak, amely a legújabb verzióra való frissítésre vagy a közzététel megszakítására kéri. Ennek elkerülése érdekében `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` adja `.csproj` hozzá a tulajdonságot a fájlhoz.

Ha egy új verzió nyilvánosan elérhető, a portálon lévő kérdés lehetőséget ad arra, hogy fellépjen az adott verzióra. Az új verzióra való áttérés után `FUNCTIONS_EXTENSION_VERSION` mindig használhatja az alkalmazásbeállítást az előző verzióra való visszatéréshez.

Az alábbi táblázat `FUNCTIONS_EXTENSION_VERSION` az egyes főverziók automatikus frissítések engedélyezéséhez szükséges értékeit mutatja be:

| Főverzió | `FUNCTIONS_EXTENSION_VERSION`Érték |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2,x  | `~2` |
| 1,x  | `~1` |

A futásidejű verzió módosítása a függvényalkalmazás újraindítását okozza.

## <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése és frissítése

Módosíthatja a függvényalkalmazás által használt futásidejű verziót. A módosítások esetleges megszakadása miatt csak a futásidejű verziót módosíthatja, mielőtt bármilyen függvényt létrehozott volna a függvényalkalmazásban. 

> [!IMPORTANT]
> Bár a futásidejű verzió `FUNCTIONS_EXTENSION_VERSION` határozza meg a beállítást, ezt a módosítást az Azure Portalon, és nem a beállítás közvetlen módosításával. Ennek az az oka, hogy a portál ellenőrzi a módosításokat, és szükség szerint egyéb kapcsolódó módosításokat hajt végre.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Az Azure Portal használatával nem módosíthatja a függvényalkalmazás futásidejű verzióját, amely már tartalmaz függvényeket.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Az Azure CLI-ből

Megtekintheti és beállíthatja `FUNCTIONS_EXTENSION_VERSION` az Azure CLI-ből.

>[!NOTE]
>Mivel a futásidejű verzió más beállításokat is érinthet, módosítania kell a verziót a portálon. A portál automatikusan elkészíti a többi szükséges frissítést, például a Node.js verziót és a futásidejű veremeket, amikor módosítja a futásidejű verziókat.  

Az Azure CLI használatával tekintse meg az aktuális futásidejű verziót az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) paranccsal.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le `<function_app>` a függvényalkalmazás nevét. Is `<my_resource_group>` cserélje ki a függvényalkalmazás erőforráscsoportjának nevét is. 

Az egyértelműség `FUNCTIONS_EXTENSION_VERSION` érdekében a következő kimenetlátható:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

A beállítás `FUNCTIONS_EXTENSION_VERSION` frissíthető a függvényalkalmazásban az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) paranccsal.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Cserélje `<function_app>` le a függvényalkalmazás nevére. Is `<my_resource_group>` cserélje ki a függvényalkalmazás erőforráscsoportjának nevét is. Cserélje le `<version>` az 1.x futásidejű érvényes `~2` verziójára vagy a 2.x-es verzióra is.

Ezt a parancsot az [Azure Cloud Shell](../cloud-shell/overview.md) az előző kódminta **Kipróbálása** lehetőséget választva futtathatja. Az [Azure CLI helyileg](/cli/azure/install-azure-cli) is futtathatja ezt a parancsot az [az bejelentkezés](/cli/azure/reference-index#az-login) végrehajtása után a bejelentkezéshez.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A 2.0-s futásidő megcélzása a helyi fejlesztési környezetben](functions-run-local.md)

> [!div class="nextstepaction"]
> [Lásd: Kibocsátási megjegyzések a futásidejű verziókhoz](https://github.com/Azure/azure-webjobs-sdk-script/releases)
