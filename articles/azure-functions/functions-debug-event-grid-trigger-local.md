---
title: Az Azure Event Grid függvény helyi hibakeresése
description: Ismerje meg, hogyan helyileg az Azure-Event Grid-esemény által aktivált függvények hibakeresése
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: az Azure functions, függvények, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 96d88fafd6824ed85f1d91bab59374b3490a55b2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736498"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure-függvény Event Grid eseményindító helyi hibakeresés

Ez a cikk bemutatja, hogyan hibakeresése helyi függvény, amely kezeli az Azure Event Grid-esemény váltotta ki a tárfiókot. 

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre vagy használjon egy meglévő függvényalkalmazás
- Létrehozhat vagy használhat meglévő storage-fiókot
- Töltse le [ngrok](https://ngrok.com/) engedélyezni az Azure a helyi függvény meghívásához

## <a name="create-a-new-function"></a>Új függvény létrehozása

A Visual Studióban nyissa meg a függvényalkalmazást, és kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, és kattintson a **Hozzáadás > új Azure-függvény**.

Az a *új Azure-függvény* ablakban válassza **Event Grid-trigger** kattintson **OK**.

![Új függvény létrehozása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

A függvény létrehozása után nyissa meg a kódot fájlt, és a fájl elején megjegyzésként szerepel az URL-cím másolása. Ez a hely szolgál az Event Grid eseményindító konfigurálásakor.

![Másolat helye](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ezután állítson be egy töréspontot a sor kezdődő `log.LogInformation`.

![Töréspont beállítása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Ezután **nyomja le az F5** hibakeresési munkamenet indításához.

## <a name="allow-azure-to-call-your-local-function"></a>A helyi függvény hívásához Azure engedélyezése

A gépen replikaszámát függvény felosztása, engedélyeznie kell a oly módon, hogy kommunikálni a helyi függvény a felhőből az Azure.

A [ngrok](https://ngrok.com/) segédprogram lehetővé teszi az Azure-hoz a gépen futó függvény. Indítsa el *ngrok* a következő paranccsal:

```bash
ngrok http -host-header=localhost 7071
```
A segédprogram be van állítva, mert a parancssori ablakban az alábbi képernyőfelvételhez hasonlóan kell kinéznie:

![Indítsa el a ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Másolás a **HTTPS** URL-cím jön létre, mikor *ngrok* futtatása. Ez az érték használható az event grid esemény végpont konfigurálásakor.

## <a name="add-a-storage-event"></a>A storage esemény hozzáadása

Nyissa meg az Azure Portalt, és lépjen a tárfiókhoz, és kattintson a a **események** lehetőséget.

![Storage-fiók esemény hozzáadása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Az a *események* ablakban kattintson a a **esemény-előfizetés** gombra. Az a *még az előfizetés* ablakban kattintson a a *típusú végpont* legördülő listából válassza ki **Webhook**.

![Előfizetés-típus kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Miután konfigurálta a végpont típusa, kattintson a **válasszon végpontot** konfigurálása a végpont értékét.

![Válassza ki a végpont típusa](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

A *előfizető végpontja* érték kérés érkezett három különböző értékeket. Az előtag, az HTTPS URL-cím által generált *ngrok*. Az URL-cím része az URL-címet, a függvény nevével, végén hozzáadva a függvény kódját fájlban található származik. Az URL-címet, a függvény kódját fájlból, kezdve a *ngrok* URL-címet lecseréli `http://localhost:7071` és a függvény neve cserél `{functionname}`.

A következő képernyőképen látható, hogyan kell kinéznie a végső URL-címe:

![Végpont kiválasztása](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Miután megadta a megfelelő értéket, kattintson a **kijelölés megerősítéséhez**.

> [!IMPORTANT]
> Minden egyes indításakor *ngrok*, novému generování HTTPS URL-CÍMÉT, és az értéke módosul. Ezért, létre kell hoznia egy új esemény-előfizetés minden alkalommal, amikor elérhetővé teszi a funkció az Azure-bA keresztül *ngrok*.

## <a name="upload-a-file"></a>Fájl feltöltése

Most már feltöltheti egy fájlt a tárfiókhoz való kezeléséhez a helyi függvény egy Event Grid-esemény. 

Nyissa meg [Tártallózó](https://azure.microsoft.com/features/storage-explorer/) , és csatlakoztassa a storage-fiókjában. 

- Bontsa ki a **Blob-tárolók** 
- Kattintson a jobb gombbal, és válassza ki **Blobtároló létrehozása**.
- A tároló neve **tesztelése**
- Válassza ki a *tesztelése* tároló
- Kattintson a **feltöltése** gomb
- Kattintson a **fájlok feltöltése**
- Válasszon ki egy fájlt, és töltse fel a blob-tároló

## <a name="debug-the-function"></a>A függvény hibakeresése

Miután az Event Grid felismeri, hogy egy új fájlt töltenek fel a storage-tárolót, a helyi függvényben elérte a töréspontot.

![Indítsa el a ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen cikkben létrehozott erőforrások törléséhez törölje a **tesztelése** a storage-fiókban lévő tárolóba.

## <a name="next-steps"></a>További lépések

- [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid-trigger az Azure Functions szolgáltatáshoz](./functions-bindings-event-grid.md)
