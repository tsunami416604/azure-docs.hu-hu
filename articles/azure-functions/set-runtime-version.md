---
title: Az Azure Functions runtime verziók bemutatásához
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg, hogyan adja meg az Azure-ban futó függvényalkalmazást a futtatókörnyezet-verzióját.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 889a5a40409238462ee81d3bbd51ac6b77d28173
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947488"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions runtime verziók bemutatásához

Egy függvényalkalmazást az Azure Functions futtatókörnyezete egy adott verzióját futtatja. Két fő verziója: [1.x és a 2.x](functions-versions.md). Ez a cikk ismerteti a függvényalkalmazás konfigurálása az Azure-ban való futtatásához a kiválasztott verziótól. Egy adott verziót a helyi fejlesztési környezetet konfigurálásával kapcsolatos további információkért lásd: [kódolás és tesztelés az Azure Functions helyi](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Frissítések automatikus és manuális verzió

Funkciók lehetővé teszi a célként meghatározott verziójához a modul használatával a `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace v souboru függvényalkalmazást. A függvényalkalmazás másolatok megadott főverziója mindaddig, amíg explicit módon kívánja áthelyezni az új verzióra.

Ha csak a főverziót ("~ 2" a 2.x-es vagy a "~ 1" 1.x) adja meg, a függvényalkalmazás automatikusan frissül a futtatókörnyezet új alverziót, ha elérhetővé válnak. Új alverziót kompatibilitástörő változásokat vezet be. Ha megad egy Alverzió (például "2.0.12345"), a függvényalkalmazás másolatok verzió mindaddig, amíg explicit módon módosítja. 

Ha új verzió érhető el nyilvánosan, a portál figyelmeztetést biztosít arra, hogy fel azt a verziót. Helyezze át az új verzióra, bármikor használhat a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás korábbi verzióinak helyreállításához.

Futtatókörnyezet verziójának módosítása hatására a függvényalkalmazás újraindítása.

Az értékek állítható be a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás lehetővé teszi az automatikus frissítések jelenleg "~ 1" 1.x futásidejű és a "~ 2" 2.x.

## <a name="view-the-current-runtime-version"></a>Aktuális futtatókörnyezet verziójának megtekintése

A következő eljárással a függvényalkalmazás által jelenleg használt futtatókörnyezet verziójának megtekintéséhez. 

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a függvényalkalmazáshoz, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások függvény**. 

    ![Válassza ki a függvényalkalmazás beállításai](./media/functions-versions/add-update-app-setting.png)

2. Az a **Alkalmazásbeállítások függvény** lapra, keresse meg a **verze modulu Runtime**. Vegye figyelembe az adott futtatókörnyezet-verzió és a kért főverziója. Az alábbi példában a FUNCTIONS\_BŐVÍTMÉNY\_verzió Alkalmazásbeállítás értéke `~1`.
 
   ![Válassza ki a függvényalkalmazás beállításai](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Cél-verzió, a portál használatával

Ha szeretne egy verziója eltérő az aktuális főbb verzióhoz vagy 2.0-s verziója, beállítása a `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a függvényalkalmazáshoz, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások**.

    ![Válassza ki a függvényalkalmazás beállításai](./media/functions-versions/add-update-app-setting1a.png)

2. Az a **Alkalmazásbeállítások** lapra, keresse meg a `FUNCTIONS_EXTENSION_VERSION` beállítást, és módosítsa az értéket a futtatókörnyezet 1.x egy érvényes verziót vagy `~2` 2.0-s verzió. A főverzió tilde azt jelenti, hogy a főverzió (például "~ 1") legújabb verzióját használja. 

    ![A függvény Alkalmazásbeállítás beállítása](./media/functions-versions/add-update-app-setting2.png)

3. Kattintson a **mentése** menteni az alkalmazás-beállítás frissítése. 

## <a name="target-a-version-using-azure-cli"></a>A cél egy verziójú Azure parancssori felületével

 Is beállíthat a `FUNCTIONS_EXTENSION_VERSION` az Azure parancssori felületen. Az Alkalmazásbeállítás a függvényalkalmazáshoz az Azure CLI használatával frissítse a [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Ebben a kódban cserélje le a `<function_app>` a függvényalkalmazás nevére. Továbbá cserélje le `<my_resource_group>` az erőforráscsoport a függvényalkalmazás nevére. Cserélje le `<version>` egy érvényes verziójú futtatókörnyezet 1.x vagy `~2` verzió 2.x. 

Ez a parancs futtatható a [Azure Cloud Shell](../cloud-shell/overview.md) kiválasztásával **kipróbálás** a fenti kódmintában. Is használhatja a [Azure parancssori felület helyi](/cli/azure/install-azure-cli) végrehajtása után ez a parancs végrehajtásához [az bejelentkezési](/cli/azure/reference-index#az-login) való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A cél a 2.0-s modul az a helyi fejlesztési környezetbe](functions-run-local.md)

> [!div class="nextstepaction"]
> [Tekintse meg a futtatókörnyezet-verzió kibocsátási megjegyzései](https://github.com/Azure/azure-webjobs-sdk-script/releases)
