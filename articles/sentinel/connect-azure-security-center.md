---
title: Az Azure Security Center adatainak összekapcsolása az Azure Sentinelhez
description: Ismerje meg, hogyan kapcsolhatja össze az Azure Security Center adatait az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588535"
---
# <a name="connect-data-from-azure-security-center"></a>Adatok csatlakoztatása az Azure Security Centerből





Az Azure Sentinel lehetővé teszi, hogy az [Azure Security Centerből](../security-center/security-center-intro.md) riasztásokat csatlakoztasson, és továbbítsa azokat az Azure Sentinelbe. 

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Security Centerből származó riasztások exportálásához rendelkeznie kell a Security Reader szerepkörrel az adatfolyamok előfizetésében.

- Az Azure [Security Center standard szintű alapcsomagnak](../security-center/security-center-pricing.md) az előfizetésen kell futnia. Ha nem, [frissítse az előfizetést a standardra.](https://azure.microsoft.com/pricing/details/security-center/)



## <a name="connect-to-azure-security-center"></a>Csatlakozás az Azure Security Centerhez

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure Security Center** csempére.

1. A jobb oldalon kattintson a **Csatlakozás** lehetőségre minden olyan előfizetés mellett, amelynek az Azure Sentinelbe szeretne streamelni a riasztásait. Győződjön meg arról, hogy minden előfizetést az Azure Security Center standard szintre frissít, hogy riasztásokat továbbítson az Azure Sentinelbe.

1. Kiválaszthatja, hogy szeretné-e, hogy az Azure Security Center ből érkező riasztások automatikusan generálják az incidenseket az Azure Sentinelben. Az **Incidensek létrehozása csoportban** válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **Analytics,** majd **az Aktív szabályok**csoportban szerkesztheti.

3. Kattintson a **Csatlakozás** gombra.

4. Ha az Azure Security Center-riasztások naplóanalytics szolgáltatásában a megfelelő sémát szeretné használni, keresse meg a **SecurityAlert (SecurityAlert)** kifejezést.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Security Centert az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
