---
title: H264 Egybitrát 4x3 SD Media Encoder Standard készlet - Azure | Microsoft dokumentumok
description: A témakör áttekintést ad a **H264 Single Bitrate 4x3 SD** feladatkészletről.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 171689fe-7c4f-4d5a-b48e-281136d8ac97
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: a1359bacdf6735ca49e22d7c7b6d0014d56f1735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61129474"
---
# <a name="h264-single-bitrate-4x3-sd"></a>H264 Single Bitrate 4x3 SD
`Media Encoder Standard`meghatározza a kódolási feladatok létrehozásakor használható kódolási készleteket. Az a segítségével `preset name` megadhatja, hogy milyen formátumba szeretné kódolni a médiafájlt. Vagy létrehozhat saját JSON- vagy XML-alapú készleteket (UTF-8 vagy UTF-16 kódolással. Ezután adja át az egyéni készletet a kódolónak. A `Media Encoder Standard` kódoló által támogatott összes előre beállított név listáját a [Media Encoder Standard feladatkészletek című témakörében tartalmazza.](media-services-mes-presets-overview.md)  
  
 Ez a `H264 Single Bitrate 4x3 SD` témakör az előre beállított xml és JSON formátumban jelenik meg.  
  
 Ez az előre beállított egy 1800 kbps bitráta-sebességű MP4-fájlt és sztereó AAC hangot eredményez. Az előre beállított profillal, bitrátával, mintavételi sebességgel stb. A készletek egyes elemeinek és az egyes elemek érvényes értékeinek magyarázatát a [Media Encoder Standard séma](media-services-mes-schema.md) témakörében talál.  
  
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
