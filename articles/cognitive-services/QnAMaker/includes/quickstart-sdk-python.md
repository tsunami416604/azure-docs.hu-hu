---
ms.openlocfilehash: a77041c0e53c2f0f8b6d0891a0f755e8ca474923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946347"
---

Használja a QnA Maker ügyfélkönyvtárat a python hoz:

* Tudásbázis létrehozása
* Tudásbázis kezelése
* Tudásbázis közzététele

[Referenciadokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python-minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a QnA Maker számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen.

Miután beszerzett egy kulcsot az erőforrásból, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az erőforráshoz, amelynek neve és. `QNAMAKER_KEY` `QNAMAKER_HOST` Használja az Azure Portalon található kulcs- és végpontértékeket.

### <a name="install-the-python-library-for-qna-maker"></a>A QnA Maker python könyvtárának telepítése

A Python telepítése után telepítheti az ügyfélkönyvtárat a következőkkel:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Objektummodell

Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) a kulccsal, és használja azt a végpontjával egy [QnAMakerClient objektum](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) létrehozásához.

Az ügyfél létrehozása után a [Tudásbázis segítségével](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) hozza létre, kezelje és tegye közzé tudásbázisát.

Azonnali műveletek esetén a metódus általában egy JSON-objektumot ad vissza, amely jelzi az állapotot. Hosszú ideig futó műveletek esetén a válasz a művelet azonosítója. Hívja fel az [ügyfelet. Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) metódus a műveletazonosítóval [a kérelem állapotának](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python)meghatározásához.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Python QnA Maker ügyfélkönyvtárával:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Művelet állapotának beszereznie](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Új python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást a kívánt szerkesztőben vagy IDE-ben. Ezután importálja a következő könyvtárakat.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

|Környezeti változó|Változó|Példa|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|A kulcs egy 32 karakterből álló karakterlánc, és elérhető az Azure Portalon, a QnA Maker erőforrás, a gyorsútmutató lapon. Ez nem ugyanaz, mint az előrejelzési végpont kulcs.|
|`QNAMAKER_HOST`|`host`| A szerzői végpont a formátumában `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`tartalmazza az **erőforrás nevét.** Ez nem ugyanaz az URL-cím, amelyet az előrejelzési végpont lekérdezéséhez használnak.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ezután hozzon létre egy CognitiveServicesCredentials objektumot a kulccsal, és használja azt a végpontjával egy [QnAMakerClient objektum](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) létrehozásához.


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

 Az ügyfélobjektum segítségével [tudásbázisműveleti objektumot](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) kaphat be.

A tudásbázis három forrásból tárolja a [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) objektum kérdés- és válaszpárjait:

* Szerkesztői **tartalom**esetén használja a [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python) objektumot.
* **Fájlok**esetén használja a [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python) objektumot.
* **Url-címekhez**használja a karakterláncok listáját.

Hívja meg a [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) metódust, majd adja át a visszaadott műveletazonosítót az [Operations.getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Győződjön meg [`_monitor_operation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres létrehozása érdekében.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázis tikóállapot-objektumát frissítheti a tudásbázis-azonosító és az [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) segítségével, amely [hozzáadja,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python) [frissíti](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)és [törli](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) a [Frissítési](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) metódus DTO-objektumait. Az [Operation.getDetail](#get-status-of-an-operation) metódus segítségével állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Győződjön meg [`_monitor_operation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres frissítése érdekében.

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) módszerrel. Ez veszi az aktuális mentett és betanított modell, a tudásbázis-azonosító által hivatkozott, és közzéteszi, hogy egy végponton.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) módszerrel töltse le az adatbázist a [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python)listájaként. Ez _nem_ egyenértékű a QnA Maker portál **Nak** a Beállítások oldalról történő exportálásával, mert ennek a módszernek az eredménye nem TSV-fájl.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a tudásbázis azonosítójának paraméterét tartalmazó [törlési](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) módszerrel.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beszereznie

Egyes módszerek, például a létrehozás és a frissítés, elegendő időt vehet igénybe, hogy ahelyett, hogy megvárnák a folyamat befejezését, egy [műveletet](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python) adnak vissza. Használja a művelet azonosítóját a művelet lekérdezési (újrapróbálkozási logikával) állapotának meghatározásához az eredeti módszer.

A _setTimeout_ hívás a következő kódblokkban az aszinkron kód szimulálására szolgál. Cserélje le ezt az újrapróbálkozási logikára.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa `python knowledgebase_quickstart.py` az alkalmazást az alkalmazáskönyvtárból származó paranccsal.

A cikkben szereplő összes kódrészlet [elérhető,](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) és egyetlen fájlként futtatható.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
