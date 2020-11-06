---
title: Iparági szabványnak megfelelő modellek integrálása
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan integrálhatja az iparági szabványnak megfelelő modelleket az Azure Digital Twins DTDL-ba, akár speciális DTDL ontológiákat vagy meglévő ontológiákat átalakításával
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338394"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Iparági szabványnak megfelelő modellek integrálása az Azure Digital Twins DTDL

Az iparági szabványokon alapuló modellek használata vagy a standard ontológia-képviselet (például RDF vagy bagoly) használata gazdag kiindulási pontot biztosít az Azure digitális Twins-modelljeinek tervezésekor. Az iparági modellek használata a szabványosítás és az információmegosztás terén is segít.

Az Azure Digital Twins szolgáltatással való használathoz a modellnek a JSON-LD-alapú [**digitális Twins Definition Language (DTDL) nyelven**](concepts-models.md)kell szerepelnie. Ebből kifolyólag ez a cikk bemutatja, hogyan jelentheti az iparági szabványnak megfelelő modelleket a DTDL-ben, és integrálhatja a meglévő iparági fogalmakat DTDL szemantikaokkal, hogy az Azure digitális Twins használhassa őket. A DTDL modell ezután az igazság forrásaként szolgál a modellhez az Azure Digital Twins-n belül.

Az iparági szabványnak megfelelő modellek DTDL való integrálásának három lehetséges útja van:
* **Elfogadás** : megkezdheti a megoldást egy olyan nyílt forráskódú DTDL ontológia használatával, amely széles körben elfogadott iparági szabványokra épül. 
* **Konvertálás** : Ha már rendelkezik meglévő modellekkel, a DTDL-re kell alakítania azokat.
* **Szerző** : saját egyéni DTDL-modelljeit bármikor kifejlesztheti a semmiből, ahogyan az [*útmutató: egyéni modellek kezelése*](how-to-manage-model.md)című témakörben leírtak szerint.

## <a name="adopt-an-open-source-dtdl-ontology"></a>Nyílt forráskódú DTDL ontológia bevezetése

Általában egyszerűbb a nyílt forráskódú DTDL, mint egy üres oldaltól kezdődően. 

Az intelligens épületek megoldásai például kihasználhatják a nyílt forráskódú [**DTDL-alapú RealEstateCore ontológia**](https://github.com/Azure/opendigitaltwins-building)-t, amely általános teret biztosít az intelligens épületek modellezéséhez, miközben az iparági szabványokat kihasználva megakadályozza az újratalálmányt. 

Ezek a nyílt forráskódú DTDL-ontológiákat a modellek felhasználására és megfelelő kiterjesztésére vonatkozó ajánlott eljárásokat is biztosítanak. 

## <a name="convert-existing-models-to-dtdl"></a>Meglévő modellek átalakítása DTDL

A legtöbb iparági modell (más néven **ontológiákat** ) olyan szemantikai webes szabványokon alapul, mint például a [bagoly](https://www.w3.org/OWL/), az [RDF](https://www.w3.org/2001/sw/wiki/RDF)és a [kemence](https://www.w3.org/2001/sw/wiki/RDFS). 

Az Azure Digital Twins-modell használatához DTDL formátumban kell megadni. Ez a szakasz az RDF-alapú modellek DTDL való átalakítására szolgáló **átalakítási minta** formájában történő általános tervezési útmutatót ismerteti, hogy az Azure Digital Twins használatával is használható legyen. 

Tartalmaz [egy **minta-átalakító kódot** is egy RDF-átalakítóhoz](#sample-converter-application), amely a [tégla](https://brickschema.org/ontology/) -sémához lett érvényesítve, és az építőipar más sémái számára is bővíthető.

### <a name="conversion-pattern"></a>Átalakítási minta

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

### <a name="sample-converter-application"></a>Minta-átalakító alkalmazás 

Rendelkezésre áll egy minta alkalmazás, amely egy RDF-alapú modellt konvertál a [DTDL (2. verzió)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) az Azure Digital Twins szolgáltatás általi használatra. A rendszer ellenőrizte a [tégla](https://brickschema.org/ontology/) sémát, és kiterjeszthető az építőipar más sémái számára (például a [topológiai ontológia (bot)](https://w3c-lbd-cg.github.io/bot/), a [szemantikai érzékelő hálózat](https://www.w3.org/TR/vocab-ssn/)vagy a [buildingSmart Industry Foundation classs (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

A minta egy **RdfToDtdlConverter** nevű .net Core parancssori alkalmazás.

A mintát itt érheti el: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

A kód a gépre való letöltéséhez nyomja le a *zip letöltése* gombot a minta kezdőlapján a cím alatt. Ezzel letölt egy *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* nevű *zip* -fájlt, amelyet később kibonthat és megvizsgálhat.

Ezzel a mintával megtekintheti az átalakítási mintákat a kontextusban, és létrehozhat építőelemeket a saját igényeinek megfelelő modell-konverziókat végző saját alkalmazások számára.

## <a name="validate-and-upload-dtdl-models"></a>DTDL-modellek ellenőrzése és feltöltése

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

A modell átalakítása és ellenőrzése után **feltöltheti azt az Azure Digital Twins-példányba**. A folyamattal kapcsolatos további információkért tekintse meg az *egyéni modellek kezelése* című témakör [*modellek feltöltése*](how-to-manage-model.md#upload-models) szakaszát.

## <a name="next-steps"></a>További lépések 

További információ a digitális kettős modellek tervezéséről és kezeléséről:
 
* [*Fogalmak: egyéni modellek*](concepts-models.md)
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)
* [*Útmutató: modellek elemzése és érvényesítése*](how-to-parse-models.md)
