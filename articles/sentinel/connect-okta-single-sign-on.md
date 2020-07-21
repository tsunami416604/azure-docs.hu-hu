---
title: Okta-alapú egyszeri bejelentkezési adatkapcsolatok összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a okta-alapú egyszeri bejelentkezések az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531525"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>A okta-alapú egyszeri bejelentkezés és az Azure Sentinel összekapcsolása az Azure-függvénnyel

Az okta egyszeri bejelentkezési (SSO) összekötővel könnyedén csatlakoztathatja az [okta egyszeri bejelentkezési (SSO)](https://www.okta.com/products/single-sign-on/) biztonsági megoldásának naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A okta-alapú egyszeri bejelentkezés és az Azure Sentinel közötti integráció a Azure Functions használatával kéri le a naplózási adataikat a REST API használatával.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-okta-single-sign-on"></a>Okta-alapú egyszeri bejelentkezés konfigurálása és összekapcsolása

A Azure Functions az eseményeket és naplókat közvetlenül az okta egyszeri bejelentkezéssel integrálhatja és lehívhatja, és továbbíthatja őket az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** lehetőségre, és válassza az **okta egyszeri bejelentkezés** összekötőt.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse az **okta egyszeri bejelentkezés** oldalán található utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **Okta_CL** táblázat alatt log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az okta egyszeri bejelentkezés az Azure Sentinelhez az Azure Function Apps használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

