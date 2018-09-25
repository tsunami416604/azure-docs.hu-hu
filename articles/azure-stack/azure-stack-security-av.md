---
title: Frissítés a Windows Defender víruskereső az Azure Stackben
description: A víruskereső részleteiért van mindig naprakész az Azure Stackben
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/13/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 3c4be228442bf67ccf16236e36cbf015eca6d4e0
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092058"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Frissítés a Windows Defender víruskereső az Azure Stackben

[A Windows Defender víruskereső](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) kártevőirtó megoldás, amely a biztonsági és védelmet biztosít. Minden Azure Stack-infrastruktúra összetevő (a Hyper-V-gazdagépek és virtuális gépeket) a Windows Defender víruskereső használható védett. A védelem naprakészen a Windows Defender víruskereső-definíciókat, a motor és a platform rendszeres frissítés szükséges. Konfigurációjától függ, hogy hogyan telepítse a frissítéseket. 

## <a name="connected-scenario"></a>Csatlakoztatott forgatókönyv

A kártevőirtó definíciójának és keresőmotor-frissítések, az Azure Stack [frissítés erőforrás-szolgáltató](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-updates#the-update-resource-provider) letölti a kártevőirtó-definíciókat és motor-frissítések naponként több alkalommal. Minden egyes Azure Stack-infrastruktúra összetevője a frissítés beolvassa a frissítési erőforrás-szolgáltató, és automatikusan alkalmazza a frissítést.

A kártevőirtó platform frissítéseinek alkalmazása a [havi Azure Stack-frissítés](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-apply-updates). A havi Azure Stack-frissítés magában foglalja a Windows Defender víruskereső platform frissítései hónap.

## <a name="disconnected-scenario"></a>Kapcsolat nélküli forgatókönyv

 Alkalmazza a [havi Azure Stack-frissítés](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-apply-updates). A havi Azure Stack-frissítés is tartalmaz, a Windows Defender víruskereső definíciókat, motor és a hónap frissítései platform.

## <a name="next-steps"></a>További lépések

[További információ az Azure Stack-biztonság](azure-stack-security-foundations.md)
