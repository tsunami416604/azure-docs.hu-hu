---
title: A szolgáltatással kapcsolatos információk megjelenítése a Azure Maps Android SDK-ban | Microsoft Docs
description: Ismerje meg, hogyan jelenítheti meg a szolgáltatással kapcsolatos információkat a Azure Maps Android SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 0dcabb0e5141a92394f2be38cbe7e71fa6e03d58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928536"
---
# <a name="display-feature-information"></a>Funkcióinformációk megjelenítése

A térbeli adataikat gyakran pontok, vonalak és sokszögek használatával jelölik. Ezek az adatok gyakran metaadatokkal kapcsolatos adatokkal rendelkeznek. Előfordulhat például, hogy egy adott pont az áruház helyét és a hozzájuk tartozó metaadatokat a neve, címe és az általa kiszolgált élelmiszer típusát jelöli. Ezeket a metaadatokat `JsonObject`használatával lehet felvenni a szolgáltatások tulajdonságaiba. A következő kód egy egyszerű pont funkciót hoz létre egy olyan `title` tulajdonsággal, amelynek értéke ""Helló világ!"alkalmazás!".

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

Ha a felhasználó a térképen található szolgáltatással kommunikál, az események felhasználhatók az adott műveletekre való reagálásra. Gyakori forgatókönyv egy olyan szolgáltatás metaadat-tulajdonságaiból álló üzenet megjelenítése, amellyel a felhasználó az általa kommunikált. A `OnFeatureClick` esemény a fő esemény, amellyel észlelhető, ha a felhasználó kihasznál egy funkciót a térképen. Van egy `OnLongFeatureClick` esemény is. Ha a `OnFeatureClick` eseményt hozzáadja a térképhez, az egy réteg AZONOSÍTÓjának átadásával korlátozható egyetlen rétegre. Ha a rendszer nem adja át a réteg AZONOSÍTÓját, koppintson a Térkép bármelyik szolgáltatására, függetlenül attól, hogy melyik rétegben található, az eseményt. A következő kód létrehoz egy szimbólum réteget a térkép megjelenítéséhez, majd egy `OnFeatureClick` eseményt hoz létre, és ezt a szimbólum rétegre korlátozza.

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

## <a name="display-a-toast-message"></a>Toast-üzenet megjelenítése

A Toast-üzenet az egyik legegyszerűbb módja a felhasználó információinak megjelenítésének, és az Android minden verziójában elérhető. Semmilyen típusú felhasználói bevitelt nem támogat, és csak rövid ideig jelenik meg. Ha azt szeretné, hogy a felhasználó gyorsan tudja, mit találtak a szolgáltatásban, a Toast üzenet jó választás lehet. A következő kód azt mutatja be, hogyan használható a Toast-üzenet a `OnFeatureClick` eseményhez.

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

![egy kihasznált szolgáltatás animációja, és egy Toast üzenet jelenik meg](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

A pirítós üzenetein kívül számos más módon is bemutathatja egy szolgáltatás metaadat-tulajdonságait, például:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) – a Snackbars egyszerű visszajelzést nyújt a műveletről. Egy rövid üzenetet jelenítenek meg a képernyő alján a mobilon és a bal alsó sarokban a nagyobb eszközökön. A Snackbars a képernyő összes többi eleme felett jelenik meg, és egyszerre csak egy látható.
- [Párbeszédpanelek](https://developer.android.com/guide/topics/ui/dialogs) – a párbeszédpanel egy kis ablak, amely arra kéri a felhasználót, hogy hozzon döntést, vagy adjon meg további információkat. Egy párbeszédpanel nem tölti ki a képernyőt, és általában olyan modális eseményekhez használatos, amelyek megkövetelik, hogy a felhasználók a folytatás előtt végrehajtsák a műveletet.
- Adjon hozzá egy [töredéket](https://developer.android.com/guide/components/fragments) az aktuális tevékenységhez.
- Navigáljon egy másik tevékenységhez vagy nézethez.

## <a name="next-steps"></a>Következő lépések

További információ hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Alakzatok hozzáadása Android-térképhez](how-to-add-shapes-to-android-map.md)
