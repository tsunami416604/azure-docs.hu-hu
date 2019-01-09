---
title: Frissítés a Windows Defender víruskereső az Azure Stackben
description: A víruskereső részleteiért van mindig naprakész az Azure Stackben
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/8/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: d4eb0cbf6c55f3d8da460370ec9209638e3e1d62
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118217"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Frissítés a Windows Defender víruskereső az Azure Stackben

[A Windows Defender víruskereső](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) kártevőirtó megoldás, amely a biztonsági és védelmet biztosít. Minden Azure Stack-infrastruktúra összetevő (a Hyper-V-gazdagépek és virtuális gépeket) a Windows Defender víruskereső használható védett. A védelem naprakészen a Windows Defender víruskereső-definíciókat, a motor és a platform rendszeres frissítés szükséges. Konfigurációjától függ, hogy hogyan telepítse a frissítéseket.

## <a name="connected-scenario"></a>Csatlakoztatott forgatókönyv

A kártevőirtó definíciójának és keresőmotor-frissítések, az Azure Stack [frissítés erőforrás-szolgáltató](azure-stack-updates.md#the-update-resource-provider) letölti a kártevőirtó-definíciókat és motor-frissítések naponként több alkalommal. Minden egyes Azure Stack-infrastruktúra összetevője a frissítés beolvassa a frissítési erőforrás-szolgáltató, és automatikusan alkalmazza a frissítést.

A kártevőirtó platform frissítéseinek alkalmazása a [havi Azure Stack-frissítés](azure-stack-apply-updates.md). A havi Azure Stack-frissítés magában foglalja a Windows Defender víruskereső platform frissítései hónap.

## <a name="disconnected-scenario"></a>Kapcsolat nélküli forgatókönyv

 Alkalmazza a [havi Azure Stack-frissítés](azure-stack-apply-updates.md). A havi Azure Stack-frissítés is tartalmaz, a Windows Defender víruskereső definíciókat, motor és a hónap frissítései platform.

## <a name="next-steps"></a>További lépések

[További információ az Azure Stack-biztonság](azure-stack-security-foundations.md)
