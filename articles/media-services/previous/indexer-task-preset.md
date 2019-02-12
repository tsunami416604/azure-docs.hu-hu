---
title: Az Azure Media Indexer előbeállítást feladat
description: Ez a témakör áttekintést nyújt az Azure Media Indexer előbeállítást feladat.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/08/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 8790a5dd596a98409db9a3470b17461359882a38
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994353"
---
# <a name="task-preset-for-azure-media-indexer"></a>Az Azure Media Indexer előbeállítást feladat 

Az Azure Media Indexer segítségével a következő feladatok végrehajtásával médiafeldolgozó: adathordozó-fájlok és tartalmak kereshetővé, lezárt feliratozási nyomon követi és a kulcsszavak hozhat létre, az eszköz részét képező adategység-fájlok indexelése.

Ez a témakör ismerteti a feladatot, hogy szeretne-e át az indexelési feladatot előbeállítást. Teljes példa: [médiafájlokat az Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Az Azure Media Indexer konfigurációs XML

A következő táblázat ismerteti az elemek és attribútumok az XML-konfiguráció.

|Name (Név)|Kötelező|Leírás|
|---|---|---|
|Input (Bemenet)|true|Az eszközintelligencia fájl, amelyet szeretne indexelni.<br/>Az Azure Media Indexer a következő fájl médiaformátumok támogatja: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Megadhatja a fájl neve (s) a **neve** vagy **lista** attribútuma a **bemeneti** elem (ahogy az alább látható). Ha nem ad meg melyik adategységfájlon indexre, az elsődleges fájl kivétele történik. Ha nincsenek elsődleges eszközfájl van beállítva, az első fájl a bemeneti objektuma indexelve van.<br/><br/>Az eszköz nevét adja meg explicit módon, hajtsa végre:<br/>```<input name="TestFile.wmv" />```<br/><br/>Több eszköz fájlok egyszerre (legfeljebb 10) indexelésére is használhatja. Ehhez tegye a következőket:<br/>– Hozzon létre egy szövegfájlt (Alkalmazásjegyzék-fájl), és adjon meg egy .lst bővítmény.<br/>-Az összes eszközintelligencia-nevek listája modul hozzáadása a bemeneti objektuma a jegyzékfájlt.<br/>– Az eszköz adjon hozzá (feltöltés) a jegyzékfájl fájlt.<br/>– A bemeneti lista attribútum adja meg a jegyzékfájl neve.<br/>```<input list="input.lst">```<br/><br/>**Megjegyzés:** Ha több mint 10 fájlokat ad hozzá a jegyzékfájlt, az indexelési feladatot 2006 hibakóddal sikertelen lesz.|
|metaadatok|false|A megadott eszköz fájl metaadatait.<br/>```<metadata key="..." value="..." />```<br/><br/>Előre definiált kulcsok értéket ad meg. <br/><br/>Jelenleg a következő kulcsok támogatottak:<br/><br/>**cím** és **leírás** – beszédfelismerés pontosságának javítása érdekében a nyelvi modell frissítése használatos.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**felhasználónév** és **jelszó** – a hitelesítéshez a http vagy https használatával internetes fájlok letöltése esetén használatos.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>A username és password értékeket a bemeneti jegyzékfájlban összes médiafájljainak URL-címei érvényesek.|
|funkciókkal<br/><br/>Az 1.2-es verziójában hozzáadva. Az egyetlen támogatott szolgáltatás jelenleg a beszédfelismerés ("ASR").|false|A beszédfelismerés funkció az alábbi beállítások kulcsok rendelkezik:<br/><br/>Nyelv:<br/>-A természetes nyelv ismeri fel a multimédiás fájlban.<br/>– Angol, spanyol<br/><br/>CaptionFormats:<br/>– egy listában pontosvesszővel elválasztva a kívánt kimeneti felirat formátumok (ha van)<br/>-ttml; Számi; webvtt<br/><br/><br/>GenerateAIB:<br/>-A logikai jelzőt megadó AIB-fájl-e szükséges (az SQL Server és az ügyfél az indexelő ifilterrel való használatra). További információkért lásd: AIB-fájlok használata az Azure Media Indexer és az SQL Server.<br/>– Igaz FALSE (hamis)<br/><br/>GenerateKeywords:<br/>-A logikai jelzőt megadó-e egy kulcsszót XML-fájl megadása kötelező.<br/>– Igaz FALSE (hamis).|

## <a name="azure-media-indexer-configuration-xml-example"></a>Az Azure Media Indexer konfigurációs XML-példa

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>További lépések

Lásd: [médiafájlokat az Azure Media Indexer](media-services-index-content.md).

