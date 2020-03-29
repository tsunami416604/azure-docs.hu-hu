---
title: fájl belefoglalása
description: fájl belefoglalása
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392375"
---
Az alábbi táblázat ismerteti az Azure Scheduler főbb kvótáit, korlátait, alapértelmezett és szabályozásait.

| Erőforrás | Korlát leírása |
| -------- | ----------------- |
| **Feladat mérete** | A maximális feladat mérete 16 000. Ha egy PUT vagy PATCH művelet ennél a korlátnál nagyobb feladatot eredményez, a rendszer 400 hibás kérelem állapotkódot ad vissza. | 
| **Feladatgyűjtemények** | Azure-előfizetésenként legfeljebb 200 000 feladatgyűjtemény érhető el. | 
| **Feladatok gyűjteményenként** | Alapértelmezés szerint a feladatok maximális száma öt feladat egy ingyenes feladatgyűjteményben és 50 feladat egy szabványos feladatgyűjteményben. Módosíthatja a feladatok maximális számát egy feladatgyűjteményben. A feladatgyűjteményben lévő összes feladat a feladatgyűjteményben beállított értékre korlátozódik. Ha a maximális feladatkvótánál több feladatot próbál létrehozni, a kérés egy 409-es ütközési állapotkóddal sikertelen lesz. | 
| **Ideje a kezdési időpontnak** | A maximális "kezdési idő" 18 hónap. |
| **Ismétlődési tartomány** | A maximális ismétlődési időtartam 18 hónap. | 
| **Frekvencia** | Alapértelmezés szerint a maximális gyakorisági kvóta egy óra egy ingyenes feladatgyűjteményben és egy perc egy szabványos feladatgyűjteményben. <p>A feladatgyűjtemény maximális gyakoriságát a maximálisnál alacsonyabbra is csökkentheti. A feladatgyűjteményben lévő összes feladat a feladatgyűjteményben beállított értékre korlátozódik. Ha a feladatgyűjtemény maximális gyakoriságától nagyobb gyakorisággal próbál létrehozni egy feladatot, a kérelem sikertelen lesz egy 409-es ütközési állapotkóddal. | 
| **Testméret** | A kérelem maximális testmérete 8192 karakter. |
| **Kérelem URL-címének mérete** | A kérelem URL-címének maximális mérete 2048 karakter. |
| **Fejlécek száma** | A fejlécek maximális száma 50 fejléc. | 
| **Élőfej összesítése** | Az összesített fejléc maximális mérete 4096 karakter. |
| **Időtúllépés** | A kérelem időtúlára statikus, azaz nem konfigurálható. és 60 másodperc a HTTP-műveletek. Hosszabb ideig futó műveletek esetén kövesse a HTTP aszinkron protokollokat. Például azonnal visszaad egy 202-est, de folytassa a munkát a háttérben. | 
| **Feladatelőzmények** | A feladatelőzményekben tárolt maximális választörzs 2048 bájt. |
| **Feladatelőzmények megőrzése** | A feladatelőzményeklegfeljebb két hónapig vagy az utolsó 1000 kivégzésig megmaradnak. | 
| **Befejezett és hibás feladatmegőrzés** | A befejezett és hibás feladatok 60 napig megmaradnak. |
||| 

