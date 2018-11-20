---
title: 'Gyors útmutató: Választ kap a Tudásbázis - REST, a Go - QnA Maker'
titlesuffix: Azure Cognitive Services
description: A Go REST-alapú rövid útmutató végigvezeti egy válasz lekérése a Tudásbázis programozott módon.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 54c2133e35de09cae411f3bcec9f4421e4586f18
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977455"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-go"></a>A Tudásbázis a Go egy kérdésre választ kaphat

Ez a rövid útmutató végigvezeti programozott módon első válasz a QnA Maker közzétett Tudásbázis. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/data-sources-supported.md). A kérdés, a JSON-formátumban, az API-kérelem törzse zajlik. 

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. 
* **Közzététel** beállítások lapon. Közzétett Tudásbázis nem rendelkezik, hozzon létre egy üres Tudásbázis, akkor a Tudásbázis importálja a **beállítások** lapon, majd közzéteheti. Letöltheti és használhatja [Ez a alapvető Tudásbázis](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    A közzétételi oldalon beállítások közé tartozik a POST útvonal, gazdagép értékét, és EndpointKey értékkel. 

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Ez a rövid útmutató kódja megtalálható a [ https://github.com/Azure-Samples/cognitive-services-qnamaker-Go ](https://github.com/Azure-Samples/cognitive-services-qnamaker-Go/tree/master/documentation-samples/quickstarts/get-answer) tárház. 

## <a name="create-a-go-file"></a>Go-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy új fájlt `get-answer.go` , és adja hozzá a következő osztályok:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Fent a `main` függvény felső részén a `get-answer.go` fájlt, adja hozzá a projekthez szükséges függőséget:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Felső részén a `main` működik, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. A rendszer ezeket az értékeket a **közzététel** lapon a Tudásbázis közzététele után. 

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adjon hozzá egy POST-kérés küldése a kérdés és válasz

A következő kódot egy HTTPS-kérést küld a QnA Maker API, a kérdés küldeni a Tudásbázis és a választ kap:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értékének tartalmazza a karakterláncot `EndpointKey `. 

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozhat létre, és a program futtatása a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

1. A fájl létrehozása:

    ```bash
    go build get-answer.go
    ```

1. Futtassa a fájlt:

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)