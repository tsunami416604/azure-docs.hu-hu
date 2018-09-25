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
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006499"
---
Az alábbi táblázat ismerteti a fő kvóták, korlátozások, alapértékek és az Azure Schedulerben szabályozások.

| Erőforrás | Korlát leírása |
| -------- | ----------------- |
| **Feladat mérete** | A feladat maximális mérete 16 KB. Ha egy PUT vagy PATCH művelet eredménye egy feladat ennél nagyobb méretű, 400 Hibás kérés állapotkódot adott vissza. | 
| **Feladatgyűjtemények** | Feladatgyűjtemények Azure-előfizetésenként legfeljebb 200 000. | 
| **Feladatok száma gyűjteményenként** | Alapértelmezés szerint a feladatok maximális száma öt feladatok az ingyenes feladatgyűjtemények és a egy standard feladatgyűjtemény 50 feladatok. <p>Módosíthatja a feladatgyűjtemények feladatok maximális számát. A feladatgyűjtemény minden feladata a feladatgyűjtemény a beállított érték korlátozódnak. Ha tárolórétegeket próbál létrehozni, mint a maximális feladatok kvóta feladatot, a kérelem meghiúsul, az ütközés 409 állapotkódot. | 
| **Kezdés ideje** | A legnagyobb "idő, kezdési idő" 18 hónap. |
| **Ismétlődési tartomány** | A maximális ismétlődési span 18 hónap. | 
| **Gyakoriság** | Alapértelmezés szerint a maximális gyakoriság kvóta, az ingyenes feladatgyűjtemények egy óra és a egy percet a standard feladatgyűjtemény. <p>Hogy a maximális gyakorisága a feladatgyűjtemények alacsonyabb, mint a maximális érték. A feladatgyűjtemény minden feladata a feladatgyűjtemény a beállított érték korlátozódnak. Ha létrehoz egy feladatot egy nagyobb gyakorisággal, mint a feladatgyűjtemény maximális gyakoriságát megpróbálja, a kérelem meghiúsul, az ütközés 409 állapotkódot. | 
| **Szervezet mérete** | A kérés maximális törzs mérete 8192 karaktert. |
| **Kérelem URL-cím mérete** | A kérelem URL-cím maximális mérete 2048 karakter. |
| **Fejléc száma** | A fejléc maximális száma 50 fejlécek. | 
| **Összesített fejléc mérete** | A maximális összesített fejléc mérete 4096 karakter. |
| **Időtúllépés** | Az egyes kérelmek időkorlátját je statická. (nem konfigurálható) és a HTTP-műveletek esetében 60 másodperc. A hosszabb ideig futó műveletek kövesse a HTTP-aszinkron protokollokat. Például egy 202 azonnal vissza, de közben tovább dolgozhat a háttérben. | 
| **Feladatelőzmények** | A feladatelőzmények tárolt maximális válasz törzsében 2048 bájt. |
| **Feladat Előzmények megőrzése** | Feladatelőzmények legfeljebb 2 hónapra, vagy akár a legutóbbi 1000 végrehajtások másolatok. | 
| **A befejeződött vagy meghiúsult feladatok megőrzése** | A befejeződött vagy meghiúsult feladatok 60 napig őrzi meg. |
||| 

