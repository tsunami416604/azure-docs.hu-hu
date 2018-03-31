---
title: 'Elemzés platformok: Stream Analytics alatt futó Apache Storm összehasonlítása |} Microsoft Docs'
description: Útmutató a felhőalapú analytics platform kiválasztása az Apache Storm összehasonlítás Stream Analytics segítségével beolvasása. Szolgáltatások és különbségek megértése.
keywords: elemzési platformot, analytics platform, elemzés felhőplatform, storm összehasonlítása
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: 48599c362c5db50ebec3acd0aaa4359439edb00b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>A folyamatos átviteli analytics platform kiválasztása: az Apache Storm és az Azure Stream Analytics összehasonlítása
Azure adatfolyam-továbbítási adatok elemzése több megoldást nyújt: [Azure Streaming Analytics](https://docs.microsoft.com/azure/stream-analytics/) és [Azure HDInsight alatt futó Apache Storm](https://azure.microsoft.com/services/hdinsight/apache-storm/). Mindkét analytics platform előnyei a PaaS megoldást. Azonban a platformok jelentős eltérések képességeit, valamint hogyan konfigurálhatja és kezelheti azokat. 

Ez a cikk ismerteti a szolgáltatások segítségével válassza ki az Apache Storm és Azure Stream Analytics felhő analytics platformként között egymás melletti összehasonlítása. 

## <a name="general-features"></a>Általános szolgáltatások

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Az Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>A HDInsight alatt futó Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Nyílt forráskódú?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nem. Az Azure Stream Analytics a Microsoft védett kínál.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Igen. Apache Storm egy olyan licenccel rendelkező Apache technológia.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>A Microsoft támogatási?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Igen </p>
            </td>
            <td width="246" valign="top">
                <p>
Igen </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Hardverkövetelmények</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nincs. Az Azure Stream Analytics egy olyan Azure-szolgáltatás.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nincs. Apache Storm egy olyan Azure-szolgáltatás.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Legfelső szintű egység</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Felhasználók telepítheti és figyelheti a folyamatos átviteli feladat.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Felhasználók központi telepítése, és figyelje az egész fürt, amelyek több Storm feladatok, valamint egyéb munkaterhelések (beleértve a kötegelt) tartalmazhatnak.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Tarifacsomag</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Áron által feldolgozott adatokat és a szükséges, hogy fut-e a feladat óránként streamelési egységek számát. 
                </p>
                    <p>További információkért lásd: <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Stream Analytics Díjszabásáról</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A vásárlás fürt-alapú, és fel van töltve, a rendszer fut a fürtön, független telepített feladatok ideje alapján.
                </p>
                <p>
További információkért lásd: <a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight árképzési</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Szerzői műveletek

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Az Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>A HDInsight alatt futó Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Képességek: SQL DSL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Igen. A Stream Analytics egy SQL-szerű nyelv nyújt átalakítások létrehozásához.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nem. Felhasználók írhat kódot a Java vagy C# vagy Trident az API-kkal.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Képességek: Az ideiglenes operátorok?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ablakos összesítéseket és az időalapú illesztéseket alapértelmezés szerint támogatott.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Az ideiglenes operátorok a felhasználónak kell végrehajtania.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fejlesztési felület</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Felhasználók létrehozása, hibakeresési, és figyelje a feladatokat az Azure-portálon keresztül mintaadatok származó élő adatfolyam használata.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Felhasználók a .NET fejlesztés, hibakeresését és figyelése a Visual Studio alkalmazással. Java vagy más nyelvű felhasználók használhatják az általuk választott IDE.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Hibaelhárítási támogatás</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Alapszintű állapotának és műveleteinek feladatnaplóit elérhetők segítségére hibakeresésben. A Stream Analytics jelenleg megadásakor a felhasználók nem adja meg a tartalmat, vagy mekkora a tartalom megtalálható a naplók (azaz részletes üzemmód).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Részletes naplózási érhetők el. A felhasználók naplók a Visual Studio vagy jelentkezik be a fürt és a naplók elérése közvetlenül.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>A bővítési egyéni kód használatával?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Részlegesen támogatja a JavaScript felhasználó által megadott függvények rendelkező. További információkért lásd: <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">JavaScript UDF integrációs</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Igen. Felhasználók is egyéni kód írását, a C#, Java, vagy bármely más, a Storm támogatott nyelven.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Adatforrások (bemeneti) és -kimenetek ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Az Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>A HDInsight alatt futó Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Bemeneti adatforrások</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Az Azure Event Hubs és az Azure Blob storage.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Összekötők Azure Event Hubs, Azure Service Bus, Kafka és több érhetők el. Felhasználók hozhatnak létre további összekötők egyéni kód használatával.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>A bemeneti adatok formátumok</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Tetszőleges méretű egyéni kód használatával a felhasználók is végrehajthatják.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>kimenetek</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
A folyamatos átviteli feladatnak rendelkezhet több kimenet. Támogatott kimenetek az Azure Event Hubs, Azure Blob storage, Azure Table storage, Azure SQL Database és Power bi-ban.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm sok kimenetek támogatja a topológiában, és minden egyes kimeneti lehet alárendelt feldolgozási egyéni logikát. A Storm összekötők Power bi-ban, az Azure Event Hubs, Azure Blob storage, Azure Cosmos DB, SQL és HBase magában foglalja. Felhasználók hozhatnak létre további összekötők egyéni kód használatával.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Adatok kódolási formátum</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Adatok UTF-8 használatával kell formázni.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Felhasználók bármilyen egyéni kód használatával adatok kódolási formátum is létrehozható.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Felügyelete és műveletei ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Az Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>A HDInsight alatt futó Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Feladat telepítési modell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure-portálon, a PowerShell és a REST API-k.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure-portálon, a PowerShell, a Visual Studio és a REST API-k.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoring (statisztikák, számlálók, stb.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Figyelése Azure portál és a REST API-k segítségével történik. Felhasználók is konfigurálhatja az Azure riasztásokat.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Figyelés a Storm felhasználói felület és a REST API-k segítségével történik.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Méretezhetőség</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Méretezhetőség minden folyamatos átviteli egységek (SUs) számát határozza meg. Folyamatos átviteli egységenként legfeljebb 1 MB/másodperc, a maximális 50 egységek dolgozza fel. További információkért lásd: <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">átviteli sebesség növelése méretezési</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Méretezhetőség a HDInsight alatt futó Storm-fürt a csomópontok száma határozza meg. A felhasználó Azure kvóta határozza meg a csomópontok száma a felső korlátot.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Adatok feldolgozási korlátok</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
A felhasználók növelheti az adatok feldolgozása vagy növelésével vagy csökkentésével a Streaming Units számát és a felső korlátja 1 GB/s optimalizálni a költségeket.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Felhasználók méretezheti fürtméret felfelé vagy lefelé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Állítsa le vagy folytatása</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Állítsa le, és indítsa újra az utolsó helyen leállt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Legutóbbi folytatni helyezze leállított vízjel alapján.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Frissítési szolgáltatás és -keretrendszer</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Automatikus javítás állásidő nélkül.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Automatikus javítás állásidő nélkül.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Az üzletmenet folytonossága garantált SLA-k a magas rendelkezésre álló szolgáltatáson keresztül</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>Szolgáltatásiszint-szerződésben garantált 99,9 %-os hasznos üzemidő</li>
                <li>A hibák automatikus helyreállítás</li>
                <li>Állapot-nyilvántartó az ideiglenes operátorok beépített helyreállítása</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
Szolgáltatásiszint-szerződésben garantált 99,9 %-os üzemidőt a Storm-fürt. 
                </p>
                <p>
Apache Storm egy hibatűrő adatfolyam platform. Azonban a felhasználó felelőssége, hogy győződjön meg arról, hogy a folyamatos átviteli feladat futtatása folyamatos.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Speciális funkciók ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Az Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>A HDInsight alatt futó Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Késő érkezés és soron eseménykezelésnek</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Beépített konfigurálható házirendek események sorrendjének módosításához, dobja el az események, vagy állítsa be az esemény időpontja.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Felhasználók logika ebben a forgatókönyvben kezeléséhez meg kell valósítania.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Referenciaadatok</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Az Azure Blob storage egy legfeljebb 100 MB memória gyorsítótárának referenciaadatok érhető el. Hivatkozás az adatok a szolgáltatás frissítése.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nem határoz meg az adatok mérete. Összekötők HBase, az Azure Cosmos adatbázis, az SQL Server és az Azure érhetők el. Felhasználók hozhatnak létre további összekötők egyéni kód használatával. Referenciaadatok frissíteni kell az egyéni kód használatával.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integráció a gépi tanulás</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Közzétett Azure Machine Learning modellek konfigurálhatók funkciók feladat létrehozása során. További információkért lásd: <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">skálája a Machine Learning funkciók</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A Storm Boltokhoz keresztül elérhető.
                </p>
            </td>
        </tr>
    </tbody>
</table>
