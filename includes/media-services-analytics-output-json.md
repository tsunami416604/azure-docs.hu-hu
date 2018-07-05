A feladat egy észlelt, és a nyomon követett arcok kapcsolatos metaadatokat tartalmazó JSON-kimenet fájlt hoz létre. Metaadatok helyét, arcok, valamint a nyomon követheti, hogy egyes jelző face ID szám jelző koordinátákat tartalmaznak. Face ID számok során gyakran fordul elő alaphelyzetbe körülmények között, ha a elülső face elvesztése vagy a keretbe átfedett bizonyos felhasználók több ID első hozzárendelt eredményez.

A kimenet JSON az alábbi elemeket tartalmazza:

### <a name="root-json-elements"></a>Legfelső szintű JSON-elemek

| Elem | Leírás |
| --- | --- |
| verzió: |Ez vonatkozik a Video API verziója. |
| időskálára |"Órajel során végbemenő" a videó másodpercenként. |
| offset |Ez az az idő eltolása az időbélyegekhez. Videó API-k 1.0-s verziójában ez mindig 0 lesz. A jövőben támogatott forgatókönyveket, ez az érték változhatnak. |
| szélesség, kontrasztos |A szélesség és a kimeneti videót keret, képpontban kontrasztos.|
| Képkockasebesség |A videó képkockamásodperc. |
| [töredékek száma](#fragments-json-elements) |A metaadatok nevű töredék különböző részekre van darabolásos fel. Minden részlet egy kezdő, időtartamának, időszakának száma és esemény tartalmazza. |

### <a name="fragments-json-elements"></a>Töredékek JSON-elemek

|Elem|Leírás|
|---|---|
| start |A kezdési idejét az első esemény a "órajelben." |
| időtartam |A "órajelben.", a részlet hossza |
| index | (Vonatkozik az Azure Media Redactor csak) határozza meg az aktuális esemény keret indexét. |
| interval |Az időszak mindegyik esemény bejegyzése a részlet a "órajelben." |
| események |Minden egyes esemény észlelt, és nyomon követett belül időt töltött az arcok tartalmazza. Az események egy tömb. A külső tömb jelöli egy időközt. A belső tömbben 0 vagy több esemény történt ekkor időben áll. Egy üres zárójel [] azt jelenti, hogy nincs arcok észlelt. |
| id |A face a nyomon követett azonosítója. Ez a szám véletlenül változhat, ha egy ARC válik a rendszer nem észleli. Egy adott személy során a teljes videó ugyanazzal az Azonosítóval kell rendelkeznie, de ez az algoritmus (hangelnyelés, stb.) korlátozásai miatt nem lehet garantálni. |
| x, y |A bal felső sarokban X és a face 0,0 és 1,0 határolókeret normalizált méretezési Y koordinátáját. <br/>-X és Y koordináták relatív fekvő mindig, és, így ha egy videó (vagy feje-le, iOS esetén) álló, kell a koordináták transzponálása ennek megfelelően. |
| szélesség, hosszúság |A szélesség és a face 0,0 és 1,0 határolókeret normalizált méretezési magassága. |
| facesDetected |Ez a JSON-eredményeket végén található, és összefoglalja az arcokat, az algoritmus a videó során észlelt száma. Mivel az azonosítók alaphelyzetbe lehet véletlenül, ha egy ARC válik a rendszer nem észleli (például a face megfelelően képernyőt, azonnal keresi ki), ez a szám nem lehet, hogy mindig egyenlő az arcokat a videókban igaz száma. |
