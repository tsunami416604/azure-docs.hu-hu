---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888696"
---
Az adatok a várt értékkel és az alapértelmezett felső és alsó határértékekkel lettek visszaadva. A gyakorlatban megadhat egy [sensitivity] (érzékenység) paramétert, majd saját kezűleg hangolhatja a rendellenességi pontot a (ExpectedValue + sensitivity * UpperMargin) és (ExpectedValue - sensitivity * LowerMargin) értékekkel ((várt érték + érzékenység * felső határ) és (várt érték + érzékenység * alsó határ)). Az [sensitivity] értékének 1-nél nagyobbnak kell lennie. Az alábbiakban látható néhány ábra a hangoláshoz.

> [!NOTE]
> Az ábrákat nem a mintaalkalmazás hozza létre, hanem egy külön eszköz a mintaalkalmazással.

![Hangolás: érzékenység = 1,0](../media/sensitivity_1.png)
![Hangolás: érzékenység 1,5 =](../media/sensitivity_1.5.png)
![Hangolás: érzékenység = 2](../media/sensitivity_2.png)
![Hangolás: érzékenység = 3,5](../media/sensitivity_3.5.png)