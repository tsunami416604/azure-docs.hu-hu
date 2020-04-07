---
title: A Microsoft Defender ATP-adatainak csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Microsoft Defender komplex veszélyforrások elleni védelem adatait az Azure Sentinelhez.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756357"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>A Microsoft Defender komplex veszélyforrások elleni védelemriasztásai 


> [!IMPORTANT]
> A Microsoft Defender komplex veszélyforrások elleni riasztásainak betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
 

A [Microsoft Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) összekötő lehetővé teszi a Microsoft Defender komplex veszélyforrások elleni védelemből érkező riasztások streamelését az Azure Sentinelbe. Ez lehetővé teszi, hogy átfogóbban elemezze a biztonsági eseményeket a szervezeten belül, és forgatókönyveket hozzon létre az hatékony és azonnali válasz érdekében.

## <a name="prerequisites"></a>Előfeltételek

- A Microsoft Defender komplex veszélyforrások elleni védelemhez érvényes licenccel kell rendelkeznie a [Microsoft Defender ATP telepítésének beállítása](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)című részben leírtak szerint. 
- Az Azure Sentinel-bérlő rendszergazdájának vagy biztonsági rendszergazdájának kell lennie.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Csatlakozás a Microsoft Defender komplex veszélyforrások elleni védelméhez

Ha a Microsoft Defender komplex veszélyforrások elleni védelem telepítve van, és az adatok betöltése, a riasztások könnyen streamelhető az Azure Sentinel.


1. Az Azure Sentinelben válassza **az Adatösszekötők**lehetőséget, kattintson a **Microsoft Defender komplex veszélyforrások elleni védelmének** csempéjére, és válassza az **Összekötő megnyitása lapot.**
1. Kattintson a **Csatlakozás** gombra. 
1. Ha a Defender ATP-riasztások naplóanalytics megfelelő sémáját szeretné használni, keresse meg a **SecurityAlert** kifejezést, és a **szolgáltató neve** **MDATP.**




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Microsoft Defender ATP-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
