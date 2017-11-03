## <a name="repeatability-during-copy"></a>Ismételhetőség másolása során
Ha a Másolás a kezdő és a relációs tároló kell ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. 

A szelet is futtatható automatikusan az Azure Data Factory megadott újrapróbálkozási házirend szerint. Azt javasoljuk, hogy átmeneti hibák ellen védelmet újrapróbálkozási házirendje. Ezért az ismételhetőség az adatátvitel során végrehajtására fontos eleme. 

**Forrásként:**

> [!NOTE]
> A következő mintákat az Azure SQL, de bármilyen adattároló, amely támogatja a téglalap alakú adatkészletek vonatkoznak. Előfordulhat, hogy úgy, hogy a **típus** forrás- és a **lekérdezés** tulajdonság (például: lekérdezés helyett sqlReaderQuery) az adatok tárolásához.   
> 
> 

Általában relációs áruházakból olvasásakor célszerű lehet olvasni a csak az, hogy a szelet megfelelő adatokat. Ehhez úgy lenne a rendelkezésre álló WindowStart és WindowEnd változókat az Azure Data Factory használatával. Olvassa el a változók és az Azure Data Factory Itt a függvényt a [ütemezés és a végrehajtás](../articles/data-factory/v1/data-factory-scheduling-and-execution.md) cikk. Példa: 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Ez a lekérdezés adatokat olvas a "MyTable", amely a szelet időtartama közé esik. Futtassa újból a szeletet a is minden esetben gondoskodjon arról, hogy ez a viselkedés. 

Más esetekben Kezdésként érdemes lehet olvasni a teljes táblát (tegyük fel, hogy csak egy alkalommal helyezze át a) és a következőképpen határozhatnak meg a sqlReaderQuery:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```
