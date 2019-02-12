---
title: H264 egyféle sávszélességű 720p Media Encoder Standard előre beállított – Azure |} A Microsoft Docs
description: A témakör áttekintést nyújt a **H264 egyféle sávszélességű 720p** feladat előbeállítást.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 8eb01ba54c51508b7e3ec8f40e54ac6bcbc4f8a5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993241"
---
# <a name="h264-single-bitrate-720p"></a>H264 Egyféle sávszélességű 720p
`Media Encoder Standard` határozza meg a kódolási készletek is használhatja a kódolási feladat létrehozásakor. Használhatja egy `preset name` mely formátumba, amelyet szeretne kódolása a media-fájl megadásához. Vagy létrehozhat saját JSON vagy XML-alapú készletek (UTF-8 és UTF-16 kódolást használatával. Az egyéni készletet a kódoló majd kellene átadnia. Ez által támogatott összes előre definiált nevek a lista `Media Encoder Standard` kódoló, lásd: [feladat készletek Media Encoder standard](media-services-mes-presets-overview.md).  
  
 Ez a témakör bemutatja a `H264 Single Bitrate 720p` előre definiált XML és JSON formátumban.  
  
 Ez egy sávszélességű 4500 KB/s és sztereó AAC hang előre beállított eredményez olyan egyetlen MP4 fájlt. Profillal kapcsolatos részletes információkért sávszélességű, mintavételi arány, stb. Ez a készletet, vizsgálja meg az XML- vagy JSON lényegét az alábbiakban. Az egyes milyen egyes elemei, és ezen készletek azt jelenti, hogy az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard-séma](media-services-mes-schema.md) témakör.  
  
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
          <Bitrate>4500</Bitrate>  
          <Width>1280</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>4500</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
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
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
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
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
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
