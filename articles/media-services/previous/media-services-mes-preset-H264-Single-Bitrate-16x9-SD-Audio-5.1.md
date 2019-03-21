---
title: H264 Egyszeres sávszélességű 16 x 9 SD hang 5.1 |} A Microsoft Docs
description: A témakör áttekintést nyújt a **H264 egyféle sávszélességű 16 x 9 SD hang 5.1** feladat előbeállítást.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d57cd39f-60e8-4248-a21d-c5b9f096b0ce
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 730854f572e1e2ab21140f53d59b7593ad26cb05
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259873"
---
# <a name="h264-single-bitrate-16x9-sd-audio-51"></a>H264 Egyféle sávszélességű 16 x 9 SD hang 5.1
`Media Encoder Standard` határozza meg a kódolási készletek is használhatja a kódolási feladat létrehozásakor. Használhatja egy `preset name` mely formátumba, amelyet szeretne kódolása a media-fájl megadásához. Vagy létrehozhat saját JSON vagy XML-alapú készletek (UTF-8 és UTF-16 kódolást használatával. Az egyéni készletet a kódoló majd kellene átadnia. Ez által támogatott összes előre definiált nevek a lista `Media Encoder Standard` kódoló, lásd: [feladat készletek Media Encoder standard](media-services-mes-presets-overview.md).  
  
 Ez a témakör bemutatja a `H264 Single Bitrate 16x9 SD Audio 5.1` előre definiált XML és JSON formátumban.  
  
 A készlet egyetlen MP4-fájl egy 2200 KB/s és AAC 5.1 hang sávszélességű eredményez. Profillal kapcsolatos részletes információkért sávszélességű, mintavételi arány, stb. Ez a készletet, vizsgálja meg az XML- vagy JSON lényegét az alábbiakban. Az egyes melyik minden elem azt jelenti, és az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard-séma](media-services-mes-schema.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>2200</Bitrate>  
          <Width>848</Width>  
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
          <MaxBitrate>2200</MaxBitrate>  
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
          "Bitrate": 2200,  
          "MaxBitrate": 2200,  
          "BufferWindow": "00:00:05",  
          "Width": 848,  
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
