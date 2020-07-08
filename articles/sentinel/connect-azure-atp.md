---
title: Azure ATP-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan továbbíthatja a naplókat az Azure Advanced Threat Protection (ATP) szolgáltatásból egyetlen kattintással az Azure Sentinelbe.
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
ms.openlocfilehash: f58c38ccfa234752a80c05c300d245c6c9e97cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559178"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Adatok összekapcsolása az Azure komplex veszélyforrások elleni védelemből (ATP)

> [!IMPORTANT]
> Az Azure-beli Advanced Threat Protection adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egyetlen kattintással továbbíthatja a naplókat az Azure-beli komplex [veszélyforrások elleni védelemből](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) az Azure sentinelbe.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Az Azure ATP előzetes verziójú ügyfelének kell lennie, és engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. További információ: [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Kapcsolódás az Azure ATP-hez

Győződjön meg arról, hogy az Azure ATP előzetes verziója engedélyezve van a [hálózaton](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Ha az Azure ATP üzembe helyezése és az adatai betöltése történik, a gyanús riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba. Akár 24 óráig is eltarthat, amíg a riasztások elkezdik a folyamatos átvitelt az Azure Sentinel szolgáltatásba.


1. Az Azure ATP és az Azure Sentinel összekapcsolásához először engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. Ennek módjáról az [Azure komplex veszélyforrások elleni védelem integrációja](https://docs.microsoft.com/cloud-app-security/aatp-integration)című témakörben olvashat bővebben.

1. Az Azure Sentinel **szolgáltatásban válassza az adatösszekötők** lehetőséget, majd kattintson az **Azure Advanced Threat Protection (előzetes verzió)** csempére.

1. Kiválaszthatja, hogy az Azure ATP-riasztások automatikusan előállítanak-e incidenseket az Azure Sentinel szolgáltatásban. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **elemzés** , majd az **aktív szabályok**területen módosíthatja.

1. Kattintson a **Csatlakozás** gombra.

1. Ha az Azure ATP-riasztások esetében a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SecurityAlert**.

> [!NOTE]
> Ha a riasztások 30 KB-nál nagyobbak, az Azure Sentinel nem jeleníti meg az entitások mezőt a riasztásokban.

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure Advanced Threat Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

