---
title: Funkcióinformációk megjelenítése az Azure Maps Android SDK-ban | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a funkcióinformációkat a térképen a Microsoft Azure Maps Android SDK használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911691"
---
# <a name="display-feature-information"></a>Funkcióinformációk megjelenítése

A térbeli adatokat gyakran pontok, vonalak és sokszögek használatával ábrázolják. Ezek hez gyakran metaadat-adatok kapcsolódnak. Előfordulhat például, hogy egy pont egy üzlet helyét jelöli, és az adott étterem metaadatai lehetnek a neve, címe és az általa kiszolgálott élelmiszer típusa. Ezek a metaadatok hozzáadhatók a szolgáltatások `JsonObject`tulajdonságaiként a használatával. A következő kód létrehoz egy `title` egyszerű pont funkciót egy tulajdonsággal, amelynek értéke "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Amikor egy felhasználó interakcióba lép egy funkcióval a térképen, az események segítségével reagálhat ezekre a műveletekre. Gyakori forgatókönyv egy olyan szolgáltatás metaadat-tulajdonságairól szóló üzenet megjelenítése, amelyet a felhasználó használt. Az `OnFeatureClick` esemény a fő esemény, amely azt észleli, hogy a felhasználó mikor koppintott meg egy funkcióa a térképen. Van egy `OnLongFeatureClick` esemény is. Amikor hozzáadja az `OnFeatureClick` eseményt a térképhez, egyetlen rétegre korlátozható, ha átadja egy réteg azonosítóját, hogy korlátozza azt. Ha nem ad át rétegazonosítót, a térkép bármely funkciójára koppintva, függetlenül attól, hogy melyik rétegben van, az esemény beindítja. A következő kód létrehoz egy szimbólumréteget a pontadatok `OnFeatureClick` rendereléséhez a térképen, majd hozzáad egy eseményt, és korlátozza azt ehhez a szimbólumréteghez.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Bejelentési üzenet megjelenítése

A bejelentési üzenet az egyik legegyszerűbb módja az információk megjelenítésének a felhasználó számára, és az Android minden verziójában elérhető. Nem támogatja a felhasználói bevitel t, és csak rövid ideig jelenik meg. Ha azt szeretné, hogy gyorsan hagyja, hogy a felhasználó tud valamit arról, hogy mit megérintette, a pirítós üzenet lehet egy jó lehetőség. A következő kód bemutatja, hogyan használható `OnFeatureClick` a bejelentési üzenet az eseményhez.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![A lehallgatott funkció animációja és a bejelentési üzenet megjelenítése](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

A bejelentési üzenetek mellett számos más módon is bemutathat egy szolgáltatás metaadat-tulajdonságait, például:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) - Snackbars nyújt könnyű visszajelzést egy műveletet. Ezek egy rövid üzenetet jelenítenek meg a képernyő alján a mobil és a bal alsó nagyobb eszközökön. A snackbárok a képernyőn minden más elem felett jelennek meg, és egyszerre csak egy jeleníthető meg.
- [Párbeszédek](https://developer.android.com/guide/topics/ui/dialogs) – A párbeszéd egy kis ablak, amely arra kéri a felhasználót, hogy hozzon döntést, vagy adjon meg további információkat. A párbeszédablak nem tölti ki a képernyőt, és általában olyan modális eseményekhez használatos, amelyek folytatása előtt a felhasználóknak műveletet kell végrehajtaniuk.
- [Töredék](https://developer.android.com/guide/components/fragments) hozzáadása az aktuális tevékenységhez.
- Lépjen egy másik tevékenységre vagy nézetre.

## <a name="next-steps"></a>További lépések

További adatok hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](how-to-add-shapes-to-android-map.md)
