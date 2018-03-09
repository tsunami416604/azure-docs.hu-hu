>[!NOTE]
>A nem rögzített erőforrások esetében kérvényezheti a kvóták emelését egy támogatási jegy megnyitásával. **Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy előfizetéshez tartozó Azure Media Services- (AMS-) fiókok | 25 (rögzített) |
| Media szolgáltatás számára fenntartott egységek AMS-fiókonként |25 (S1)<br/>10 (S2, S3) <sup>(1)</sup> | 
| Feladatok AMS-fiókonként | 50,000<sup>(2)</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Objektumok AMS-fiókonként | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>(4)</sup> |
| Élő csatornák AMS-fiókonként |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| Futó állapotú streamvégpontok AMS-fiókonként|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 1000<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>(6)</sup> |
| Fájlméret| Bizonyos esetekben korlátozva van a Media Services feldolgozás támogatott maximális fájlméretet. <sup>7</sup> |
  
<sup>1</sup> Ha módosítja a típusának (például a S1 S2), a maximális RU korlátok állnak vissza.

<sup>2</sup> Ez a szám a várólistás, a befejeződött, az aktív, illetve a megszakított feladatokat is magában foglalja. A törölt feladatokat nem tartalmazza. A régi feladatokat az **IJob.Delete** vagy a **DELETE** HTTP-kérés segítségével lehet törölni.

2017. április 1. frissítésétől bármely feladat rekord 90 napnál régebbi fiókja automatikusan törlődik, a társított feladat bejegyzéseket, valamint akkor is, ha a rekordok száma nem éri el a kvóta felső határát. A feladatok/műveletek adatainak archiválásához használhatja az [itt](../articles/media-services/media-services-dotnet-manage-entities.md) ismertetett programkódot.

<sup>3</sup> amikor kérést egy listához feladat entitások, legfeljebb 1000 feladatot kérelmenként adja vissza. Ha az összes elküldött feladatot nyomon szeretné követni, ezt a top/skip (felső korlát/kihagyás) beállítás segítségével teheti meg az [OData rendszerlekérdezési beállításait](http://msdn.microsoft.com/library/gg309461.aspx) ismertető cikkben leírtak szerint.

<sup>4</sup> A keresőket nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat. További információ [itt](../articles/media-services/media-services-content-protection-overview.md) érhető el.

<sup>5</sup> A tárfiókoknak ugyanahhoz az Azure-előfizetéshez kell tartozniuk.

<sup>6</sup> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket stb. használja, a szabályzatazonosítónak is ugyanannak kell lennie. További információkért és példákért lásd [ezt](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) a szakaszt.

<sup>7</sup>Ha feltölteni kívánt tartalmat egy eszközhöz, az Azure Media Services feldolgozni a szolgáltatás a média processzorok egyikével (Ez azt jelenti, például a Media Encoder Standard és a Media Encoder prémium munkafolyamat vagy az analysis motorok kódolók, például Arcfelismerési érzékelő), Ezután a támogatott maximális fájlméret korlátozásaival tisztában kell lennie. 

Egy BLOB támogatott maximális méretnél jelenleg az Azure Blob Storage legfeljebb 5 TB. Azonban további korlátok alapján a Virtuálisgép-méretek a szolgáltatás által használt Azure Media Services alkalmazni. Az alábbi táblázat a korlátok egyes az adathordozó fenntartott egységek (S1, S2, S3.) Ha a forrásfájl nagyobb, mint a tábla meghatározott korlátozásokat, a kódolási feladat sikertelen lesz. Ha a 4 KB-os feloldási források hosszú időtartam kódolni, meg kell S3 Media fenntartott egységek használja a szükséges teljesítmény eléréséhez. Ha 4 KB-os tartalom, amely nagyobb, mint 260 GB vonatkozó korlátozást a S3 Media fenntartott egységek, lépjen velünk kapcsolatba amshelp@microsoft.com a lehetséges megoldást a támogatásához.

| Media szolgáltatás számára fenntartott egység típusa | Maximális bemeneti méret (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
