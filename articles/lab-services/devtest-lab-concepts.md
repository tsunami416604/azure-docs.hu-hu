---
title: DevTest Labs fogalmak |} Microsoft Docs
description: Ismerje meg, az alapvető fogalmait DevTest Labs szolgáltatásban, és hogyan azt is megkönnyítheti létrehozására, kezelésére és az Azure virtuális gépek figyelése
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
ms.openlocfilehash: ce0c51a3b97c57095a336128919c5e27f860905b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-fogalmak
## <a name="overview"></a>Áttekintés
Az alábbi lista tartalmazza az alapfogalmakat DevTest Labs és meghatározások:

## <a name="labs"></a>Tesztkörnyezetek
Egy laboratóriumi környezet az infrastruktúra, amely magában foglalja egy csoportot az erőforrások, például a virtuális gépek (VM), amely lehetővé teszi, hogy jobb kezelése ezeket az erőforrásokat korlátozásai és a kvóták megadásával.

## <a name="virtual-machine"></a>Virtuális gép
Egy Azure virtuális gép egyike a számos különböző típusú [igény szerinti, méretezhető számítási erőforrások](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) , amely az Azure biztosít. Azure virtuális gépek a munkakörnyezetnek a virtualizáció kellene vásárolnia és karbantartania a fizikai hardveren futó, bár továbbra is szeretné a virtuális gép karbantartása bizonyos feladatokat, például a konfigurálását, javítását és a rajta futó szoftverek telepítése nélkül.

[A Windows Azure virtuális gépek – áttekintés](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) által biztosított előtt érdemes megfontolni kapcsolatos információk létrehozni egy virtuális Gépet, hogyan hoz létre, és hogyan kezelheti.

## <a name="claimable-vm"></a>Claimable VM
Egy Azure Claimable a virtuális gép egy virtuális gépre, amelyik a labor engedélyekkel rendelkező felhasználó által használható. A tesztkörnyezet rendszergazdája készítse elő a virtuális gépek az adott alap képek és összetevők, és mentse azokat egy megosztott készletéhez. Egy lab-felhasználó akkor is jogcím egy működő virtuális gép a készletből, ha szükségük van egy adott adott konfigurációval.

Egy virtuális Gépet, amely claimable kezdetben nincs hozzárendelve a felhasználóhoz, de fog megjelenni a "Claimable virtual machines" minden felhasználó partnereinek listájához. Miután a felhasználó által a virtuális gépek igényelnek, akár helyezik át az "A virtuális gépnek" területen, és már nem claimable, amelyet semmilyen más felhasználó.

## <a name="environment"></a>Környezet
A DevTest Labs szolgáltatásban környezet az Azure-erőforrások egy tesztkörnyezetben gyűjteményre hivatkozik. [Ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) virtuális Gépre kiterjedő környezetek létrehozása az Azure Resource Manager-sablonok alapján a módját ismerteti.

## <a name="base-images"></a>Alap lemezképek
Alap képek az eszközök és beállítások előtelepített és konfigurált virtuális gép gyors létrehozásához a VM-lemezképekkel történik. A virtuális gépek válassza háttérszínnek. egy meglévő talál, és vegye fel a test-ügynök telepítéséhez összetevő építhető ki. Majd mentheti a kiosztott virtuális Gépen alapul, hogy a következő anélkül, hogy újra kell telepítenie a teszt ügynök minden virtuális gép üzembe helyezéséhez használható.

## <a name="artifacts"></a>Összetevők
Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás, egy virtuális gép kiépítése után. Az összetevők lehetnek:

* A virtuális Gépen – például ügynökök, a Fiddler és a Visual Studio telepíteni kívánt eszközök.
* A virtuális Gépen – például egy tárház klónozása futtatni kívánt műveletek.
* Tesztelni kívánt alkalmazások.

Összetevőket [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) JSON-fájlokat, amelyek tartalmazzák a telepítési utasításokat konfiguráció alkalmazásához.

## <a name="artifact-repositories"></a>Összetevő adattárak
Összetevő adattárak git tárházak találhatók, ahol a összetevők be van jelölve a rendszer. Összetevő tárolóhelyekkel a szervezet újbóli engedélyezése és megosztása több labs lehet hozzáadni.

## <a name="formulas"></a>Képletek
Képletek alap képek mellett a virtuális gép gyors kiépítése egy olyan mechanizmus biztosítása. A DevTest Labs szolgáltatásban egy képlete VM labor létrehozásához használt alapértelmezett tulajdonság értékek listáját.
A képletekkel, ugyanazokat a kulcstulajdonságokat tulajdonságai – például az alapjául szolgáló lemezképhez, a virtuális gép mérete, a virtuális hálózat és a összetevők - rendelkező virtuális gépek hozhatók létre anélkül, hogy adja meg azokat a tulajdonságokat, minden alkalommal. Az alapértelmezett értékeket egy képletet a virtuális gép létrehozásakor használható- vagy módosítani.

## <a name="policies"></a>Házirendek
A házirendek segítenek a költség, a laborban vezérlése. Például létrehozhat egy házirendet, amellyel a virtuális gépek a meghatározott ütemezés szerint automatikusan leáll.

## <a name="caps"></a>CAPS
Caps az eljárást, amely a laborkörnyezetben pazarlás minimalizálásához. Például beállíthat egy kap a felhasználónként vagy egy tesztkörnyezetben hozható létre virtuális gépek számát korlátozhatja.

## <a name="security-levels"></a>Biztonsági szint
Biztonsági hozzáférést által átruházásához hozzáférés-vezérlés (RBAC) határozza meg. Hozzáférés működésének megismerése segít engedélyt, a szerepkör és a Szerepalapú által meghatározott hatókör közötti különbségek megismeréséhez.

* Hozzáférés - engedély egy bizonyos művelet (pl. írásvédett minden virtuális gépnek) definiált elérésére.
* Szerepkör - szerepkör csoportosítva, és a felhasználóhoz rendelt engedélyek egy készletét. Például a *előfizetés tulajdonosa* szerepkör hozzáfér egy előfizetésen belüli összes erőforrást.
* Hatókör - hatókör egy Azure-erőforrás, például egy erőforráscsoportot, egy laboratóriumi vagy az egész előfizetésre, a hierarchiában egy szint.

DevTest Labs keretén belül a felhasználói engedélyek megadásához szerepkörök két típusa van: a tesztkörnyezet tulajdonosa és lab-felhasználó.

* A tesztkörnyezet tulajdonosa - A tesztkörnyezet tulajdonosa hozzáférni semmilyen erőforráshoz belül van. Ezért a tesztkörnyezet tulajdonosa is házirendek módosíthatók, olvasási és írási bármely virtuális gépek, módosítsa a virtuális hálózatot, és így tovább.
* Lab-felhasználó - lab-felhasználó összes lab-erőforrások, például a virtuális gépek, a házirendek és a virtuális hálózatok, megtekinthetik, de nem módosíthatja a házirendeket, vagy más felhasználók által létrehozott bármely virtuális gépeket.

Egyéni szerepkörök létrehozása a DevTest Labs megtekintéséhez tekintse meg a cikk [felhasználói engedélyeket adott labor házirendek](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel a hatókörök hierarchikus, amikor egy felhasználó jogosult bizonyos hatókörre, ezeket az engedélyeket minden alacsonyabb szintű hatókörből lefedett automatikusan kapnak. Például ha egy felhasználó a szerepkört az előfizetés tulajdonosa van rendelve, majd hozzáférhetnek, előfizetés összes olyan erőforrást, többek között az összes virtuális gép, az összes virtuális hálózatot és az összes labs. Ezért egy előfizetés tulajdonosa automatikusan örökli a tesztkörnyezet tulajdonosa szerepe. Azonban a ennek fordítottja nem igaz. A tesztkörnyezet tulajdonosa hozzáfér egy tesztkörnyezetet, amelyhez a hatókör alacsonyabb, mint az előfizetés szintjéről. Ezért a tesztkörnyezet tulajdonosa csak akkor láthatók a virtuális gépek vagy virtuális hálózatok, vagy minden olyan erőforrásnál, amely a labor kívül esnek.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A jelen cikkben ismertetett fogalmakat összes Azure Resource Manager-sablonok, amelyek lehetővé teszik, hogy az infrastruktúra /-konfiguráció a Azure megoldás, és ismételten telepítheti konzisztens állapotú segítségével konfigurálható.

[A struktúra és az Azure Resource Manager-sablonok szintaxisát](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) Azure Resource Manager-sablon és a tulajdonságok érhetők el a különböző szakaszokat, egy sablon leíró.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
[Labor létrehozása a DevTest Labs szolgáltatásban](devtest-lab-create-lab.md)
