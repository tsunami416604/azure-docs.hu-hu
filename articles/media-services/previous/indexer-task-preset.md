---
title: Feladatkészlet az Azure Media Indexelőhez
description: Ez a témakör áttekintést nyújt az Azure Media Services Media Media Indexer feladatkészletéről.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896043"
---
# <a name="task-preset-for-azure-media-indexer"></a>Feladatkészlet az Azure Media Indexelőhez 

Az Azure Media Indexer egy médiaprocesszor, amelyet a következő feladatok elvégzésére használhat: a médiafájlok és a tartalom kereshetővé tehető, feliratozási sávokat és kulcsszavakat hozhat létre, indexelheti az eszköz részét tartalmazó eszközfájlokat.

Ez a témakör az indexelési feladatnak átadandó feladatkészletet ismerteti. A teljes például lásd: [Médiafájlok indexelése az Azure Media Indexelővel.](media-services-index-content.md)

## <a name="azure-media-indexer-configuration-xml"></a>Az Azure Media Indexer konfigurációs XML-je

Az alábbi táblázat a konfigurációs XML elemeit és attribútumait ismerteti.

|Név|Kötelező|Leírás|
|---|---|---|
|Input (Bemenet)|igaz|Az indexozni kívánt eszközfájl(ok).<br/>Az Azure Media Indexer a következő médiafájlformátumokat támogatja: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>A bemeneti elem **nevében** vagy **listaattribútumában** megadhatja a fájlnevet (eke)t (lásd alább). **input** Ha nem adja meg, hogy melyik eszközfájlt indexelje, a rendszer kiveszi az elsődleges fájlt. Ha nincs beállítva elsődleges eszközfájl, a bemeneti eszköz első fájlja indexelésre kerül.<br/><br/>Az eszközfájl nevének explicit megadásához tegye a következőket:<br/>```<input name="TestFile.wmv" />```<br/><br/>Egyszerre több eszközfájlt is indexelhet (legfeljebb 10 fájl). Ehhez tegye a következőket:<br/>- Hozzon létre egy szöveges fájlt (jegyzékfájl), és adja meg a .lst kiterjesztést.<br/>- Adja hozzá a bemeneti eszközben szereplő összes eszközfájl nevét ehhez a jegyzékfájlhoz.<br/>- Adja hozzá (töltse fel) a jegyzékfájlt az eszközhöz.<br/>- Adja meg a jegyzékfájl nevét a bemeneti lista attribútumában.<br/>```<input list="input.lst">```<br/><br/>**Megjegyzés:** Ha 10-nél több fájlt ad hozzá a jegyzékfájlhoz, az indexelési feladat sikertelen lesz a 2006-os hibakóddal.|
|metaadatok|hamis|A megadott eszközfájl(ok) metaadatai.<br/>```<metadata key="..." value="..." />```<br/><br/>Az előre definiált kulcsok értékeit megadhatja. <br/><br/>Jelenleg a következő kulcsok támogatottak:<br/><br/>**cím** és **leírás** - a nyelvi modell frissítésére szolgál a beszédfelismerés pontosságának javítása érdekében.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**felhasználónév** és **jelszó** - hitelesítésre használt, amikor internetfájlokat tölt le http vagy https-en keresztül.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>A felhasználónév és a jelszó értékek a bemeneti jegyzékben lévő összes médiaURL-re vonatkoznak.|
|funkciók<br/><br/>Hozzáadva az 1.2-es verzióban. Jelenleg az egyetlen támogatott funkció a beszédfelismerés ("ASR").|hamis|A Beszédfelismerés szolgáltatás a következő beállítási gombokkal rendelkezik:<br/><br/>Nyelv:<br/>- A természetes nyelv, hogy fel kell ismerni a multimédiás fájlt.<br/>- Angol, spanyol<br/><br/>Feliratformátumok:<br/>- a kívánt kimeneti feliratformátumok pontosvesszővel elválasztott listája (ha van ilyen)<br/>- ttml;webvtt<br/><br/><br/>Generálási kulcsszavak:<br/>- Logikai jelző, amely meghatározza, hogy szükség van-e kulcsszó XML-fájlra.<br/>- Igaz, de nem tudom, hogy mi a jó Hamis.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Példa az Azure Media Indexer konfigurációs XML-használatára

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

Lásd: [Médiafájlok indexelése az Azure Media Indexelővel.](media-services-index-content.md)

