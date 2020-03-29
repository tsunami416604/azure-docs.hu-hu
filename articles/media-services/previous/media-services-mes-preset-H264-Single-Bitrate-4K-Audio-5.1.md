---
title: H264 Egybitráta 4K Audio 5.1 | Microsoft dokumentumok
description: A témakör áttekintést ad a **H264 Single Bitrate 4K Audio 5.1** feladatkészletről.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 9c4b133ae74438def30f4fcd9e49a5ca5e6f9cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463565"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>H264 Single Bitrate 4K Audio 5.1
`Media Encoder Standard`meghatározza a kódolási feladatok létrehozásakor használható kódolási készleteket. Az a segítségével `preset name` megadhatja, hogy milyen formátumba szeretné kódolni a médiafájlt. Vagy létrehozhat saját JSON- vagy XML-alapú készleteket (UTF-8 vagy UTF-16 kódolással. Ezután adja át az egyéni készletet a kódolónak. A `Media Encoder Standard` kódoló által támogatott összes előre beállított név listáját a [Media Encoder Standard feladatkészletek című témakörében tartalmazza.](media-services-mes-presets-overview.md)  
  
 Ez a `H264 Single Bitrate 4K Audio 5.1` témakör a készletet mutatja (XML és JSON formátumban).  
  
 Ez a készlet egyetlen MP4-fájlt hoz létre 18000 kbps bitsebességgel és AAC 5.1-es hanggal. Az előre beállított profillal, bitrátával, mintavételi sebességgel stb. Az egyes elemek jelentéseiről és az egyes elemek érvényes értékeinek magyarázatáról a [Media Encoder Standard sémában](media-services-mes-schema.md)lehet eltekinteni.  
  
> [!NOTE]
>  A Premium lefoglalt egység típusát 4K kódolással kell lefoglalnia. További információt a [Kódolás méretezése](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)című témakörben talál.  
  
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
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
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
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
