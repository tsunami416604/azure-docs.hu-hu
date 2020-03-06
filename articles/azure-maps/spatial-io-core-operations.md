---
title: Alapszintű IO-műveletek | Microsoft Azure térképek
description: Megtudhatja, hogyan lehet hatékonyan beolvasni és írni az XML-és tagolt adatok alap kódtárak használatával történő használatát a térbeli IO-modulból.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370933"
---
# <a name="core-io-operations"></a>Alapszintű i/o-műveletek

Amellett, hogy eszközöket biztosít a térbeli adatfájlok olvasásához, a térbeli IO-modul elérhetővé teszi az alapszintű könyvtárakat az XML-és tagolt adatok gyors és hatékony olvasásához és írásához.

A `atlas.io.core` névtér két alacsony szintű osztályt tartalmaz, amelyek gyorsan olvashatnak és írhatnak CSV-és XML-fájlokat. Ezek az alaposztályok a térbeli IO-modul térbeli adatolvasóit és írókat is kikapcsolják. Nyugodtan használhatja őket a CSV-vagy XML-fájlok további olvasási és írási támogatásának hozzáadásához.
 
## <a name="read-delimited-files"></a>Tagolt fájlok olvasása

A `atlas.io.core.CsvReader` osztály a tagolt adatkészleteket tartalmazó karakterláncokat olvas. Ez az osztály két módszert biztosít az adatolvasáshoz:

- A `read` függvény beolvassa a teljes adathalmazt, és egy 2 dimenziós tömböt ad vissza, amely a tagolt adatkészlet összes celláját jelképezi.
- A `getNextRow` függvény a szöveg minden sorát beolvassa egy tagolt adatkészletből, és az adott sorban lévő összes cellát megadó karakterlánc-tömböt ad vissza. A felhasználó feldolgozhatja a sort, és a következő sor feldolgozása előtt el tudja végezni a felesleges memóriát a sorból. Így a függvény nagyobb memória-hatékonyságot igényel.

Alapértelmezés szerint az olvasó a vessző karaktert fogja használni elválasztóként. A határolójelek azonban egyetlen karakterre módosíthatók, vagy a `'auto'`ra állíthatók be. Ha `'auto'`re van állítva, az olvasó a karakterlánc első sorát fogja elemezni. Ezután kiválasztja a leggyakoribb karaktert az alábbi táblázatból, hogy elválasztóként használja.

| | |
| :-- | :-- |
| Vesszővel | `,` |
| Lapon | `\t` |
| Adatcsatornán | `|` |

Ez az olvasó olyan szöveges minősítőket is támogat, amelyek a határoló karaktert tartalmazó cellák kezelésére szolgálnak. Az idézőjel (`'"'`) karakter az alapértelmezett szöveges minősítő, de egyetlen karakterre is módosítható.

## <a name="write-delimited-files"></a>Tagolt fájlok írása

A `atlas.io.core.CsvWriter` objektumok tömbjét írja egy tagolt karakterláncként. Egyetlen karakter is használható határolójelként vagy szöveges minősítőként. Az alapértelmezett határolójel a vessző (`','`), az alapértelmezett szöveges minősítő pedig az idézőjel (`'"'`) karakter.

Az osztály használatához kövesse az alábbi lépéseket:

- Hozza létre az osztály egy példányát, és opcionálisan állítson be egy egyéni határolójelet vagy szöveges minősítőt.
- Adatírás az osztályba az `write` függvény vagy a `writeRow` függvény használatával. A `write` függvénynél adjon meg egy két dimenziós tömböt, amely több sort és cellát jelképez. Az `writeRow` függvény használatához adjon át egy olyan objektumot, amely egy több oszlopból álló adatsort jelöl.
- A tagolt karakterlánc beolvasásához hívja meg a `toString` függvényt. 
- Ha szeretné, meghívja a `clear` metódust, hogy az író újra felhasználható legyen, és csökkentse az erőforrás-kiosztását, vagy hívja meg a `delete` metódust az író példányának eldobásához.

> [!Note]
> A megírt oszlopok száma az író számára átadott adatmennyiség első sorában található cellák számának megfelelően lesz korlátozva.

## <a name="read-xml-files"></a>XML-fájlok olvasása

A `atlas.io.core.SimpleXmlReader` osztály gyorsabb a `DOMParser`XML-fájlok elemzésekor. A `atlas.io.core.SimpleXmlReader` osztálynak azonban XML-fájlokra van szüksége, hogy megfelelően formázottak legyenek. A nem megfelelően formázott XML-fájlok (például a záró címkék hiánya) valószínűleg hibát okoznak.

A következő kód azt mutatja be, hogyan használható a `SimpleXmlReader` osztály egy XML-karakterlánc JSON-objektumba való elemzéséhez, és hogyan szerializálható egy kívánt formátumba.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML-fájlok írása

A `atlas.io.core.SimpleXmlWriter` osztály jól formázott XML-kódot ír a memóriában.

A következő kód azt mutatja be, hogyan használható a `SimpleXmlWriter` osztály egy jól formázott XML-karakterlánc létrehozásához.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

A fenti kódból létrehozott XML-fájl az alábbihoz hasonlóan fog kinézni.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Az alábbi cikkekben további kódokat talál a Maps-hez való hozzáadáshoz:

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)