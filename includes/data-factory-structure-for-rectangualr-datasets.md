## 指定矩形資料集的結構定義
中的資料集 JSON 的結構區段是 **選擇性** 區段矩形資料表 （含資料列和資料行），並包含資料表的資料行的集合。 結構區段將用於提供類型轉換的類型資訊或執行資料行對應。 下列各節更詳細說明這些功能。 

各資料行包含下列屬性：

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 名稱 | 資料行的名稱。 | 是 |
| 類型 | 資料行的資料類型。 有關何時應指定類型資訊的詳細資訊，請參閱下文類型轉換的部份 | 否 |
| culture | 當有指定 type (類型) 時要使用的 .NET 型文化特性，且是 .NET 類型的 Datetime 或 Datetimeoffset。 預設值為 “en-us”。  | 否 |
| format | 當有指定 type (類型) 時要使用的格式字串，且是 .NET 類型的 Datetime 或 Datetimeoffset。 | 否 |

下列範例顯示具有 userid、name、lastlogindate 三個資料行的資料表的結構區段 JSON。

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

請使用下列指導方針，何時要包括 「 結構 」 的資訊和哪些要包含在 **結構** 一節。

1.  **結構化的資料來源的** ，儲存的資料結構描述和類型以及資料本身 （例如 SQL Server、 Oracle、 Azure 資料表等來源） 的資訊，您應該指定 「 結構 」 區段，只有當您想進行資料行對應至接收器中的特定資料行的特定來源資料行，其名稱都不相同 （請參閱下方的資料行對應區段中的詳細資料）。 

    如上所述，“structure” 區段中的類型資訊是選擇性的。 針對結構化的來源，類型資訊已可使用做為資料存放區中的資料集定義的一部分，因此當您包含 “structure” 區段時不應包含類型資訊。
2. **讀取的資料來源 (尤其是 Azure blob) 的結構描述**  您可以選擇儲存資料但不儲存任何結構描述或型別資訊的資料。 針對這些類型的資料來源，在以下 2 種案例中應包含 “structure”：
    1. 您想要進行對應。
    2. 當資料集是「複製」活動中的來源時，您可以在 “structure” 中提供類型資訊，資料處理站轉換為原生類型以利接收時會使用此類型資訊。 請參閱 [從 Azure Blob 來回移動資料](../articles/data-factory/data-factory-azure-blob-connector.md) 文件，如需詳細資訊。

### 支援 .NET 型的類型 
資料處理站支援下列符合 CLS 標準的 .NET 型類型，以利針對讀取的資料來源 (如 Azure blob) 的結構描述在 “structure” 中提供類型資訊。

- Int16
- Int32 
- Int64
- 單一
- 兩倍
- 十進位
- Byte]
- Bool
- String 
- Guid
- Datetime
- Datetimeoffset
- Timespan 

針對 Datetime 和 Datetimeoffset，您可以選擇性地指定 “culture” 和 “format” 字串以幫助剖析您的自訂 Datetime 字串。 請參閱下方的類型轉換範例。



