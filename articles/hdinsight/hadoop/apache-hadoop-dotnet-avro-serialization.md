---
title: Szerializálni az adatokat a Hadoop - Microsoft Avro Library - Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan lehet szerializálni, és adatokat a Hadoop on HDInsight használatával a Microsoft az Avro Library megőrizni a memória, egy adatbázis vagy a fájl.
keywords: az avro, hadoop avro
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 0d195ab3b84a522eae4010f3b08a829f7056a35f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>A Microsoft az Avro Library Hadoop adatok szerializálása

>[!NOTE]
>Az Avro SDK a Microsoft már nem támogatott. A könyvtárban, nyílt forráskódú közösségi támogatott. A szalagtár források találhatók [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Ez a témakör bemutatja, hogyan használja a [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) objektumok és egyéb adatstruktúrák szerializálni az adatfolyamok megőrizni a memória, adatbázis vagy egy fájlt. Azt is bemutatja, hogyan őket helyreállítása az eredeti objektum deszerializálása.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> valósítja meg az Apache Avro szerializálási rendszer Microsoft.NET környezetre. Apache Avro kompakt bináris adatcsere adatformátum szerializálási biztosít. Használja <a href="http://www.json.org" target="_blank">JSON</a> egy nyelvtől független sémát, amely lehetővé teszi a nyelvi együttműködést meghatározásához. Több nyelven érhetőek szerializált adatok olvashatók egy másik. Jelenleg a C, C++, C#, Java, PHP, Python és Ruby támogatottak. A részletes információk találhatók a <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro specifikációjában</a>. 

>[!NOTE]
>A Microsoft az Avro Library nem támogatja a távoli eljáráshívásnak (RPC) hívások részét ez az előírás.
>

Az Avro rendszerben objektum szerializált ábrázolását két részből áll: séma, a tényleges érték. Az Avro-séma a nyelvfüggetlen az a szerializált adatok JSON adatmodell ismerteti. A bináris adatok megjelenítése és jelölőnégyzetként jelenik. Rendelkező elkülönül a bináris megjelenítése a séma lehetővé teszi az egyes objektumok nem érték terhek, így gyors szerializálási, és a ábrázolását kis írni.

## <a name="the-hadoop-scenario"></a>A Hadoop-forgatókönyv
Az Apache Avro szerializálási formátum széles körben használt Azure HDInsight és egyéb Apache Hadoop-környezetekben. Az Avro Hadoop MapReduce feladatot összetett adatstruktúra képviselő kényelmes megoldást kínál. Az Avro-fájlok (Avro tároló fájlt) formátuma támogatja az elosztott MapReduce programozási modellt tervezték. A kulcs szolgáltatása, amely lehetővé teszi, hogy a terjesztési, az, hogy a fájlok "feloszthatók", abban az értelemben, hogy egy fájl bármely pontot kikereshet, és az egy adott blokktól kezdheti-e.

## <a name="serialization-in-avro-library"></a>Az Avro könyvtárban szerializálási
A .NET könyvtár az avro-hoz támogatja a szerializálási objektumok két módon:

* **Fejlécreflexiós** -a JSON-séma a következő típusokhoz automatikusan össze az adatokat a .NET típusú szerializálandó szerződés attribútumok.
* **általános rekord** -A JSON-séma explicit módon megadott által képviselt rekordban a [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) indulásakor nem .NET-típusok jelen a sémát szerializálandó leírására.

Ha az adatok séma ismert író és az adatfolyam olvasó, az adatküldés sémájú nélkül. Azokban az esetekben az Avro objektum tároló fájl használata esetén a séma tárolja a fájlon belül. Más paraméterek, például a használt adatok tömörítési kodek adható meg. Ezek a forgatókönyvek részletesen ismertetett és a következő kód példákban szereplő megoldásokat:

## <a name="install-avro-library"></a>Telepítse az Avro-könyvtár
A következőkre szükség a szalagtár telepítése előtt:

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">A Microsoft .NET-keretrendszer 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 vagy újabb)

Vegye figyelembe, hogy a Newtonsoft.Json.dll függőség a Microsoft az Avro Library a telepítés automatikusan le. Az eljárás a következő szakaszban találhatók:

A Microsoft az Avro Library az alábbi eljárás segítségével telepíthető a Visual Studio NuGet-csomag terjesztése:

1. Válassza ki a **projekt** lapon -> **NuGet-csomagok kezelése...**
2. Keressen a "Microsoft.Hadoop.Avro" kifejezésre a **keresési Online** mezőbe.
3. Kattintson a **telepítése** gombra **Microsoft Azure HDInsight Avro könyvtár**.

Vegye figyelembe, hogy a Newtonsoft.Json.dll (> = 6.0.4) függőségi együtt a Microsoft az Avro Library is letöltődik automatikusan.

A Microsoft az Avro Library a forráskód nem érhető el [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Az Avro szalagtárat használó sémák fordítása
A Microsoft az Avro Library tartalmaz egy kód generálása segédprogram, amely lehetővé teszi, hogy a korábban meghatározott JSON-séma alapján automatikusan C# típusok létrehozása. A kód generálása segédprogram nem terjesztése egy bináris végrehajtható fájlt, de az alábbi eljárás segítségével könnyen építhetők:

1. Töltse le a .zip fájlt forráskódjának HDInsight SDK legújabb verziójával <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK a Hadoop</a>. (Kattintson a **letöltése** ikonra, nem a **letölti** lapon.)
2. A HDInsight SDK a kivonatot egy könyvtár a .NET-keretrendszer 4 gépen telepítve, és csatlakozik az internethez szükséges függőség NuGet-csomagok letöltéséhez. Az alábbiakban feltételezzük, hogy a forráskód C:\SDK kicsomagolta.
3. A mappában C:\SDK\src\Microsoft.Hadoop.Avro.Tools, és futtassa a build.bat. (A fájl hívások MSBuild a 32 bites terjesztési a .NET-keretrendszer. Ha azt szeretné, hogy 64 bites verzióját használja, szerkesztése build.bat, a megjegyzéseket, a fájlban a következő.) Győződjön meg arról, hogy a build sikeres. (Egyes rendszerek MSBuild készíthet figyelmeztetéseket. Ezek a figyelmeztetések nem befolyásolják a segédprogram mindaddig, amíg nincsenek build hibák.)
4. A lefordított segédprogram C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools található.

Ahhoz, hogy ismeri a parancssori szintaxist, a mappából, ahol a kód generálása segédprogram-e végre az alábbi parancsot: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

A segédprogram teszteléséhez C# osztályokat is generálása a megadott forráskódja minta JSON-fájl. Hajtsa végre a következő parancsot:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Ez az aktuális könyvtárban található fájlok két C# létrehozásához kellene: SensorData.cs és Location.cs.

A programot, amely a JSON-séma C# típusú konvertálása során használja a kód generálása segédprogram ismertetése: a fájl GenerationVerification.feature C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc található.

Névterek kinyert a JSON-séma, a fájlban, az előző bekezdésben szereplő esetekben ismertetett logika használatával. A séma kinyert névterek élveznek függetlenül biztosított parancssori segédprogram, az n paraméterrel. Ha azt szeretné, felülbírálhatja a névterek, a séma belül található, akkor használja a /nf paramétert. Például a SampleJSONSchema.avsc my.own.nspace az összes névtér módosításához hajtsa végre a következő parancsot:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Tudnivalók a minták
Ebben a témakörben bemutatott hat példák bemutatják a Microsoft az Avro Library által támogatott különböző forgatókönyveket. A Microsoft az Avro Library tervezték az adatfolyam. Ezekben a példákban-adatok n keresztül memória adatfolyamok helyett, fájl adatfolyamok vagy az egyszerűség és konzisztenciáját. Éles környezetben a megközelítést a pontos forgatókönyv-követelményeinek, az adatforrás és a kötet, a teljesítmény korlátozások és a más tényezőktől függ.

Az első két példák bemutatják, hogyan szerializálása és deszerializálása adatok memória adatfolyam pufferek az általános és a reflexió használatával. Két esetben a séma feltételezett, hogy az olvasók és írók között meg kell osztani a sávon kívüli.

A harmadik és negyedik példák bemutatják, hogyan lehet szerializálni, és az Avro objektum tároló fájlokkal adatok deszerializálása. Adatok az Avro-tároló fájl tárolja, amikor a séma mindig tárolása vele, mert a séma meg kell osztani a deszerializáláshoz.

A minta az első négy példák tartalmazó tölthető le: a <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">Azure mintakódok</a> hely.

Az ötödik példa bemutatja, hogyan használható egy egyéni tömörítési kodek Avro objektum tárolófájlokba. Az ebben a példában letölthető szükséges kódot tartalmazó minta a <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">Azure mintakódok</a> hely.

A hatodik minta bemutatja, hogyan használható Avro szerializálása feltölteni az adatokat az Azure Blob storage és majd elemezni egy HDInsight (Hadoop) fürthöz való Hive használatával. Le is tölthetők: a <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure mintakódok</a> hely.

Az alábbiakban a hat minták, a témakörben tárgyalt mutató hivatkozásokat:

* <a href="#Scenario1">**A reflexió szerializálási** </a> -típusok szerializálandó a JSON-séma automatikusan össze az adatokat szerződés attribútumok.
* <a href="#Scenario2">**Általános rekordot tartalmazó szerializálási** </a> -a JSON-séma explicit módon megadott rekord elérhető reflexióra nincs .NET típus esetén.
* <a href="#Scenario3">**Objektum tároló fájlok használata a reflexió szerializálási** </a> -a JSON-séma automatikusan összeállítása és megosztott együtt a szerializált adatok keresztül az Avro tároló fájlt.
* <a href="#Scenario4">**Általános rekordot tartalmazó objektum tárolófájlokba használatával szerializálási** </a> -a JSON-séma explicit módon megadott előtt a szerializálás és az Avro objektum tárolót fájlon keresztül az adatok és megosztott.
* <a href="#Scenario5">**Objektum tárolófájlokba használata egy egyéni tömörítési kodek szerializálási** </a> -a példa bemutatja, hogyan hozzon létre egy Avro objektum tároló fájlt egy testreszabott .NET megvalósításáról a Deflate adatok tömörítési kodek.
* <a href="#Scenario6">**Az Avro használatával feltölteni az adatokat a Microsoft Azure HDInsight szolgáltatás** </a> -a példa bemutatja, hogyan kommunikál az Avro szerializálási a HDInsight-szolgáltatás. Ez a példa futtatásához szükséges egy aktív Azure-előfizetés és Azure HDInsight-fürtök a hozzáférést.

## <a name="Scenario1"></a>1. példa: Szerializálási a reflexió
A JSON-séma a következő típusokhoz automatikusan épül fel az adatokat a reflexió használatával a Microsoft az Avro Library a C# objektumokat szerializálni szerződés attribútumait. A Microsoft az Avro Library létrehoz egy [ **IAvroSeralizer<T>**  ](http://msdn.microsoft.com/library/dn627341.aspx) szerializálandó mezők azonosításához.

Ebben a példában objektumokat (egy **SensorData** tag osztályra **hely** struct) szerializálva vannak memória adatfolyamba, és az adatfolyam pedig deszerializálva. Az eredmény ezután annak ellenőrzésére, hogy a kezdeti példányszámnak a rendszer összehasonlítja a **SensorData** az eredeti helyre objektum megegyezik.

Ebben a példában a séma feltételezett, hogy között meg kell osztani az olvasók és írók, így nincs szükség az Avro objektum tárolóformátummal. Példa bemutatja, hogyan szerializálása és deszerializálása adatok a memóriában puffereli használatával reflexiós a tároló objektum formátumban, abban az esetben, ha a séma meg kell osztani az adatokat, lásd: <a href="#Scenario3">objektum tároló fájlok használata a reflexiószerializálási</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>2. példa: Szerializálási általános rekordot tartalmazó
A JSON-séma adható explicit módon meg egy általános rekordban Ha reflexiós nem használható, mert az adatok nem reprezentálhatók egy adategyezmény a .NET-osztályok keresztül. Ez a módszer akkor lassabb, mint a következőt reflexió használatával. Ilyen esetben a sémát is lehet dinamikus, ez azt jelenti, hogy nem ismeri a fordítás során. Vesszővel tagolt (CSV) fájlok, amelyek séma nem ismeretlen, amíg a futási időben az Avro formátum rendszer átalakítja-ként adata az ilyen dinamikus forgatókönyv egy példát.

Ez a példa bemutatja, hogyan létrehozhat és használhat egy [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) való explicit módon adja meg a JSON-séma, hogyan töltse fel adatokkal az adatokat, és majd szerializálása és deszerializálása azt. Az eredmény ezután összeveti a kezdeti példányszámnak annak ellenőrzéséhez, hogy a rekord helyreállítani az eredeti azonos.

Ebben a példában a séma feltételezett, hogy között meg kell osztani az olvasók és írók, így nincs szükség az Avro objektum tárolóformátummal. Példa bemutatja, hogyan szerializálása és deszerializálása adatok a memóriában puffereli használatával egy általános rekordban a tároló objektum formátumban, ha a séma szerepelnie kell a szerializált adatok a, tekintse meg a <a href="#Scenario4">objektum tároló fájlokkal rendelkező szerializálási általános rekord</a> példa.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>3. példa: Szerializálási a reflexió objektum tároló fájlok és a szerializálási használja
Ebben a példában a forgatókönyv hasonlít a <a href="#Scenario1"> első példában</a>, ahol a séma implicit módon megadott a reflexió. A különbség, hogy itt, a séma nem feltételezett, hogy az olvasó deserializes azt, hogy ismert. A **SensorData** szerializálható objektumok és azok implicit módon megadott séma fájlban vannak tárolva az Avro objektum tároló által képviselt a [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) osztály.

Ebben a példában a szerializált adatok [ **SequentialWriter<SensorData>**  ](http://msdn.microsoft.com/library/dn627340.aspx) és a deszerializált [ **SequentialReader<SensorData>**  ](http://msdn.microsoft.com/library/dn627340.aspx). Az eredmény ezután a rendszer összehasonlítja a kezdeti példányok identitás biztosításához.

A rendszer tömöríti az adatokat a objektum tároló fájlban az alapértelmezett keresztül [ **Deflate** ] [ deflate-100] tömörítési kodek a .NET-keretrendszer 4. Tekintse meg a <a href="#Scenario5"> ötödik példa</a> ebben a témakörben további információt az újabb és felső szintű verzióját használja a [ **Deflate** ] [ deflate-110] tömörítési kodek .NET-keretrendszer 4.5 érhető el.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>4. példa: Szerializálási általános rekordot tartalmazó objektum tárolófájlokba és szerializálási használatával
Ebben a példában a forgatókönyv hasonlít a <a href="#Scenario2"> második példáját</a>, ahol a séma explicit módon megadott rendelkező JSON-NÁ. A különbség, hogy itt, a séma nem feltételezett, hogy az olvasó deserializes azt, hogy ismert.

Egy gyűjtött adatok TesztKészlet [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) keresztül explicit módon megadott JSON-séma objektumokat, és egy objektum tároló fájl által képviselt majd tárolja a [  **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) osztály. A tároló fájlt hoz létre egy író szerializálni az adatokat, majd a fájl mentett memória adatfolyamba tömörítetlen használt. A [ **Codec.Null** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) az olvasó létrehozásakor használt paraméter határozza meg, hogy ezek az adatok nem tömöríti.

Az adatok majd kiolvasni a fájlból és az objektumok gyűjteménye deszerializálni. Ez a gyűjtemény a rendszer összehasonlítja a kiindulási lista az Avro rekordok annak ellenőrzéséhez, hogy azok egyezését.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>5. példa: Szerializálási egy egyéni tömörítési kodek objektum tároló fájlok használata
Az ötödik példa bemutatja, hogyan használható egy egyéni tömörítési kodek Avro objektum tárolófájlokba. Az ebben a példában letölthető szükséges kódot tartalmazó minta a [Azure mintakódok](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) hely.

A [Avro specifikációjában](http://avro.apache.org/docs/current/spec.html#Required+Codecs) lehetővé teszi, hogy egy nem kötelező tömörítési kodek használatát (kívül **Null** és **Deflate** alapértelmezett). Ebben a példában nem implementálja az Snappy például egy új kodek (egy támogatott választható kodek az említett a [Avro specifikációjában](http://avro.apache.org/docs/current/spec.html#snappy)). Azt illusztrálja, hogyan használata a .NET-keretrendszer 4.5 megvalósítása a [ **Deflate** ] [ deflate-110] kodek, így jobban tömörítési algoritmust alapján a [zlib ](http://zlib.net/) verziójú, mint az alapértelmezett .NET-keretrendszer 4-tömörítési könyvtárat.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>6. példa: Avro feltölteni az adatokat a Microsoft Azure HDInsight szolgáltatás használatával.
A hatodik példa bemutatja az egyes programozási módszerek használják az Azure HDInsight szolgáltatással kapcsolatos. Az ebben a példában letölthető szükséges kódot tartalmazó minta a [Azure mintakódok](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) hely.

A minta a következő feladatokat hajtja végre:

* Csatlakozik egy meglévő HDInsight-fürtre.
* Rendezi sorba több CSV-fájlt és feltölti az eredményt az Azure Blob Storage tárolóban. (A CSV-fájlok a minta együtt küld, és megfelelnek-e terjesztve mellékletben készlet előzményadatokat kivonatát [Infochimps](http://www.infochimps.com/) 1970-2010 időszakra. A minta CSV-fájl adatokat olvas, alakítja át a rekordok példányait a **készlet** osztály, és ezután rendezi sorba őket reflexió használatával. A JSON-séma segítségével a Microsoft az Avro Library kód generálása segédprogram készlet definíciójának készült.
* Táblázatot hoz létre új külső nevű **készletek** a Hive és a hivatkozásokat úgy, hogy az adatok feltöltése az előző lépésben.
* A lekérdezés végrehajtja a Hive használatával keresztül a **készletek** tábla.

Emellett a minta folyamatot hajt végre a karbantartás előtt és után fő műveletet hajt végre. Során a karbantartás minden a kapcsolódó Azure-Blob adatok és a mappák törlődnek, és a Hive tábla megszakad. A karbantartás eljárás a minta parancssorból is hívhat meg.

A minta előfeltételei a következők:

* Egy aktív Microsoft Azure-előfizetést és az előfizetés-azonosító.
* Egy felügyeleti tanúsítványt, az előfizetés, a megfelelő titkos kulccsal. A tanúsítványt kell telepíteni, az aktuális felhasználó titkos tárolási a minta futtatásához használt számítógépen.
* Aktív HDInsight-fürtöt.
* A HDInsight-fürthöz az előző előfeltétel, és a megfelelő elsődleges vagy másodlagos elérési kulcsot a kapcsolódó Azure Storage-fiók.

Minden adatot a Előfeltételek kell adni a minta konfigurációs fájl a minta futtatása előtt. Ehhez két lehetséges módja van:

* Szerkessze az app.config fájlt, a minta gyökérkönyvtárában, és majd kialakítható a minta
* A minta először létre, és szerkessze a AvroHDISample.exe.config a build könyvtárban

Mindkét esetben minden módosításokat kell végezni a **<appSettings>** beállítások szakaszban. Kövesse a megjegyzéseket, a fájlban.
A minta futtatása a parancssorból futtassa a következő parancsot (amelyben a .zip-fájlt a mintával lett feltételezi, hogy kibontott C:\AvroHDISample; Ha ellenkező esetben használja a megfelelő elérési útja):

    AvroHDISample run C:\AvroHDISample\Data

A fürt karbantartása, a következő parancsot:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
