---
title: A Barracuda CloudGen tűzfal csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja a Barracuda CloudGen tűzfalat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588501"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>A Barracuda CloudGen tűzfal csatlakoztatása

A Barracuda CloudGen firewall (CGFW) összekötő segítségével egyszerűen csatlakoztathatja a Barracuda CGFW naplókat az Azure Sentinelhez, irányítópultokat tekinthet meg, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez több betekintést nyújt a szervezet hálózatába, és javítja a biztonsági műveleti képességeket.




## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyek az Azure Sentinel-munkaterületre.

- A Barracuda CloudGen tűzfalat úgy kell konfigurálni, hogy a Syslog-on keresztül exportálja a naplókat.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Az Azure Sentinel csatlakoztatása a Barracuda CloudGen tűzfalhoz

1. Az Azure Portalon keresse meg az **Azure Sentinel** > **Data-összekötők,** és válassza ki a **Barracuda CloudGen tűzfal** összekötő.

2. Válassza **az Összekötő lap megnyitása lehetőséget**.

3. Kövesse a **Barracuda CloudGen tűzfal** oldalán található utasításokat.


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Barracuda CloudGen tűzfalat az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


