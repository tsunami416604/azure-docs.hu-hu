---
title: 'Rövid útmutató: Vizuális keresési lekérdezés létrehozása Python nyelven – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ez a dokumentum bemutatja, hogyan tölthet fel képet a Bing Visual Search API-ba, illetve hogyan kaphat a képpel kapcsolatos megállapításokat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3a0d92e42eed097e244118a60ec0a4223c9cedf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440941"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Rövid útmutató: Az első Bing Visual Search-lekérdezés létrehozása Python nyelven

A Bing Visual Search API információkat ad vissza egy Ön által megadott képről. A kép feltöltéséhez használhatja a kép URL-címét, egy megállapítási jogkivonatot, vagy feltöltheti magát a képet. Információ ezekről a lehetőségekről: [Mi a Bing Visual Search API?](../overview.md) Ez a cikk egy kép feltöltését mutatja be. A képfeltöltés olyan mobil forgatókönyveknél lehet hasznos, amikor képet készít egy ismert nevezetességről, amelyről információt szeretne kapni. Az információk között szerepelhetnek például az adott nevezetességre vonatkozó érdekességek. 

Ha helyi képet tölt fel, az alábbiakban láthatja a POST-törzsben kötelezően megadandó űrlapadatokat. Az űrlapadatoknak tartalmazniuk kell a Tartalom-Témakör fejlécet. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk egy egyszerű konzolalkalmazást ismertet, amely Bing Visual Search API-kérést küld, majd megjeleníti a JSON-keresés eredményeit. Bár ez az alkalmazás Python nyelven lett megírva, az API egy RESTful-webszolgáltatás, azaz kompatibilis minden olyan programozási nyelvvel, amely képes HTTP-kérések küldésére és JSON-elemzésre. 

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [Python 3](https://www.python.org/) szükséges.

Ebben a rövid, szüksége lesz egy előfizetést, S9 árkategória elindításához, ahogyan [Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Előfizetés indítása az Azure Portalon:
1. A szövegmezőbe, amely szerint az Azure portal tetején adja meg a "BingSearchV7" `Search resources, services, and docs`.  
2. Marketplace-en a legördülő listában, válassza a `Bing Search v7`.
3. Adja meg `Name` az új erőforrás.
4. Válassza ki `Pay-As-You-Go` előfizetés.
5. Válassza ki `S9` tarifacsomag.
6. Kattintson a `Enable` az előfizetés indításához.

## <a name="running-the-walkthrough"></a>Az útmutató futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
2. Hozzon létre egy visualsearch.py nevű fájlt, és adja hozzá a jelen rövid útmutatóban szereplő kódot.
3. Cserélje le a `SUBSCRIPTION_KEY` értéket saját előfizetői azonosítójára.
3. Az `imagePath` értéket cserélje le a feltölteni kívánt kép elérési útjára.
4. Futtassa a programot.



Az alábbiakban azt láthatja, hogyan küldhet üzenetet többrészes űrlapadatok használatával, Python nyelven.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>További lépések

[Képpel kapcsolatos információk lekérése Insights-jogkivonat segítségével](../use-insights-token.md)  
[Bing Visual Search képfeltöltés – oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás – oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search – áttekintés](../overview.md)  
[Kipróbálás](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próbaverzióhoz tartozó hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API – referencia](https://aka.ms/bingvisualsearchreferencedoc)
