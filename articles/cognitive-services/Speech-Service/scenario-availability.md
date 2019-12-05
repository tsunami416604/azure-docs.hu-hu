---
title: Forgatókönyv rendelkezésre állása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech SDK számos különféle forgatókönyvet tartalmaz a programozási nyelvek és környezetek széles körében. Nem minden forgatókönyv érhető el minden programozási nyelven vagy az összes környezetben. Az alábbiakban az egyes forgatókönyvek rendelkezésre állása látható.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: e8fc6e8c2f37dcd3edec24fb4d8ed81b32a84bd0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816595"
---
# <a name="scenario-availability"></a>Forgatókönyv rendelkezésre állása

A Speech SDK számos különféle forgatókönyvet tartalmaz a programozási nyelvek és környezetek széles körében. Nem minden forgatókönyv érhető el minden programozási nyelven vagy az összes környezetben. Az alábbiakban az egyes forgatókönyvek rendelkezésre állása látható.

- **Beszédfelismerés (SR), kifejezések listája, szándék, fordítás és helyszíni tárolók**
  - Minden programozási nyelv/környezet, ahol van nyíl hivatkozás <img src="media/index/link.jpg" height="15" width="15"></img> [itt](https://aka.ms/csspeech)találja a rövid útmutató táblázatát.
- **Szöveg – beszéd (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE és Android)
  - Python
  - Swift
  - Objective-C
  - A TTS REST API minden más helyzetben használható.
- **Kulcsszavas pecsételés (KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - A "a" kulcsszó-bepecsételés (KWS) funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
- **Hangvezérelt asszisztensek**
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
  - Java/Linux, Android és iOS
