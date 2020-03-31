---
title: F5 BIG-IP-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja az F5 BIG-IP-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588280"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Csatlakoztassa az F5 BIG-IP készüléket 

> [!IMPORTANT]
> Az Azure Sentinel F5 BIG-IP-adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az F5 BIG-IP-csatlakozó lehetővé teszi, hogy könnyedén csatlakoztassa az Összes F5 BIG-IP-naplóját az Azure Sentinelhez, munkafüzeteket tekinthet meg, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez több betekintést nyújt a szervezet hálózatába, és javítja a biztonsági műveleti képességeket. Az F5 BIG-IP és az Azure Sentinel integrációja a REST API-t használja.


> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG-IP konfigurálása és csatlakoztatása 

Az F5 BIG-IP közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja a naplókat.

1. Az Azure Sentinel portálon kattintson az **Adatösszekötők** elemre, válassza **az F5 BIG-IP lehetőséget,** majd **nyissa meg az összekötőlapot.** 
1. Az F5 BIG-IP csatlakoztatásához json-deklarációt kell küldenie a rendszer API-végpontjára. Ennek módjáról [az F5 BIG-IP integrálása az Azure Sentinellel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)című témakörben talál.
8. Az F5 BIG-IP-összekötő lapról másolja a munkaterület-azonosítót és az elsődleges kulcsot, és illessze be őket a [Streamelési adatok](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)csoportban az Azure Log Analytics szolgáltatásba.
1. Miután befejezte az F5 BIG-IP utasításokat, az Azure Sentinel-összekötő lapon láthatja a csatlakoztatott adattípusokat.
1. Ha az F5 BIG-IP-események hez a Log Analytics megfelelő sémáját szeretné használni, keresse meg a **F5Telemetry_LTM_CL,** **a F5Telemetry_system_CL**és **a F5Telemetry_ASM_CL.**


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az F5 BIG-IP-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)


