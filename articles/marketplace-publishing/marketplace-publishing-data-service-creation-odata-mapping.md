---
title: "Egy szolgáltatás számára a piactér létrehozását bemutató útmutatónak |} Microsoft Docs"
description: "Részletes utasításokra van szüksége, hogy hogyan hozhatja létre, hitelesíthet és az adatok szolgáltatás telepítése az Azure piactéren vásárolhat."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Meglévő webszolgáltatás leképezése OData CSDL keresztül
> [!IMPORTANT]
> **Jelenleg dolgozunk már nem bevezetési bármely új adatszolgáltatás közzétevők. Új dataservices nem get jóváhagyott listaelem.** Ha egy SaaS-üzleti AppSource a közzétenni kívánt alkalmazás további információt talál [Itt](https://appsource.microsoft.com/partners). Ha egy infrastruktúra-szolgáltatási alkalmazások vagy fejlesztői szolgáltatás szeretne közzétenni az Azure piactérről, további információt talál [Itt](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Ez a cikk áttekintést nyújt a CSDL meglévő szolgáltatás hozzárendelése egy OData-kompatibilis szolgáltatás használatával. Ismerteti, hogyan a leképezési dokumentum (CSDL) létrehozásához, amely átalakítja a bemeneti kérelmet az ügyfél a szolgáltatás híváson keresztül, és az adatcsatorna keresztül egy kompatibilis OData-ügyfél vissza a kimeneti (adatok). A Microsoft Azure piactér a végfelhasználóknak történő közzététele az OData protokoll használatával. Tartalomszolgáltatók (adatok tulajdonosai) által feltárt szolgáltatások érhetők el a többféle formában, mint például a többi, SOAP, stb.

## <a name="what-is-a-csdl-and-its-structure"></a>Mi az a CSDL és struktúrája?
Egy CSDL (Fogalmi séma Definition Language) a webszolgáltatás vagy az adatbázis-szolgáltatás közös XML szóhasználatára hasonlítanak, az Azure piactéren leírása definiáló specifikációval.

– Sematikus ábra a **kérelem folyamata:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

A **adatfolyama** az ellenkező irányba van:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**1. ábra** ábrázolja, hogyan ügyfél volna a szerezhet a tartalomszolgáltató (a szolgáltatás) által az Azure piactéren keresztül.  A CSDL a leképezés vagy átalakítási összetevő használja kezelni a kérést, és adatokat továbbítani a tartalomszolgáltató Services-szolgáltatás(ok) és a kérést küldő ügyfél között.

*1. ábra: Részletes adatfolyam-kérelmező ügyfélről az Azure piactéren keresztül tartalomszolgáltató*

  ![rajz](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Az Atom a háttérben, Atom Pub, és az OData protokoll, amelyre az Azure piactér bővítmények összeállítása, tekintse át: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Hivatkozás promptjai kivonata: *"az Open Data protokoll (a továbbiakban: OData) célja egy REST-alapú protokoll számára történő CRUD-stílusú műveleteket (létrehozás, Olvasás, frissítés és törlés) adatszolgáltatások elérhető erőforrásokon. A "data"szolgáltatás"" végpont ahol van egy vagy több "gyűjtemények" minden nulla vagy több "bejegyzés", álló származó adatokat adta-e nevű-érték párokat. OData által közzétett Microsoft OASIS (a fizetési a strukturált adatokat szabványok szervezet) szabványok szerint, hogy bárki szeretné hozhat létre, kiszolgálók, ügyfelek vagy eszközök jogdíjak és korlátozások nélkül."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Három kritikus, amelyek a CSDL által meghatározott van:
* A **végpont** , a szolgáltatás szolgáltató a webes cím (URI) szolgáltatás
* A **Eseményadat-paraméterek** bemenetként a szolgáltató átadta a paraméterek az adattípus le a tartalomszolgáltató szolgáltatásnak küldött a definíciók.
* **Séma** ad vissza a kért szolgáltatás kézbesíti, a tartalomszolgáltató szolgáltatás a Adatséma adatok, beleértve a tároló, a gyűjtemények, illetve táblákat, a változók/oszlopok és az adatok típus.

Az alábbi ábra a folyamatot, ahol az ügyfél belép a eredmények letöltésével vissza az OData-utasítás (meghívásához a tartalomszolgáltató webszolgáltatás) a áttekintését mutatja.

  ![rajz](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>lépéseket:
1. Ügyfél küld szolgáltatás híváson keresztül kész, de az Azure piactéren XML definiált bemeneti paraméterek
2. CSDL a szolgáltatás hívást ellenőrzésére szolgál.
   * A formázott szolgáltatás hívása küldi el a rendszer a tartalom szolgáltatók szolgáltatás által az Azure piactéren
3. A webszolgáltatás hajtja végre, és előforma tartozó műveletet a Http-műveletet (pl. GET) a visszaküldött adatok az Azure piactéren, ahol a kért adatok (ha vannak) az XML formátumban, az ügyfél tesz elérhetővé a CSDL meghatározott leképezés használatával.
4. Az ügyfélnek küldött adatok (ha van ilyen) XML- vagy JSON formátumban

## <a name="definitions"></a>Meghatározások
### <a name="odata-atom-pub"></a>OData ATOM pub
Az ATOM pub, ahol minden bejegyzésben eredményeként a több sorban is bővítménye, állítsa be. A bejegyzés a tartalom részét magában foglalja az értékeket, sor: a kulcs-érték párként továbbfejlesztett. További információt itt talál: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - fogalmi séma adatdefiníciós nyelv
Megadhatja a funkciók (SPROCs) és egy adatbázis keresztül közzétett entitásokat. További információt itt talál: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> Kattintson a **más verzióiban** legördülő lista és a megfelelő verzió kiválasztása, ha nem látja a cikk.
> 
> 

### <a name="edm---entry-data-model"></a>EDM - bejegyzés adatmodell
* Áttekintés: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Kép: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Adattípusok: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Az alábbi látható, a részletes balról jobbra haladjanak az ügyfél kerül, ahol az OData-utasítás (meghívásához a tartalomszolgáltató webszolgáltatás) a eredmények letöltésével biztonsági:

  ![rajz](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>CSDL alapjai
Egy CSDL (Fogalmi séma Definition Language) a webszolgáltatás vagy az adatbázis-szolgáltatás közös XML szóhasználatára hasonlítanak, az Azure piactéren leírása definiáló specifikációval. CSDL ismerteti a kritikus fontosságú, amely darab **adattovábbítás az adatforrásból az Azure piactéren lehetővé teszi.** A fő eleme a dokumentum ismerteti:

* Az összes nyilvánosan elérhető funkciók (FunctionImport csomópont) leíró adatokat csatoló
* Adattípus-információ az összes üzenet requests(input) és üzenet responses(outputs) (EntityContainer/EntitySet készlet/EntityType csomópontok)
* Kötési információ kell lennie az átviteli protokoll használt (fejléc csomópont)
* Címadatok a megadott szolgáltatás (a BaseURI attribútum) megkeresése

A CSDL a ez már a vég, a szolgáltatást lekérdező és a szolgáltató platform - és nyelvfüggetlen szerződés jelöli. A CSDL, egy ügyfél segítségével keresse meg a webszolgáltatás service, illetve az adatbázis és a nyilvánosan elérhető funkciók meghívni.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Egy CSDL vonatkozó egy adatbázist vagy egy gyűjtemény
**A CSDL-specifikációja**

CSDL egy webszolgáltatás-bővítmény leíró XML-nyelvtan. Maga a meghatározás 4 fő elemet oszlik: EntitySet, a következő functionimport elemben; Névtér, és az entitytype típus.

Ez az absztrakció megértéséhez megadható, hogy könnyebben egy tábla egy CSDL vonatkoznak.

Ne feledje;

  CSDL jelöli a platform - és nyelvfüggetlen szerződés között a **szolgáltatás kérelmező** és a **szolgáltató**. CSDL, használja a **ügyfél** keresheti meg a **webes szolgáltatás, illetve az adatbázis-szolgáltatás** és a nyilvánosan elérhető meghívni **funkciók.**

Egy szolgáltatás a négy részből álló egy CSDL-re egy adatbázis, táblázatok, oszlopok és eljárás tekintetében.

Vonatkozó ezek egy szolgáltatás számára az alábbiak szerint:

* EntityContainer ~ adatbázis =
* EntitySet ~ = táblázat
* EntityType ~ oszlopok =
* FunctionImport ~ = tárolt eljárás

**HTTP-műveletek engedélyezett**

* GET-értéket ad vissza értéket az adatbázisából (gyűjteményének beolvasása)
* POST-adatokat és a választható visszatérési értékek adhatók át a db (hozzon létre egy új bejegyzést a gyűjteményben, visszatérési azonosítója vagy URI-) használatával
* TÖRLÉS – törlések adatokat az Adatbázisból (a gyűjtemény törlése)
* PUT – adatok frissítéséhez be egy DB (cserélje ki egy gyűjteményt, vagy hozzon létre egyet)

## <a name="metadatamapping-document"></a>Metaadat-hozzárendelés dokumentum
A metaadat-hozzárendelés dokumentum való hozzárendelésére a tartalomszolgáltató meglévő webszolgáltatások, így akkor is elérhető egy OData webszolgáltatást az Azure piactér rendszer által használt. CSDL alapul, és megvalósít néhány REST szükségleteinek CSDL-bővítmények alapú webszolgáltatások közzétéve az Azure piactéren. A bővítmények találhatók a [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) névtér.

A következő egy példa a CSDL: (másolási és beillesztési műveleteket az alábbi példában CSDL egy XML-szerkesztő és módosítsa a szolgáltatás megfelelően.  Majd illessze be CSDL-leképezési DataService lapon a szolgáltatás létrehozásakor a [Azure piactér közzétételi Portáljára](https://publish.windowsazure.com)).

**Feltételek:** számára a CSDL vonatkozó feltételeket a [közzétételi Portáljára](https://publish.windowsazure.com) felhasználói felület (PPUI) feltételeit.

* Kínálnak a PPUI "Title" MyWebOffer vonatkozik.
* A a PPUI értéket vonatkozik **Publisher megjelenített név** a a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) felhasználói felület
* Az API-vonatkozik egy webes vagy adatok szolgáltatást (egy tervet a PPUI)

**Hierarchia:** egy vállalat (tartalomszolgáltató) tulajdonában lévő esetében, amelyek Plan(s), nevezetesen service(s), mely sor fel egy API-t.

### <a name="webservice-csdl-example"></a>Webszolgáltatás CSDL-példa
Csatlakozik egy szolgáltatáshoz, amely adategyezményt alkalmazza a webes alkalmazás a végpont (például egy C#-alkalmazás)

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
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Tekintse meg a cikk további CSDL-webszolgáltatás példákat [példák leképezése egy meglévő webszolgáltatás segítségével OData CSDLs keresztül](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>DataService CSDL-példa
Egy szolgáltatás, amely adategyezményt alkalmazza adatbázis tábla vagy nézet végpont az alábbi példában látható, két API-k, az alapszintű adatok alapján (használható táblák helyett nézetek) API CSDL csatlakozik.

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

## <a name="see-also"></a>Lásd még:
* Ha érdekli tanulási és az adott csomópont, és a paraméterek ismertetése, olvassa el ebben a cikkben [szolgáltatás OData leképezési Adatcsomópontokat](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) definíciók és magyarázatokat, példák és a nagybetűk használatát a környezetben.
* Ha érdekli példák megtekintésével, olvassa el ebben a cikkben [adatok szolgáltatás OData leképezési példák](marketplace-publishing-data-service-creation-odata-mapping-examples.md) mintakód és kód szintaxis és a környezetben.
* Térjen vissza az előírt elérési út egy szolgáltatás-közzététel az Azure piactéren, olvassa el ebben a cikkben [adatok szolgáltatás közzétételi útmutató](marketplace-publishing-data-service-creation.md).

