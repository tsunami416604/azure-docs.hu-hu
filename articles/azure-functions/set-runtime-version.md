---
title: A Azure Functions futtatókörnyezet verzióinak megcélzása
description: Azure Functions támogatja a futtatókörnyezet több verzióját. Megtudhatja, hogyan határozhatja meg az Azure-ban üzemeltetett Function app futásidejű verzióját.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: 6e9ac50f38b2cf7bc3531e58e87ff2a8768c0a45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650471"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>A Azure Functions futtatókörnyezet verzióinak megcélzása

A Function alkalmazás a Azure Functions futtatókörnyezet egy adott verziójára fut. Két fő verzió létezik: [1. x és 2. x](functions-versions.md). Alapértelmezés szerint a functions alkalmazások a futtatókörnyezet 2. x verzióját hozták létre. Ez a cikk azt ismerteti, hogyan konfigurálható egy Function alkalmazás az Azure-ban a kiválasztott verzió futtatásához. A helyi fejlesztési környezet adott verzióra való konfigurálásával kapcsolatos információkért lásd: [kód-és tesztelési Azure functions helyileg](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Az automatikus és a manuális verzió frissítései

Azure functions lehetővé teszi a futtatókörnyezet egy adott verziójának megcélzását a `FUNCTIONS_EXTENSION_VERSION` functions alkalmazásban lévő Alkalmazásbeállítások használatával. A Function alkalmazás a megadott főverzión marad, amíg explicit módon nem választ egy új verzióra.

Ha csak a főverziót ("~ 2") választja 2. x vagy "~ 1" értékre az 1. x esetében, a rendszer automatikusan frissíti a Function alkalmazást a futásidejű új, az elérhetővé váláshoz szükséges másodlagos verzióira. Az új alverziók nem vezetnek be feltörési módosításokat. Ha alverziót (például "2.0.12345") ad meg, a Function alkalmazás az adott verzióra van rögzítve, amíg explicit módon nem módosítja.

> [!NOTE]
> Ha a Azure Functions egy adott verziójához rögzít, majd megpróbál az Azure-ban közzétenni az Azure-ban a Visual Studióval, megnyílik egy párbeszédpanel, amely felszólítja, hogy frissítsen a legújabb verzióra, vagy szakítsa meg a közzétételt. Ennek elkerüléséhez adja hozzá a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` tulajdonságot `.csproj` a fájlhoz.

Ha egy új verzió nyilvánosan elérhető, a portálon megjelenik egy üzenet, amely lehetővé teszi az adott verzióra való áttérést. Az új verzióra való áttérés után bármikor használhatja az `FUNCTIONS_EXTENSION_VERSION` alkalmazás beállítását, hogy visszalépjen egy korábbi verzióra.

A futtatókörnyezet verziójának módosítása a Function alkalmazás újraindítását eredményezi.

Az Alkalmazásbeállítások által az automatikus frissítések engedélyezéséhez beállítható `FUNCTIONS_EXTENSION_VERSION` értékek jelenleg "~ 1" az 1. x futtatókörnyezethez és a "~ 2" 2. x verzióhoz.

## <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése és frissítése

Módosíthatja a Function alkalmazás által használt futtatókörnyezet verzióját. A módosítások megszakítása miatt a funkció alkalmazásban való létrehozása előtt csak a futásidejű verziót lehet módosítani. Bár a futásidejű verziót a `FUNCTIONS_EXTENSION_VERSION` beállítás határozza meg, ezt a módosítást a Azure Portalon kell megtennie, nem pedig közvetlenül a beállítás módosításával. Ennek az az oka, hogy a portál érvényesíti a módosításokat, és szükség szerint más kapcsolódó módosításokat hajt végre.

### <a name="from-the-azure-portal"></a>A Azure Portal

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> A Azure Portal használatával nem módosítható olyan Function alkalmazás futásidejű verziója, amely már tartalmaz függvényeket.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Az Azure CLI-ből

Az Azure CLI-ről is megtekintheti és beállíthatja az `FUNCTIONS_EXTENSION_VERSION` alkalmazást.

>[!NOTE]
>Mivel előfordulhat, hogy a futásidejű verzió más beállításokat is érint, érdemes módosítani a verziót a portálon. A portál automatikusan elvégzi a többi szükséges frissítést, például a Node. js-verziót és a futásidejű veremet, amikor megváltoztatja a futásidejű verziókat.  

Az Azure CLI használatával tekintse meg az aktuális futtatókörnyezet verzióját az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le `<function_app>` a helyére a Function alkalmazás nevét. Cserélje le `<my_resource_group>` a függvényt a Function alkalmazás erőforráscsoport nevére is. 

A következő kimenet `FUNCTIONS_EXTENSION_VERSION` jelenik meg, amelyet az egyértelműség érdekében csonkolt:

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

A Function alkalmazásban `FUNCTIONS_EXTENSION_VERSION` található beállításokat az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal frissítheti.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Cserélje `<function_app>` le a helyére a Function alkalmazás nevét. Cserélje le `<my_resource_group>` a függvényt a Function alkalmazás erőforráscsoport nevére is. Továbbá cserélje le `<version>` az 1. x futtatókörnyezet vagy `~2` a 2. x verzió érvényes verzióját.

Ezt a parancsot a [Azure Cloud Shell](../cloud-shell/overview.md) futtathatja, ha az előző kódrészletben a **kipróbálás** lehetőséget választja. Az [Azure CLI helyi](/cli/azure/install-azure-cli) használatával is végrehajthatja ezt a parancsot az [az login (bejelentkezés](/cli/azure/reference-index#az-login) ) parancs végrehajtása után.



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az 2,0 futtatókörnyezet megcélzása a helyi fejlesztési környezetben](functions-run-local.md)

> [!div class="nextstepaction"]
> [Lásd a futásidejű verziók kibocsátási megjegyzéseit](https://github.com/Azure/azure-webjobs-sdk-script/releases)
