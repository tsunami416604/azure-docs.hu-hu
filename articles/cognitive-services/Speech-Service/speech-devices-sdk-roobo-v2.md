---
title: Beszédeszközök SDK Roobo Smart Audio Dev Kit v2 - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások a beszédeszközök SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371583"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Eszköz: Roobo Smart Audio Dev Kit v2

Ez a cikk eszközspecifikus információkat tartalmaz a Roobo Smart Audio Dev Kit2 számára.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. A fejlesztőkészlet két mikro USB csatlakozóval rendelkezik. A bal oldali csatlakozó a fejlesztői készlet áramellátásához szükséges, és az alábbi képen power-ként van kiemelve. A helyes az, hogy ellenőrizzék azt, és van jelölve Debug a képen. 
    ![A fejlesztői készlet csatlakoztatása](media/speech-devices-sdk/roobo-v2-connections.png)
1. A fejlesztői készletet mikro USB-kábellel csatlakoztathatja a hálózati porthoz egy PC-hez vagy hálózati adapterhez. A felső tábla alatt zöld energiajelző világít.
1. A fejlesztői készlet vezérléséhez csatlakoztassa a hibakeresési portot a számítógéphez egy második mikro-USB-kábellel. A megbízható kommunikáció érdekében elengedhetetlen, hogy kiváló minőségű kábelt használjunk.
1. A fejlesztőkészlet et körkörösen - függőlegesen, a felett látható módon a mennyezet felé néző mikrofonokkal


## <a name="development-information"></a>Fejlesztési információk

További fejlesztési információkért tekintse meg a [Roobo fejlesztési útmutatót](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Hangfelvétel/lejátszás

A DDK2 hangműveletek a következő módokon hajthatók végre:
* Használja az ALSA nyílt forráskódú kódtártárakat és azok alkalmazásait.
* Használja appmainprog felület en alkalmazásfejlesztés. DDK2 audio - kapcsolódó szoftver keretrendszer szabványos ALSA keretrendszer, használhatja libasound. Tehát, hogy dolgozzon ki szoftvert közvetlenül. Így az ALSA arecord és aplay segítségével közvetlenül rögzítheti és lejátszhatja a hangot.
