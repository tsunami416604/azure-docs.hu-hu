---
title: DevTest Labs fogalmak | Microsoft dokumentumok
description: Ismerje meg a DevTest Labs alapfogalmait, és hogyan teheti lehetővé az Azure virtuális gépek létrehozását, kezelését és figyelését
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428918"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-fogalmak
## <a name="overview"></a>Áttekintés
Az alábbi lista a DevTest Labs legfontosabb fogalmait és definícióit tartalmazza:

## <a name="labs"></a>Tesztkörnyezetek
A tesztkörnyezet az az infrastruktúra, amely erőforrások egy csoportját foglalja magában, például a virtuális gépeket (VM),amely lehetővé teszi, hogy ezeket az erőforrásokat korlátok és kvóták megadásával jobban kezelje.

## <a name="virtual-machine"></a>Virtuális gép
Az Azure virtuális gép az Azure által kínálott igény [szerinti, méretezhető számítási erőforrások](/azure/architecture/guide/technology-choices/compute-decision-tree) számos típusának egyike. Az Azure virtuális gépek a virtualizáció rugalmasságát biztosítják anélkül, hogy meg kellene vásárolniuk és karban kellene tartaniuk az azt futtató fizikai hardvert, bár bizonyos feladatok elvégzésével, például konfigurálásával, javításával és telepítésével továbbra is fenn kell tartania a virtuális gépet. meg.

[Az Azure-beli Windows virtuális gépek áttekintése](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) tájékoztatást nyújt arról, hogy mit kell figyelembe vennie a virtuális gép létrehozása előtt, hogyan hozza létre, és hogyan kell kezelni.

## <a name="claimable-vm"></a>Követelhető virtuális gép
Az Azure-ban igényelhető virtuális gép egy virtuális gép, amely bármely engedélyekkel rendelkező tesztkörnyezet-felhasználó számára használható. A labor rendszergazdája előkészítheti a virtuális gépeket adott alaplemezképek és összetevők, és mentse őket egy megosztott készletbe. A tesztkörnyezet-felhasználó ezután igényelhet egy működő virtuális gép a készletből, ha szükség van egy adott konfigurációval.

A virtuális gép, amely igény szerint nem egy adott felhasználóhoz van rendelve, de megjelenik minden felhasználó listájában a "Követelhető virtuális gépek" alatt. Miután egy felhasználó igényli a virtuális gépet, a rendszer áthelyezi a "Saját virtuális gépei" területre, és más felhasználók már nem igényelhetők.

## <a name="environment"></a>Környezet
A DevTest Labs, egy környezetben egy környezetben az Azure-erőforrások gyűjteménye. [Ez a blogbejegyzés](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) ismerteti, hogyan hozhat létre többvirtuális gépkörnyezeteket az Azure Resource Manager-sablonokból.

## <a name="base-images"></a>Alapként szolgáló rendszerképek
Az alaplemezképek virtuálisgép-lemezképek, amelyek az összes előre telepített és a virtuális gép gyors létrehozására konfigurált eszközökkel és beállításokkal vannak konfigurálva. A virtuális gép kiépítése egy meglévő alap kiválasztásával, és egy összetevő hozzáadása a tesztügynök telepítéséhez. Ezután a kiosztott virtuális gép alapként mentheti, így az alap anélkül használható, hogy újra kellene telepítenie a tesztügynököt a virtuális gép minden egyes kiépítéséhez.

## <a name="artifacts"></a>Összetevők
A szolgáltatáshibák az alkalmazás üzembe helyezéséhez és konfigurálásához használatosak a virtuális gép kiépítése után. A műtermékek a következők lehetnek:

* A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
* A virtuális gépen futtatni kívánt műveletek – például egy tármű-klónozás.
* A tesztelni kívánt alkalmazások.

A műtermékek [olyan Azure Resource Manager](../azure-resource-manager/management/overview.md) JSON-fájlok, amelyek utasításokat tartalmaznak a központi telepítés és a konfiguráció alkalmazására.

## <a name="artifact-repositories"></a>Műtermék-tárolók
A műtermék-tárolók git-tárolók, ahol a műtermékek et beadják. A műtermék-tárolók a szervezet több laborjához is hozzáadhatók, amelyek lehetővé teszik az újrafelhasználást és a megosztást.

## <a name="formulas"></a>Képletek
Képletek, az alaplemezképek mellett, biztosít egy mechanizmust a gyors virtuális gép kiépítése. A DevTest Labs képlete a tesztkörnyezet virtuális gépének létrehozásához használt alapértelmezett tulajdonságértékek listája.
Képletek esetén az azonos tulajdonságokkal rendelkező virtuális gépek – például az alaplemezkép, a virtuális gép mérete, a virtuális hálózat és az összetevők – anélkül hozhatók létre, hogy minden alkalommal meg kellene adniuk ezeket a tulajdonságokat. Virtuális gép létrehozásakor egy képlet, az alapértelmezett értékeket lehet használni, ahogy van, vagy módosítani.

## <a name="policies"></a>Házirendek
A szabályzatok segítenek a költségek szabályozásában a laborban. Létrehozhat például egy házirendet, amely automatikusan leállítja a virtuális gépeket egy meghatározott ütemezés alapján.

## <a name="caps"></a>Sapka
A kupakok egy olyan mechanizmus, amely minimálisra csökkenti a hulladékot a laborban. Beállíthatja például egy korlátot, amely korlátozza a felhasználónként vagy egy tesztkörnyezetben létrehozható virtuális gépek számát.

## <a name="security-levels"></a>Biztonsági szintek
A biztonsági hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (RBAC) határozza meg. A hozzáférés működésének megértéséhez segít megérteni az engedély, a szerepkör és az RBAC által meghatározott hatókör közötti különbségeket.

* Engedély – Az engedély egy meghatározott hozzáférés egy adott művelethez (pl. olvasási hozzáférés az összes virtuális géphez).
* Szerepkör – A szerepkör olyan engedélyek csoportja, amelyek csoportosíthatók és hozzárendelhetők egy felhasználóhoz. Például az *előfizetés tulajdonosi* szerepkör e-hozzáféréssel rendelkezik az előfizetésen belüli összes erőforráshoz.
* Scope – A hatókör egy azure-erőforrás hierarchiáján belüli szint, például egy erőforráscsoport, egy tesztkörnyezet vagy a teljes előfizetés.

A DevTest Labs hatókörén belül kétféle szerepkör határozza meg a felhasználói engedélyeket: a labor tulajdonosa és a tesztkörnyezet-felhasználó.

* Labor tulajdonos – A labor tulajdonosa hozzáfér a laboron belüli erőforrásokhoz. Ezért a tesztkörnyezet tulajdonosa módosíthatja a szabályzatokat, olvashatja és írhatja a virtuális gépeket, módosíthatja a virtuális hálózatot, és így tovább.
* Laborfelhasználó – A tesztkörnyezet-felhasználó megtekintheti az összes tesztkörnyezet-erőforrások, például a virtuális gépek, szabályzatok és a virtuális hálózatok, de nem módosíthatja a szabályzatokat, vagy a más felhasználók által létrehozott virtuális gépek.

Ha meg szeretné tekinteni, hogyan hozhat létre egyéni szerepköröket a DevTest Labs alkalmazásban, olvassa el a [felhasználói engedélyek megadása adott laborházirendeknek című cikket.](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)

Mivel a hatókörök hierarchikusak, ha a felhasználó rendelkezik engedélyekkel egy bizonyos hatókörben, akkor automatikusan megkapja ezeket az engedélyeket minden alacsonyabb szintű hatókörben. Például ha egy felhasználó van rendelve az előfizetés tulajdonosa, majd hozzáférhetnek az összes erőforrás egy előfizetésben, amely magában foglalja az összes virtuális gépek, az összes virtuális hálózatok és az összes labs. Ezért az előfizetés tulajdonosa automatikusan örökli a labor tulajdonosának szerepkörét. Ennek az ellenkezője azonban nem igaz. A tesztkörnyezet tulajdonosa rendelkezik hozzáféréssel egy tesztkörnyezethez, amely alacsonyabb hatókörrel rendelkezik, mint az előfizetési szint. Ezért a tesztkörnyezet tulajdonosa nem fogja tudni látni a virtuális gépek vagy a virtuális hálózatok, illetve a laboron kívüli erőforrásokat.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A cikkben tárgyalt összes fogalom konfigurálható az Azure Resource Manager-sablonok használatával, amelyek lehetővé teszik az Azure-megoldás infrastruktúrájának/konfigurációjának meghatározását, és ismételten konzisztens állapotban való üzembe helyezését.

[Ismerje meg az Azure Resource Manager-sablonok szerkezetét és szintaxisát,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) amely ismerteti az Azure Resource Manager-sablon szerkezetét és a sablon különböző szakaszaiban elérhető tulajdonságokat.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
[Labor létrehozása a DevTest Labs-ben](devtest-lab-create-lab.md)
