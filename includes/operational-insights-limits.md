
>[!NOTE]
>A Log Analytics korábban Operational Insights néven volt ismert.
>
>

Az alábbi korlátok vonatkoznak az egyes Log Analytics-erőforrásokra előfizetésenként:

| Erőforrás | Alapértelmezett korlát | Megjegyzések
| --- | --- | --- |
| Ingyenes munkaterületek száma előfizetésenként | 10 | Ez a korlát nem növelhető. |
| Fizetős munkaterületek száma előfizetésenként | N/A | Az erőforráscsoporton belüli erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátozza | 


Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületekre:

|  | Ingyenes | Standard | Prémium | Különálló | OMS |
| --- | --- | --- | --- | --- | --- |
| Naponta összegyűjtött adatmennyiség |500 MB<sup>1</sup> |None |None | None | None
| Adatmegőrzés időtartama |7 nap |1 hónap |12 hónap | 1 hónap<sup>2</sup> | 1 hónap<sup>2</sup>|

<sup>1</sup> Ha az ügyfél eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú.

<sup>2</sup> Az önálló és OMS tarifacsomagok adatmegőrzési időtartama megnövelhető 730 napra.

| Kategória | Korlátok | Megjegyzések
| --- | --- | --- |
| Data Collector API | Az egyedi közzétételek maximális mérete 30 MB<br>A mezőértékek maximális mérete 32 KB | A nagyobb mennyiségeket bontsa több közzétételre<br>A 32 KB-nál hosszabb mezők csonkolva lesznek. |
| Keresési API | 5000 visszaadott rekord a nem összesített adatok esetében<br>500 000 rekord az összesített adatok esetében | Az összesített adatok a `measure` parancsot tartalmazó kereséssel lekért adatok
 


<!--HONumber=Feb17_HO3-->


