---
title: Az Azure Functions eseményrács helyi hibakeresése
description: Ismerje meg, hogyan lehet helyileg hibakeresést végzett az Event Grid-esemény által kiváltott Azure-függvények között
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227068"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Az Azure Függvény eseményrácsának helyi hibakeresési eseménye

Ez a cikk bemutatja, hogyan hibakeresést egy helyi függvény, amely kezeli az Azure Event Grid-esemény egy tárfiók által kiváltott. 

## <a name="prerequisites"></a>Előfeltételek

- Meglévő függvényalkalmazás létrehozása vagy használata
- Meglévő tárfiók létrehozása vagy használata
- Töltse le [a ngrok-ot,](https://ngrok.com/) hogy az Azure meghívja a helyi függvényt

## <a name="create-a-new-function"></a>Új függvény létrehozása

Nyissa meg a függvényalkalmazást a Visual Studióban, és kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, és válassza **> Új Azure-függvény hozzáadása parancsra.**

Az *Új Azure-függvény* ablakban válassza az **Eseményrács eseményindítója** lehetőséget, és kattintson az **OK**gombra.

![Új függvény létrehozása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

A funkció létrehozása után nyissa meg a kódfájlt, és másolja a fájl tetejére megjegyzéssel elmondott URL-címet. Ez a hely az Eseményrács eseményindítójának konfigurálásakor használatos.

![Hely másolása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ezután állítson be egy töréspontot `log.LogInformation`a .

![Töréspont beállítása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Ezután **nyomja le az F5 billentyűt** a hibakeresési munkamenet elindításához.

## <a name="allow-azure-to-call-your-local-function"></a>A helyi függvény hívásának engedélyezése az Azure-ban

Ahhoz, hogy betörjön egy funkció hibakeresés a gépen, engedélyeznie kell egy módja az Azure kommunikálni a helyi függvénya felhőből.

A [ngrok](https://ngrok.com/) segédprogram lehetővé teszi, hogy az Azure meghívja a számítógépen futó függvényt. Indítsa el a *ngrok* parancsot a következő paranccsal:

```bash
ngrok http -host-header=localhost 7071
```
A segédprogram beállításakor a parancsablaknak a következő képernyőképhez hasonlóan kell kinéznie:

![Kezdés ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Másolja a *ngrok* futtatásakor létrehozott HTTPS-URL-címet. **HTTPS** Ez az érték az eseményrács eseményvégpontjának konfigurálásakor használatos.

## <a name="add-a-storage-event"></a>Tárolási esemény hozzáadása

Nyissa meg az Azure Portalon, és keresse meg a tárfiókot, és kattintson az **Események** opcióra.

![Tárfiók-esemény hozzáadása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Az *Események* ablakban kattintson az **Esemény-előfizetés** gombra. A *Páros előfizetés* ablakban kattintson a *Végpont típusa* legördülő menüre, és válassza a **Web Hook**lehetőséget.

![Előfizetéstípus kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

A végponttípus konfigurálása után kattintson a **Végpont kiválasztása** gombra a végpont értékének konfigurálásához.

![Végponttípus kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Az *előfizetői végpont* értéke három különböző értékből áll. Az előtag az *ngrok*által létrehozott HTTPS URL. Az URL többi része a függvénykódfájlban található URL-címből származik, amelynek végén a függvény neve is megjelenik. A függvénykódfájl URL-címével kezdve a *ngrok* URL-címe lecseréli, `http://localhost:7071` és a függvény neve lecseréli a programot. `{functionname}`

A következő képernyőkép bemutatja, hogyan kell kinéznie a végső URL-nek:

![Végpont kijelölése](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Miután megadta a megfelelő értéket, kattintson a **Kijelölés megerősítése**gombra.

> [!IMPORTANT]
> Minden alkalommal, amikor elindítja *a ngrok-ot,* a HTTPS URL-címe újragenerálódik, és az érték megváltozik. Ezért létre kell hoznia egy új esemény-előfizetést minden alkalommal, amikor a függvényt elérhetővé teszi az *Azure-ban a ngrok*segítségével.

## <a name="upload-a-file"></a>Fájl feltöltése

Most már feltölthet egy fájlt a tárfiókba, hogy eseményrács-eseményt indítson el a helyi funkció számára. 

Nyissa [meg a Storage Explorer alkalmazást,](https://azure.microsoft.com/features/storage-explorer/) és csatlakozzon a tárfiókhoz. 

- **Blob-tárolók kibontása** 
- Kattintson a jobb gombbal, és válassza **a Blob Container létrehozása parancsot.**
- A **tárolóteszt elnevezése**
- Válassza ki a *teszttárolót*
- Kattintson a **Feltöltés** gombra
- Kattintson **a Fájlok feltöltése gombra**
- Jelöljön ki egy fájlt, és töltse fel a blobtárolóba

## <a name="debug-the-function"></a>Hibakeresés a függvényben

Miután az Event Grid felismeri, hogy egy új fájlt tölt fel a tárolóba, a töréspont a helyi függvényben lesz lesújt.

![Kezdés ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikkben létrehozott erőforrások karbantartásához törölje a **teszttárolót** a tárfiókban.

## <a name="next-steps"></a>További lépések

- [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Eseményrács eseményindítója az Azure Functionshez](./functions-bindings-event-grid.md)
