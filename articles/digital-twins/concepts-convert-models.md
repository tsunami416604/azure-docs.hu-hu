---
title: Iparági szabványnak megfelelő modellek átalakítása
titleSuffix: Azure Digital Twins
description: Az iparági szabványnak megfelelő (RDF/bagoly) modellek DTDL alakítási mintázatának megismerése
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002635"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Iparági szabványnak megfelelő modellek átalakítása az Azure Digital Twins DTDL

Az Azure Digital Twins modelljei a JSON-LD-alapú [**digitális Twins Definition Language (DTDL) nyelven**](concepts-models.md)jelennek meg. Ha olyan Azure Digital Ikreken kívüli modellekkel rendelkezik, amelyek egy iparági szabványon (például RDF-en vagy bagoly-n) alapulnak, akkor a DTDL-be kell **alakítania** őket az Azure Digital Twins használatával. A DTDL verziója ezután a modell igazságának forrása lesz az Azure digitális Ikrekben.

Ez a cikk az RDF-alapú iparág vagy az egyéni modellek DTDL alakítására szolgáló mintát ismerteti. A következőket tartalmazza:
* **Stratégiai szintű útmutató** , amely különböző szabványokra és típusokra alkalmazható
* [**Minta kódja** egy RDF-specifikus átalakítóhoz](#sample-converter-application)

## <a name="industry-models"></a>Iparági modellek  

Az iparági modellek gazdag kiindulási pontot biztosítanak az Azure Digital Twins-modell tervezésekor. Az iparági modellek használata a szabványosítás és az információmegosztás terén is segít. 

Egyes gyakori iparági modellek a következők:  

| Iparági vertikális | Modell |
| --- | --- | 
| Épület/létesítmény kezelése | [RealEstateCore](https://www.realestatecore.io/)<br>[TÉGLA-séma](https://brickschema.org/ontology/1.1/)<br>[Felépítési topológia – ontológia (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Szemantikai érzékelő hálózata](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart Industry Foundation-osztályok (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Intelligens városok | [ETSI NGSI – LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Intelligens alkalmazások referenciája (SAREF)](https://saref.etsi.org/) |
| Energiaellátási rács | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

Az igényektől függően a DTDL használatával testreszabhatja vagy kiterjesztheti az iparági modelleket, vagy akár saját egyéni modellt is fejleszthet. 

## <a name="create-and-edit-models"></a>Modellek létrehozása és szerkesztése

A modellezés első lépéseként létrehozza a modelleket. Az iparági szabványnak megfelelő modelleket az DTDL konvertálása előtt is létrehozhatja és elvégezheti, de a DTDL is konvertálhatja, és DTDL-dokumentumként folytathatja a szerkesztést.

### <a name="with-industry-standards"></a>Iparági szabványokkal

A legtöbb iparági modell (más néven **ontológiákat**) olyan szemantikai webes szabványokon alapul, mint például a [bagoly](https://www.w3.org/OWL/[), az [RDF](https://www.w3.org/2001/sw/wiki/RDF)és a [kemence](https://www.w3.org/2001/sw/wiki/RDFS). 

Bizonyos esetekben előfordulhat, hogy a bagoly-alapú modell eszközeivel szeretne modellt létrehozni vagy szerkeszteni. Tetszőleges számú modellt készítő eszközt használhat a bagoly-alapú modell kialakításához, beleértve az alábbiakat is.
* A [WebProtégé](https://protege.stanford.edu/products.php#web-protege) és a [pártfogoltja Desktop](https://protege.stanford.edu/products.php#desktop-protege) népszerű példák. Az iparági modelleket több formátumban is importálhatja, szerkesztheti vagy bővítheti a modelleket, és exportálhatja a modellt. 
* A [WebVOWL](http://www.visualdataweb.de/webvowl/) egy másik népszerű eszköz a modellek megjelenítéséhez. 

Ha olyan modellt hoz létre, amely nem DTDL, akkor a DTDL-re kell konvertálnia, és fel kell töltenie az Azure digitális Twins szolgáltatásba. 

#### <a name="common-model-file-formats"></a>Közös modell fájlformátumai 

Az RDF, a bagoly és a kemence a szemantikai webes alapszintű építőeleme. 

Az **RDF** fogalmi struktúrát biztosít a dolgok leírásához **hármas**formában. A hármas három részből áll: **subject**, **predikátum**és **Object**. Az objektumok URI-k használatával hozhatók létre. 

Íme néhány példa az RDF triples-re:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Ezek a példák mind érvényes RDF-t tartalmaznak, de az utolsó utasítás szemantikailag érvénytelen. Ez a helyzet, amikor a bagoly specifikációja belép a képbe. A **bagoly** meghatározza, hogy mit ÍRHAT az RDF-sel, hogy érvényes ontológia-t lehessen létrehozni.

Íme egy példa a bagoly használatára: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

A **kemence** további, az osztályok definiálásához és leírásához segítséget nyújtó szókincs-szemantikai lehetőségeket biztosít. Például az egyik ilyen osztály a következő `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

A modellek menthetők, importálhatók és exportálhatók számos fájlformátumban, beleértve a következőket:  
* RDF/XML 
* Turtle (TTL) 
* BAGOLY/XML 

### <a name="with-dtdl"></a>DTDL

Az Azure Digital Twins a JSON-LD-alapú **Digital Twin Definition Language (DTDL) nyelvet** használja modellezéshez. Az Azure Digital Twins szolgáltatással használható modelleket eredetileg a DTDL-be kell írni, vagy át kell alakítani.

A DTDL-modellek használatakor használhatja a [**DTDL extension**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)    [Visual Studio Code](https://code.visualstudio.com/)-hoz elérhető DTDL-bővítményt, amely szintaxis-ellenőrzést és egyéb funkciókat biztosít a DTDL modellek írásához.

Az Azure digitális Twins modelljeiről és azok összetevőiről a [*fogalmak: egyéni modellek*](concepts-models.md) és [*útmutató: egyéni modellek kezelése*](how-to-manage-model.md)című témakörben olvashat bővebben.

## <a name="convert-models-to-dtdl"></a>Modellek átalakítása DTDL

Az Azure Digital Twins-modell használatához DTDL formátumban kell megadni. Ez a szakasz bemutatja, hogyan alakíthatja át az RDF-alapú modelleket DTDL, hogy azok használhatók legyenek az Azure digitális Twins szolgáltatásban.

Az RDF-alapú modellek DTDL való konvertálásakor több külső gyártótól származó kódtár is használható. Ezen könyvtárak némelyike lehetővé teszi a modell fájljának betöltését egy gráfba. A diagramon az adott kemence-és bagoly-szerkezeteket keresi, és a DTDL alakíthatja át őket.   

A következő táblázat egy példát mutat be arra, hogy a KEMENCÉk és a bagoly szerkezetek hogyan képezhetők le a DTDL. 

| KEMENCE/bagoly koncepció | KEMENCE/bagoly-összeállítás | DTDL koncepció | DTDL-összeállítás |
| --- | --- | --- | --- |
| Osztályok | `owl:Class`<br>IRI utótag<br>``rdfs:label``<br>``rdfs:comment`` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Alosztályok | `owl:Class`<br>IRI utótag<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Adattípus tulajdonságai | `owl:DatatypeProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:label`<br>`rdfs:range` | Interfész tulajdonságai | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objektum tulajdonságai | `owl:ObjectProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Kapcsolat | `type:Relationship`<br>`name`<br>`target` (vagy nincs megadva `rdfs:range` )<br>`comment`<br>`displayName`<br>

A következő C# kódrészlet bemutatja, hogyan tölthető be egy RDF-modell egy gráfba, és hogyan konvertálható DTDL a [**dotNetRDF**](https://www.dotnetrdf.org/) -könyvtár használatával. 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>DTDL-modellek ellenőrzése és feltöltése

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

A modell átalakítása és ellenőrzése után **feltöltheti azt az Azure Digital Twins-példányba**. A folyamattal kapcsolatos további információkért tekintse meg az *egyéni modellek kezelése*című témakör [*modellek feltöltése*](how-to-manage-model.md#upload-models) szakaszát.

## <a name="sample-converter-application"></a>Minta-átalakító alkalmazás 

Rendelkezésre áll egy minta alkalmazás, amely egy RDF-alapú modellt konvertál a [DTDL (2. verzió)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) az Azure Digital Twins szolgáltatás általi használatra. A minta egy **RdfToDtdlConverter**nevű .net Core parancssori alkalmazás.

A mintát itt érheti el: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

A kód a gépre való letöltéséhez nyomja le a *zip letöltése* gombot a minta kezdőlapján a cím alatt. Ezzel letölt egy *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*nevű *zip* -fájlt, amelyet később kibonthat és megvizsgálhat.

Ezzel a mintával megtekintheti az átalakítási mintákat a kontextusban, és létrehozhat építőelemeket a saját igényeinek megfelelő modell-konverziókat végző saját alkalmazások számára.

## <a name="next-steps"></a>Következő lépések 

További információ a digitális kettős modellek tervezéséről és kezeléséről:
 
* [*Fogalmak: egyéni modellek*](concepts-models.md)
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)
* [*Útmutató: modellek elemzése és érvényesítése*](how-to-parse-models.md)