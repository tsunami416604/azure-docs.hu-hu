---
title: Váratlan vesznek el virtuális gépek Azure Linux virtuális gépekhez csatlakoztatott virtuális merevlemezek hibaelhárítása |} Microsoft Docs
description: Linux virtuális gépek váratlan újraindítások elhárításáról.
keywords: ssh kapcsolat visszautasította, ssh hiba és az azure-ssh, SSH-kapcsolat sikertelen
services: virtual-machines-linux
author: tamram
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: 8ccb6b61c8de1599cd3db011d6401c781cefc31a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Váratlan újraindítások csatlakoztatott virtuális merevlemezek a virtuális gépek hibaelhárítása

Ha egy Azure virtuális gép (VM) ugyanabban a tárfiókban lévő csatlakoztatott virtuális merevlemezek nagy mennyiségű, túllépheti egyedi storage-fiók esetén a méretezhetőségi célok, amely a virtuális gép váratlanul újraindul. Ellenőrizze a tárfiók percenkénti metrikákat (**TotalRequests**/**TotalIngress**/**TotalEgress**) a teljesítményt, amelyek mérete meghaladja a méretezhetőségi célok tárfiókok esetén. Lásd: [PercentThrottlingError metrika növelését](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) meghatározásakor, hogy sávszélesség-szabályozás történt a tárfiók segítségét.

Általában minden egyes bemeneti vagy kimeneti művelet a virtuális gép virtuális merevlemez az eszköz **első oldal** vagy **Put lap** az alapul szolgáló oldalakra vonatkozó blob műveleteket. Ezért a környezetnek a becsült IOPS segítségével is rendelkezik egyetlen tárfiókokban alapján az adott viselkedést az alkalmazás hány virtuális merevlemezek finomítsa. A Microsoft azt javasolja, 40 vagy kevesebb lemezek egyetlen tárfiókokban. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../storage/common/storage-scalability-targets.md) vonatkozó további információért méretezhetőségi célok storage-fiókok, különösen a teljes kérelmek aránya és a tárfióknak a típusa sávszélességet használ.

Ha a tárfiók túllépte a vonatkozó méretezhetőségi célok, helyezze a virtuális merevlemezek több tárfiókot a tevékenység minden egyes számlára csökkentése érdekében.
