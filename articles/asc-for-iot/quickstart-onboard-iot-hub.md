---
title: A IoT szolgáltatás Azure Security Centerának engedélyezése a IoT Hubban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti Azure Security Center a IoT szolgáltatáshoz a IoT Hubban.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596962"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Gyors útmutató: Azure Security Center a IoT Service-ben IoT Hub

Ez a cikk azt ismerteti, hogyan engedélyezhető a IoT szolgáltatás Azure Security Center a meglévő IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) című témakört. 

> [!NOTE]
> A IoT Azure Security Center jelenleg csak a standard szintű IoT hubokat támogatja.
> A IoT Azure Security Center egyetlen központi megoldás. Ha több hubhoz van szüksége, a IoT-megoldásokhoz több Azure Security Center is szükség van. 

## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezésének előfeltételei

- Log Analytics-munkaterület
  - A Log Analytics munkaterület alapértelmezés szerint két típusú információt tárol a IoT számára Azure Security Center alapján. **biztonsági riasztások** és **javaslatok**. 
  - Dönthet úgy is, hogy hozzáad egy további adattípust, **nyers eseményeket**. Vegye figyelembe, hogy a Log Analytics **nyers események** tárolása további tárolási költségekkel jár. 
- IoT Hub (standard szint)
- Az összes [szolgáltatás](service-prerequisites.md) előfeltételeinek teljesítése 

|Támogatott Azure-szolgáltatási régiók | ||
|---|---|---|
| USA középső régiója |East US |USA 2. keleti régiója |
| USA nyugati középső régiója |USA nyugati régiója |USA 2. nyugati régiója |
| USA déli középső régiója|USA északi középső régiója | Közép-Kanada|
| Kelet-Kanada| Észak-Európa|Dél-Brazília|
| Közép-Franciaország| Az Egyesült Királyság nyugati régiója|Az Egyesült Királyság déli régiója|
|Nyugat-Európa|Észak-Európa| Nyugat-Japán|
|Kelet-Japán | Délkelet-Ausztrália|Kelet-Ausztrália|
|Kelet-Ázsia| Délkelet-Ázsia| Korea középső régiója|
|Korea déli régiója| Közép-India| Dél-India|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Azure Security Center engedélyezése a IoT a IoT Hub 

A IoT Hub biztonságának engedélyezéséhez tegye a következőket: 

1. Nyissa  meg a IoT Hubt Azure Portal. 
1. A **Biztonság** menüben kattintson a **IoT-megoldás biztonságossá tétele** lehetőségre.
1. Hagyja bejelölve az **Engedélyezés** beállítást alapértelmezettként. 
1. Válassza ki a log Analytics-munkaterületet.
1. Adja meg a Log Analytics-munkaterület részleteit. 
   - Úgy dönt, hogy engedélyezi a **Twin Collectiont** a **Twin Collection** bekapcsolásának bekapcsolása mellett.
   - Úgy dönt, hogy a **nyers eseményeket** a tároló alapértelmezett adattípusain kívül tárolja, ha kiválasztja a **nyers eszköz biztonsági eseményeinek tárolása** log Analyticsban. Hagyja bekapcsolni a  **nyers esemény** bekapcsolását. 
    
1. Kattintson a **Save** (Mentés) gombra. 

Gratulálunk! Befejezte a IoT Azure Security Centerének engedélyezését a IoT Hub. 

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkel a megoldás konfigurálásához...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)


