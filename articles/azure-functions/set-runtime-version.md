---
title: Az Azure Functions runtime verziók bemutatásához
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg, hogyan adja meg az Azure-ban futó függvényalkalmazást a futtatókörnyezet-verzióját.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: a0f66f5a1ba64c955fe0669d3ed215ee7c2895c0
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498399"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions runtime verziók bemutatásához

Egy függvényalkalmazást az Azure Functions futtatókörnyezete egy adott verzióját futtatja. Két fő verziója: [1.x és a 2.x](functions-versions.md). Alapértelmezés szerint a függvény verzió létrehozott alkalmazásoknál 2.x verziójú futtatókörnyezet. Ez a cikk ismerteti a függvényalkalmazás konfigurálása az Azure-ban való futtatásához a kiválasztott verziótól. Egy adott verziót a helyi fejlesztési környezetet konfigurálásával kapcsolatos további információkért lásd: [kódolás és tesztelés az Azure Functions helyi](functions-run-local.md).

> [!NOTE]
> A futtatókörnyezet verziója, amely rendelkezik egy vagy több függvényt függvényalkalmazás nem módosítható. Futtatókörnyezet verziójának módosításához használjon az Azure Portalon.

## <a name="automatic-and-manual-version-updates"></a>Frissítések automatikus és manuális verzió

Funkciók lehetővé teszi a célként meghatározott verziójához a modul használatával a `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace v souboru függvényalkalmazást. A függvényalkalmazás másolatok megadott főverziója mindaddig, amíg explicit módon kívánja áthelyezni az új verzióra.

Ha csak a főverziót ("~ 2" a 2.x-es vagy a "~ 1" 1.x) adja meg, a függvényalkalmazás automatikusan frissül a futtatókörnyezet új alverziót, ha elérhetővé válnak. Új alverziót kompatibilitástörő változásokat vezet be. Ha megad egy Alverzió (például "2.0.12345"), a függvényalkalmazás rögzítve van, hogy adott verzió mindaddig, amíg explicit módon módosítja.

Ha új verzió érhető el nyilvánosan, a portál figyelmeztetést biztosít arra, hogy fel azt a verziót. Helyezze át az új verzióra, bármikor használhat a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás korábbi verzióinak helyreállításához.

Futtatókörnyezet verziójának módosítása hatására a függvényalkalmazás újraindítása.

Az értékek állítható be a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás lehetővé teszi az automatikus frissítések jelenleg "~ 1" 1.x futásidejű és a "~ 2" 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Megtekintése és módosítása az aktuális futtatókörnyezet-verzió

Módosíthatja az Ön által használt futtatókörnyezet verziójának függvényalkalmazást. A parancsban történt használhatatlanná tévő lehetséges, mert csak akkor módosítsa a futtatókörnyezet-verzió előtt létrehozott függvényeinek a függvényalkalmazásban. Bár a futtatókörnyezet verziója határozza meg a `FUNCTIONS_EXTENSION_VERSION` beállításnál kell a módosítás végrehajtása az Azure Portalon, és nem módosítja a beállítást a közvetlenül. Ez azért, mert a portál ellenőrzi a módosításokat, és más kapcsolódó módosítást hajt végre igény szerint.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Az Azure parancssori felületen

Akkor is megtekintheti, és állítsa be a `FUNCTIONS_EXTENSION_VERSION` az Azure parancssori felületen.

>[!NOTE]
>Egyéb beállítások hatással lehet a futtatókörnyezet-verzió, mert akkor kell megváltoztatnia a verzió a portálon. A portál automatikusan futtatókörnyezet-verzió változásakor lehetővé teszi az egyéb szükséges frissítések, például a Node.js verzió és a futtatókörnyezeti verem.  

Az Azure CLI használatával megtekintheti az aktuális futtatókörnyezet verziójának a [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le a `<function_app>` a függvényalkalmazás nevére. Továbbá cserélje le `<my_resource_group>` az erőforráscsoport a függvényalkalmazás nevére. 

Megjelenik a `FUNCTIONS_EXTENSION_VERSION` a következő kimenet, amely csonkolta az átláthatóság érdekében:

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

Frissítheti a `FUNCTIONS_EXTENSION_VERSION` a függvényalkalmazás a beállítás a [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Cserélje le `<function_app>` a függvényalkalmazás nevére. Továbbá cserélje le `<my_resource_group>` az erőforráscsoport a függvényalkalmazás nevére. Továbbá cserélje le `<version>` egy érvényes verziójú futtatókörnyezet 1.x vagy `~2` verzió 2.x.

Ez a parancs futtatható a [Azure Cloud Shell](../cloud-shell/overview.md) kiválasztásával **kipróbálás** a fenti kódmintában. Is használhatja a [Azure parancssori felület helyi](/cli/azure/install-azure-cli) végrehajtása után ez a parancs végrehajtásához [az bejelentkezési](/cli/azure/reference-index#az-login) való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A cél a 2.0-s modul az a helyi fejlesztési környezetbe](functions-run-local.md)

> [!div class="nextstepaction"]
> [Tekintse meg a futtatókörnyezet-verzió kibocsátási megjegyzései](https://github.com/Azure/azure-webjobs-sdk-script/releases)
