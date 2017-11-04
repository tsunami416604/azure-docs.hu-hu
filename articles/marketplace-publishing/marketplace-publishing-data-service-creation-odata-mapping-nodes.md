---
title: "Egy szolgáltatás számára a piactér létrehozását bemutató útmutatónak |} Microsoft Docs"
description: "Részletes utasításokra van szüksége, hogy hogyan hozhatja létre, hitelesíthet és az adatok szolgáltatás telepítése az Azure piactéren vásárolhat."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 8ff76ea21ba684ae2a2afcb74d66b4912d7be053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>A csomópontok séma meglévő webszolgáltatás hozzárendelése CSDL keresztül OData ismertetése
> [!IMPORTANT]
> **Jelenleg dolgozunk már nem bevezetési bármely új adatszolgáltatás közzétevők. Új dataservices nem get jóváhagyott listaelem.** Ha egy SaaS-üzleti AppSource a közzétenni kívánt alkalmazás további információt talál [Itt](https://appsource.microsoft.com/partners). Ha egy infrastruktúra-szolgáltatási alkalmazások vagy fejlesztői szolgáltatás szeretne közzétenni az Azure piactérről, további információt talál [Itt](https://azure.microsoft.com/marketplace/programs/certified/).
>
>

Ez a dokumentum elmagyarázza a csomópont-szerkezet CSDL leképezése egy OData protokoll segítségével. Fontos megjegyezni, hogy jól-e a csomópont struktúra formátumú XML-kódot. Ezért legfelső szintű szülő és gyermek séma az OData-leképezés tervezése során is alkalmazható.

## <a name="ignored-elements"></a>Figyelmen kívül hagyott elemek
Az alábbiakban a magas szintű CSDL elemek (XML-csomópontnak), amelyek nem kerülnek a webszolgáltatás metaadatok importálása során az Azure piactér háttéralkalmazása által használandó. Akkor használható, de figyelmen kívül hagyja.

| Elem | Hatókör |
| --- | --- |
| Elem használatával |A csomópont, a sub csomópontok és az összes attribútum |
| Documentation elem |A csomópont, a sub csomópontok és az összes attribútum |
| ComplexType |A csomópont, a sub csomópontok és az összes attribútum |
| Társítás elem |A csomópont, a sub csomópontok és az összes attribútum |
| A bővített tulajdonság |A csomópont, a sub csomópontok és az összes attribútum |
| EntityContainer |Csak a következő attribútumok figyelmen kívül lesznek hagyva: *bővíti* és *AssociationSet* |
| Séma |Csak a következő attribútumok figyelmen kívül lesznek hagyva: *Namespace* |
| FunctionImport |Csak a következő attribútumok figyelmen kívül lesznek hagyva: *mód* (alapértelmezett értéke ln feltételezve) |
| EntityType |Csak az alábbi sub csomópontot a rendszer figyelmen kívül hagyja: *kulcs* és *PropertyRef* |

A következő változásait ismerteti (fel, és figyelmen kívül hagyja az elemek) számára a különböző CSDL XML-csomópontnak részletesen.

## <a name="functionimport-node"></a>FunctionImport csomópont
Egy FunctionImport csomópont egy URL-címe (belépési pont), amely elérhetővé teszi a szolgáltatás a végfelhasználó számára jelöli. A csomópont lehetővé teszi, hogy az URL-cím címzett hogyan leíró, mely paraméterek érhetők el a végfelhasználó és hogy ezek a paraméterek találhatók.

Ez a csomópont adatait találhatók a(z) [itt][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)

Az alábbiakban a további attribútumok (vagy attribútumok kiegészítéseit), amelyek ki vannak téve a következő functionimport elemben csomópont:

**d:BaseUri** -piactéren elérhetővé REST-erőforrás az URI-sablonja. A piactér a sablon a REST-alapú webszolgáltatás lekérdezések összeállításához. Az URI a sablonban {parameterName} formájában paraméterek helyőrzőit parameterName esetén a paraméter neve. Példa apiVersion = {apiVersion}.
Paraméterek jelennek meg az URI-paraméterek vagy az URI elérési út egy része számára engedélyezett. Az elérési út megjelenését esetén azok megadása mindig kötelező (nem jelölhető meg a nullázható). *Példa:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Név** -az importált függvény neve.  Nem lehet ugyanaz, mint más definiált nevek a CSDL megadott.  Példa Name = "GetModelUsageFile"

**EntitySet** *(nem kötelező)* – Ha a függvény entitástípusok, a értékének gyűjteményének beolvasása az **EntitySet** az entitás értékre kell állítani a gyűjtemény tartozik. Ellenkező esetben a **EntitySet** attribútum nem használható. *Példa:*`EntitySet="GetUsageStatisticsEntitySet"`

**A returnType típust** *(nem kötelező)* -URI-azonosítóval visszaadott elemek típusát határozza meg.  Ne használja ezt az attribútumot, ha a függvény nem ad vissza értéket. A támogatott típusok a következők:

* **Gyűjtemény (<Entity type name>)**: Adja meg a definiált entitástípusok gyűjteménye. A név megtalálható-e a EntityType csomópont Name attribútuma. Példa: a gyűjtemény (WXC. HourlyResult).
* **Nyers (<mime type>)**: Adja meg a nyers dokumentum/blob, amely a felhasználó küld vissza. Példa van Raw(image/jpeg) más példák:

  * ReturnType="Raw(text/plain)"
  * A returnType típust = "gyűjtemény (sage. DeleteAllUsageFilesEntity) "*

**d:Paging** -határozza meg, hogyan kezeli a lapozás a REST-erőforrás. A paraméterértékek használt belül kapcsos ágazatok, pl. lapon = {$page} & az elemek laponkénti száma = {$size} elérhető lehetőségek a következők:

* **Nincs:** lapozás nem érhető el
* **Kihagyás:** lapozást egy logikai "skip" és "hajtsa végre a megfelelő" (felső) van megadva. Kihagyás M elemek egyszer, és hajtsa végre a megfelelő majd a következő N elemeket adja vissza. Paraméter értéke: $skip
* **Hajtsa végre a megfelelő:** hajtsa végre a következő N elemeket adja vissza. Paraméter értéke: $take
* **A pageSize értékének:** lapozás van kifejezve a logikai mérete és (oldalankénti elemek) keresztül. Lap az aktuális oldalon visszaadott jelöli. Paraméter értéke: $page
* **Méret:** méretét az összes lapon visszaküldött elemek számát jelenti. Paraméter értéke: $size

**d:AllowedHttpMethods** *(nem kötelező)* -határozza meg, melyik műveletet végzi el a REST-erőforrás. Emellett korlátozza elfogadott művelet a megadott érték.  Alapértelmezett = POST.  *Példa:* `d:AllowedHttpMethods="GET"` elérhető lehetőségek a következők:

* **GET:** általában ad vissza adatokat
* **POST:** általában segítségével új adatok beszúrása
* **PUT:** általában használt adatok frissítése
* **TÖRLÉS:** adatok törlése

További gyermekcsomópontok (nem fedi le a CSDL-dokumentáció) a következő functionimport elemben csomóponton belül van:

**d:RequestBody** *(nem kötelező)* -a kérés törzsében meghatározására szolgál, hogy a kérelem vár küldendő törzs. A kérelem törzsében belül paraméterek adható meg. Vannak megadva kapcsos zárójelek, pl. {parameterName}. Ezek a paraméterek a tartalomszolgáltató szolgáltatásnak továbbított törzsébe felhasználói bemeneti képezi le. A requestBody elemnek neve httpMethod attribútumot. Az attribútum lehetővé teszi, hogy a két érték:

* **POST:** használja, ha a kérelem HTTP-KÖZZÉTÉTELLEL
* **GET:** használja, ha a kérelem HTTP GET

    Példa:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** és **d:Namespace** – Ez a csomópont az XML-tartalomban (URI-endpoint) függvényimport által visszaadott meghatározott névterek ismerteti. A háttérrendszer által visszaadott XML-névterek különbséget tenni a tartalmat, visszaadott tetszőleges számú is tartalmazhatnak. **Összes ezek névterek d:Map vagy d:Match XPath lekérdezések esetén kell szerepelnie.** A d:Namespaces csomópont d:Namespace csomópontok egy set/listáját tartalmazza. A háttér-szolgáltatás válasza szerepel egy névtér azok listája. Az attribútum a d:Namespace csomópont a következők:

* **d:prefix:** az előtagot a névtérhez, XML által adott eredmények a szolgáltatást, pl. f:FirstName, f:LastName, ahol f az előtag látható módon.
* **d:URI:** a névtér, az eredmény dokumentumban használt a teljes URI-címe. Az előtag futásidőben a feloldott értéket képviseli.

**d:ErrorHandling** *(nem kötelező)* – Ez a csomópont tartalmazza a hibakezelés feltételeit. A feltételek mindegyikének a eredményeknél a tartalomszolgáltató szolgáltatás által visszaadott van hitelesítve. Ha egy feltétel egyezik a javasolt HTTP-hibakódot a végfelhasználó egy hibaüzenetet küld vissza.

**d:ErrorHandling** *(nem kötelező)* és **d:Condition** *(nem kötelező)* -feltétel csomópont egy feltétel, amely be van jelölve, a a tartalomszolgáltató szolgáltatás által visszaadott eredmény tartalmazza. Az alábbiakban a **szükséges** attribútumok:

* **d:Match:** az XPath-kifejezés, amely ellenőrzi, hogy egy adott csomópont-érték megtalálható-e a tartalomszolgáltató kimenetneve XML. Az XPath vonatkozóan a kimeneti fut, és IGAZ, ha a feltétel egy egyezés, és hamis értéket, ellenkező esetben kell visszaadnia.
* **d:HttpStatusCode:** kell által visszaadott piactér abban az esetben, ha a feltétel a HTTP-állapotkód: megegyezik. Piactér signalizes a felhasználó leállította a HTTP-állapotkódok hibákat. A HTTP-állapotkódok listáját http://en.wikipedia.org/wiki/HTTP_status_code webhelyen érhetők el
* **d:errorMessage:** lett visszaadva – a HTTP-állapotkód: – a végfelhasználó hibaüzenetet. A felhasználóbarát hibaüzenet, amely nem tartalmazza a titkos kulcsok legyen.

**d:Title** *(nem kötelező)* -lehetővé teszi, hogy a cím, a függvény leíró. A cím értékét származik

* A választható térkép attribútum (xpath), amely meghatározza, hogy hol található a szolgáltatási kérelem válasza a cím.
* – Vagy – a cím, a csomópont megadva.

**d:Rights** *(nem kötelező)* -függvény kapcsolódó jogosultságok (pl. szerzői jogi). Az érték a jogok származik:

* A választható térkép attribútum (xpath) adja meg, hol található a jogosultságokat a szolgáltatási kérelem által visszaadott válaszban.
* – Vagy – a megadott értéknek a csomópont jogosultságok.

**d:Description** *(nem kötelező)* - egy rövid leírást a függvény. A leírás érték származik

* A választható térkép attribútum (xpath), amely megadja, hogy a szolgáltatási kérelem válasza a leírása található.
* – Vagy – a leírása a csomópont megadva.

**d:EmitSelfLink** - *lásd a fenti "A visszaadott adatok"lapozást"FunctionImport" – példa*

**d:EncodeParameterValue** -OData választható bővítménye

**d:QueryResourceCost** -OData választható bővítménye

**d:Map** -OData választható bővítménye

**d:Headers** -OData választható bővítménye

**d:Headers** -OData választható bővítménye

**d:value** -OData választható bővítménye

**d:HttpStatusCode** -OData választható bővítménye

**d:errorMessage** -OData választható bővítménye

## <a name="parameter-node"></a>A paraméter csomópont
Ebben a csomópontban jelöl egy paraméter, amely az URI-sablon részét képező / kérése a következő functionimport elemben csomópontban megadott törzsében.

A "Paraméter elem" csomópont nagyon hasznos részleteket dokumentumoldal a következő címen található [Itt](http://msdn.microsoft.com/library/ee473431.aspx) (használja a **egyéb verzió** legördülő menü eltérő verziójú válassza, ha szükséges, a dokumentáció megtekintése). *Példa:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Paraméter-attribútumhoz | Szükséges | Érték |
| --- | --- | --- |
| Név |Igen |A paraméter neve. Kis-és nagybetűket!  A BaseUri nagybetű. **Példa:**`<Property Name="IsDormant" Type="Byte" />` |
| Típus |Igen |A paraméter típusa. Az értéknek kell lennie egy **EDMSimpleType** vagy összetett típus, amely a modell hatókörén belül van. További információkért tekintse meg a "6 támogatott paramétere/paraméter tulajdonsága típusok".  (Kis-és nagybetűket! Első karakter nagybetűssé, rest kisbetű.)  Is láthatja, [fogalmi modell típusa (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx). **Példa:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mód |Nem |**A**, lejárt, vagy be/ki attól függően, hogy a paraméter egy bemeneti, kimeneti vagy bemeneti/kimeneti paraméter. (Csak "az IN" érhető el az Azure piactéren.) **Példa:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength |Nem |Az engedélyezett maximális hosszúság paraméter. **Példa:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Pontosság |Nem |A paraméter pontosságát. **Példa:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Méretezés |Nem |A skála paraméter. **Példa:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

A CSDL-előírásoknak megfelelően hozzáadott attribútumokat a következők:

| Paraméter-attribútumhoz | Leírás |
| --- | --- |
| **d:Regex** *(nem kötelező)* |A bemeneti érték a paraméterhez érvényesítéséhez használt reguláris kifejezéssel nyilatkozat. Ha a bemeneti érték nem egyezik meg a kivonat értéke elutasítva. Ez lehetővé teszi is-készletet adjon meg a lehetséges értékek, például ^ [0-9] +? $, hogy csak számokat. **Példa:** "< paraméternév ="name"mód ="A"típus"Karakterlánc"-d =: nullázható ="false"d:Regex =" ^ [a-zA-Z] * $"d:Description ="A név nem tartalmazhat szóközt vagy nem alfanumerikus nem angol karakterek"d:SampleValues ="George |
| **d:enum** *(nem kötelező)* |A cső elválasztott értékekből a paraméter érvényes. A definiált típusnak paraméter egyezniük kell a értékeinek típusát. Példa: ' angol nyelven |
| **d: nullázható** *(nem kötelező)* |Megadhatja, hogy a paraméter null értékű lehet. Az alapértelmezett érték: igaz. Azonban, amelyek ki vannak téve az URI-sablonja elérési út része nem lehetnek null értékűek. Ha az attribútum értéke false e paraméterek – a felhasználó által megadott felülbírálja. **Példa:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(nem kötelező)* |A minta érték arra, hogy az ügyfél a felhasználói felületen jeleníti meg.  Több értéket adhat a cső elválasztott listáját, azaz segítségével lehetséges "egy |

## <a name="entitytype-node"></a>EntityType csomópont
Ez a csomópont a piactérről a végfelhasználó számára visszaadott típusú jelöli. A hozzárendelés az értékeket, amelyeket a rendszer visszairányítja a végfelhasználó számára a tartalomszolgáltató szolgáltatás által visszaadott kimenetében is tartalmaz.

Ez a csomópont adatait találhatók [Itt](http://msdn.microsoft.com/library/bb399206.aspx) (használja a **egyéb verzió** legördülő menü eltérő verziójú válassza, ha a dokumentáció megtekintéséhez szükséges.)

| Attribútum neve | Szükséges | Érték |
| --- | --- | --- |
| Név |Igen |Az entitástípus neve. **Példa:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |Nem |Egy másik entitás típusa, amely az alaptípus a entitástípusú, amely meghatározza a neve. **Példa:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

A CSDL-előírásoknak megfelelően hozzáadott attribútumokat a következők:

**d:Map** -végrehajtása a szolgáltatás kimeneti szemben az XPath-kifejezést. A itt feltételezése, hogy a szolgáltatás kimeneti tartalmaz elemei, ismétlődő, például egy ATOM hírcsatornát, ha nincs meg ismétlődő bejegyzést csomópontok. Ezen ismétlődő csomópontok egy bejegyzést tartalmaz. Az XPath majd mutasson a tartalomszolgáltató szolgáltatás eredményt, amelynek az adott bejegyzéshez értékei az egyes ismétlődő csomópontjának van megadva. A szolgáltatás kimeneti példa:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

Az XPath-kifejezés volna /foo kell/sávot, mert egyes a sáv csomópont a kimeneti ismétlődő csomópontja, és a tényleges tartalmat, a végfelhasználó számára visszaadott tartalmazza.

**Kulcs** -Ez az attribútum piactér figyelmen kívül hagyja. REST-alapú webszolgáltatások, általában nem teszi közzé a elsődleges kulcs.

## <a name="property-node"></a>Tulajdonság-csomópont
Ez a csomópont tartalmazza a rekordok egy tulajdonságot.

Ez a csomópont adatait találhatók [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (használja a **egyéb verzió** legördülő menü eltérő verziójú válassza, ha a dokumentáció megtekintéséhez szükséges.) *Példa:*`<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| Attribútumnév | Szükséges | Érték |
| --- | --- | --- |
| Név |Igen |A tulajdonság nevét. |
| Típus |Igen |A tulajdonság értékének típusa. A tulajdonság értékének típusa lehet egy **EDMSimpleType** vagy a komplex típus (egy teljesen minősített nevet jelölve), amely a modell hatókörén belül van. További információkért tekintse meg a fogalmi modell típusa (CSDL). |
| Nullázható |Nem |**Igaz** (az alapértelmezett érték) vagy **hamis** attól függően, hogy a tulajdonság is null értékű. Megjegyzés: A CSDL által jelzett verzióját a [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) névtér, rendelkeznie kell egy összetett típus tulajdonság Nullable = "False". |
| DefaultValue érték |Nem |A tulajdonság alapértelmezett értéke. |
| MaxLength |Nem |A tulajdonság értékének maximális hosszát. |
| FixedLength |Nem |**Igaz** vagy **hamis** attól függően, hogy a tulajdonság értékének fiexed hosszúságú karakterláncként tárolódik. |
| Pontosság |Nem |A numerikus értéket megőrizni kívánt számjegyek maximális száma hivatkozik. |
| Méretezés |Nem |A numerikus értéket megőrizni kívánt tizedesjegyek maximális számát. |
| Unicode |Nem |**Igaz** vagy **hamis** attól függően, hogy kell-e a tulajdonság értékének egy Unicode karakterlánc tárolja. |
| Rendezés |Nem |Az adatforrás használhatók rendezési sorrend meghatározó karakterlánc. |
| ConcurrencyMode |Nem |**Nincs** (az alapértelmezett érték) vagy **rögzített**. Ha a változó értéke **rögzített**, a tulajdonság értéket fogja használni az optimista konkurencia ellenőrzése. |

A további attribútumok CSDL-előírásoknak megfelelően hozzáadott a következők:

**d:Map** -XPath kifejezés végrehajtása a szolgáltatás kimeneti, és kinyeri a kimenet egy tulajdonságot. Az XPath megadott értéke van megadva a EntityType csomópont XPath ismétlődő csomópont viszonyítva. Akkor is engedélyezi, beleértve a kimeneti csomópontok, mint például a szerzői jogi utasítás csak egyszer talált az eredeti szolgáltatásban statikus erőforrás kimeneti, de az OData kimeneti sora szerepel egy abszolút XPath megadásához. Példa a szolgáltatásból:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Itt az XPath-kifejezés az baz0 csomópont lekérni a tartalomszolgáltató szolgáltatásból ./bar/baz0 lenne.

**d:CharMaxLength** -karakterlánc típusú, megadhatja a maximális hossz. DataService CSDL-példa

**d:IsPrimaryKey** -azt jelzi, hogy az oszlop a tábla/nézet elsődleges kulcsa. Tekintse meg a DataService CSDL példát.

**d:isExposed** -határozza meg, ha a következő tábla sémáját tesz elérhetővé (általában igaz). DataService CSDL-példa

**d:IsView** *(nem kötelező)* – igaz, ha ez a táblázat, hanem egy nézet alapul.  DataService CSDL-példa

**d:Tableschema** -DataService CSDL-példa

**d:ColumnName** -tábla/nézet oszlopának neve.  DataService CSDL-példa

**d:IsReturned** -a logikai változó, amely azt határozza meg, ha a szolgáltatás elérhetővé teszi az ügyfél ezt az értéket.  DataService CSDL-példa

**d:IsQueryable** -a logikai változó, amely meghatározza, hogy ha egy adatbázis-lekérdezést az oszlop használható.   DataService CSDL-példa

**d:OrdinalPosition** -az oszlop numerikus helyzete megjelenését, x, a tábla vagy a nézet, ahol x: 1 – a tábla oszlopainak száma.  DataService CSDL-példa

**d:DatabaseDataType** – az adattípus az adatbázisban, azaz SQL adattípus oszlop. DataService CSDL-példa

## <a name="supported-parametersproperty-types"></a>Támogatott paraméterek/tulajdonság típusa
Az alábbiakban a támogatott típusok a paraméterek és a tulajdonságok. (Kis-és nagybetűket)

| Az egyszerű típusok | Leírás |
| --- | --- |
| NULL értékű |Egy érték elérhetetlenségét |
| Logikai érték |A bináris értékű logika matematikai fogalmát jelenti. |
| Bájt |Aláíratlan 8 bites egész szám |
| Dátum és idő |Dátum és idő jelöli a 12:00:00 éjféltől. január 1, 1753 A.D. közötti értékeket 11:59:59 óráig, December 9999 A.D. keresztül |
| Decimális |A rögzített pontosság és a skála numerikus értéket jelöli. Ez a típus leírhatja numerikus érték negatív 10 a ^ 255 + 1 pozitív 10 ^ 255 -1 |
| Dupla |A lebegőpontos szám értékek ± 2.23e-308 osztályig + 1.79e hozzávetőleges tartománnyal jelölő 15 jegy pontossággal jelöli +308. **Használja a decimális Exel exportálási probléma miatt** |
| Egyetlen |A lebegőpontos szám értékek ± 1.18e-38 osztályig + 3.40e hozzávetőleges tartománnyal jelölő 7 számjegyek pontossággal jelöli + 38 |
| GUID |Egy 16 bájtos (128 bites) egyedi azonosító értékét jelöli |
| Int16 |Egy aláírt 16 bites egész értéket jelöli. |
| Int32 |Egy aláírt 32 bites egész értéket jelöli. |
| Int64 |Egy aláírt 64 bites egész értéket jelöli. |
| Karakterlánc |Rögzített - vagy változó hosszúságú karakteres adatot jelöli. |

## <a name="see-also"></a>Lásd még:
* Ha a teljes OData-megfeleltetési folyamat és a rendeltetés megválaszolásával, olvassa el ebben a cikkben [szolgáltatás OData megfeleltetése](marketplace-publishing-data-service-creation-odata-mapping.md) áttekintheti a definíciókat, és a utasításokat.
* Ha érdekli példák megtekintésével, olvassa el ebben a cikkben [adatok szolgáltatás OData leképezési példák](marketplace-publishing-data-service-creation-odata-mapping-examples.md) mintakód és kód szintaxis és a környezetben.
* Térjen vissza az előírt elérési út egy szolgáltatás-közzététel az Azure piactéren, olvassa el ebben a cikkben [adatok szolgáltatás közzétételi útmutató](marketplace-publishing-data-service-creation.md).
