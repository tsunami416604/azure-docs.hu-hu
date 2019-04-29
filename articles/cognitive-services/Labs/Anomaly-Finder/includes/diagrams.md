---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709865"
---
Az adatok a várt értékkel és az alapértelmezett felső és alsó határértékekkel lettek visszaadva. A gyakorlatban megadhat egy [sensitivity] (érzékenység) paramétert, majd saját kezűleg hangolhatja a rendellenességi pontot a (ExpectedValue + sensitivity * UpperMargin) és (ExpectedValue - sensitivity * LowerMargin) értékekkel ((várt érték + érzékenység * felső határ) és (várt érték + érzékenység * alsó határ)). Az [sensitivity] értékének 1-nél nagyobbnak kell lennie. Az alábbiakban látható néhány ábra a hangoláshoz.

> [!NOTE]
> Az ábrákat nem a mintaalkalmazás hozza létre, hanem egy külön eszköz a mintaalkalmazással.

![Hangolás: érzékenység = 1,0](../media/sensitivity_1.png)
![Hangolás: érzékenység 1,5 =](../media/sensitivity_1.5.png)
![Hangolás: érzékenység = 2](../media/sensitivity_2.png)
![Hangolás: érzékenység = 3,5](../media/sensitivity_3.5.png)
