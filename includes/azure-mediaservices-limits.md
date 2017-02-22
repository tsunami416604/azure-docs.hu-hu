>[!NOTE]
>A nem rögzített erőforrások esetében kérvényezheti a kvóták emelését egy támogatási jegy megnyitásával. **Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy előfizetéshez tartozó Azure Media Services- (AMS-) fiókok | 25 (rögzített) |
| Media szolgáltatás számára fenntartott egységek AMS-fiókonként |25 (S1, S2)<br/>10 (S3) <sup>1</sup> | 
| Feladatok AMS-fiókonként | 50,000<sup>2</sup> |
| Kapcsolt műveletek feladatonként | 30 (rögzített) |
| Objektumok AMS-fiókonként | 1,000,000|
| Objektumok műveletenként | 50 |
| Objektumok feladatonként | 100 |
| Egy objektumhoz egyszerre társított egyedi keresők | 5<sup>4</sup> |
| Élő csatornák AMS-fiókonként |5|
| Leállított állapotú programok csatornánként |50|
| Futó állapotú programok csatornánként |3|
| Futó állapotú streamvégpontok AMS-fiókonként|2|
| Streamelési egységek streamvégpontonként |10 |
| Tárfiókok | 1000<sup>5</sup> (rögzített) |
| Házirendek | 1,000,000<sup>6</sup> |
| Fájlméret| Bizonyos esetekben a Media Services által feldolgozható maximális támogatott fájlméret korlátozva van. <sup>7</sup> |
  
<sup>1</sup> Az S3 fenntartott egységek Nyugat-Indiában nem érhetők el.

<sup>2</sup> Ez a szám a várólistás, a befejeződött, az aktív, illetve a megszakított feladatokat is magában foglalja. A törölt feladatokat nem tartalmazza. A régi feladatokat az **IJob.Delete** vagy a **DELETE** HTTP-kérés segítségével lehet törölni.

2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. A feladatok/műveletek adatainak archiválásához használhatja az [itt](../articles/media-services/media-services-dotnet-manage-entities.md) ismertetett programkódot.

<sup>3</sup> A Feladat entitások listázásakor kérésenként maximum 1000 entitás jeleníthető meg. Ha az összes elküldött feladatot nyomon szeretné követni, ezt a top/skip (felső korlát/kihagyás) beállítás segítségével teheti meg az [OData rendszerlekérdezési beállításait](http://msdn.microsoft.com/library/gg309461.aspx) ismertető cikkben leírtak szerint.

<sup>4</sup> A keresőket nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat. További információ [itt](../articles/media-services/media-services-content-protection-overview.md) érhető el.

<sup>5</sup> A tárfiókoknak ugyanahhoz az Azure-előfizetéshez kell tartozniuk.

<sup>6</sup> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. 

>[!NOTE]
> Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket stb. használja, a szabályzatazonosítónak is ugyanannak kell lennie. További információkért és példákért lásd [ezt](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies) a szakaszt.

<sup>7</sup> Ha az Azure Media Services használatával tartalmat tölt fel egy objektumba, mert azt a szolgáltatás egyik médiafeldolgozójával szeretné feldolgozni (például a Media Encoder Standard kódolóval, a Media Encoder Premium-munkafolyamattal vagy egy, a Face Detectorhoz hasonló elemző programmaggal), akkor a következő korlátozásokkal kell tisztában lennie. 

| Media szolgáltatás számára fenntartott egység típusa | Maximális fájlméret (GB)| 
| --- | --- | 
|S1    | 325|
|S2    | 640|
|S3    | 260|


<!--HONumber=Feb17_HO2-->


