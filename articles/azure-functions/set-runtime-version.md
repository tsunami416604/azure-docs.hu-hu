---
title: A Azure Functions futtatókörnyezet verzióinak megcélzása
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogyan határozhatja meg az Azure-ban üzemeltetett Function app futásidejű verzióját.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 74ee0d382dcd468aed118a7de330eef95b329402
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830869"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>A Azure Functions futtatókörnyezet verzióinak megcélzása

A Function alkalmazás a Azure Functions futtatókörnyezet egy adott verziójára fut. Három fő verzió létezik: [1. x, 2. x és 3. x](functions-versions.md). Alapértelmezés szerint a Function apps a futtatókörnyezet 3. x verziójában jön létre. Ez a cikk azt ismerteti, hogyan konfigurálható egy Function alkalmazás az Azure-ban a kiválasztott verzió futtatásához. A helyi fejlesztési környezet adott verzióra való konfigurálásával kapcsolatos információkért lásd: [kód-és tesztelési Azure functions helyileg](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Az automatikus és a manuális verzió frissítései

Azure Functions lehetővé teszi a futtatókörnyezet egy adott verziójának megcélzását a `FUNCTIONS_EXTENSION_VERSION` functions alkalmazásban lévő Alkalmazásbeállítások használatával. A Function alkalmazás a megadott főverzión marad, amíg explicit módon nem választ egy új verzióra. Ha csak a főverziót adta meg, akkor a rendszer automatikusan frissíti a Function alkalmazást a futtatókörnyezet új, az elérhetővé válására szolgáló másodlagos verzióira. Az új alverziók nem mutatják be a törési változásokat. 

Ha alverziót (például "2.0.12345") ad meg, a Function alkalmazás az adott verzióra van rögzítve, amíg explicit módon nem módosítja. A régebbi alverziókat a rendszer rendszeresen eltávolítja az éles környezetből. Ha ez bekövetkezik, a Function alkalmazás a legújabb verzióján fut a-ben beállított verzió helyett `FUNCTIONS_EXTENSION_VERSION` . Emiatt gyorsan fel kell oldania a Function alkalmazás olyan hibáit, amelyek egy adott alverziót igényelnek, így helyette a főverziót is megcélozhatja. A másodlagos verziók eltávolítása [app Service hirdetményekben](https://github.com/Azure/app-service-announcements/issues)jelent meg.

> [!NOTE]
> Ha a Azure Functions egy adott főverziójára rögzít, majd megpróbál az Azure-ban közzétenni a Visual Studióval, egy párbeszédpanel jelenik meg, amely felszólítja, hogy frissítsen a legújabb verzióra, vagy szakítsa meg a közzétételt. Ennek elkerüléséhez adja hozzá a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` tulajdonságot a `.csproj` fájlhoz.

Ha egy új verzió nyilvánosan elérhető, a portálon megjelenik egy üzenet, amely lehetővé teszi az adott verzióra való áttérést. Az új verzióra való áttérés után bármikor használhatja az `FUNCTIONS_EXTENSION_VERSION` alkalmazás beállítását, hogy visszalépjen egy korábbi verzióra.

Az alábbi táblázat az `FUNCTIONS_EXTENSION_VERSION` egyes főverziók értékeit mutatja be az automatikus frissítések engedélyezéséhez:

| Főverzió | `FUNCTIONS_EXTENSION_VERSION`érték |
| ------------- | ----------------------------------- |
| 3. x  | `~3` |
| 2. x  | `~2` |
| 1. x  | `~1` |

A futtatókörnyezet verziójának módosítása a Function alkalmazás újraindítását eredményezi.

## <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése és frissítése

Módosíthatja a Function alkalmazás által használt futtatókörnyezet verzióját. A módosítások megszakítása miatt a funkció alkalmazásban való létrehozása előtt csak a futásidejű verziót lehet módosítani. 

> [!IMPORTANT]
> Bár a futásidejű verziót a beállítás határozza meg `FUNCTIONS_EXTENSION_VERSION` , ezt a módosítást a Azure Portalon kell megtennie, nem pedig közvetlenül a beállítás módosításával. Ennek az az oka, hogy a portál érvényesíti a módosításokat, és szükség szerint más kapcsolódó módosításokat hajt végre.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> A Azure Portal használatával nem módosítható olyan Function alkalmazás futásidejű verziója, amely már tartalmaz függvényeket.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Az Azure CLI-ből

Az Azure CLI-ről is megtekintheti és beállíthatja az alkalmazást `FUNCTIONS_EXTENSION_VERSION` .

>[!NOTE]
>Mivel előfordulhat, hogy a futásidejű verzió más beállításokat is érint, érdemes módosítani a verziót a portálon. A portál automatikusan elvégzi a többi szükséges frissítést, például a Node.js verziót és a futtatókörnyezetet, amikor megváltoztatja a futtatókörnyezet verzióit.  

Az Azure CLI használatával tekintse meg az aktuális futtatókörnyezet verzióját az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le a helyére a `<function_app>` Function alkalmazás nevét. Cserélje le a `<my_resource_group>` függvényt a Function alkalmazás erőforráscsoport nevére is. 

A `FUNCTIONS_EXTENSION_VERSION` következő kimenet jelenik meg, amelyet az egyértelműség érdekében csonkolt:

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

A `FUNCTIONS_EXTENSION_VERSION` Function alkalmazásban található beállításokat az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal frissítheti.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Cserélje le a helyére `<function_app>` a Function alkalmazás nevét. Cserélje le a `<my_resource_group>` függvényt a Function alkalmazás erőforráscsoport nevére is. Továbbá cserélje le az `<version>` 1. x futtatókörnyezet vagy a `~2` 2. x verzió érvényes verzióját.

Ezt a parancsot a [Azure Cloud Shell](../cloud-shell/overview.md) futtathatja, ha az előző kódrészletben a **kipróbálás** lehetőséget választja. Az [Azure CLI helyi](/cli/azure/install-azure-cli) használatával is végrehajthatja ezt a parancsot az [az login (bejelentkezés](/cli/azure/reference-index#az-login) ) parancs végrehajtása után.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az 2,0 futtatókörnyezet megcélzása a helyi fejlesztési környezetben](functions-run-local.md)

> [!div class="nextstepaction"]
> [Lásd a futásidejű verziók kibocsátási megjegyzéseit](https://github.com/Azure/azure-webjobs-sdk-script/releases)
