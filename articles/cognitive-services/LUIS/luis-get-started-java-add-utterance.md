---
title: Rövid Java-útmutató – modell módosítása és LUIS-alkalmazás betanítása
titleSuffix: Azure Cognitive Services
description: Ebben a rövid Java-útmutatóban kimondott példaszövegeket ad egy otthonautomatizálási alkalmazáshoz, és betanítja az alkalmazást. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: bfe4aae25a34799370e52c8677871a62935ab5c2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419741"
---
# <a name="quickstart-change-model-using-java"></a>Rövid útmutató: Modell módosítása a Java használatával 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* [A Google GSON JSON-kódtára](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>A rövid útmutató kódjának létrehozása

1. Adja hozzá a Java-függőségeket az `AddUtterances.java` nevű fájlhoz.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Hozza létre az `AddUtterances` osztályt. Ehhez az osztály tartalmazza az összes következő kódrészletet.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Adja hozzá a LUIS-állandókat az osztályhoz. Másolja ki az alábbi kódot, és módosítsa a szerzői kulcsnak, az alkalmazásazonosítónak és a verzióazonosítónak megfelelően.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Adja hozzá a LUIS API-ba meghívásokat intéző metódust az AddUtterances osztályhoz. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Adja hozzá a LUIS API HTTP-válaszait intéző metódust az AddUtterances osztályhoz.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Adja hozzá a kivételkezelést az AddUtterances osztályhoz. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Adja hozzá a fő függvényt az AddUtterances osztályhoz.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Kód buildelése

A függőségeket tartalmazó AddUtterance fordítása

```CMD
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Kód futtatása
Az `AddUtterance` argumentumok nélkül történő meghívása betanítás nélkül adja hozzá a LUIS-beli kimondott szövegeket az alkalmazáshoz.

```CMD
> java -classpath .;gson-2.8.2.jar AddUtterances
```

A parancssor megjeleníti a kimondott szövegek hozzáadása API meghívásának eredményét. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md) 