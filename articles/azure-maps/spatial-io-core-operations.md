---
title: Alapvető IO-műveletek | Microsoft Azure Maps
description: Ismerje meg, hogyan lehet hatékonyan olvasni és írni az XML-t és a tagolt adatokat a térbeli IO-modul alapvető kódtárai használatával.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371442"
---
# <a name="core-io-operations"></a>Alapvető IO-műveletek

A térbeli IO-modul a térbeli ADATfájlok olvasására szolgáló eszközök mellett az alapvető mögöttes könyvtárakat is elérhetővé teszi az XML olvasásához és írásához, valamint a korlátozott adatok gyors és hatékony definiálásához.

A `atlas.io.core` névtér két alacsony szintű osztályt tartalmaz, amelyek gyorsan képesek CSV- és XML-adatok olvasására és írására. Ezek az alaposztályok a térbeli adatolvasókat és az írókat a térbeli IO modulban működtetik. Nyugodtan használja őket, hogy adjunk további olvasási és írási támogatást CSV vagy XML fájlokat.
 
## <a name="read-delimited-files"></a>Elválasztott fájlok olvasása

Az `atlas.io.core.CsvReader` osztály beolvassa a tagolt adatkészleteket tartalmazó karakterláncokat. Ez az osztály két módszert kínál az adatok olvasására:

- A `read` függvény beolvassa a teljes adatkészletet, és a tagolt adatkészlet összes celláját jelölő karakterláncok kétdimenziós tömbjét adja vissza.
- A `getNextRow` függvény beolvassa a tagolt adatkészlet minden sorát, és az adatkészlet összes celláját jelölő karakterlánctömböt ad vissza. A felhasználó feldolgozhatja a sort, és a következő sor feldolgozása előtt megsemmisítheti a sor felesleges memóriáját. Tehát, függvény van több memória hathatós.

Alapértelmezés szerint az olvasó a vesszőkaraktert fogja használni elhatárolójelként. A határolójel azonban bármely karakterre vagy beállításra `'auto'`módosítható. Ha `'auto'`a beállítás , az olvasó elemzi az első sorban a szöveg a karakterláncban. Ezután kiválasztja a leggyakoribb karaktert az alábbi táblázatból, amelyet határolóként használhat.

| | |
| :-- | :-- |
| Vessző | `,` |
| Tab | `\t` |
| Cső | `|` |

Ez az olvasó támogatja a határoló karaktert tartalmazó cellák kezelésére használt szövegminősítőket is. Az idézet`'"'`( ) karakter az alapértelmezett szövegminősítő, de bármely karakterre módosítható.

## <a name="write-delimited-files"></a>Tagolt fájlok írása

Az `atlas.io.core.CsvWriter` objektumok tömbjét tagolt karakterláncként írja. Bármely karakter használható határolóként vagy szövegminősítőként. Az alapértelmezett határolójel a`','`vessző ( ) és az`'"'`alapértelmezett szövegminősítő az idézet ( ) karakter.

Az osztály használatához kövesse az alábbi lépéseket:

- Hozzon létre egy példányt az osztályból, és tetszés szerint egyéni határolóvagy szövegminősítőt állítson be.
- Adatok írása az `write` osztályba `writeRow` a függvény vagy a függvény használatával. A `write` függvényhez adja át a több sort és cellát képviselő objektumok kétdimenziós tömbjét. A függvény `writeRow` használatához adja át a több oszlopot tartalmazó adatsort képviselő objektumok tömbjét.
- Hívja `toString` meg a függvényt a tagolt karakterlánc beolvasásához. 
- Ha meg is `clear` adod a metódust, hívja meg az `delete` író újrafelhasználhatóvá és csökkentse az erőforrás-elosztást, vagy hívja meg az írópéldány ártalmatlanítására vonatkozó metódust.

> [!Note]
> Az írott oszlopok száma az írónak átadott adatok első sorának celláinak számára lesz korlátozva.

## <a name="read-xml-files"></a>XML-fájlok olvasása

Az `atlas.io.core.SimpleXmlReader` osztály gyorsabb az XML-fájlok `DOMParser`elemzésén, mint a . Az osztály `atlas.io.core.SimpleXmlReader` azonban megköveteli, hogy az XML-fájlok jól formázhatók legyenek. A nem jól formázott XML-fájlok, például a zárócímkék hiányoznak, valószínűleg hibát eredményeznek.

A következő kód bemutatja, `SimpleXmlReader` hogyan használhatja az osztályt egy XML-karakterlánc JSON-objektummá történő elemzésére és a kívánt formátumba való szerializálására.

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

Az `atlas.io.core.SimpleXmlWriter` osztály jól formázott XML-t ír memóriatakarékos módon.

A következő kód bemutatja, `SimpleXmlWriter` hogyan használható az osztály egy jól formázott XML-karakterlánc létrehozásához.

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

A fenti kódból származó létrehozott XML a következőkre néz.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

További kódmintákat a térképekhez további kódmintákért tekintse meg:

> [!div class="nextstepaction"]
> [Támogatott adatformátum részletei](spatial-io-supported-data-format-details.md)