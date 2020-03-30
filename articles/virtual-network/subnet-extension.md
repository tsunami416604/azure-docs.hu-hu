---
title: Alhálózati bővítmény az Azure-ban | Microsoft dokumentumok
description: További információ az Azure-beli alhálózati bővítményről.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587510"
---
# <a name="subnet-extension"></a>Alhálózati bővítmény
A nyilvános felhőbe való számítási feladatok áttelepítése gondos tervezést és koordinációt igényel. Az egyik legfontosabb szempont lehet az IP-címek megőrzésének lehetősége. Ami különösen akkor lehet fontos, ha az alkalmazások IP-címfüggőséget tartalmaznak, vagy ha megfelelőségi követelményekkel rendelkezik adott IP-címek használatához. Az Azure Virtual Network megoldja ezt a problémát azáltal, hogy lehetővé teszi a virtuális hálózatok és alhálózatok létrehozását az Ön által választott IP-címtartomány használatával.

Az áttelepítések egy kicsit kihívást jelenthetnek, ha a fenti követelmény hez egy további követelmény tartozik, hogy egyes alkalmazások a helyszínen maradjanak. Például egy adott helyzetben, az alkalmazások at az Azure és a helyszíni, anélkül, hogy az IP-címek mindkét oldalon újraszámozása. Ezenkívül engedélyeznie kell, hogy az alkalmazások úgy kommunikáljanak, mintha ugyanabban a hálózatban lennének.

A fenti probléma egyik megoldása az alhálózati kiterjesztés. A hálózat bővítése lehetővé teszi az alkalmazások számára, hogy ugyanazon a szórásos tartományon keresztül beszéljenek, ha különböző fizikai helyeken léteznek, így nincs szükség a hálózati topológia újramegtervezésére. 

Bár a hálózat bővítése általában nem jó gyakorlat, az alábbi használati esetek szükségessé tehetik.

- **Szakaszos áttelepítés:** A leggyakoribb forgatókönyv az, hogy az áttelepítést szeretné fázisba lépni. Azt szeretné, hogy néhány alkalmazás először és idővel migrálja a többi alkalmazás az Azure-ba.
- **Késés:** Alacsony késési követelmények lehet egy másik ok, amiért bizonyos alkalmazások a helyszínen, annak érdekében, hogy azok a lehető legközelebb az adatközponthoz.
- **Megfelelőség:** Egy másik használati eset, hogy előfordulhat, hogy megfelelőségi követelmények et tartani néhány alkalmazás a helyszínen.
 
> [!NOTE] 
> Az alhálózatokat csak akkor hosszabbítsa meg, ha erre szükség van. Azokban az esetekben, ahol nem hosszabbítja meg az alhálózatok, meg kell próbálnia, hogy ez egy köztes lépés. Idővel próbálja meg újraszámozni az alkalmazásokat a helyszíni hálózatban, és migrálni őket az Azure-ba.

A következő szakaszban bemutatjuk, hogyan bővítheti alhálózatait az Azure-ra.


## <a name="extend-your-subnet-to-azure"></a>Az alhálózat kiterjesztése az Azure-ra
 A helyszíni alhálózatokat kiterjesztheti az Azure-ra egy 3- as réteges átfedéses hálózati alapú megoldás használatával. A legtöbb megoldás egy átfedési technológiát használ, például a VXLAN-t a réteg-2 hálózat 3-as rétegű átfedési hálózat használatával történő kiterjesztéséhez. Az alábbi ábra egy általános megoldás. Ebben a megoldásban ugyanaz az alhálózat mindkét oldalon létezik, az Az Azure és a helyszíni. 

![Példa alhálózati bővítményre](./media/subnet-extension/subnet-extension.png)

Az alhálózat IP-címei az Azure-ban és a helyszíni virtuális gépekhez vannak rendelve. Mind az Azure, mind a helyszíni telepek egy NVA-t illesztbe a hálózataikba. Amikor egy virtuális gép az Azure-ban megpróbál beszélni egy virtuális gép a helyszíni hálózaton, az Azure NVA rögzíti a csomagot, beágyazi azt, és elküldi azt a VPN/Express route a helyszíni hálózatra. A helyszíni NVA megkapja a csomagot, decapsulates, és továbbítja azt a tervezett címzett a hálózaton. A visszáruforgalom hasonló elérési utat és logikát használ.

A fenti példában az Azure NVA és a helyszíni NVA kommunikálnak, és megismerik az EGYMÁS MÖGÖTT i-címeket. Összetettebb hálózatok is rendelkezhetnek egy leképezési szolgáltatás, amely fenntartja a leképezés között nva-k és az IP-címek mögött. Amikor egy NVA csomagot kap, lekérdezi a leképezési szolgáltatást, hogy megtudja, a címe az NVA, amelynek a cél IP-cím mögött.

A következő szakaszban az Azure-ban tesztelt alhálózati bővítménymegoldások részleteit találja.

## <a name="next-steps"></a>További lépések 
[Bővítse ki az alhálózatot az Azure-ra a szállítói megoldások használatával.](https://github.com/microsoft/Azure-LISP)