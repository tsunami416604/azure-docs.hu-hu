---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348151"
---
Az adatokat, a várt érték és az alapértelmezett felső és alsó margó lett visszaadva. A gyakorlatban egy [érzékenységi] paraméter határozza meg, és használja (ExpectedValue + érzékenységi * UpperMargin), a felső határa és (ExpectedValue - érzékenységi * LowerMargin), az alsó határ értékének a anomáliadetektálási hangolására irányítsa yourselves. A [érzékenységi] értékének 1-nél nagyobbnak kell lennie. Az alábbiakban néhány diagramok a finomhangoláshoz.

> [!NOTE]
> Az ábrák nem generálja a mintaalkalmazáshoz. A mintaalkalmazás külön eszköz létrehozzák őket.

![– Hangolási: érzékenységi = 1.0](../media/sensitivity_1.png)
![– hangolási: érzékenységi 1.5-ös =](../media/sensitivity_1.5.png)
![– hangolási: érzékenységi = 2](../media/sensitivity_2.png)
![– hangolási: érzékenységi = 3.5](../media/sensitivity_3.5.png)