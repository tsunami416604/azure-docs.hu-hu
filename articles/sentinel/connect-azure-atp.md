---
title: Az Azure ATP-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja az Azure ATP-adatokat az Azure Sentinelhez.
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
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588586"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Adatok csatlakoztatása az Azure Komplex veszélyforrások elleni védelemből (ATP)

> [!IMPORTANT]
> Az Azure Advanced Threat Protection adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Egyetlen kattintással streamelheti az [Azure Komplex veszélyforrások elleni védelemből](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) származó naplókat az Azure Sentinelbe.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Az Azure ATP előzetes verziójának kell lennie, és engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. További információ: [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Csatlakozás az Azure ATP-hez

Győződjön meg arról, hogy az Azure ATP előzetes verziója engedélyezve van [a hálózaton.](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)
Ha az Azure ATP telepítve van, és az adatok betöltése, a gyanús riasztások könnyen streamelhető az Azure Sentinel. Akár 24 órát is igénybe vehet, amíg a riasztások elindulnak az Azure Sentinel streamelése.


1. Az Azure ATP és az Azure Sentinel csatlakoztatásához először engedélyeznie kell az Azure ATP és a Microsoft Cloud App Security közötti integrációt. Ennek módjáról az Azure [Komplex veszélyforrások elleni védelem integrációja című](https://docs.microsoft.com/cloud-app-security/aatp-integration)témakörben talál további információt.

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Azure Komplex veszélyforrások elleni védelem (előzetes verzió)** csempére.

1. Kiválaszthatja, hogy szeretné-e, hogy az Azure ATP riasztásai automatikusan generálják az incidenseket az Azure Sentinelben. Az **Incidensek létrehozása csoportban** válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, amely automatikusan létrehozza az incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ezt a szabályt az **Analytics,** majd **az Aktív szabályok**csoportban szerkesztheti.

1. Kattintson a **Csatlakozás** gombra.

1. Ha a megfelelő sémát szeretné használni a Log Analytics az Azure ATP-riasztások, keresse meg a **SecurityAlert.**

> [!NOTE]
> Ha a riasztások nagyobbak, mint 30 KB, az Azure Sentinel leállítja az entitások mező megjelenítését a riasztásokban.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Azure Komplex veszélyforrások elleni védelmet az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

