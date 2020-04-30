---
title: fájl belefoglalása
description: fájl belefoglalása
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67179490"
---
## <a name="run-the-web-application"></a>A webalkalmazás futtatása

1. A folyamat megkönnyítése érdekében a GitHubon megtalálható egy egyoldalas, mintául szolgáló webalkalmazás. Nyissa meg a [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)böngészőt a alkalmazásban.

    > [!NOTE]
    > A HTML-fájl forrása a következő helyen található: [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Ha a rendszer felkéri a függvényalkalmazás kiindulási URL-címének megadására, írja be a következőt: `http://localhost:7071`.

1. Amikor a rendszer erre kéri, adja meg a felhasználónevet.

1. A webalkalmazás meghívja a *GetSignalRInfo* függvényt a függvényalkalmazásban az Azure SignalR Service-hez való kapcsolódáshoz szükséges kapcsolatadatok lekéréséhez. Ha a kapcsolat létrejött, megjelenik a csevegőüzenet beviteli mezője.

1. Írja be az üzenetet, és nyomja le az Enter billentyűt. Az alkalmazás elküldi az üzenetet a *SendMessage* függvénynek az Azure Functions-alkalmazásban, amely ezt követően SignalR kimeneti kötés használatával továbbítja az üzenetet az összes csatlakoztatott ügyfélnek. Ha minden megfelelően működik, akkor az üzenetnek meg kell jelennie az alkalmazásban.

    ![Az alkalmazás futtatása](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Nyissa meg a webalkalmazás egy másik példányát egy másik böngészőablakban. Látni fogja, hogy az elküldött üzenet az alkalmazás összes példányában megjelenik.

> [!IMPORTANT]
> Mivel a HTML-oldal HTTPS-kapcsolaton keresztül érhető el, de a helyi Azure Functions futtatókörnyezet alapértelmezés szerint HTTP-t használ, a böngésző (például a Firefox) olyan vegyes tartalmú házirendet kényszerít ki, amely blokkolja a weboldalról érkező kéréseket a függvények számára. Ennek megoldásához olyan böngészőt használjon, amely nem rendelkezik ezzel a korlátozással, vagy indítson el egy helyi HTTP-kiszolgálót, például a [http-Servert](https://www.npmjs.com/package/http-server) a */docs/demo/chat-v2* könyvtárban. Győződjön meg arról, hogy a forrás `CORS` hozzá van adva a *Local. Settings. JSON*beállításhoz.