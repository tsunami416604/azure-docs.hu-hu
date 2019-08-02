---
title: Azure ATP-beli adatkapcsolatok az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Azure ATP-beli adatközpontok az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599154"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Adatok összekapcsolása az Azure komplex veszélyforrások elleni védelemből (ATP)

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Egyetlen kattintással továbbíthatja a naplókat az Azure-beli komplex [veszélyforrások elleni védelemből](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) az Azure sentinelbe.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Az Azure ATP előzetes verziójú ügyfelének kell lennie

## <a name="connect-to-azure-atp"></a>Kapcsolódás az Azure ATP-hez

Győződjön meg arról, hogy az Azure ATP előzetes verziója engedélyezve van a [hálózaton](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Ha az Azure ATP üzembe helyezése és az adatai betöltése történik, a gyanús riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba. Akár 24 óráig is eltarthat, amíg a riasztások elkezdik a folyamatos átvitelt az Azure Sentinel szolgáltatásba.


1. Az Azure ATP és az Azure Sentinel összekapcsolásához először engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. Ennek módjáról az [Azure komplex veszélyforrások elleni védelem integrációja](https://docs.microsoft.com/cloud-app-security/aatp-integration)című témakörben olvashat bővebben.

1. Az Azure Sentinelben válassza az adatösszekötők lehetőséget, majd kattintson az **Azure ATP** csempére.

2. Kattintson a **Csatlakozás** gombra.

6. Ha az Azure ATP-riasztások esetében a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure Advanced Threat Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

