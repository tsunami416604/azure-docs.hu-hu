---
title: Váratlan vesznek el virtuális gépek Azure Windows virtuális gépekhez csatlakoztatott virtuális merevlemezek hibaelhárítása |} Microsoft Docs
description: Váratlan elhárítása Windows virtuális gépek újraindul.
keywords: ssh kapcsolat visszautasította, ssh hiba és az azure-ssh, SSH-kapcsolat sikertelen
services: virtual-machines-windows
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 5c16c2d08a1a317f8f718330ff6c86a452ac1815
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362601"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek hibaelhárítása

Ha egy Azure virtuális gép (VM) ugyanabban a tárfiókban lévő csatlakoztatott virtuális merevlemezek nagy mennyiségű, túllépheti egyedi storage-fiók esetén a méretezhetőségi célok, amely a virtuális gép váratlanul újraindul. Ellenőrizze a tárfiók percenkénti metrikákat (**TotalRequests**/**TotalIngress**/**TotalEgress**) a teljesítményt, amelyek mérete meghaladja a méretezhetőségi célok tárfiókok esetén. Lásd: [PercentThrottlingError metrika növelését](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) meghatározásakor, hogy sávszélesség-szabályozás történt a tárfiók segítségét.

Általában minden egyes bemeneti vagy kimeneti művelet a virtuális gép virtuális merevlemez az eszköz **első oldal** vagy **Put lap** az alapul szolgáló oldalakra vonatkozó blob műveleteket. Ezért a környezetnek a becsült IOPS segítségével is rendelkezik egyetlen tárfiókokban alapján az adott viselkedést az alkalmazás hány virtuális merevlemezek finomítsa. A Microsoft azt javasolja, 40 vagy kevesebb lemezek egyetlen tárfiókokban. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../storage/common/storage-scalability-targets.md) vonatkozó további információért méretezhetőségi célok storage-fiókok, különösen a teljes kérelmek aránya és a tárfióknak a típusa sávszélességet használ.

Ha a tárfiók túllépte a vonatkozó méretezhetőségi célok, helyezze a virtuális merevlemezek több tárfiókot a tevékenység minden egyes számlára csökkentése érdekében.
