---
title: Példány és hitelesítés beállítása (portál)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása a Azure Portal használatával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperfq
ms.openlocfilehash: 2dc19432d2185b95376717c570cd3165ebc85075
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027286"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure digitális Twins-példány és-hitelesítés beállítása (portál)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának** lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A cikk jelen verziója ezeket a lépéseket manuálisan, egyenként, a Azure Portal használatával hajtja végre. Az Azure Portal egy egységesített felületet biztosító webes konzol, amely alternatívát biztosít a parancssori eszközökkel szemben.
* Ha ezeket a lépéseket manuálisan szeretné használni a parancssori felületről, tekintse meg a jelen cikk CLI-verzióját: [*útmutató: példány és hitelesítés (CLI) beállítása*](how-to-set-up-instance-cli.md).
* Ha az üzembe helyezési parancsfájl mintájának használatával szeretne automatikus telepítést végezni, tekintse meg a jelen cikk parancsfájlokkal ellátott verzióját: [*útmutató: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Az Azure Digital Twins-példány létrehozása

Ebben a szakaszban az **Azure Digital Twins új példányát fogja létrehozni** a [Azure Portal](https://ms.portal.azure.com/)használatával. Lépjen a portálra, és jelentkezzen be a hitelesítő adataival.

A portálon először kattintson az _erőforrás létrehozása_ elemre az Azure-szolgáltatások Kezdőlap menüjében.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Keressen az *Azure Digital Twins* kifejezésre a keresőmezőbe, majd az eredmények közül válassza ki az **Azure Digital Twins (előzetes verzió)** szolgáltatást. Válassza a _Létrehozás_ gombot a szolgáltatás új példányának létrehozásához.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

A következő *Erőforrás-létrehozási* oldalon adja meg az alább megadott értékeket:
* **Előfizetés** : az Ön által használt Azure-előfizetés
  - **Erőforráscsoport** : az az erőforráscsoport, amelyben a példányt telepíteni kell. Ha még nem rendelkezik meglévő erőforráscsoporthoz, itt létrehozhat egyet az *új csoport létrehozása* hivatkozásra kattintva és egy új erőforráscsoport nevének megadásával.
* **Hely** : egy Azure digitális Twins-kompatibilis régió az üzembe helyezéshez. A regionális támogatással kapcsolatos további információkért látogasson el a [*régió (Azure Digital Twins) által elérhető Azure-termékek területére*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Erőforrás neve** : az Azure Digital Twins-példány neve. Az új példány nevének a régión belül egyedinek kell lennie az előfizetéshez (ami azt jelenti, hogy ha az előfizetés egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már használja a választott nevet), a rendszer kérni fogja, hogy válasszon másik nevet.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Ha elkészült, válassza a _felülvizsgálat + létrehozás_ elemet. Ekkor megjelenik egy összefoglaló oldal, ahol áttekintheti a megadott példányok részleteit, és elvégezheti a _létrehozást_ . 

### <a name="verify-success-and-collect-important-values"></a>Sikeres ellenőrzés és fontos értékek összegyűjtése

A *create (létrehozás* ) gombra kattintva megtekintheti a példány üzembe helyezésének állapotát az Azure-értesítésekben a portál ikon sávján keresztül. Az értesítés akkor jelenik meg, ha az üzembe helyezés sikeres volt, és a létrehozott példány megtekintéséhez válassza az _Ugrás az erőforráshoz_ gombot.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Ha az üzembe helyezés meghiúsul, az értesítésben az is látható, hogy miért. Figyelje meg a hibaüzenetben szereplő tanácsokat, és próbálja meg újra létrehozni a példányt.

>[!TIP]
>A példány létrehozása után bármikor visszatérhet a lapjához, ha a Azure Portal keresési sávban keresi a példány nevét.

A példány *Áttekintés* lapján jegyezze fel a *nevét* , az *erőforráscsoport* és az *állomásnév* nevét. Ezek mind olyan fontos értékek, amelyekre szüksége lehet az Azure Digital Twins-példánnyal folytatott munka folytatásához. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket meg kell osztani velük.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra. Ezután meg kell adnia a megfelelő Azure-felhasználói engedélyeket a kezeléséhez.

## <a name="set-up-user-access-permissions"></a>Felhasználói hozzáférési engedélyek beállítása

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Először nyissa meg az Azure Digital Twins-példány oldalát a Azure Portal. A példány menüjében válassza a *hozzáférés-vezérlés (iam)* lehetőséget. Válassza a  *Hozzáadás* gombot a *szerepkör-hozzárendelés hozzáadása* területen.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Az alábbi *szerepkör-hozzárendelés hozzáadása* lapon adja meg az értékeket (az Azure-előfizetéshez [megfelelő engedélyekkel](#prerequisites-permission-requirements) rendelkező felhasználónak kell elvégeznie):
* **Szerepkör** : válassza ki a legördülő menüből az *Azure digitális Twins-adatok tulajdonosa* lehetőséget
* **Hozzáférés kiosztása** : válassza ki az *Azure ad-felhasználó,-csoport vagy-szolgáltatásnév* elemet a legördülő menüből
* **Válassza** a következőt: keresse meg a hozzárendelni kívánt felhasználó nevét vagy e-mail-címét. Az eredmény kiválasztásakor a felhasználó megjelenik a *kiválasztott tagok* szakaszban.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ha végzett a részletek beírásával, nyomja meg a *Save (Mentés* ) gombot.

### <a name="verify-success"></a>Sikeres ellenőrzés

Megtekintheti a *hozzáférés-vezérlés (iam) > szerepkör-* hozzárendelések alatt beállított szerepkör-hozzárendelést. A felhasználónak az *Azure digitális Twins-adattulajdonosi* szerepkörrel kell megjelennie a listában. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra, és hozzárendelt engedélyekkel rendelkezik a kezeléséhez.

## <a name="next-steps"></a>Következő lépések

Tesztelje az egyes REST API hívásokat a példányon az Azure Digital Twins CLI parancsaival: 
* [az DT Reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Útmutató: az Azure digitális Twins parancssori felületének használata*](how-to-use-cli.md)

Vagy tekintse meg a következő témakört: ügyfélalkalmazás összekötése a példánnyal a hitelesítési kóddal:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)