<properties
   pageTitle="建立資料服務的市場指南 |Microsoft Azure"
   description="如何建立、認證和部署資料服務供他人在 Azure Marketplace 上購買的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="12/06/2015"
      ms.author="hascipio; avikova" />

# 透過 CSDL 將現有的 Web 服務對應至 OData

本文提供如何使用 CSDL 來將現有的服務對應至 OData 相容服務的概觀。 其中將說明如何建立對應文件 (CSDL)，透過服務呼叫轉換來自用戶端的輸入要求，並透過 OData 相容的摘要將輸出 (資料) 傳輸回用戶端。 Microsoft Azure Marketplace 會使用 OData 通訊協定，向使用者公開服務。 內容提供者 (資料擁有者) 所公開的服務會以各種不同形式 (例如 REST、 SOAP 等) 來公開。

## 什麼是 CSDL 及其結構？
CSDL (概念結構定義語言) 是一項規格，會定義如何使用常見的 XML 用語來說明對於 Azure Marketplace 的 Web 服務或資料庫服務。

簡單概觀 **要求流程:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

 **資料流程** 是以相反的方向:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**[圖 1** 圖表如何用戶端會從取得資料內容的提供者 (服務) 透過 Azure Marketplace。  對應/轉換元件會使用 CSDL 來處理內容提供者的服務和要求用戶端之間的要求和資料傳遞。

*圖 1：透過 Azure Marketplace，從要求用戶端到內容提供者的詳細流程*

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

如需有關 Atom 背景，Atom Pub 和 OData 通訊協定的 Marketplace</延伸模組建置時，請檢閱: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

摘錄 from above 連結:
    *「 開放式資料通訊協定 (以下稱為 OData) 的目的是提供 CRUD 樣式作業 (建立、 讀取、 更新和刪除) 對資源公開為資料服務以 REST 為基礎的通訊協定。「資料服務」是一個端點，其中含有公開自一或多個「集合」的資料，每個集合均包含零到多個「項目」，這些項目是由具類型的具名值組所組成。OData 是 Microsoft 基於 OASIS (Organization for the Advancement of Structured Information Standards) 標準所發佈，因此，任何有需要的人都能建置伺服器、用戶端或工具，而沒有任何權利金或限制。」*

### 以下是三個必須由 CSDL 定義的關鍵部分：

-  **端點** 服務提供者
  服務的 Web 位址 (URI)
-  **資料參數** 傳遞做為服務提供者的輸入
  傳送至內容提供者服務下的資料類型的參數定義。
- **結構描述** 要求服務所傳回的資料
  內容提供者的服務，包括容器、 集合/資料表、 變數/資料行和資料型別所傳遞的資料結構描述。

下圖顯示從用戶端輸入 OData 陳述式 (呼叫內容提供者的 Web 服務) 到取回結果/資料的流程概觀。

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### 步驟：

1. 用戶端透過使用 XML 中定義的輸入參數填入的服務呼叫，將要求傳送到 Azure Marketplace
2. CSDL 可用來驗證服務呼叫。
    - 格式化的服務呼叫接著會由 Azure Marketplace 傳送到內容提供者服務
3. Web 服務會執行並 preforms Http 動詞命令的動作 (亦即 GET)
  將資料傳回至要求的資料 (如果有的話) 的公開給用戶端 XML 格式的 Azure Marketplace 使用 CSDL 中定義的對應。
4. 用戶端會以 XML 或 JSON 格式傳送資料 (如果有的話)

## 定義

### OData ATOM Pub

ATOM Pub 的延伸模組，其中每個項目代表結果集的一個資料列。 項目的內容部分已增強來包含資料列的值 - 做為索引鍵值組。 在這裡找到更多的資訊:
[https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL - 概念結構定義語言

允許定義透過資料庫公開的函式 (SPROC) 和實體。 詳細資訊，請參閱: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] 按一下 [ **其他版本** 下拉式清單中選取的版本，如果您沒有看到文件。

### EDM - 項目資料模型
- 概觀: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink]
[OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- 預覽: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink]
[PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- 資料類型: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink]
[DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

下圖顯示從用戶端輸入 OData 陳述式 (呼叫內容提供者的 Web 服務) 到取回結果/資料的詳細流程 (從左至右)：

  ![繪圖](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## CSDL 基本概念

CSDL (概念結構定義語言) 是一項規格，會定義如何使用常見的 XML 用語來說明對於 Azure Marketplace 的 Web 服務或資料庫服務。 CSDL 描述重要 pieces， **可傳遞的資料來源的資料至 Azure Marketplace。**主要部分如下所示：

- 說明所有對外公開使用之函式 (FunctionImport 節點) 的介面資訊
- 適用於所有訊息要求 (輸入) 和訊息回應 (輸出) (EntityContainer/EntitySet/EntityType 節點) 的資料類型資訊
- 關於要使用之傳輸通訊協定 (標頭節點) 的繫結資訊
- 可用以尋找指定服務 (BaseURI 屬性) 的位址資訊

簡單來說，CSDL 表示在服務要求者和服務提供者之間與平台及語言無關的合約。 使用 CSDL，用戶端可以找到 Web 服務/資料庫服務，並叫用任何其公開可用的函式。

### 將 CSDL 關聯至資料庫或集合
**CSDL 規格**

CSDL 是說明 Web 服務的 XML 文法。 規格本身分成 4 個主要元素: EntitySet、 FunctionImport;命名空間和 EntityType。

若要讓這個抽象層更容易了解，可讓 CSDL 關聯至資料表。

請記住；

  CSDL 表示之間的平台和語言無關合約 **服務要求者** 和 **服務提供者**。 使用 CSDL、 **用戶端** 可以找到 **web 服務/資料庫服務** 和叫用其公開可用任何 **函式。**

針對資料服務，可從資料庫、資料表、資料行及預存程序等方面來理解 CSDL 的四個部分。

針對資料服務，可使用如下方式來關聯這些項目：

- EntityContainer ~ = 資料庫
- EntitySet ~ = 資料表
- EntityType ~ = 資料行
- FunctionImport ~ = 預存程序

**允許的 HTTP 動詞命令**
- 取得 – 從 db (傳回的集合) 的傳回值
- POST – 用來傳遞從 db (建立新的項目在集合中，傳回的識別碼 URI) 的資料和選擇性的傳回值
- 刪除 – 刪除的資料庫 (刪除集合)
- 將 – 更新到資料庫的資料 (取代集合或建立一個)

## 中繼資料/對應文件

中繼資料/對應文件可用來對應內容提供者的現有 Web 服務，讓它能夠透過 Azure Marketplace 系統公開為 OData Web 服務。 它會以 CSDL 為基礎，並實作數個 CSDL 延伸模組，以滿足透過 Azure Marketplace 公開之以 REST 為基礎的 Web 服務需求。 擴充功能中找到 [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) 命名空間。

CSDL 的範例如下: (複製和貼上下列範例 CSDL 中的 XML 編輯器和變更，以符合您的服務。  然後貼到 CSDL 對應 DataService] 索引標籤底下建立您的服務中時  [Azure Marketplace 發佈入口網站](https://publish.windowsazure.com))。

**詞彙:**
關於 CSDL 詞彙至 [發佈入口網站](https://publish.windowsazure.com) UI (PPUI) 條款。
- 提供 「 Title 」 在 PPUI 與 MyWebOffer
- 在 PPUI MyCompany 與 **發行者顯示名稱** 中 [Microsoft 開發人員中心](http://dev.windows.com/registration?accountprogram=azure) UI
- 您的 API 與 Web 或資料服務 (在 PPUI 計劃)

**階層:**
  (內容提供者) 的公司擁有 Offer(s) 的方案，也就是 service(s) 總哪一行的 api。

### WebService CSDL 範例

連接至公開 Web 應用程式端點 (例如 C# 應用程式) 的服務

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] 檢視文件中的更多的 CSDL Web 服務範例 [範例對應現有的 web 服務以透過 CSDLs OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###DataService CSDL 範例

連接至資料庫資料表或檢視做為端點所公開的服務
以下範例顯示兩個基底資料的 Api 基礎 API CSDL (可以使用檢視，而非資料表)。

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## 另請參閱
- 如果您有興趣學習和了解特定節點和其參數，請閱讀這篇文章 [資料服務 OData 對應節點](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) 的定義和說明、 範例和使用案例的內容。
- 如果您有興趣檢閱範例，請閱讀這篇文章 [資料服務 OData 對應範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 查看範例程式碼，並了解程式碼語法與內容。
- 若要返回發佈至 Azure Marketplace 資料服務的指定路徑，請閱讀本文 [資料服務的發行指導](marketplace-publishing-data-service-creation.md)。

