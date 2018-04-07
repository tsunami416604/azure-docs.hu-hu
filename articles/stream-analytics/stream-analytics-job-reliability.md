---
title: Az Azure Stream Analytics-feladatok folyamatossága
description: A cikk útmutatást az refs és a Stream Analytics-feladatok frissítése.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 47ccfe99d2ee6576dbb70324eb383f52d2a1b2e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Stream Analytics-feladat megbízhatóságának biztosítása szolgáltatás frissítései során

Egy teljes körűen felügyelt szolgáltatás, amely nem része a funkció új szolgáltatás funkcióit és fejlesztéseit gyors ütemben bevezetése. Ennek eredményeképpen a Stream Analytics heti (vagy gyakoribb) telepítése szolgáltatásfrissítés is rendelkezhetnek. Függetlenül történik, hogy mekkora tesztelése nincs továbbra is fennáll a kockázata, hogy egy meglévő, futó feladat megszakadhat bevezetése egy hiba miatt. Az ügyfelek, akik kritikus adatfolyam feldolgozási feladatok futtatása a kockázatok kell kerülni kell. Az ügyfelek használhatják a kockázatok csökkentése mechanizmusra Azure **[párosított régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hogyan hajtsa végre az Azure párosított régiók ezen probléma megoldásának?

A Stream Analytics biztosítja, hogy külön párosított régiókban feladatok frissülnek. Ennek eredményeképpen nincs elegendő idő hiány között lehetséges legfrissebb hibák azonosítása és javítása azokat a frissítéseket.

_Közép-Indiában kivételével_ (amelynek párosított régió, Dél-Indiában és nem rendelkezik Stream Analytics jelenléte), a Stream Analytics egy frissítés telepítése nem lépne párosított régiók meg egyszerre. A központi telepítések több régióba **ugyanabban a csoportban** fordulhat **egyszerre**.

A cikk a **[rendelkezésre állását és párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** a legfrissebb információk, amelyen a régiók párosítva van.

Ügyfelek központi telepítése azonos feladatok mindkét párosított régió végigvitelével. A Stream Analytics belső figyelési képességek, valamint az ügyfelek is javasolja, hogy a feladatok figyelése, mintha **mindkét** éles feladat. Ha szünet kell lennie a Stream Analytics szolgáltatás frissítés eredménye, eszkalálása megfelelően, és a megfelelő feladat kimeneti alárendelt fogyasztóval feladatátvételt. Eszkalációs támogatásához a párosított régió megakadályozza az új központi telepítés vonatkozzon, és a párosított feladatot integritásának fenntartása.
