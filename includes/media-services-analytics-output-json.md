A feladat észlelt és a nyomon követett lapok metaadatokat tartalmazó JSON kimeneti fájlt hoz létre. A metaadatok közé tartozik a koordináták helyét, a lapok, valamint az, hogy egyes követését jelző arcfelismerési azonosító számot jelzi. Arcfelismerési azonosítószámát nagyon eséllyel fordulnak elő a elülső arcfelismerési ellopása vagy átfedésben vannak a keretében körülmények alaphelyzetbe néhány első hozzárendelt több azonosítók egyének eredményez.

A kimeneti JSON tartalmazza a következő attribútumokat:

| Elem | Leírás |
| --- | --- |
| Verzió |Ez a videó API verziója vonatkozik. |
| Index | (Ez kizárólag az Azure Media Redactor) határozza meg az aktuális esemény keret indexét. |
| időskálára |A videó másodpercenként "ticks". |
| Az offset |Ez az időbélyegek idő eltolása. Videó API-k 1.0-s verziójában az mindig 0 lesz. A jövőben támogatott forgatókönyveket, ezt az értéket módosíthatja. |
| képkockasebességhez |A videó képkockasebessége. |
| töredék |A metaadatok töredék nevű különböző részekre van darabolásos fel. Minden töredéke a start, a időtartama, a időszakának száma és a esemény (eke) tartalmaz. |
| start |A "ticks" első esemény kezdési idejét. |
| Időtartam |A töredéke a "ticks" hosszát. |
| interval |Mindegyik esemény bejegyzése a töredéke a "ticks" időintervallum. |
| események |Minden esemény észlelt, és adott időtartamig belül követhetők oldalakat tartalmaz. Az események tömbje. A külső tömb egy időköz jelöli. A belső tömb 0 vagy több olyan eseményeket, amelyek ezen a ponton az időben történtek áll. Egy üres zárójel [] azt jelenti, hogy nincs lapok észlelt. |
| id |A rendszer nyomon követett arcfelismerési azonosítója. Ez a szám lehet, hogy akaratlanul is módosíthatja, ha egy lap nem válik. Egy adott személy teljes az általános videó ugyanezzel az Azonosítóval kell rendelkeznie, de ez nem garantálható az algoritmus (hangelnyelés, stb.) a korlátozásai miatt |
| x, y |A bal felső X és Y koordinátáit, valamint a 0,0 és 1,0 határolókeret normalizált méretezési felületen. <br/>-X és Y koordináták százalékosan fekvő mindig, relatív, ha egy álló video (vagy feje-tetejére, iOS esetén), összekapcsolta a koordináták transzponálása ennek megfelelően. |
| szélesség, magassága |A szélességének és magasságának a 0,0 és 1,0 határolókeret normalizált méretezési felületen. |
| facesDetected |Ez a JSON-eredményeket végén található, továbbá összefoglalja, amely az algoritmus a videó során észlelt száma. Mivel az azonosítók állítható alaphelyzetbe akaratlanul is, ha egy lap nem válik (pl. arcfelismerési kerül ki képernyőt, azonnal keres), ez a szám lehet, hogy nem mindig igaz számának egyenlőnek videóban lapokat. |

