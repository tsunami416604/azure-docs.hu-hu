---
title: Az Azure Functions runtime verziók bemutatásához
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg, hogyan adja meg az Azure-ban futó függvényalkalmazást a futtatókörnyezet-verzióját.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886807"
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

A következő eljárással a függvényalkalmazás által jelenleg használt futtatókörnyezet verziójának megtekintéséhez.

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg a függvényalkalmazást.

1. A **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások függvény**.

    ![Válassza ki a függvényalkalmazás beállításai](./media/set-runtime-version/add-update-app-setting.png)

1. Az a **Alkalmazásbeállítások függvény** lapra, keresse meg a **verze modulu Runtime**. Vegye figyelembe az adott futtatókörnyezet-verzió és a kért főverziója. Az alábbi példában a verzió értéke `~2`.

   ![Válassza ki a függvényalkalmazás beállításai](./media/set-runtime-version/function-app-view-version.png)

1. A függvényalkalmazást, hogy a verzió 1.x futásidejű rögzít, válassza a **~ 1** alatt **verze modulu Runtime**. Ez a kapcsoló le van tiltva, ha az alkalmazás funkciói.

1. Ha módosította a futtatókörnyezet-verzió, lépjen vissza a **áttekintése** lapot, majd **indítsa újra a** , indítsa újra az alkalmazást.  A függvényalkalmazás újraindul, 1.x verzió modulban fut, és a verzió 1.x sablonok függvények létrehozásakor használt.

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Megtekintése és módosítása az Azure CLI-vel futtatókörnyezet verziója

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
