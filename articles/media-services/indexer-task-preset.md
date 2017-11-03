---
title: "A feladat az Azure Media Indexer az adott néven beállítás"
description: "Ez a témakör áttekintést nyújt az Azure Media Indexer beállított feladat."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: adsolank;juliako;
ms.openlocfilehash: ae6c4da189cd6637b4e1fa9274473b62f6664e51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="task-preset-for-azure-media-indexer"></a>A feladat az Azure Media Indexer az adott néven beállítás

Az Azure Media Indexer egy adathordozó processzor, amelyekkel a következő feladatok végezhetők el: médiafájlok és a tartalom kereshető győződjön, lezárt feliratok nyomon követi és kulcsszavak létrehozása, az eszköz részét képező eszköz fájlok indexelése.

Ez a témakör ismerteti a feladat, hogy szeretné-e az indexelő feladat átadása az adott néven beállítás. Teljes példa, lásd: [médiafájlok az Azure Media Indexer indexelő](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Az Azure Media Indexer konfigurációs XML-kód

A következő táblázat ismerteti az elemek és attribútumok XML-konfiguráció.

|Név|Kötelező|Leírás|
|---|---|---|
|Input (Bemenet)|Igaz|Eszköz (oka) t, amelyet szeretne index.<br/>Az Azure Media Indexer támogatja a következő media formátumok: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Adhat meg a fájl neve (s) a **neve** vagy **lista** attribútuma a **bemeneti** elem (mivel lásd alább). Ha nem adja meg a melyik index eszköz fájlt, az elsődleges fájl nek. Ha nem elsődleges eszköz fájl be van állítva, az első fájl a bemeneti objektum indexelt.<br/><br/>Explicit módon adja meg az eszköz fájl nevét, a következőket kell tennie:<br/>```<input name="TestFile.wmv" />```<br/><br/>Több eszköz fájlok egyszerre (legfeljebb 10) is elvégezheti az indexelést. Ehhez tegye a következőket:<br/>– Hozzon létre egy szövegfájlt (jegyzékfájl), és adjon neki egy .lst bővítmény.<br/>-Hozzáadása az eszköz fájlnevek listáját a bemeneti eszközt a jegyzékfájlt.<br/>-Adjon hozzá (feltöltés) thanifest fájlt az eszköz.<br/>-Adja meg a jegyzékfájl nevének a bemeneti lista attribútum.<br/>```<input list="input.lst">```<br/><br/>**Megjegyzés:** Ha több mint 10 fájlokat ad hozzá a jegyzékfájlt, az indexelő feladat 2006 hibakóddal meghiúsul.|
|Metaadatok|hamis|A megadott objektum (ok) ban metaadatait.<br/>```<metadata key="..." value="..." />```<br/><br/>Előre definiált kulcsok értéket ad meg. <br/><br/>Jelenleg a következő kulcsok támogatottak:<br/><br/>**cím** és **leírás** - használt frissítése a nyelvi beszédfelismerés pontosságának javítása érdekében.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**felhasználónév** és **jelszó** - hitelesítéshez a http vagy HTTPS protokollon keresztül internetes fájlok letöltésekor használt.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>A felhasználónév és jelszó értéket alkalmazni a bemeneti jegyzékben az adathordozó URL-címet.|
|funkciókkal<br/><br/>A hozzáadott 1.2-es verzióját. Az egyetlen támogatott szolgáltatás jelenleg Beszédfelismerés ("automatikus").|hamis|A beszédfelismerés funkció az alábbi beállítások kulcsok rendelkezik:<br/><br/>Nyelv:<br/>-A természetes nyelvű, hogy felismerje a multimédia fájlban.<br/>-Angol, spanyol<br/><br/>CaptionFormats:<br/>– egy listában pontosvesszővel elválasztva a kívánt kimeneti fejléc formázza (ha van ilyen)<br/>-ttml; Számi; webvtt<br/><br/><br/>GenerateAIB:<br/>-A logikai jelző, adja meg, hogy-e egy AIB fájl szükséges (az SQL Server és az ügyfél indexelő IFilter való használatra). További információkért lásd: az Azure Media Indexer és az SQL Server használatával AIB fájlok.<br/>-Igaz; Hamis<br/><br/>GenerateKeywords:<br/>-A logikai jelző, adja meg, hogy-e a kulcsszó XML-fájl szükséges.<br/>-Igaz; Hamis.|

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
  
## <a name="next-steps"></a>Következő lépések

Lásd: [médiafájlok az Azure Media Indexer indexelő](media-services-index-content.md).

