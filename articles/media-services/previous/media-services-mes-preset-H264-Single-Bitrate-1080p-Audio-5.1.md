---
title: H264 Egyetlen Bitrate 1080p hang 5.1 |} A Microsoft Docs
description: A témakör áttekintést nyújt a **H264 egyetlen Bitrate 1080p hang 5.1** feladat előbeállítást.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: b42238de-2a3c-4683-ae7f-7ce19ad5162e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 83d1c279552a75b959c19fb37e859806571f48eb
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259475"
---
# <a name="h264-single-bitrate-1080p-audio-51"></a>H264 Egyetlen Bitrate 1080p hang 5.1

`Media Encoder Standard` határozza meg a kódolási készletek is használhatja a kódolási feladat létrehozásakor. Használhatja egy `preset name` mely formátumba, amelyet szeretne kódolása a media-fájl megadásához. Vagy létrehozhat saját JSON vagy XML-alapú készletek (UTF-8 és UTF-16 kódolást használatával. Az egyéni készletet a kódoló majd kellene átadnia. Ez által támogatott összes előre definiált nevek a lista `Media Encoder Standard` kódoló, lásd: [feladat készletek Media Encoder standard](media-services-mes-presets-overview.md).  
  
 Ez a témakör bemutatja a `H264 Single Bitrate 1080p Audio 5.1` előre definiált XML és JSON formátumban...  
  
 A készlet egyetlen MP4-fájl egy sávszélességű 6750 KB/s és AAC 5.1 hang eredményez. Profillal kapcsolatos részletes információkért sávszélességű, mintavételi arány, stb. Ez a készletet, vizsgálja meg az XML- vagy JSON lényegét az alábbiakban. Az egyes melyik minden elem azt jelenti, és az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard-séma](media-services-mes-schema.md).  
  
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
          <Bitrate>6750</Bitrate>  
          <Width>1920</Width>  
          <Height>1080</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6750</MaxBitrate>  
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
          "Bitrate": 6750,  
          "MaxBitrate": 6750,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1080,  
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
