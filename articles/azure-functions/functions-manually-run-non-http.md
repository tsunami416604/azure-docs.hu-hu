---
title: Nem HTTP-triggerű Azure Functions manuális futtatása
description: HTTP-kérelem használata nem HTTP-alapú aktivált Azure Functions futtatásához
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 8198ff6579aff839ff9aacb729e2f3f8d3472fae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230479"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Nem HTTP által aktivált függvény manuális futtatása

Ez a cikk bemutatja, hogyan futtathat manuálisan egy nem HTTP-triggert futtató függvényt a speciálisan formázott HTTP-kérelem használatával.

Bizonyos kontextusokban előfordulhat, hogy "igény szerinti" műveletet kell futtatnia egy közvetve aktivált Azure-függvényhez.  A közvetett eseményindítók például egy [olyan függvényt](./functions-create-scheduled-function.md) tartalmaznak, amely egy [másik erőforrás műveletének](./functions-create-storage-blob-triggered-function.md)eredményeképpen futó ütemezett vagy függvényeket tartalmaz. 

A [Poster](https://www.getpostman.com/) a következő példában szerepel, de a [curl](https://curl.haxx.se/), a [Hegedűs](https://www.telerik.com/fiddler) vagy más hasonló eszköz is használható a HTTP-kérelmek küldéséhez.

## <a name="define-the-request-location"></a>A kérelem helyének megadása

A nem HTTP-triggert futtató függvények futtatásához el kell küldenie egy kérést az Azure-nak a függvény futtatásához. A kérelem elvégzéséhez használt URL-cím egy adott űrlapot használ.

![A kérelem helyének meghatározása: állomásnév + mappa elérési útja + függvény neve](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Állomásnév:** A Function alkalmazás nyilvános helye, amely a Function alkalmazás nevéből és *azurewebsites.net* , illetve az egyéni tartományból is elérhető.
- **Mappa elérési útja:** Ha HTTP-kéréssel kívánja elérni a HTTP-triggert, akkor a kérést a mappák *adminisztrátora/függvényei*között kell elküldeni.
- **Függvény neve:** A futtatni kívánt függvény neve.

Ezt a kérést a Poster-ben, a függvény főkulcsával együtt használhatja az Azure-ba a függvény futtatásához.

> [!NOTE]
> Helyileg futtatva a függvény főkulcsa nem szükséges. Közvetlenül [hívhatja a](#call-the-function) `x-functions-key` fejlécet kihagyó függvényt.

## <a name="get-the-functions-master-key"></a>A függvény főkulcsának beolvasása

Keresse meg a függvényt a Azure Portalban, majd kattintson a **Manage (kezelés** ) gombra, és keresse meg a **gazdagép kulcsai** szakaszt. Az *_master* sorban található **Másolás** gombra kattintva másolja a főkulcsot a vágólapra.

![Főkulcs másolása a Function Management képernyőről](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

A főkulcs másolása után a függvény nevére kattintva térjen vissza a kódlap ablakába. Ezután kattintson a **naplók** fülre. Az itt bejelentkezett függvény üzeneteit akkor láthatja, amikor manuálisan futtatja a függvényt a Poster szolgáltatásból.

> [!CAUTION]  
> A főkulcs által biztosított Function app emelt szintű engedélyei miatt ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt egy alkalmazásban.

## <a name="call-the-function"></a>A függvény meghívása

Nyissa meg a Poster-t, és kövesse az alábbi lépéseket:

1. Adja meg a **kérelem helyét az URL-cím**szövegmezőben.
2. Győződjön meg arról, hogy a HTTP-metódus a **post**értékre van beállítva.
3. **Kattintson** a **fejlécek** fülre.
4. Adja meg az **x-functions-Key** értéket az első **kulcsként** , és illessze be a főkulcsot (a vágólapról) az **érték** mezőbe.
5. Adja meg a **Content-Type** **karakterláncot**második **kulcsként** , és adja meg az **Application/JSON** értéket.

    ![Poster-fejlécek beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Kattintson** a **törzs** fülre.
7. Adja meg a (z) **{"input": "test"}** értéket a kérelem törzse.

    ![Poster-törzs beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Kattintson a **Küldés**gombra.

    ![Poster-kérelem küldése](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

A Poster ekkor a 202-es állapotot **fogadja el**.

Ezután térjen vissza a függvényhez a Azure Portal. Keresse meg a *naplók* ablakot, és megtekintheti a függvény manuális hívásával érkező üzeneteket.

![Függvények naplójának eredményei a kézi hívásból](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Következő lépések

- [Stratégiák a kód teszteléséhez Azure Functions](./functions-test-a-function.md)
- [Az Azure Function Event Grid helyi hibakeresést indít](./functions-debug-event-grid-trigger-local.md)
