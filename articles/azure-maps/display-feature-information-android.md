---
title: Szolgáltatási információk megjelenítése az Android Maps szolgáltatásban | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg az információkat, amikor a felhasználók a Térkép funkcióit használják. A Azure Maps Android SDK segítségével megjelenítheti a Toast-üzeneteket és más típusú üzeneteket.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680010"
---
# <a name="display-feature-information"></a>Funkcióinformációk megjelenítése

A térbeli adataikat gyakran pontok, vonalak és sokszögek használatával jelölik. Ezek az adatok gyakran metaadatokkal kapcsolatos adatokkal rendelkeznek. Előfordulhat például, hogy egy pont egy étterem és a metaadatok helyét jelöli az adott étteremben, a neve, a címe és a kiszolgált élelmiszer típusa. Ez a metaadatok hozzáadhatók a GeoJSON tulajdonságaihoz `Feature` . A következő kód egy egyszerű pont funkciót hoz létre egy olyan `title` tulajdonsággal, amelynek értéke "„Helló világ!” alkalmazás!".

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Az [adatforrások létrehozásához](create-data-source-android-sdk.md) és a térképhez való hozzáadásához tekintse meg az adatforrás létrehozása című dokumentációt.

Ha a felhasználó a térképen található szolgáltatással kommunikál, az események felhasználhatók az adott műveletekre való reagálásra. Gyakori forgatókönyv egy olyan szolgáltatás metaadat-tulajdonságaiból álló üzenet megjelenítése, amellyel a felhasználó az általa kommunikált. Az `OnFeatureClick` esemény a fő esemény, amellyel észlelhető, ha a felhasználó kihasznál egy funkciót a térképen. Van egy esemény is `OnLongFeatureClick` . Az `OnFeatureClick` eseménynek a térképhez való hozzáadásakor a réteg azonosítójának átadásával egyetlen rétegre korlátozható. Ha a rendszer nem adja át a réteg AZONOSÍTÓját, koppintson a Térkép bármelyik szolgáltatására, függetlenül attól, hogy melyik rétegben található, az eseményt. A következő kód létrehoz egy szimbólum réteget a térkép megjelenítéséhez, majd feltesz egy eseményt, `OnFeatureClick` és korlátozza azt erre a szimbólum rétegre.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Toast-üzenet megjelenítése

A Toast-üzenet az egyik legegyszerűbb módja a felhasználó információinak megjelenítésének, és az Android minden verziójában elérhető. Semmilyen típusú felhasználói bevitelt nem támogat, és csak rövid ideig jelenik meg. Ha azt szeretné, hogy a felhasználó gyorsan tudja, mit találtak a szolgáltatásban, a Toast üzenet jó választás lehet. Az alábbi kód azt mutatja be, hogyan használható a Toast-üzenet az `OnFeatureClick` eseménnyel.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![A kihasznált szolgáltatás és a megjelenő Toast-üzenet animációja](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

A pirítós üzenetein kívül számos más módon is bemutathatja egy szolgáltatás metaadat-tulajdonságait, például:

- [Snackbar widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` egyszerű visszajelzést ad a műveletről. Egy rövid üzenetet jelenítenek meg a képernyő alján a mobilon és a bal alsó sarokban a nagyobb eszközökön. `Snackbars` a képernyő minden más eleme felett jelenik meg, és egyszerre csak egy látható.
- [Párbeszédpanelek](https://developer.android.com/guide/topics/ui/dialogs) – a párbeszédpanel egy kis ablak, amely arra kéri a felhasználót, hogy hozzon döntést, vagy adjon meg további információkat. Egy párbeszédpanel nem tölti ki a képernyőt, és általában olyan modális eseményekhez használatos, amelyek megkövetelik, hogy a felhasználók a folytatás előtt végrehajtsák a műveletet.
- Adjon hozzá egy [töredéket](https://developer.android.com/guide/components/fragments) az aktuális tevékenységhez.
- Navigáljon egy másik tevékenységhez vagy nézethez.

## <a name="next-steps"></a>Következő lépések

További információ hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Reagálás a leképezési eseményekre](android-map-events.md)

> [!div class="nextstepaction"]
> [Adatforrás létrehozása](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](how-to-add-shapes-to-android-map.md)
