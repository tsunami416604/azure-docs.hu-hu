---
title: Egy nem HTTP által aktivált Azure Functions manuális futtatása
description: Használata egy HTTP-kérelem futtatásához egy nem HTTP által aktivált Azure Functions
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: eba0901561b7c1455b7b06849e1773629d9d357f
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388023"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Egy nem HTTP által aktivált függvény manuális futtatása

Ez a cikk bemutatja, hogyan futtathatja manuálisan egy nem HTTP által aktivált függvény speciálisan formázott HTTP-kérelem használatával.

Bizonyos környezetekben, szükség lehet "igény" futtathat egy Azure-függvény, amely a közvetett módon aktiválódik.  Például közvetett eseményindítók [ütemezés szerint funkciók](./functions-create-scheduled-function.md) eseményeit és körülményeit rögzítő eredményeképp futtatott [egy másik erőforrás-művelet](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) szerepel a következő példában, de használhatja [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) vagy bármely más hasonló eszköz a HTTP-kérelmeket küldjön.

## <a name="define-the-request-location"></a>Határozza meg a kérelem helyet

Nem HTTP-eseményindítóval aktivált függvény futtatására oly módon, egy kérelem küldése az Azure-ba való futtatásához szükséges a függvényt. Az URL-címet, hogy a kérelem egy adott űrlapon vesz igénybe.

![Kérelem helyének meghatározásához: gazdagép neve + a mappa elérési útja + a függvény neve](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Állomásnév:** A függvényalkalmazás nyilvános helyre a függvényalkalmazás neve plusz végzett navigáció *azurewebsites.net* vagy az egyéni tartomány.
- **Mappa elérési útja:** A kérelem elküldéséhez a mappák között rendelkezik via HTTP-kérést nem HTTP által aktivált függvények eléréséhez *rendszergazdai a functions*.
- **Függvény neve:** A futtatni kívánt függvény neve.

A kérelem hely együtt főkulcs a függvény az Azure-bA a kérelem Postman használatával futtassa a funkciót.

## <a name="get-the-functions-master-key"></a>A funkció fő kulcs lekérése

Keresse meg a függvényt az Azure Portalon, majd kattintson a **kezelés** , és keresse meg a **Gazdakulcsok** szakaszban. Kattintson a **másolási** gombra a *_master* sort, hogy a fő kulcsot másolja a vágólapra.

![Másolja a főkulcs függvény felügyeleti képernyőről](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Miután átmásolta a fő kulcsot, kattintson a függvény nevét, térjen vissza a kód ablak. Ezután kattintson a **naplók** fülre. Látni fogja a függvény ide kerül, ha manuálisan futtatja a függvény a Postman üzeneteit.

## <a name="call-the-function"></a>A függvény meghívása

Nyissa meg a Postmant, és kövesse az alábbi lépéseket:

1. Adja meg a **kérnek az URL-címe szövegmezőbe helyet**. 
2. **Kattintson a** a a **fejlécek** fülre.
3. Adja meg **x-functions-key** az első is **kulcs** , és illessze be a master key (a vágólapról) a **érték** mezőbe.
4. Adja meg **Content-Type** , a második **kulcs** , és adja meg **application/json** , a **érték**.

    ![Postman-fejlécek beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

5. **Kattintson a** a a **törzs** fülre.
6. Adja meg **{"bemeneti": "teszt"}** a kérelem törzsében.

    ![Postman-törzs beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

7. Kattintson a **küldése**.

    ![A postman használatával egy kérelem küldése](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman majd jelenti az állapotot **202-es elfogadva**.

Ezután térjen vissza a függvény az Azure Portalon. Keresse meg a *naplók* ablakot, és megjelenik a manuális meghívja a függvényt a beérkező üzeneteket.

![Függvény napló manuális hívás eredménye](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>További lépések

- [A kódot tesztelés az Azure Functions stratégiák](./functions-test-a-function.md)
- [Azure-függvény Event Grid eseményindító helyi hibakeresés](./functions-debug-event-grid-trigger-local.md)