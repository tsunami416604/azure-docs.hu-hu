---
title: "Az Azure Stream Analytics-feladatok folyamatossága |} Microsoft Docs"
description: "Így a Stream Analytics útmutatást frissítés rugalmas feladatok."
services: stream-analytics
documentationCenter: 
authors: samacha
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 76e347ee62ffc07db1d8e74cf0ac5327a154fe4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Stream Analytics-feladat megbízhatóságának biztosítása szolgáltatás frissítései során

Egy teljes körűen felügyelt szolgáltatás, amely nem része a funkció új szolgáltatás funkcióit és fejlesztéseit gyors ütemben bevezetése. Ennek eredményeképpen a Stream Analytics heti (vagy gyakoribb) telepítése szolgáltatásfrissítés is rendelkezhetnek. Függetlenül történik, hogy mekkora tesztelése nincs továbbra is fennáll a kockázata, hogy egy meglévő, futó feladat megszakadhat bevezetése egy hiba miatt. Az ügyfelek, akik kritikus adatfolyam feldolgozási feladatok futtatása a kockázatok kell kerülni kell. Az ügyfelek használhatják a kockázatok csökkentése mechanizmusra Azure  **[párosított régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hogyan hajtsa végre az Azure párosított régiók ezen probléma megoldásának?

A Stream Analytics biztosítja, hogy külön párosított régiókban feladatok frissülnek. Ennek eredményeképpen nincs elegendő idő hiány között lehetséges legfrissebb hibák azonosítása és javítása azokat a frissítéseket.

_Közép-Indiában kivételével_ (amelynek párosított régió, Dél-Indiában és nem rendelkezik Stream Analytics jelenléte), a Stream Analytics egy frissítés telepítése nem lépne párosított régiók meg egyszerre. A központi telepítések több régióba **ugyanabban a csoportban** fordulhat **egyszerre**.

A cikk a  **[rendelkezésre állását és párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  a legfrissebb információk, amelyen a régiók párosítva van.

Ügyfelek központi telepítése azonos feladatok mindkét párosított régió végigvitelével. A Stream Analytics belső figyelési képességek, valamint az ügyfelek is javasolja, hogy a feladatok figyelése, mintha **mindkét** éles feladat. Ha szünet kell lennie a Stream Analytics szolgáltatás frissítés eredménye, eszkalálása megfelelően, és a megfelelő feladat kimeneti alárendelt fogyasztóval feladatátvételt. Eszkalációs támogatásához a párosított régió megakadályozza az új központi telepítés vonatkozzon, és a párosított feladatot integritásának fenntartása.
