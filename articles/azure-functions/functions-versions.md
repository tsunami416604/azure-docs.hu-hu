---
title: "Az Azure Functions futásidejű verziók bemutatásához |} Microsoft Docs"
description: "Funkciók a futtatókörnyezet több verzióit támogatja. Útmutató: Adja meg az Azure szolgáltatásban futtatott futásidejű verzióját függvény alkalmazást."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Az Azure Functions futásidejű verziók bemutatásához

Az Azure Functions futtatókörnyezettel a kiszolgáló nélküli egy kód végrehajtásának megvalósítja az Azure-ban. A futásidejű található, mint más Azure-ban üzemeltetett különböző környezetekben. A [Azure Functions Core eszközök](functions-run-local.md) valósítja meg a futtatókörnyezet a fejlesztési számítógépen. [Az Azure Functions Futtatókörnyezettel](functions-runtime-overview.md) lehetővé teszi a funkciók használata a helyszíni környezetben. 

Funkciók a futtatókörnyezet több fő verzióit támogatja. Jelentős változásokat főverzió-frissítés vethet fel. Ez a témakör ismerteti, hogyan egy adott futásidejű verzióját az Azure-ban, ha a függvény alkalmazások célba. 

Funkciók lehetővé teszi, hogy egy adott főverzió futásidejű cél használatával a `FUNCTIONS_EXTENSION_VERSION` az függvény alkalmazás alkalmazás-beállítás. Ez vonatkozik mindkét nyilvános előzetes verziók és. Amíg a felhasználó explicit módon új verziójára történő áthelyezése a függvény app másolatok a megadott fő futásidejű verzióját. Ön függvény app a futtatókörnyezet új alverziót frissülni fog, amikor azok elérhetővé válnak. Alverzió frissítések nem, akkor jelentős változásokat.  

Nyilvánosan elérhető új főverzió esetén lehetősége van arra, hogy feljebb, az adott verzióra, a függvény alkalmazást a portál megtekintésekor. Helyezze át egy új verzióra, mindig használhatja a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás helyezhetők vissza egy korábbi futásidejű verzióját.

A futásidejű verzióját minden módosítás hatására a függvény alkalmazásnak, hogy indítsa újra. A közzétett összes futásidejű kiadásokban (a fő és) kibocsátási megjegyzések a [GitHub-tárházban](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Az aktuális futásidejű verzió megtekintése

A következő eljárás segítségével megtekintheti az adott futásidejű verzióját, a függvény alkalmazás által jelenleg használt. 

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a függvény alkalmazásba, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások működéséhez**. 

    ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/add-update-app-setting.png)

2. Az a **Alkalmazásbeállítások működéséhez** lapján keresse meg a **futásidejű verzióját**. Megjegyzés: a megadott futásidejű verzióját és a kért főverzió. Az alábbi példában a főverzió értéke `~1.0`.
 
   ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>A funkciók 2.0-s verziójának futásidejű cél

>[!IMPORTANT]   
> Az Azure Functions futtatókörnyezettel 2.0 előzetes, és jelenleg nem minden Azure Functions támogatja. További információkért lásd: [Azure Functions futtatókörnyezet 2.0 ismert problémák](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

A függvény app áthelyezheti a futtatókörnyezet 2.0-s verziójának előzetes az Azure portálon. Az a **Alkalmazásbeállítások működéséhez** lapra, majd **beta** alatt **futásidejű verzióját**.  

   ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/function-app-view-version.png)

Ez a beállítás megegyezik a beállítás a `FUNCTIONS_EXTENSION_VERSION` az Alkalmazásbeállítás `beta`. Válassza ki a **~ 1** gombra kattintva helyezze vissza a jelenlegi nyilvánosan főverzió támogatott. Az Azure parancssori felület használatával frissítse az alkalmazás-beállítás. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>A cél egy adott futásidejű verzió a portálról

Ha célként egy főverzió eltérő az aktuális jelentős kell vagy 2.0-s verzióját, meg kell adni a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a függvény alkalmazásba, majd a **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások**.

    ![Válassza ki a függvény Alkalmazásbeállítások](./media/functions-versions/add-update-app-setting1a.png)

2. Az a **Alkalmazásbeállítások** lap, keresse meg a `FUNCTIONS_EXTENSION_VERSION` beállítást és módosítsa az értéket egy érvényes főverzió 1.x futásidejű vagy `beta` 2.0-s verzió. 

    ![A függvény alkalmazás beállítása](./media/functions-versions/add-update-app-setting2.png)

3. Kattintson a **mentése** menteni az alkalmazás frissítését. 

## <a name="target-a-specific-version-using-azure-cli"></a>Egy adott verziójához Azure parancssori felület használatával

 Azt is beállíthatja a `FUNCTIONS_EXTENSION_VERSION` az Azure parancssori felületen. Az Azure parancssori felület használatával frissítse az alkalmazás-beállítás, a függvény alkalmazás a [az functionapp appsettings konfiguráció](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Cserélje le ezt a kódot `<function_app>` nevű, a függvény alkalmazást. Cserélni `<my_resource_group>` függvény alkalmazás az erőforráscsoport nevét. Cserélje le `<version>` 1.x futásidejű érvényes fő verziójával vagy `beta` 2.0-s verzió. 

Ez a parancs futtatása a [Azure Cloud rendszerhéj](../cloud-shell/overview.md) kiválasztásával **kipróbálás** az előző példakódban. Használhatja a [helyileg Azure CLI](/cli/azure/install-azure-cli) végrehajtása után ez a parancs végrehajtásához [az bejelentkezési](/cli/azure#az_login) való bejelentkezéshez.
