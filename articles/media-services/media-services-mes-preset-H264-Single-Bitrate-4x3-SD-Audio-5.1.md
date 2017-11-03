---
title: "H264 Egyszeres sávszélességű 4 x 3 SD hang 5.1 |} Microsoft Docs"
description: "A témakör áttekintést a ** H264 egyféle sávszélességű 4 x 3 SD hang 5.1* * feladat készletet."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: e55dd302-2f42-46b5-ae17-bd3c72329c03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: c2e1b5a5d246a512a847c0ca2601d685a9aaeb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="h264-single-bitrate-4x3-sd-audio-51"></a>H264 Egyféle sávszélességű 4 x 3 SD hang 5.1
`Media Encoder Standard`Meghatározza a kódolási készletek kódolási feladatok létrehozásakor használható. Használhatja a `preset name` adhatja meg, melyik formátumba kódolja a médiafájl szeretné. Másik lehetőségként létrehozhat saját JSON- vagy XML-alapú készletek (UTF-8 vagy UTF-16 kódolás használatával. Az egyéni készletet a kódoló majd át lesz. Ez által támogatott összes előre definiált nevek listája `Media Encoder Standard` kódoló, lásd: [feladat készletek a Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Ez a témakör bemutatja a `H264 Single Bitrate 4x3 SD Audio 5.1` beállított XML és a JSON formátumban.  
  
 Ezt a készletet hoz létre egyetlen MP4-fájl egy sávszélességű 1800 kbit/s és AAC 5.1 hang. Profillal kapcsolatos részletes információkért sávszélességű mintavételi arány stb. a készletet, vizsgálja meg az XML- vagy JSON-ban megadva. Az egyes azt jelenti, hogy milyen egyes elemei, és az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard séma](media-services-mes-schema.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>1800</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1800</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 1800,  
          "MaxBitrate": 1800,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
