---
title: Zimperium Mobile Threat Defense csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Zimperium Mobile Threat Defense alkalmazást az Azure Sentinelhez.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587940"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Csatlakoztassa zimperium mobil fenyegetésvédelmi az Azure Sentinel


> [!IMPORTANT]
> A Zimperium Mobile Threat Defense adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)



A Zimperium Mobile Threat Defense összekötő lehetővé teszi a Zimperium fenyegetésnapló csatlakoztatását az Azure Sentinelhez az irányítópultok megtekintéséhez, az egyéni riasztások létrehozásához és a vizsgálat javításához. Ez több betekintést nyújt a szervezet mobilfenyegetési környezetébe, és javítja a biztonsági műveleti funkciókat.

> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>A Zimperium Mobile Threat Defense konfigurálása és csatlakoztatása

A Zimperium Mobile Threat Defense közvetlenül integrálhatja és exportálhatja a naplókat az **Azure Sentinelbe.**

1. Az Azure Sentinel portálon kattintson az Adatösszekötők elemre, és válassza a **Zimperium Mobile Threat Defense lehetőséget.**
2. Válassza **az Összekötő lap megnyitása lehetőséget**.
3. Kövesse a **Zimperium Mobile Threat Defense** összekötő oldalán található utasításokat, az alábbiak szerint összefoglalva.
 1. A zConsole-ban kattintson a navigációs sáv **Kezelése** gombjára.
 2. Kattintson az **Integrációk** fülre.
 3. Kattintson a **Fenyegetésjelentés** gombra, majd az **Integrációhozzáadása gombra.**
 4. Hozza létre az integrációt a **Microsoft Azure Sentinel** kiválasztásával a rendelkezésre álló integrációkból, és adja meg a munkaterület-azonosítót és az elsődleges kulcsot az Azure Sentinelhez való csatlakozáshoz.
 5. Lehetőség az Azure Sentinelhez leküldéses fenyegetésadatok szűrőszintjének kiválasztásához is elérhető. 

4. További információkért kérjük, látogasson el a [Zimperium ügyfélszolgálati portáljára.](https://support.zimperium.com)


## <a name="find-your-data"></a>Az adatok megkeresése

Sikeres kapcsolat létrehozása után az adatok megjelennek a Log Analytics a CustomLogs ZimperiumThreatLog_CL és ZimperiumMitigationLog_CL.

A megfelelő séma használatához a Zimperium Mobile Threat Defense Log Analytics szolgáltatásában keressen ZimperiumThreatLog_CL és ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók megjelenése a Log Analytics szolgáltatásban 20 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Zimperium Mobile Threat Defense-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:

- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)

- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

Ha többet szeretne megtudni a Zimperiumról, olvassa el az alábbi témaköröket:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobil Biztonsági Blog](https://blog.zimperium.com)

- [Zimperium ügyfélszolgálati portál](https://support.zimperium.com)

