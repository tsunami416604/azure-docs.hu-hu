---
title: Forcepoint-termékek csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Forcepoint-termékeket az Azure Sentinelhez.
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
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588229"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>A Forcepoint-termékek csatlakoztatása az Azure Sentinelhez

> [!IMPORTANT]
> A Forcepoint termékek adatösszekötő az Azure Sentinel jelenleg nyilvános előzetes verzióban. Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


Ez a cikk bemutatja, hogyan csatlakoztathatja a ForcePoint-termékeket az Azure Sentinelhez. 

A Forcepoint-adatösszekötők lehetővé teszik a Forcepoint Cloud Access Security Broker és a Forcepoint next Generation firewall naplók és az Azure Sentinel összekapcsolását. Ily módon automatikusan exportálhatja a felhasználó által definiált naplókat az Azure Sentinelbe valós időben. Az összekötő gazdagabbá teszi a Forcepoint-termékek által rögzített felhasználói tevékenységeket. Emellett további korrelációt tesz lehetővé az Azure-munkaterhelésekből és más hírcsatornákból származó adatokkal, és javítja a figyelési képességet az Azure Sentinelen belüli munkafüzetekkel.

> [!NOTE]
> Az adatok at annak a munkaterületnek a földrajzi helyén tároljuk, amelyen az Azure Sentinelt futtatja.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint-terméknaplók továbbítása a Syslog ügynöknek 

Állítsa be a Forcepoint-terméket úgy, hogy a Syslog-üzeneteket CEF formátumban továbbítsa az Azure-munkaterületre a Syslog ügynökön keresztül.

1. Állítsa be a Forcepoint-terméket az Azure Sentinel-integrációra az alábbi telepítési útmutatókban leírtak szerint:
 - [Forcepoint CASB integrációs útmutató](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW integrációs útmutató](https://frcpnt.com/ngfw-sentinel)

2. Keresse meg a CommonSecurityLog a megfelelő séma a Log Analytics DeviceVendor név tartalmazza a "Forcepoint". 

3. Folytassa a [3.](connect-cef-verify.md)



## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Forcepoint-termékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:

- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)

- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)