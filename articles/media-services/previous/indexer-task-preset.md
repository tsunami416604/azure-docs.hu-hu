---
title: Azure Media Indexer feladatának beállítása
description: Ez a témakör áttekintést nyújt a Azure Media Services Media Indexer feladatának beállításáról.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74896043"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure Media Indexer feladatának beállítása 

Azure Media Indexer a következő feladatok elvégzéséhez használt adathordozó-feldolgozó: médiafájlok és tartalmak kereshetővé tétele, lezárt feliratozási számok és kulcsszavak létrehozása, az eszköz részét képező indexfájl-fájlok.

Ez a témakör az indexelési feladatnak átadandó feladattípust ismerteti. A teljes példa: [médiafájlok indexelése Azure Media Indexersal](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer konfigurációs XML

Az alábbi táblázat a konfigurációs XML elemeit és attribútumait ismerteti.

|Name (Név)|Kötelező|Leírás|
|---|---|---|
|Input (Bemenet)|igaz|Az indexelni kívánt adatfájl (ok).<br/>A Azure Media Indexer a következő médiafájl-formátumokat támogatja: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Megadhatja a fájl nevét (ke) t a **bemeneti** elem **Name** vagy **List** attribútumában (az alább látható módon). Ha nem határozza meg, hogy melyik adatfájlt szeretné indexelni, az elsődleges fájl lesz kiválasztva. Ha nincs beállítva elsődleges adatfájl, a bemeneti eszköz első fájlja indexelve lesz.<br/><br/>Ha explicit módon meg szeretné adni az eszköz fájljának nevét, tegye a következőket:<br/>```<input name="TestFile.wmv" />```<br/><br/>Egyszerre több adatfájlt is indexelheti (legfeljebb 10 fájlt). Ehhez tegye a következőket:<br/>– Hozzon létre egy szövegfájlt (manifest-fájlt), és adjon hozzá egy. lst kiterjesztést.<br/>-Adja meg a bemeneti objektumban található összes adatfájl nevét ebbe a jegyzékfájlba.<br/>-Adja hozzá (feltölti) a jegyzékfájlt az objektumhoz.<br/>-Adja meg a jegyzékfájl nevét a bemenet List attribútumában.<br/>```<input list="input.lst">```<br/><br/>**Megjegyzés:** Ha több mint 10 fájlt ad hozzá a jegyzékfájlhoz, az indexelési feladat sikertelen lesz az 2006-es hibakód miatt.|
|metaadatok|hamis|A megadott adatfájl (ok) metaadatai.<br/>```<metadata key="..." value="..." />```<br/><br/>Megadhatja az előre definiált kulcsok értékeit. <br/><br/>Jelenleg a következő kulcsok támogatottak:<br/><br/>**cím** és **Leírás** – a nyelvi modell frissítésére szolgál a beszédfelismerés pontosságának javítása érdekében.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**Felhasználónév** és **jelszó** – az internetes fájlok http-vagy https-kapcsolaton keresztüli letöltéséhez használt hitelesítéshez.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>A Felhasználónév és a jelszó érték a bemeneti jegyzékfájlban lévő összes adathordozó URL-címére érvényes.|
|funkciók<br/><br/>Az 1,2-es verzióban lett hozzáadva. Jelenleg az egyetlen támogatott funkció a beszédfelismerés ("ASR").|hamis|A beszédfelismerési funkció a következő beállítások kulcsokkal rendelkezik:<br/><br/>Nyelv:<br/>– A multimédiás fájlban felismerhető természetes nyelv.<br/>-Angol, Spanyol<br/><br/>CaptionFormats:<br/>– a kívánt kimeneti felirat formátumának pontosvesszővel tagolt listája (ha van ilyen)<br/>-ttml; webvtt<br/><br/><br/>GenerateKeywords:<br/>– Logikai jelző, amely meghatározza, hogy szükség van-e egy kulcsszó XML-fájlra.<br/>Igaz Hamis.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer konfigurációs XML-példa

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
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>További lépések

Lásd: [médiafájlok indexelése Azure Media Indexerokkal](media-services-index-content.md).

