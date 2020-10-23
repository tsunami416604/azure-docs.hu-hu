---
title: Azure kommunikációs szolgáltatások – webes hívás mintája
titleSuffix: An Azure Communication Services sample
description: A kommunikációs szolgáltatások webes hívási mintájának ismertetése
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6fc3441fac5fe037e9a268d26012761d1fece70
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463255"
---
# <a name="get-started-with-the-web-calling-sample"></a>Ismerkedés a webes hívási mintával

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Ez a minta a githubon érhető el.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Az Azure kommunikációs szolgáltatások **webes hívási mintája** azt mutatja be, hogy a kommunikációs szolgáltatások hogyan hívhatják meg az ügyféloldali kódtárat a JavaScripttel való telefonálási élmény létrehozásához.

Ebből a rövid útmutatóból megtudhatja, hogyan működik a minta a minta helyi gépen való futtatása előtt. Ezután üzembe helyezzük a mintát az Azure-ban a saját Azure kommunikációs szolgáltatások erőforrásaival.

## <a name="overview"></a>Áttekintés

A web Calling Sample egy webalkalmazás, amely a kommunikációs szolgáltatások webes hívási ügyféloldali kódtár által nyújtott különböző képességek lépésenkénti bemutatója. 

Ez a minta fejlesztők számára készült, és nagyon egyszerűvé teszi a kommunikációs szolgáltatások megkezdését. A felhasználói felülete több szakaszra oszlik, amelyek mindegyike egy "show code" (kód megjelenítése) gombot tartalmaz, amely lehetővé teszi, hogy közvetlenül a böngészőjéből másoljon kódot a saját kommunikációs szolgáltatások alkalmazásba.

Ha a [web Calling minta](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) a gépen fut, a következő Kezdőlap jelenik meg:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>A felhasználók üzembe helyezése és az SDK inicializálása 

A bemutató használatának megkezdéséhez adja meg a kapcsolati karakterláncot a [kommunikációs szolgáltatások erőforrásaiból](../quickstarts/create-communication-resource.md) a alkalmazásba `config.json` . Ez egy [felhasználói hozzáférési jogkivonat](../concepts/authentication.md) kiépítésére szolgál, hogy a hívó SDK inicializálható legyen.

Adja meg a saját személyes azonosítóját a felhasználói identitás bemenetében. Ha itt semmi sincs megadva, akkor a rendszer véletlenszerű felhasználói identitást hoz létre. 

Kattintson a "kiépítési felhasználó és az SDK inicializálása" elemre az SDK inicializálásához a háttér-jogkivonat kiépítési szolgáltatása által kiépített jogkivonat használatával. Ez a háttér-szolgáltatás a-ben érhető el `/project/webpack.config.js` .

Kattintson a "kód megjelenítése" gombra a saját megoldásában használható mintakód megtekintéséhez.

Az SDK inicializálása után a következőket kell látnia:

:::image type="content" source="./media/user-provisioning.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/user-provisioning.png":::

Most már készen áll arra, hogy megkezdje a hívások küldését a kommunikációs szolgáltatások erőforrásaival!

## <a name="placing-and-receiving-calls"></a>Hívások elhelyezése és fogadása

A kommunikációs szolgáltatások web Calling SDK lehetővé teszi a **1:1**, **1: N**és a **csoportos** hívás használatát.

1:1 vagy 1: N kimenő hívások esetén több kommunikációs szolgáltatás felhasználói identitást is megadhat a vesszővel tagolt értékek használatával történő híváshoz. Megadhat hagyományos (PSTN-) telefonszámokat is, amelyeket vesszővel tagolt értékek használatával hívhat meg. 

A PSTN-telefonszámok hívásakor adja meg az alternatív hívó AZONOSÍTÓját. A kimenő hívások elhelyezéséhez kattintson a hívás elhelyezése gombra:

:::image type="content" source="./media/place-a-call.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/place-a-call.png":::

Egy csoportos híváshoz való csatlakozáshoz adja meg a hívást azonosító GUID azonosítót, és kattintson a "csatlakozás csoport" gombra:

:::image type="content" source="./media/join-a-group-call.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/join-a-group-call.png":::

A "kód megjelenítése" gombra kattintva megtekintheti a hívások elhelyezésére, fogadására és a csoportos hívások csatlakoztatására szolgáló mintakód.

Egy aktív hívás a következőképpen néz ki:

:::image type="content" source="./media/group-call.png" alt-text="Webes hívási oktatóanyag 1" lightbox="./media/group-call.png":::

Ez a minta kódrészleteket is biztosít a következő képességekhez:

  - A videó ikonra kattintva be-és kikapcsolhatja a videokamerát
  - A mikrofon ikonra kattintva be-és kikapcsolhatja a mikrofont
  - A lejátszás ikonra kattintva megtarthatja/megtarthatja a hívást
  - Kattintson a képernyő ikonjára a képernyő megosztásának elindításához/leállításához
  - A személy ikonra kattintva vehet fel egy résztvevőt a hívásba
  - Ha egy adott résztvevőt szeretne eltávolítani a hívásból, kattintson a résztvevő eltávolítása elemre a résztvevő névsorában


## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"] 
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a hívó ügyféloldali kódtár használatával](../quickstarts/voice-video-calling/calling-client-samples.md)
- További információ a [hívás működéséről](../concepts/voice-video-calling/about-call-types.md)
- Az [API-referenciák dokumentációjának](https://docs.microsoft.com/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js) áttekintése

## <a name="additional-reading"></a>További információ

- [Azure Communication GitHub](https://github.com/Azure/communication) – további példákat és információkat talál a hivatalos GitHub-oldalon
- [Redux](https://redux.js.org/) – ügyféloldali állapot kezelése
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft powered UI Library
- [Reagálás](https://reactjs.org/) – függvénytár a felhasználói felületek létrehozásához
- [ASP.net Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) -keretrendszer webalkalmazások létrehozásához
