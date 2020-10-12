---
title: A Azure Functions futtatókörnyezet verzióinak megcélzása
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogyan határozhatja meg az Azure-ban üzemeltetett Function app futásidejű verzióját.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88926575"
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

| Főverzió | `FUNCTIONS_EXTENSION_VERSION` érték |
| ------------- | ----------------------------------- |
| 3. x  | `~3` |
| 2. x  | `~2` |
| 1. x  | `~1` |

A futtatókörnyezet verziójának módosítása a Function alkalmazás újraindítását eredményezi.

## <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése és frissítése

Módosíthatja a Function alkalmazás által használt futtatókörnyezet verzióját. A módosítások megszakítása miatt a funkció alkalmazásban való létrehozása előtt csak a futásidejű verziót lehet módosítani. 

> [!IMPORTANT]
> Bár a futásidejű verziót a beállítás határozza meg `FUNCTIONS_EXTENSION_VERSION` , ezt a módosítást a Azure Portalon kell megtennie, nem pedig közvetlenül a beállítás módosításával. Ennek az az oka, hogy a portál érvényesíti a módosításokat, és szükség szerint más kapcsolódó módosításokat hajt végre.

# <a name="portal"></a>[Portál](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> A Azure Portal használatával nem módosítható olyan Function alkalmazás futásidejű verziója, amely már tartalmaz függvényeket.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Az Azure CLI-ről is megtekintheti és beállíthatja az alkalmazást `FUNCTIONS_EXTENSION_VERSION` .  

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
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Cserélje le a helyére `<FUNCTION_APP>` a Function alkalmazás nevét. Cserélje le a `<RESOURCE_GROUP>` függvényt a Function alkalmazás erőforráscsoport nevére is. Azt is `<VERSION>` megteheti, hogy egy adott verziót, vagy, `~3` `~2` vagy `~1` .

Ezt a parancsot a [Azure Cloud Shell](../cloud-shell/overview.md) futtathatja, ha az előző kódrészletben a **kipróbálás** lehetőséget választja. Az [Azure CLI helyi](/cli/azure/install-azure-cli) használatával is végrehajthatja ezt a parancsot az [az login (bejelentkezés](/cli/azure/reference-index#az-login) ) parancs végrehajtása után.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Azure Functions futtatókörnyezetének vizsgálatához használja a következő parancsmagot: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Cserélje le a `<FUNCTION_APP>` nevet a Function alkalmazás nevére, `<RESOURCE_GROUP>` és. A rendszer a beállítás aktuális értékét `FUNCTIONS_EXTENSION_VERSION` adja vissza a kivonatoló táblában.

A függvények futtatókörnyezetének módosításához használja a következő parancsfájlt:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Ahogy korábban is, a helyére írja a `<FUNCTION_APP>` Function alkalmazás nevét és az `<RESOURCE_GROUP>` erőforráscsoport nevét. Továbbá cserélje le `<VERSION>` az-t az adott verzióra vagy a főverzióra, például: `~2` vagy `~3` . A `FUNCTIONS_EXTENSION_VERSION` visszaadott kivonatoló táblában ellenőrizheti a beállítás frissített értékét. 

---

A Function alkalmazás újraindul, miután megtörtént a módosítás az alkalmazás beállításainál.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az 2,0 futtatókörnyezet megcélzása a helyi fejlesztési környezetben](functions-run-local.md)

> [!div class="nextstepaction"]
> [Lásd a futásidejű verziók kibocsátási megjegyzéseit](https://github.com/Azure/azure-webjobs-sdk-script/releases)
