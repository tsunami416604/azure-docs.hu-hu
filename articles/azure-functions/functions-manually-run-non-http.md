---
title: Nem HTTP-által aktivált Azure-függvények manuális futtatása
description: NEM HTTP-vel aktivált Azure-függvények http-kérelem használata
author: craigshoemaker
ms.topic: article
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6571482d738549d2708fd8ab23eaf8c9f6fb1f70
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892359"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Nem HTTP által aktivált függvény manuális futtatása

Ez a cikk bemutatja, hogyan manuálisan futtatható egy nem HTTP-aktivált függvény speciálisan formázott HTTP-kérelem segítségével.

Bizonyos környezetekben előfordulhat, hogy futtatnia kell egy "igény szerinti" egy Azure-függvényt, amely közvetetten aktiválódik.  A közvetett eseményindítók közé tartoznak például [az ütemezésen alapuló függvények](./functions-create-scheduled-function.md) vagy függvények, amelyek [egy másik erőforrás műveletének](./functions-create-storage-blob-triggered-function.md)eredményeként futnak . 

[Postman](https://www.getpostman.com/) használják a következő példában, de használhatja [cURL](https://curl.haxx.se/), [Hegedűs](https://www.telerik.com/fiddler) vagy bármely más hasonló eszköz küldeni HTTP kéréseket.

## <a name="define-the-request-location"></a>A kérelem helyének meghatározása

Egy nem HTTP-aktivált függvény futtatásához kell egy módja annak, hogy küldjön egy kérést az Azure-nak a függvény futtatásához. A kérelem hez használt URL-cím egy adott formában jelenik meg.

![A kérelem helyének megadása: állomásnév + mappa elérési útja + függvénynév](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Állomás neve:** A függvényalkalmazás nyilvános helye, amely a függvényalkalmazás nevéből, valamint *azurewebsites.net* vagy az egyéni tartományból áll.
- **Mappa elérési útja:** Ahhoz, hogy http-kérelemmel férjen hozzá a nem HTTP-k által aktivált függvényekhez, a kérelmet az *admin/funkciók*mappáin keresztül kell elküldenie.
- **Függvény neve:** A futtatni kívánt függvény neve.

Használja ezt a kérési helyet a Postman együtt a függvény fő kulcsa az Azure-hoz a függvény futtatásához.

> [!NOTE]
> Helyi futtatás esetén a függvény főkulcsa nem szükséges. Közvetlenül `x-functions-key` [meghívhatja a fejlécet](#call-the-function) elhagyó függvényt.

## <a name="get-the-functions-master-key"></a>A függvény főkulcsának beszerezése

Keresse meg a függvényt az Azure Portalon, és kattintson a **Manage** and find the **Host Keys (Gazdakulcsok)** szakaszra. Kattintson a **másolás** gombra a *_master* sorban, hogy másolja a főkulcsot a vágólapra.

![Mesterkulcs másolása a Funkciókezelés képernyőről](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

A főkulcs másolása után kattintson a függvény nevére a kódfájl ablakába való visszatéréshez. Ezután kattintson a **Naplók** fülre. Látni fogja az üzeneteket a funkció bejelentkezett itt, amikor manuálisan fut a funkció postás.

> [!CAUTION]  
> A főkulcs által megadott emelt szintű engedélyek miatt ne ossza meg ezt a kulcsot harmadik felekkel, és ne ossza el azt egy alkalmazásban.

## <a name="call-the-function"></a>A függvény felhívása

Nyissa meg a Postman t, és kövesse az alábbi lépéseket:

1. Írja be a **kérelem helyét az URL-cím mezőbe.**
2. Győződjön meg arról, hogy a HTTP metódus **POST**.
3. **Kattintson** a **Fejlécek** fülre.
4. Írja be **az x-functions-key-t** első **kulcsként,** és illessze be a főkulcsot (a vágólapról) az **értékmezőbe.**
5. Írja be **a Content-Type értéket** második **kulcsként,** és írja be **az application/json** értéket **értékként.**

    ![Postás fejlécek beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Kattintson** a **Törzs** fülre.
7. Írja be **a { "input": "test" }** értéket a kérelem törzseként.

    ![Postás test beállításai](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Kattintson a **Küldés** gombra.

    ![Kérelem küldése a Postánál](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman majd jelentést állapotban **202 Elfogadott**.

Ezután térjen vissza a függvényhez az Azure Portalon. Keresse meg a *Naplók ablakot,* és látni fogja a funkció kézi hívásából érkező üzeneteket.

![A funkciónapló eredménye kézi hívásból](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>További lépések

- [Kódtesztelési stratégiák az Azure Functions szolgáltatásban](./functions-test-a-function.md)
- [Az Azure Függvény eseményrácsának helyi hibakeresési eseménye](./functions-debug-event-grid-trigger-local.md)
