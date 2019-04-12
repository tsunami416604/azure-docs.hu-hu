---
title: Csatlakozás az Azure ATP-adatok Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: 'Útmutató: Azure ATP adatok csatlakozni az Azure-Sentinel.'
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 153b6cb44cbcf5ecca9a4941c6bef7a8721dc56c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492651"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Adatok csatlakoztatása az Azure komplex veszélyforrások elleni védelem (ATP)

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Naplóinak streamelheti [Azure komplex veszélyforrások elleni védelem](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) be Azure Sentinel-egyetlen kattintással.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó
- Az Azure ATP privát előzetes verziójú ügyfél kell lennie

## <a name="connect-to-azure-atp"></a>Csatlakozás az Azure ATP

Ellenőrizze, hogy az Azure ATP privát előzetes verzióját [engedélyezve van a hálózaton](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Ha az Azure ATP telepíti, és az adatok feldolgozására, a gyanús riasztásokat is könnyen lehet streameli Azure Sentinel. A riasztások az Azure-Sentinel streamelésének megkezdéséhez, akár 24 óráig is eltarthat.



1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Azure ATP** csempére.

2. Kattintson a **Connect** (Csatlakozás) gombra.

6. A megfelelő sémát használ a Log Analytics az Azure ATP-riasztásokat, keresse meg **SecurityAlert**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure komplex veszélyforrások elleni védelem csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

