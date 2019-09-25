---
title: Azure ATP-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Azure ATP-beli adatközpontok az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240190"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>Adatok összekapcsolása az Azure komplex veszélyforrások elleni védelemből (ATP) – előzetes verzió




Egyetlen kattintással továbbíthatja a naplókat az Azure-beli komplex [veszélyforrások elleni védelemből](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) az Azure sentinelbe.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Az Azure ATP előzetes verziójú ügyfelének kell lennie

## <a name="connect-to-azure-atp"></a>Kapcsolódás az Azure ATP-hez

Győződjön meg arról, hogy az Azure ATP előzetes verziója engedélyezve van a [hálózaton](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Ha az Azure ATP üzembe helyezése és az adatai betöltése történik, a gyanús riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba. Akár 24 óráig is eltarthat, amíg a riasztások elkezdik a folyamatos átvitelt az Azure Sentinel szolgáltatásba.


1. Az Azure ATP és az Azure Sentinel összekapcsolásához először engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. Ennek módjáról az [Azure komplex veszélyforrások elleni védelem integrációja](https://docs.microsoft.com/cloud-app-security/aatp-integration)című témakörben olvashat bővebben.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson az **Azure ATP** csempére.

1. Kiválaszthatja, hogy az Azure ATP-riasztások automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

1. Kattintson a **Csatlakozás** gombra.

1. Ha az Azure ATP-riasztások esetében a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SecurityAlert**.

> [!NOTE]
> Ha a riasztások 30 KB-nál nagyobbak, az Azure Sentinel nem jeleníti meg az entitások mezőt a riasztásokban.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure Advanced Threat Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

