---
title: Migrálás v3 gépen – megtanult entitás
titleSuffix: Azure Cognitive Services
description: A v3 authoring egy új entitást, a géppel megtanult entitást biztosít, valamint lehetővé teszi a kapcsolatok hozzáadását a géppel megismert entitáshoz és az alkalmazás egyéb szerveihez vagy funkcióihoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563833"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrálás v3 authoring entitásba

A v3 authoring egy új entitást, a géppel megtanult entitást biztosít, valamint lehetővé teszi a kapcsolatok hozzáadását a géppel megismert entitáshoz és az alkalmazás egyéb szerveihez vagy funkcióihoz.

## <a name="entities-are-decomposable-in-v3"></a>Az entitások kiállíthatók a v3-as verzióban

A v3 authoring API-kkal létrehozott entitások az [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) -k vagy a [betekintő portál](https://preview.luis.ai/)használatával is létrehozhatnak egy szülő és gyermek típusú rétegbeli entitást. A szülő ismert, hogy a gép **által megtanult entitás** , a gyerekek pedig az ismert gép **alösszetevői** .

Minden alösszetevő egy géppel megtanult entitás is, de a megkötések és a leírók konfigurációs beállításai is megtalálhatók.

* A **megkötések** pontos szöveges egyeztetési szabályok, amelyek garantálják, hogy a rendszer kinyeri az entitásokat, amikor egy szabálynak megfelel A szabályt egy pontos szöveg-egyeztetési entitás definiálja, jelenleg: egy előre [elkészített entitás](luis-reference-prebuilt-entities.md), egy [reguláris kifejezés entitás](reference-entity-regular-expression.md)vagy egy [lista entitás](reference-entity-list.md).
* A **leírók** olyan [szolgáltatások](luis-concept-feature.md), mint például a kifejezések listája vagy az entitások, amelyek az entitások nyomatékos jelzésére szolgálnak.

A v3 authoring egy új entitást, a géppel megtanult entitást biztosít, valamint lehetővé teszi a kapcsolatok hozzáadását a géppel megismert entitáshoz és az alkalmazás egyéb szerveihez vagy funkcióihoz.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hogyan hasonlítják össze ezeket az új kapcsolatokat a v2 authoring használatával

V2 a szerzői műveletek hierarchikus és összetett entitásokat, valamint szerepköröket és szolgáltatásokat biztosítanak ugyanezen feladat végrehajtásához. Mivel az entitások, szolgáltatások és szerepkörök nem voltak explicit módon egymással összekapcsolva, nehéz volt megérteni, hogy a LUIS milyen módon utalt a kapcsolatokra az előrejelzés során.

A v3-as verzióban a kapcsolat explicit módon és az alkalmazás szerzője által lett kialakítva. Ez lehetővé teszi, hogy az alkalmazás szerzője:

* Vizuálisan megtudhatja, hogyan Jósolja meg a LUIS ezeket a kapcsolatokat, a példában hosszúságú kimondott szöveg
* Tesztelje ezeket a kapcsolatokat az [interaktív teszt ablaktáblával](luis-interactive-test.md) vagy a végponton
* Használja ezeket a kapcsolatokat az ügyfélalkalmazás egy jól strukturált, elnevezett, beágyazott [. JSON-objektumon keresztül.](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Tervezés

Az áttelepítés során vegye figyelembe a következőket az áttelepítési tervben:

* Készítsen biztonsági másolatot a LUIS-alkalmazásról, és végezze el az áttelepítést egy külön alkalmazáson. Ha egy v2 és v3 verziójú alkalmazás is elérhető egyszerre, akkor ellenőrizheti a szükséges módosításokat, és hatással lehet az előrejelzés eredményeire.
* Aktuális előrejelzési sikerességi mérőszámok rögzítése
* Aktuális irányítópult-információk rögzítése az alkalmazás állapotának pillanatképe
* Meglévő leképezések, entitások, kifejezések listája, mintázatok és batch-tesztek áttekintése
* A következő elemek migrálása **változtatás nélkül**történhet:
    * Leképezések
    * Entitások
        * Reguláriskifejezés-entitás
        * Listaentitás
    * Jellemzők
        * Kifejezések listája
* A következő elemeket kell áttelepíteni a **változásokkal**:
    * Entitások
        * Hierarchikus entitás
        * Összetett entitás
    * Szerepkörök – a szerepköröket csak gépi megtanult (szülő) entitásra lehet alkalmazni. A szerepkörök nem alkalmazhatók alösszetevőkre
    * A hierarchikus és összetett entitásokat használó batch-tesztek és-minták

Ha megtervezi az áttelepítési tervet, hagyjon időt arra, hogy áttekintse a gép által megismert utolsó entitásokat, miután az összes hierarchikus és összetett entitás át lett telepítve. Míg az egyenes áttelepítés működni fog, miután elvégezte a módosítást, és áttekinti a Batch-teszt eredményeit, és előrejelzési JSON-t, a több egységesített JSON-t úgy is megteheti, hogy az ügyféloldali alkalmazáshoz tartozó végső adatokat különbözőképpen rendezi. Ez hasonló a kód újrabontásához, és ugyanazzal a felülvizsgálati folyamattal kell kezelni, mint a szervezet.

Ha nem rendelkezik batch-tesztekkel a v2-modellhez, és az áttelepítés részeként áttelepíti a Batch-teszteket a v3-modellre, nem fogja tudni ellenőrizni, hogy az áttelepítés milyen hatással lesz a végpont-előrejelzés eredményeire.

## <a name="migrating-from-v2-entities"></a>Áttelepítés v2 entitásokból

Ahogy elkezdi a v3 authoring modelre való áttérést, érdemes megfontolnia, hogyan helyezheti át a gépi megtanult entitásra és annak alösszetevőire, beleértve a korlátozásokat és a leírókat.

A következő táblázat azt ismerteti, hogy mely entitásoknak kell áttérniük a v2-ből egy v3-es entitás kialakítására.

|V2 szerzői entitás típusa|V3 szerzői entitás típusa|Példa|
|--|--|--|
|Összetett entitás|Gépi megtanult entitás|[tudj meg többet](#migrate-v2-composite-entity)|
|Hierarchikus entitás|Gépi megtanult entitás szerepköre|[tudj meg többet](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>V2 összetett entitás migrálása

A v2 kompozit minden gyermekének szerepelnie kell a v3 számítógép által megismert entitás alösszetevőjével. Ha az összetett gyermek egy előre elkészített, reguláris kifejezés vagy egy lista entitás, ezt a gyermeket képviselő alösszetevő **korlátozásként** kell alkalmazni.

Összetett entitások gépi megtanult entitásba való áttelepítésének tervezésekor megfontolandó szempontok:
* A gyermek entitások nem használhatók mintázatban
* A gyermek entitások már nincsenek megosztva
* A gyermek entitásokat címkével kell ellátni, ha nem gépi megismertnek használták

### <a name="existing-descriptors"></a>Meglévő leírók

Az összetett entitás szavainak növelésére használt kifejezések listáját leíróként kell alkalmazni a gépi megtanult (szülő) entitás, az alösszetevő (gyermek) entitás vagy a szándék (ha a kifejezés lista csak egy szándékra vonatkozik). Tervezze meg, hogy hozzáadja a leírót az entitáshoz, amelyet a legjelentősebb mértékben fokoznia kell. Ne adja hozzá a leírót általános módon a gépi megtanult (szülő) entitáshoz, ha az a legjelentősebb mértékben növeli egy alösszetevő (gyermek) előrejelzését.

### <a name="new-descriptors"></a>Új leírók

A v3 authoring szolgáltatásban vegyen fel egy tervezési lépést, amely az entitások és a szándékok lehetséges leírókként való értékelését teszi elérhetővé.

### <a name="example-entity"></a>Példa entitás

Ez az entitás csak példaként szolgál. A saját entitások áttelepítése más szempontokat is igényelhet.

Vegye fontolóra a v2 összetett verzióját a következőt használó pizza-`order` módosítására:
* előre elkészített datetimeV2 a szállítási időhöz
* kifejezések listája az egyes szavak, például a pizza, a pite, a kéreg és a feltöltések növeléséhez
* entitások listázása az olyan feltöltések észleléséhez, mint például a gombák, az olajbogyók, a pepperoni.

Erre az entitásra például a következőt kell kimondani:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|`Order` nevű szülő-összetevő entitás|Szülő – gép – megtanult entitás `Order`|
|Gyermek előre elkészített datetimeV2|* Az előre elkészített entitás migrálása új alkalmazásba.<br>* Az előre elkészített datetimeV2 esetében adja meg a megkötést a szülőnél.|
|Gyermek-lista entitás a feltöltésekhez|* A List entitás migrálása új alkalmazásba.<br>* Ezután vegyen fel egy korlátozást a szülőhöz a lista entitáshoz.|


## <a name="migrate-v2-hierarchical-entity"></a>A v2 hierarchikus entitás migrálása

A v2-authoring szolgáltatásban egy hierarchikus entitás lett megadva a LUIS-ben meglévő szerepkörök előtt. Mindkettő ugyanazt a célt szolgálja, hogy a környezet használata alapján kinyerje az entitásokat. Ha hierarchikus entitásokkal rendelkezik, a szerepköröket egyszerű entitásként tekintheti meg.

A v3 létrehozásakor:
* A rendszer a gépi megtanult (szülő) entitáson is alkalmazhat szerepkört.
* Egy szerepkör nem alkalmazható alösszetevőkre.

Ez az entitás csak példaként szolgál. A saját entitások áttelepítése más szempontokat is igényelhet.

Vegye fontolóra a v2 hierarchikus entitást a pizza `order`módosításához:
* ahol az egyes gyermekek az eredeti vagy a végső feltöltést határozzák meg

Erre az entitásra például a következőt kell kimondani:

`Change the topping from mushrooms to olives`

Az alábbi táblázat az áttelepítést mutatja be:

|V2 modellek|V3 modellek|
|--|--|
|`Order` nevű szülő-összetevő entitás|Szülő – gép – megtanult entitás `Order`|
|Gyermek-hierarchikus entitás eredeti és utolsó pizza-feltöltéssel|* Adja hozzá a szerepkört az egyes feltöltésekhez `Order`hoz.|

## <a name="next-steps"></a>Következő lépések

* [Fejlesztői erőforrások](developer-reference-resource.md)
