---
title: A tudásbázis fejlesztése - QnA Maker
description: Javítsa tudásbázisa minőségét aktív tanulással. Tekintse át, fogadja el vagy utasítsa el, adja hozzá a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071132"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktív tanulás használata a tudásbázis továbbfejlesztéséhez

[Az aktív tanulás](../Concepts/active-learning-suggestions.md) lehetővé teszi a tudásbázis minőségének javítását alternatív kérdések javaslatával. A felhasználói beküldött anyagokat figyelembe vesszük, és javaslatokként jelennek meg az alternatív kérdések listájában. Rugalmasan hozzáadhatja ezeket a javaslatokat alternatív kérdésként, vagy elutasíthatja azokat.

A tudásbázis nem változik automatikusan. Ahhoz, hogy bármilyen változás érvénybe lépjen, el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket vetnek fel, de nem módosítják és nem távolítják el a meglévő ket.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>A futásidejű verzió frissítése az aktív tanulás használatához

Az Active Learning a 4.4.0-s és újabb verziókban támogatott. Ha a tudásbázisegy korábbi verzión jött létre, [frissítse a futásidejűt](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) a szolgáltatás használatához.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Az aktív tanulás bekapcsolása alternatív kérdésekesetén

Az aktív tanulás alapértelmezés szerint ki van kapcsolva. Kapcsolja be a javasolt kérdések megtekintéséhez. Miután bekapcsolta az aktív tanulást, információkat kell küldenie az ügyfélalkalmazásból a QnA Makernek. További információ: [Építészeti folyamat a GenerateAnswer és a Betanítási API-k használatának robotból.](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. A Tudásbázis közzétételéhez válassza a **Közzététel** lehetőséget. Az aktív tanulási lekérdezések csak a GenerateAnswer API előrejelzési végpontból kerülnek összegyűjtésre. A QnA Maker portál tesztablaktáblájára leadott lekérdezések nincsenek hatással az aktív tanulásra.

1. Ha be szeretné kapcsolni az aktív tanulást a QnA Maker portálon, lépjen a jobb felső sarokba, válassza a **Nevét**, és nyissa meg a [**Szolgáltatás beállításai lehetőséget.**](https://www.qnamaker.ai/UserSettings)

    ![Kapcsolja be az aktív tanulás javasolt kérdés alternatíváit a Szolgáltatás beállításai oldalon. Válassza ki a felhasználónevét a jobb felső menüben, majd válassza a Szolgáltatás beállításai lehetőséget.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd váltsa be **az Active Learning**szolgáltatást.

    > [!div class="mx-imgBorder"]
    > [![A Szolgáltatás beállításai lapon váltson be az Active Learning szolgáltatásra. Ha nem tudja átváltani a funkciót, előfordulhat, hogy frissítenie kell a szolgáltatást.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Az előző kép pontos verziója csak példaként jelenik meg. Lehet, hogy a te verziód más.

    Ha az **Aktív tanulás** engedélyezve van, a tudásbázis rendszeres időközönként új kérdéseket javasol a felhasználó által beküldött kérdések alapján. Az **Active Learning** letiltásához tiltsa le a beállítást.

## <a name="review-suggested-alternate-questions"></a>A javasolt alternatív kérdések áttekintése

Tekintse át az [alternatív javasolt kérdéseket](improve-knowledge-base.md) az egyes tudásbázisok **Szerkesztés** lapján.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./manage-knowledge-bases.md)