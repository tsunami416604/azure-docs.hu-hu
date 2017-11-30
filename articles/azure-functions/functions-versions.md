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
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 588437af80ecf60b7c4b24dbf6bccc67fc33da7a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions futásidejű verziók bemutatásához

Egy függvény alkalmazást az Azure Functions futtatókörnyezettel egy adott verzión fut. Két fő verziója: 1.x és a 2.x. Ez a cikk azt ismerteti, hogyan kiválaszthatja, mely főverzió használatára és a függvény alkalmazások konfigurálása az Azure-verzió futtathatnak válassza. Egy helyi fejlesztési környezet egy adott verziójához konfigurálásával kapcsolatos további információkért lásd: [kódot és az Azure Functions tesztelése helyileg](functions-run-local.md).

## <a name="differences-between-runtime-1x-and-2x"></a>Futásidejű közötti különbségek 1.x és a 2.x

> [!IMPORTANT] 
> Futásidejű 1.x az üzemi használatra jóváhagyott csak verziója.

| Futásidejű | status |
|---------|---------|
|1.x|Általában elérhető (GA)|
|2.x|Előzetes verzió|

Az alábbi szakaszok ismertetik a nyelveket, kötéseit és platformfüggetlen fejlesztésekhez támogatás különbségeit.

### <a name="languages"></a>Nyelvek

A következő táblázat minden egyes futásidejű verziójában támogatott programozási nyelveket.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információkért lásd: [támogatott nyelv](supported-languages.md).

### <a name="bindings"></a>Kötések 

Futásidejű 2.x készíthet egyéni [bővítmények kötés](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). A bővítési modellt használó beépített kötések csak találhatók 2.x; az első között van a [Microsoft Graph kötések](functions-bindings-microsoft-graph.md).

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Kötések támogatási és egyéb működési hiányosságait 2.x kapcsolatban további információkért lásd: [ismert problémák futtatókörnyezet 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

### <a name="cross-platform-development"></a>Platformfüggetlen fejlesztésekhez

Csak a portál vagy a Windows; futásidejű 1.x támogatja függvény fejlesztési a 2.x is létrehozhat és az Azure Functions Linux vagy macOS futtatni.

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

## <a name="target-the-version-20-runtime"></a>A 2.0-s verziójának futásidejű cél

>[!IMPORTANT]   
> Az Azure Functions futtatókörnyezettel 2.0 előzetes, és jelenleg nem minden Azure Functions támogatja. További információkért lásd: [futásidejű közötti különbségek 1.x és a 2.x](#differences-between-runtime-1x-and-2x) korábbi ebben a cikkben.

Egy függvény app áthelyezheti a futtatókörnyezet 2.0-s verziójának előzetes az Azure portálon. Az a **Alkalmazásbeállítások működéséhez** lapra, majd **beta** alatt **futásidejű verzióját**.  

![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/function-app-view-version.png)

Ez a beállítás megegyezik a beállítás a `FUNCTIONS_EXTENSION_VERSION` az Alkalmazásbeállítás `beta`. Válassza ki a **~ 1** gombra kattintva helyezze vissza a jelenlegi nyilvánosan főverzió támogatott. Az Azure parancssori felület használatával frissítse az alkalmazás-beállítás. 

## <a name="target-a-version-using-the-portal"></a>Célként egy olyan verzióra, a portál használatával

Ha egy eltérő az aktuális főverzió verzió vagy 2.0-s verziója van szüksége, meg kell adni a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a függvény alkalmazásba, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások**.

    ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/add-update-app-setting1a.png)

2. Az a **Alkalmazásbeállítások** lap, keresse meg a `FUNCTIONS_EXTENSION_VERSION` beállítást és módosítsa az értéket egy érvényes 1.x futtatókörnyezet-verzió vagy `beta` 2.0-s verzió. 

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A helyi fejlesztési környezetben 2.0 futásidejű cél](functions-run-local.md)

> [!div class="nextstepaction"]
> [Tekintse meg a kibocsátási megjegyzések a futtatókörnyezet-verzió](https://github.com/Azure/azure-webjobs-sdk-script/releases)