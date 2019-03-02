---
title: Az Azure Security Center adatokat gyűjt a Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti össze az Azure Security Center Azure Sentinel-adatokat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f59c5f6a9f497a6420172996f9f327f16ffd26f9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242272"
---
# <a name="collect-data-from-azure-security-center"></a>Az Azure Security Center adatokat gyűjteni

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Az Azure Sentinel lehetővé teszi, hogy a riasztások gyűjtése [az Azure Security Center](../security-center/security-center-intro.md) és streamelése őket az Azure-Sentinel. 

## <a name="prerequisites"></a>Előfeltételek

- Ha azt szeretné, az Azure Security Center riasztások exportálása, az előfizetésre, amelynek a naplói notebookkal közreműködő kell lennie.

- Rendelkeznie kell a [Azure Security Center Standard csomagja](../security-center/security-center-pricing.md) fut az előfizetésben. Ha nem, [frissítsen standard](https://azure.microsoft.com/pricing/details/security-center/).

- Az egyes előfizetésekhez szeretne csatlakozni a globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználóként kell bejelentkeznie.


## <a name="connect-to-azure-security-center"></a>Csatlakozás az Azure Security centerhez

1. Az Azure-Sentinel, válassza **adatgyűjtés** és kattintson a **az Azure Security Center** csempére.
1. Kattintson a jobb **Connect** mellett minden egyes előfizetés, amelynek streamelése az Azure-Sentinel kívánt riasztásokat. Ellenőrizze, hogy minden egyes előfizetés frissítése az Azure Security Center Standard csomagja stream a riasztásokra az Azure-Sentinel.

3. Kattintson a **Connect** (Csatlakozás) gombra.

4. A megfelelő sémát használ a Log Analytics az Azure Security Center riasztásait, keresse meg **SecurityEvent**.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerkedhetett az Azure Security Center csatlakozni az Azure-Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
