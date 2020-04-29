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
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400148"
---
# <a name="scenario-availability"></a>Forgatókönyv rendelkezésre állása

A Speech SDK számos különféle forgatókönyvet tartalmaz a programozási nyelvek és környezetek széles körében. Nem minden forgatókönyv érhető el minden programozási nyelven vagy az összes környezetben. Az alábbiakban az egyes forgatókönyvek rendelkezésre állása látható.

- **Beszédfelismerés (SR), kifejezések listája, szándék, fordítás és helyszíni tárolók**
  - C++/Windows & Linux & macOS rendszerhez
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE és Android)
  - JavaScript (böngésző és NodeJS)
  - Python
  - Swift
  - Objective-C  
- **Szöveg – beszéd (TTS)**
  - C++/Windows & Linux rendszerhez
  - C#/Windows & UWP & Unity
  - Java (JRE és Android)
  - Python
  - Swift
  - Objective-C
  - A TTS REST API minden más helyzetben használható.
- **Kulcsszavas pecsételés (KWS)**
  - C++/Windows & Linux rendszerhez
  - C#/Windows & Linux rendszerhez
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - A "a" kulcsszó-bepecsételés (KWS) funkció bármilyen mikrofonos típussal működhet, a hivatalos KWS-támogatás azonban jelenleg az Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofon-tömbökre korlátozódik.
- **Hangvezérelt asszisztensek**
  - C++/Windows & Linux & macOS rendszerhez
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Beszélgetés átirata**
  - C++/Windows & Linux rendszerhez
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
- **Több eszközre kiterjedő beszélgetés**
  - C++/Windows
  - C# (Framework & .NET Core)/Windows
- **Call Center átirata**
  - REST API és bármilyen helyzetben használható
- **Codec tömörített hangbemenet**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android és iOS
