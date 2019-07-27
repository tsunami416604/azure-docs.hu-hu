---
title: Forgatókönyv rendelkezésre állása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 94fd415909e86a43916ee2f510732a6a6d9c5ed3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552953"
---
# <a name="scenario-availability"></a>Forgatókönyv rendelkezésre állása

A Speech Service SDK számos különböző forgatókönyvet tartalmaz a programozási nyelvek és környezetek széles körében.  Jelenleg nem minden forgatókönyv érhető el az összes programozási nyelven vagy az összes környezetben.  Az alábbiakban az egyes forgatókönyvek rendelkezésre állása látható.

- **Beszédfelismerés (SR), kifejezések listája, szándék, fordítás és helyszíni tárolók**
  - Minden programozási nyelv/környezet, ahol van nyíl hivatkozás <img src="media/index/link.jpg" height="15" width="15"></img> [itt](https://aka.ms/csspeech)találja a rövid útmutató táblázatát.
- **Szöveg – beszéd (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - A TTS REST API minden más helyzetben használható.
- **Wake Word (kulcsszó-megfigyelő/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - A Felébresztési szó (a felderítő/KWS) funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
- **Hang – első virtuális asszisztens**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Beszélgetés átirata**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
- **Call Center átirata**
  - REST API és bármilyen helyzetben használható
- **Codec tömörített hangbemenet**
  - C++/Linux
  - C#/Linux
  - Java/Linux & Android
