---
title: Azure CDN használati minták elemzése | Microsoft Docs
description: Ez a cikk a Azure CDN termékekhez elérhető elemzési jelentések különböző típusait ismerteti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81253611"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Az Azure CDN használati mintáinak elemzése

Miután engedélyezte a CDN-t az alkalmazáshoz, figyelheti a CDN használatát, ellenőrizheti a kézbesítés állapotát, és elháríthatja a lehetséges problémákat. A Azure CDN a következő módokon biztosítja ezeket a funkciókat: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Alapvető elemzések az Azure diagnosztikai naplóin keresztül

Az alapszintű Analitika minden díjszabási szinten elérhető a CDN-végpontokhoz. Az Azure Diagnostics-naplók lehetővé teszik az alapvető elemzési szolgáltatás exportálását az Azure Storage-ba, az Event hubokba vagy a Azure Monitor-naplókba. A Azure Monitor-naplók megoldást biztosítanak a felhasználó által konfigurálható és testreszabható gráfokkal. További információ az Azure diagnosztikai naplóiról: [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon Core-jelentések

Azure CDN felhasználóként a Verizon-profilból származó, a verizontól vagy a **Azure CDN premiumtól** származó **Azure CDN szabványnak** megfelelő Verizon Core-jelentéseket a Verizon kiegészítő portálon tekinthet meg. A Verizon Core-jelentések a Azure Portal **kezelés** lehetőségével érhetők el, és számos gráfot és nézetet biztosítanak. További információ: [a Verizon alapvető jelentései](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Egyéni Verizon-jelentések

A Verizon és a **Azure CDN Premium** **Azure CDN standard szintű** Azure CDN felhasználóként a Verizon kiegészítő portálon megtekintheti a Verizon egyéni jelentéseit. A Verizon egyéni jelentései a Azure Portal **kezelés** lehetőségével érhetők el. Az egyéni Verizon-jelentések lapon az Azure CDN-profilhoz tartozó minden egyes peremhálózati CName rekordhoz tartozó találatok vagy adatforgalom száma látható. Az adat a HTTP-válasz kódja vagy a gyorsítótár állapota tetszőleges időszakon belül csoportosítható. További információ: [egyéni jelentések a verizonban](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Prémium szintű Azure CDN Verizon-jelentésekben

A **Verizon Azure CDN Premium**csomaggal a következő jelentések is elérhetők:
   * [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)
   * [Valós idejű statisztikák](cdn-real-time-stats.md)
   * [Határcsomópont teljesítménye](cdn-edge-performance.md)

