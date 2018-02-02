---
title: "Az Azure Functions futásidejű verziók bemutatásához"
description: "Az Azure Functions a futtatókörnyezet több verzióit támogatja. Megtudhatja, hogyan adja meg egy Azure-ban üzemeltetett függvény app futásidejű verzióját."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: c87455f43c6e580c52b3f772a1cbe0c1ac9c9f5b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions futásidejű verziók bemutatásához

Egy függvény alkalmazást az Azure Functions futtatókörnyezettel egy adott verzión fut. Két fő verziója: [1.x és a 2.x](functions-versions.md). Ez a cikk ismerteti a függvény alkalmazások konfigurálása az Azure-futtatásához a kiválasztott verziótól. Egy helyi fejlesztési környezet egy adott verziójához konfigurálásával kapcsolatos további információkért lásd: [kódot és az Azure Functions tesztelése helyileg](functions-run-local.md).

>[!IMPORTANT]   
> Az Azure Functions futtatókörnyezettel 2.0 előzetes, és jelenleg nem minden Azure Functions támogatja. További információkért lásd: [Azure Functions futásidejű verziók áttekintése](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Frissítések automatikus és manuális verziója

Funkciók lehetővé teszi, hogy a futtatókörnyezet egy adott verziójához a cél a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás függvény alkalmazásban. A függvény app másolatok megadott főverziója amíg explicit módon áthelyezése új verziója.

Ha csak a főverziót ("~ 1" 1.x) vagy a "béta" 2.x, adja meg, a függvény alkalmazás automatikusan a futtatókörnyezet új alverziót amikor azok elérhetővé válnak. Új alverziót vezet be jelentős változásokat. Ha megad egy Alverzió (például "1.0.11360"), a függvény app másolatok verzió addig explicit módon. 

Ha új verzió nyilvánosan elérhető, a portálon megjelenő lehetővé teszi az azonnali mozgatása felfelé a verzió. Helyezze át egy új verzióra, mindig használhatja a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás helyezhetők vissza egy korábbi verzióját.

Módosítva lett a futásidejű verzióját egy függvény alkalmazás újraindítását eredményezi.

Az értékeket állíthatja be a `FUNCTIONS_EXTENSION_VERSION` alkalmazás lehetővé teszi az automatikus frissítések beállítása jelenleg "~ 1" 1.x futásidejű és 2.x "béta".

## <a name="view-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése

A következő eljárással megtekintheti egy függvény alkalmazás által jelenleg használt futásidejű verzió. 

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a függvény alkalmazásba, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások működéséhez**. 

    ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/add-update-app-setting.png)

2. Az a **Alkalmazásbeállítások működéséhez** lapján keresse meg a **futásidejű verzióját**. Megjegyzés: a megadott futásidejű verzióját és a kért főverzió. Az alábbi példában a funkciók\_BŐVÍTMÉNY\_verzió Alkalmazásbeállítás értéke `~1`.
 
   ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Célként egy olyan verzióra, a portál használatával

Amikor egy verziója eltérő az aktuális főverzió vagy 2.0-s verziója van szüksége, állítsa be a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a függvény alkalmazásba, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások**.

    ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/add-update-app-setting1a.png)

2. Az a **Alkalmazásbeállítások** lap, keresse meg a `FUNCTIONS_EXTENSION_VERSION` beállítást és módosítsa az értéket egy érvényes 1.x futtatókörnyezet-verzió vagy `beta` 2.0-s verzió. A főverzió tildével azt jelenti, hogy az adott főverziója (például "~ 1") legújabb verzióját használja. 

    ![A függvény alkalmazás beállítása](./media/functions-versions/add-update-app-setting2.png)

3. Kattintson a **mentése** menteni az alkalmazás frissítését. 

## <a name="target-a-version-using-azure-cli"></a>Célként egy olyan verzióra, Azure parancssori felület használatával

 Azt is beállíthatja a `FUNCTIONS_EXTENSION_VERSION` az Azure parancssori felületen. Az Azure parancssori felület használatával frissítse az alkalmazás-beállítás, a függvény alkalmazás a [az functionapp appsettings konfiguráció](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Cserélje le ezt a kódot `<function_app>` nevű, a függvény alkalmazást. Cserélni `<my_resource_group>` függvény alkalmazás az erőforráscsoport nevét. Cserélje le `<version>` 1.x futásidejű érvényes verziójával vagy `beta` 2.0-s verzió. 

Ez a parancs futtatása a [Azure Cloud rendszerhéj](../cloud-shell/overview.md) kiválasztásával **kipróbálás** az előző példakódban. Használhatja a [helyileg Azure CLI](/cli/azure/install-azure-cli) végrehajtása után ez a parancs végrehajtásához [az bejelentkezési](/cli/azure#az_login) való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyi fejlesztési környezetben 2.0 futásidejű cél](functions-run-local.md)

> [!div class="nextstepaction"]
> [Tekintse meg a kibocsátási megjegyzések a futtatókörnyezet-verzió](https://github.com/Azure/azure-webjobs-sdk-script/releases)