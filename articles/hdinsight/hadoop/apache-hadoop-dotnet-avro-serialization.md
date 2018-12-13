---
title: Az Azure-ban Apache Hadoop - Microsoft Avro Library - adatok szerializálása
description: Megtudhatja, hogyan szerializálható és deszerializálható Hadoop on HDInsight a Microsoft Avro Library segítségével memóriában, adatbázisban vagy fájlban való megőrizni az adatokat.
keywords: az avro, hadoop avro-hoz
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.custom: hdiseo17may2017
ms.openlocfilehash: 8ef8f66a67ee93ea8c015c33e69b87e7c5d2a898
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259987"
---
# <a name="serialize-data-in-apache-hadoop-with-the-microsoft-avro-library"></a>Az Apache Hadoop, a Microsoft Avro Library segítségével az adatok szerializálása

>[!NOTE]
>Az Avro SDK-t a Microsoft már nem támogatott. A kódtár támogatja a nyílt forráskódú fejlesztői Közösség. A források a könyvtárban található [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Ez a témakör bemutatja, hogyan használhatja a [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) megőrizni azokat a memória, adatbázis vagy fájl alakíthatók objektumok és más adatszerkezetek szerializálásához. Azt is bemutatja, hogyan lehet őket helyreállítani az eredeti objektum deszerializálása.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Az Apache avro-hoz
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> valósítja meg az Apache Avro szerializálási rendszer Microsoft.NET környezetre. Apache Avro szerializálási biztosít egy kompakt bináris adatcsere-formátumot. Használ <a href="http://www.json.org" target="_blank">JSON</a> egy nyelvtől független sémát, amely a nyelvi együttműködést nyelvben meghatározásához. Az egyik nyelven szerializált adatok egy másik olvasható. Jelenleg a C, C++, C#, Java, PHP, Python és Ruby támogatottak. Részletes információkat a formátumban található a <a href="https://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro specifikációjában</a>. 

>[!NOTE]
>A Microsoft Avro Library nem támogatja a távoli eljáráshívás (RPC) hívásokat részét ez az meghatározás.
>

Az Avro rendszerben objektum szerializált reprezentációja két részből áll: séma- és tényleges értékkel. Az Avro-sémát a JSON-szerializált adatok nyelvtől független adatmodell ismerteti. Tartalomcsomagjai bináris kódolású karakterláncként adatok jelennek meg. Minden egyes objektum nincs érték – általános költségeket, lehetővé téve a gyors szerializálási, és a reprezentáció kis se dají zapsat kellene elkülönül a bináris kódolású karakterláncként a séma lehetővé teszi.

## <a name="the-hadoop-scenario"></a>A Hadoop-forgatókönyv
Az Apache Avro szerializálási formátum széles körben használt, az Azure HDInsight és a többi Apache Hadoop-környezetben. Az Avro jelölnek összetett adattárakon belül egy Hadoop-MapReduce-feladatot egy kényelmes módot biztosít. (Az Avro objektum kontejner soubor) Avro-fájlok formátuma támogatja az elosztott MapReduce programozási modellt úgy lett kialakítva. A legfontosabb funkció, amely lehetővé teszi, hogy a terjesztési, hogy a fájlok "feloszthatók", abban az értelemben, hogy az egyik bármely pontot kikereshet egy fájlban, és egy adott blokktól-e.

## <a name="serialization-in-avro-library"></a>Szerializálása az Avro Libraryvel
A .NET-kódtár az Avróhoz szerializálási objektumok két módon támogatja:

* **tükröződés** -típusok a JSON-séma automatikusan épül az adatokból a szerződés attribútumai a .NET-típusok szerializálni kell.
* **általános rekord** -egy JSON-sémájában explicit módon szerepel egy bejegyzés által képviselt a [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) osztályhoz, ha nincsenek .NET típusai jelen írja le a sémát a szerializálni kell.

Ha a sémát a író és az olvasó a Stream is ismert, az adatküldés nélkül sémáját. Azokban az esetekben az Avro-objektum kontejner soubor használata esetén a séma tárolja a fájlt. További paraméterek, például az adattömörítés, a használt kodek adható meg. Ezek a forgatókönyvek részletesen leírt, a következő kódot a példákban szemléltetett:

## <a name="install-avro-library"></a>Telepítse az Avro Libraryvel
A következőkre szükség a kódtár telepítése előtt:

* <a href="https://www.microsoft.com/download/details.aspx?id=17851" target="_blank">A Microsoft .NET-keretrendszer 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 vagy újabb)

> [!Note]
> A Microsoft Avro Library NuGet-csomagként már nem érhető el. Ha szeretné használni az Avro Libraryvel Klónozás a [Microsoft.Hadoop.Avro GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) és állíthat össze a kódot a gépen.

## <a name="compile-schemas-using-avro-library"></a>Fordítsa le a sémák használata az Avro Libraryvel
A Microsoft Avro Library tartalmaz egy code generation segédprogram, amely lehetővé teszi, hogy a korábban meghatározott JSON-sémája alapján automatikusan C# típusok létrehozása. A code generation segédprogram nem elosztott bináris futtatható, de az alábbi eljárás segítségével könnyen építhető:

1. Töltse le a .zip fájlt HDInsight SDK forráskódját a legújabb verziójának <a href="https://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">a Microsoft .NET SDK-t a Hadoop</a>. (Kattintson a **letöltése** ikonra, nem a **letölti** lapon.)
2. Bontsa ki a HDInsight SDK-t a gépen egy könyvtár a .NET-keretrendszer 4 telepítve van, és csatlakozik az internethez, a szükséges függőséget NuGet-csomagok letöltése. Az alábbiakban feltételezzük, hogy a forráskód C:\SDK kicsomagolta.
3. Lépjen abba a mappába C:\SDK\src\Microsoft.Hadoop.Avro.Tools, és futtassa a build.bat. (A fájl hívásokat MSBuild a 32 bites terjesztési a .NET-keretrendszer verzióját. Ha szeretné a 64 bites verzióját használja, szerkesztését build.bat, a fájl a hozzászólások.) Győződjön meg arról, hogy a build sikeres. (Egyes rendszerek MSBuild eredményezhet figyelmeztetéseket. Ezek a figyelmeztetések nem befolyásolják a segédprogram mindaddig, amíg nem build tartalmaz hibát.)
4. A lefordított segédprogram C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools található.

A parancssor szintaxisának megismeréséhez, hajtsa végre a következő parancsot a mappát, ahol a code generation segédprogram található: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

A segédprogram teszteléséhez a forráskódja megadott minta JSON-fájl a C# osztályok hozhatja létre. Hajtsa végre a következő parancsot:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Ez feltételezhetően előállításához két C# az aktuális könyvtárban található fájlok: SensorData.cs és Location.cs.

A logika, amely a code generation segédprogramot használja, miközben átalakítja őket a JSON-sémájában C# típusok megismeréséhez tekintse meg a fájl GenerationVerification.feature C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc található.

Névterek kinyert JSON-sémáját, a fájl már említettük, az előző bekezdésben ismertetett logikát használatával. A séma kinyert névterek elsőbbséget élveznek a bármilyen példáira parancssori segédprogramot a /n paramétert. Ha szeretné felülbírálni a névterek, a sémában található, akkor használja a /nf paramétert. Ha például a SampleJSONSchema.avsc my.own.nspace az összes névtér módosításához futtassa a következő parancsot:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Tudnivalók a minták
Ebben a témakörben megadott hat példák bemutatják a Microsoft Avro Library által támogatott különböző forgatókönyveket. A Microsoft Avro Library célja adatfolyam használata. Ezekben a példákban-adatok n keresztül vagy a memória-Streamek helyett fájl Streamek adatbázisok az egyszerűség és a konzisztencia. Éles környezetben a megközelítést forgatókönyv követelményeinek, adatforrás és a kötet, teljesítmény korlátok és más tényezőktől függ.

Az első két példák bemutatják, hogyan szerializálható és deszerializálható adatok memória stream pufferek való tükröződés és általános rekordok használatával. Ebben a két esetben a séma feltételezi az olvasók és írók között lehetnek megosztva, sávon.

A harmadik és a negyedik példák bemutatják, hogyan szerializálható és deszerializálható adatokat az Avro-objektum tároló fájlok használatával. Adatok az Avro tároló fájl tárolja, ha a séma mindig tárolni vele, mert a séma meg kell osztani a deszerializálás.

A minta az első négy példák tartalmazó letölthető a <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">Azure-Kódminták</a> hely.

Az ötödik példa bemutatja egy egyéni tömörítési kodeket használata az Avro-objektum tárolófájlokat. Az ebben a példában tölthető le, amely tartalmazza a kódot egy minta a <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">Azure-Kódminták</a> hely.

A hatodik minta bemutatja, hogyan adatfeltöltés az Azure Blob storage és a Hive használatával egy HDInsight (Hadoop) fürtön elemzésével aztán szerializálása az Avro segítségével. Le is tölthetők: a <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure-Kódminták</a> hely.

Az alábbiakban a hat minták, a témakörben tárgyalt mutató hivatkozásokat:

* <a href="#Scenario1">**Szerializálás a tükröződés** </a> -típusok lehet szerializálni a JSON-séma automatikusan épül az adatok közül a szerződés attribútumok.
* <a href="#Scenario2">**Általános rekordot tartalmazó szerializálási** </a> – a JSON-sémájában explicit módon megadott egy rekordot, ha nincs a .NET-típus nem tükröződés érhető el.
* <a href="#Scenario3">**Objektum tároló fájlokkal való tükröződés szerializálási** </a> – a JSON-sémájában automatikusan létrehozott és és a szerializált adatok egy Avro objektum kontejner soubor keresztül megosztott.
* <a href="#Scenario4">**Általános rekordot tartalmazó objektum tároló fájlok használatával szerializálási** </a> – a JSON-sémájában explicit módon megadott előtt a szerializálási és és a egy Avro objektum kontejner soubor keresztül adatok megosztott.
* <a href="#Scenario5">**Objektum tárolófájlokat használata egy egyéni tömörítési kodek szerializálási** </a> – a példa bemutatja, hogyan hozhat létre az Avro-objektum kontejner soubor Deflate adatok tömörítési kodeket egy testre szabott .NET megvalósítását.
* <a href="#Scenario6">**Avro segítségével a Microsoft Azure HDInsight szolgáltatás az adatok feltöltése** </a> – példa szemlélteti, hogyan kommunikál az Avro-szerializálás a HDInsight szolgáltatást. Ez a példa futtatásához szükséges egy aktív Azure-előfizetéssel, és a egy Azure HDInsight-fürtön való hozzáférést.

## <a name="Scenario1"></a>1. példa: Szerializálás a tükröződés
A JSON-sémájában típusok is automatikusan hozott létre a Microsoft Avro Library segítségével az adatok közül tükröződés szerződés attribútumai a C#-objektumokra szerializálni kell. A Microsoft Avro Library segítségével hoz létre egy [ **IAvroSeralizer<T>**  ](https://msdn.microsoft.com/library/dn627341.aspx) azonosítani lehet szerializálni a mezőket.

Ebben a példában objektumokat (egy **SensorData** tag osztályra **hely** struct) szerializálva vannak memória adatfolyamba, és az adatfolyam pedig deszerializálva. Az eredmény ezután a rendszer összehasonlítja a kezdeti példány ellenőrizheti, hogy a **SensorData** helyreállított objektum megegyezik az eredeti.

Ebben a példában a séma az olvasók és írók, között lehetnek megosztva, így nem szükséges az Avro-objektum formátem feltételezi. Bemutatja, hogyan szerializálható és deszerializálható adatok memória pufferek történő használatával tükröződés a objektum tároló formátumban, ha a séma meg kell osztani az adatokat egy példa: <a href="#Scenario3">objektum tároló fájlokkal való tükröződésszerializálási</a>.

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


## <a name="sample-2-serialization-with-a-generic-record"></a>2. példa: Általános rekordot tartalmazó szerializáció
JSON-séma adható explicit módon meg egy általános rekordban amikor tükröződés nem használható, mivel az adatok nem jeleníthetők meg egy adategyezményben a .NET-osztályok keresztül. Ez a módszer lassabb, mint a tükröződés használatával. Ezekben az esetekben az adatok sémáját is lehet dinamikus, azt jelenti, a fordítás során nem ismert. Szerinti vesszővel elválasztott értékeket (CSV) fájlok, amelyeknek séma nem ismeretlen, amíg a futási időben az Avro formátum az átalakított adatok, amelyek az ilyen jellegű dinamikus forgatókönyv.

Ez a példa bemutatja, hogyan hozhat létre és használhat egy [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) explicit módon kell megadni a JSON-séma, hogyan lehet az adatokkal való feltöltéséhez, majd hogyan szerializálható és deszerializálható azt. Az eredmény ezután a rendszer összehasonlítja a kezdeti példány erősítse meg, hogy a rekord helyreállítása az eredeti azonos.

Ebben a példában a séma az olvasók és írók, között lehetnek megosztva, így nem szükséges az Avro-objektum formátem feltételezi. Bemutatja, hogyan szerializálható és deszerializálható adatok memória pufferek történő használatával egy általános rekordot a objektum tároló formátumban, ha a séma szerepelnie kell a szerializált adatok egy példa: a <a href="#Scenario4">szerializálási objektum tároló fájlok használatával általános rekord</a> példa.

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


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>3. példa: Szerializálási objektum tároló fájlokat és a szerializálási tükröződés való használatához
Ebben a példában a forgatókönyv hasonlít a <a href="#Scenario1"> első példa</a>, ahol a séma implicit módon megadott való tükröződés. A különbség az, hogy itt, a sémát nem veszi az olvasó, amely deszerializálja ismerni. A **SensorData** szerializálni az objektumok és azok implicit módon megadott séma az Avro objektum kontejner soubor által képviselt vannak tárolva a [ **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) osztály.

Ebben a példában a szerializált adatok [ **SequentialWriter<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx) és a deszerializált [ **SequentialReader<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx). Az eredmény ezután a rendszer összehasonlítja a kezdeti példányok identitás biztosítása érdekében.

Az objektum tároló fájlban lévő adatok tömörített keresztül az alapértelmezett [ **Deflate** ] [ deflate-100] .NET-keretrendszer 4 a tömörítési kodek. Tekintse meg a <a href="#Scenario5"> ötödik példa</a> az ebből a témakörből megtudhatja, hogyan használja az újabb és kiváló verzióját a [ **Deflate** ] [ deflate-110] tömörítési kodeket .NET-keretrendszer 4.5 érhető el.

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


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>4. példa: Szerializálási általános rekordot tartalmazó objektum tároló fájlok és a szerializálási használatával
Ebben a példában a forgatókönyv hasonlít a <a href="#Scenario2"> második példa</a>, ahol a séma explicit módon megadott JSON-fájllal. A különbség az, hogy itt, a sémát nem veszi az olvasó, amely deszerializálja ismerni.

A teszt adatkészlet összegyűjtött álló listává [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) keresztül egy explicit módon megadott JSON-sémájában objektumokat, és egy objektum tároló fájl által képviselt majd tárolja a [  **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) osztály. A tároló fájlt egy író, amellyel szerializálni az adatokat, majd mentett fájl memóriastreamje a tömörítetlen hoz létre. A [ **Codec.Null** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) használt az olvasó létrehozásához paraméter határozza meg, hogy, hogy a rendszer nem tömöríti az adatokat.

Az adatok ezután a következő fájl és deszerializálni az objektumok egy gyűjteménybe. Ez a gyűjtemény a rendszer összehasonlítja a kiindulási lista az Avro rekordok, győződjön meg arról, hogy megegyeznek.

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




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>5. példa: Objektum tárolófájlokat használata egy egyéni tömörítési kodek szerializáció
Az ötödik példa bemutatja egy egyéni tömörítési kodeket használata az Avro-objektum tárolófájlokat. Az ebben a példában tölthető le, amely tartalmazza a kódot egy minta a [Azure-Kódminták](https://code.msdn.microsoft.com/Serialize-data-with-the-67159111) hely.

A [Avro specifikációjában](https://avro.apache.org/docs/current/spec.html#Required+Codecs) lehetővé teszi, hogy egy nem kötelező tömörítési kodeket használatát (mellett **Null** és **Deflate** alapértelmezett érték). Ebben a példában nem implementálja az egy új kodek például Snappy (egy támogatott választható kodek az említett a [Avro specifikációjában](https://avro.apache.org/docs/current/spec.html#snappy)). Ez bemutatja, hogyan használhatja a .NET-keretrendszer 4.5 megvalósítása a [ **Deflate** ] [ deflate-110] kodek, amely alapján hatékonyabb tömörítési algoritmust biztosít a [zlib ](https://zlib.net/) tömörítési könyvtárban, mint az alapértelmezett .NET-keretrendszer 4 verziót.

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

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>6. példa: Avro segítségével a Microsoft Azure HDInsight szolgáltatás az adatok feltöltése
A hatodik példa bemutatja az Azure HDInsight szolgáltatással való interakcióhoz kapcsolatos néhány programozási technikákat. Az ebben a példában tölthető le, amely tartalmazza a kódot egy minta a [Azure-Kódminták](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) hely.

A minta az alábbi feladatokat hajtja végre:

* Meglévő HDInsight-fürthöz csatlakozik.
* Szerializálja több CSV-fájlt, és feltölti az eredmény az Azure Blob storage. (A CSV-fájlokat a minta együtt vannak osztva, és AMEX készlet által elosztott korábbi adatok kivonatát képviselő [Infochimps](https://www.infochimps.com/) 1970-2010 időszakra. A minta beolvassa a CSV-fájljaihoz, konvertálja a rekordok példánya a **készlet** osztályt, és ezután tükröződés használatával szerializálja. Tőzsdei típusdefiníció jön létre egy JSON-sémát, a Microsoft Avro Library code generation segédprogram használatával.
* Új táblát hoz létre külső nevű **készletek** a Hive és a hivatkozásokat, hogy az adatok feltöltése az előző lépésben.
* A lekérdezés végrehajtása keresztüli Hive használatával a **készletek** tábla.

Emellett a minta hajt végre egy karbantartási eljárást előtti és utáni műveletek fő végrehajtása. A karbantartás, során törlődnek az összes kapcsolódó Azure Blob-adatok és mappákat, és a Hive-tábla már nincs használatban. A karbantartási eljárást a minta parancssorból is hívhat.

A minta a következő előfeltételek vonatkoznak:

* Egy aktív Microsoft Azure-előfizetés és az előfizetés-azonosítójára.
* Egy felügyeleti tanúsítványt, az előfizetés a megfelelő titkos kulccsal. Az aktuális felhasználó személyes Storage azon a gépen a minta futtatásához telepíteni kell a tanúsítványt.
* Egy aktív HDInsight-fürt.
* A HDInsight-fürt együtt a megfelelő elsődleges vagy másodlagos hozzáférési kulcs az előző előfeltétel, az Azure Storage-fiókhoz csatolva.

Mindent megtalál, az Előfeltételek meg kell adni a minta konfigurációs fájl a minta futtatása előtt. Ehhez két lehetséges módja van:

* Szerkessze az app.config fájlt a mintául szolgáló gyökérkönyvtárában, és ezután hozza létre a mintát
* Először hozza létre a mintát, és szerkessze a AvroHDISample.exe.config hozhat létre a címtárban

Mindkét esetben az összes módosításokat kell elvégezni a **<appSettings>** beállítások szakaszban. Kövesse a megjegyzéseket, a fájlban.
A minta futtatásakor a parancssorban futtassa az alábbi parancsot (ahol a .zip-fájlt a minta segítségével lett feltételezi, hogy kibontott C:\AvroHDISample; Ha ellenkező esetben használja a megfelelő fájl elérési útját):

    AvroHDISample run C:\AvroHDISample\Data

A fürt törléséhez futtassa a következő parancsot:

    AvroHDISample clean

[deflate-100]: https://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: https://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
