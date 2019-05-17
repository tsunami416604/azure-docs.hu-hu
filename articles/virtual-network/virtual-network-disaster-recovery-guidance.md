---
title: Virtuális hálózat üzletmenet-folytonossági |} A Microsoft Docs
description: Ismerje meg, mi a teendő az Azure-beli virtuális hálózatok érintő Azure szolgáltatáskimaradás működésében.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 68a9523dcc9c4dd84399c68fc7e31a692c011487
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523254"
---
# <a name="virtual-network--business-continuity"></a>Virtuális hálózat – üzletmenet-folytonossági

## <a name="overview"></a>Áttekintés
Virtuális hálózat (VNet) a felhőbeli hálózatának logikai megfelelője. Lehetővé teszi a saját privát IP-címteret és a hálózat szegmentálása alhálózatra. Virtuális hálózatok egy megbízhatósági kapcsolat határán a számítási erőforrások, például az Azure Virtual Machines és Cloud Services (webes/feldolgozói szerepkörök) üzemeltetésére szolgál. Virtuális hálózat lehetővé teszi a közvetlen saját integrációs csomaggal folytatott kommunikációhoz a benne lévő üzemeltetett erőforrások között. A helyszíni hálózathoz egy VPN-átjáró vagy ExpressRoute virtuális hálózati kapcsolat.

Virtuális hálózat egy régió hatókörén belül jön létre. Is *létrehozása* egyező virtuális hálózat címtere (például USA keleti régiója, USA nyugati régiója) két különböző régióban, de mivel ugyanazt a címtartományt, akkor nem kapcsolhatja össze azokat. 

## <a name="business-continuity"></a>Üzleti folytonosság

Előfordulhat, hogy különféle módokon, hogy az alkalmazás sikerült tartománnyá. Egy régióban sikerült teljesen vágható természeti katasztrófa vagy részleges katasztrófa miatt több eszköz vagy szolgáltatás hibája miatt. A művelet hatása a VNet szolgáltatás eltér az egyes ezekben a helyzetekben.

**K: Mi a teendő, ha kimaradás fordul elő egy teljes régióra? Például ha egy régió teljesen levágja természeti katasztrófa miatt? Mi történik, a virtuális hálózatok régióban üzemeltet?**

V: A virtuális hálózat és az érintett régióban lévő erőforrásokat az a szolgáltatás megszűnésének alatt marad nem érhető el.

![Egyszerű virtuális hálózati diagramja](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: Milyen lehetőségeket újra létre kell hoznia egy másik régióban lévő ugyanazon a virtuális hálózaton?**

V: Virtuális hálózatok viszonylag könnyen használható erőforrásokat. Az azonos címtartomány egy másik régióban lévő virtuális hálózat létrehozása az Azure API-k hívhat meg. Hozza létre újból az ugyanabban a környezetben, amely érintett a régióban található, akkor győződjön meg arról, API-hívások ismételt üzembe helyezése a Cloud Services webes és feldolgozói szerepkörök és a virtuális gépek volt. Ha helyszíni kapcsolatok, például az egy hibrid telepítésben rendelkezik egy új VPN-átjáró üzembe helyezése, és csatlakoztassa a helyszíni hálózathoz.

Virtuális hálózat létrehozása: [hozzon létre egy virtuális hálózatot](manage-virtual-network.md#create-a-virtual-network).

**K: Virtuális hálózat egy adott régióban replikája lehet újra létre kelljen egy másik régióban?**

V: Két virtuális hálózat azonos magánhálózati IP-címtér és erőforrások használatával két különböző régióban kelljen Igen, hozhat létre. Internetes szolgáltatások a virtuális hálózat üzemel, ha sikerült földrajzi irányítása a régiót, amelyben aktív forgalom értékre állította be a Traffic Managert. Azonban nem lehet csatlakoztatni két virtuális hálózat az ugyanazt a címtartományt a helyszíni hálózathoz, virtuális berendezés útválasztási problémái okozna. A időben vészhelyzet és a egy virtuális hálózat egy adott régióban, adatvesztés a másik virtuális hálózat használható a régióban, csatlakozni tudjon a helyszíni hálózathoz a megfelelő címterét.

Virtuális hálózat létrehozása: [hozzon létre egy virtuális hálózatot](manage-virtual-network.md#create-a-virtual-network).

