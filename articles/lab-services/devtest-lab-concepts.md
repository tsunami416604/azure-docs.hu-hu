---
title: DevTest Labs-fogalmak |} A Microsoft Docs
description: Ismerje meg, az alapvető fogalmait, DevTest Labs szolgáltatásban, és hogyan azt teheti, hogy könnyű létrehozását, kezelését és az Azure virtuális gépek figyelése
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676470"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-fogalmak
## <a name="overview"></a>Áttekintés
Az alábbi lista a fő DevTest Labs-fogalmak és definíciókat tartalmazza:

## <a name="labs"></a>Tesztkörnyezetek
Labor a működő az erőforráscsoportokat erőforrások, például a virtuális gépek (VM), amely lehetővé teszi, hogy jobban korlátok és kvóták meghatározásával felügyelheti az erőforrásokat.

## <a name="virtual-machine"></a>Virtuális gép
Egy Azure virtuális gép számos különböző típusú [igény szerinti, méretezhető számítási erőforrás](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) Azure által kínált. Az Azure virtuális gépek a rugalmasságot biztosítanak a virtualization vásárolnia és karbantartania a fizikai hardveren futó, bár továbbra is biztosítani kell a virtuális gép által bizonyos műveletek végrehajtását, például a konfigurálását, javítását és a gépen futó szoftver telepítése nélkül azt.

[Az Azure Windows virtual machines – áttekintés](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) nyújt információt arról, hogy mit kell szem előtt létrehozása egy virtuális Gépet, hogyan hoz létre, és hogyan kezelheti azt.

## <a name="claimable-vm"></a>Igényelhető virtuális gép
Azure igényelhető virtuális gép egy virtuális gép elérhető bármely labor engedéllyel rendelkező felhasználó általi használatra. A lab rendszergazda is adott alaplemezképek és összetevők a virtuális gépek előkészítése, és mentse azokat egy megosztott készletéhez. A lab-felhasználó akkor is jogcím egy működő virtuális Gépet a készletből, amikor szükségük van egy adott adott konfigurációval.

Egy virtuális Gépet, amely igényelhető nem kezdetben rendelt egy konkrét felhasználóhoz, de minden felhasználói listájában "Igényelhető virtuális gépnek" alatt fog megjelenni. Felhasználó által a virtuális gép átvétele után helyezték át akár a "Saját virtuális gépnek" területen és már nem igényelhető, amelyet semmilyen más felhasználó.

## <a name="environment"></a>Környezet
DevTest Labs-környezetben egy környezeti tesztkörnyezetben az Azure-erőforrások gyűjteményét hivatkozik. [Ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) tárgyalja a több virtuális gépes környezet létrehozása az Azure Resource Manager-sablonok alapján.

## <a name="base-images"></a>Alapként szolgáló rendszerképek
Alaplemezképek olyan Virtuálisgép-rendszerképek a kívánt eszközökkel és a egy virtuális gép gyors létrehozása előre telepített és konfigurált beállítások. Egy virtuális Gépet telepíthet egy meglévő Ügyfélkörünk kiadási, és vegye fel a vizsgálati ügynök telepítéséhez összetevőt. Ezután mentheti a kiépített virtuális Gépet alapul, hogy az Alap az egyes a virtuális gép kiépítése a vizsgálati ügynök újratelepítése nélkül is használható.

## <a name="artifacts"></a>Összetevők
Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás egy virtuális gép kiépítése után. Az összetevők lehetnek:

* Telepítse a virtuális gép – például ügynökök, Fiddler, és a Visual Studio a kívánt eszközöket.
* – Például a tárház klónozása az a virtuális gépen futtatni kívánt műveletek.
* Tesztelni kívánt alkalmazások.

Összetevőket [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) központi telepítésének végrehajtása és a konfiguráció alkalmazásához vonatkozó utasításokat tartalmazó JSON-fájlokat.

## <a name="artifact-repositories"></a>Összetevő-adattárak
Összetevő-tárházak git-tárházakat, amelyekbe összetevőket is be. Összetevő-tárházak újbóli engedélyezése és megosztása a szervezetben több labs lehet hozzáadni.

## <a name="formulas"></a>Képletek
Képletek alaprendszerképek lekéréshez mellett a virtuális gépek gyors kiépítése mechanizmus biztosítására. DevTest Labs szolgáltatásban létrehozott képletet az alapértelmezett tulajdonságértékeket egy laborbeli virtuális gép létrehozásához használt listája.
Képleteket, tulajdonságok – alaplemezkép, a virtuális gép mérete, a virtuális hálózat és a összetevők – például ugyanazokat a virtuális gépek hozhatók létre anélkül, hogy minden alkalommal, amikor adja meg a megadott tulajdonságokkal. A képlet egy virtuális Gépet hoz létre, amikor az alapértelmezett értékeket is használható –, vagy módosítani.

## <a name="policies"></a>Házirendek
Szabályzatok segítenek a tesztkörnyezet a költségek ellenőrzés alatt tartásával. Létrehozhat például olyan szabályzatot automatikusan leállíthatók a virtuális gépek egy meghatározott ütemezés alapján.

## <a name="caps"></a>CAPS
Laboronként létrehozható egy olyan mechanizmus, maximális hatékonyság a lab-ben. Például beállíthatja a felhasználónként vagy tesztkörnyezetben létrehozható virtuális gépek számának korlátozása egy korlát.

## <a name="security-levels"></a>Biztonsági szint
Biztonsági hozzáférési által Azure szerepköralapú hozzáférés-vezérlés (RBAC) határozza meg. Szeretné megtudni, hogyan működik a hozzáférés, segít a engedélyt, a szerepkör és egy hatókör szerint RBAC közötti különbségeket.

* Hozzáférés - engedély egy meghatározott hozzáférést egy adott művelet (pl. írásvédett összes virtuális gépen).
* Szerepkör - szerepkör csoportosítva, és a felhasználóhoz rendelt engedélyek egy készletét. Ha például a *előfizetés tulajdonosa* egy előfizetésen belüli összes erőforrás hozzáférése van.
* Hatókör - hatókör a hierarchiában, az Azure-erőforrás, például egy erőforráscsoport, egy egyetlen labor vagy a teljes előfizetés szintet.

DevTest Labs hatókörén belül a felhasználói engedélyek megadásához szerepkörök két típusa van: lab tulajdonos és a lab-felhasználó.

* Tesztlabor - egy tesztlabor tulajdonos tulajdonosa belül a labor létrehozása minden olyan erőforrásokhoz való hozzáférés. Ezért egy tesztlabor tulajdonosa is módosíthatja a szabályzatokat, olvassa el és írhat olyan virtuális gépek, módosítsa a virtuális hálózat, és így tovább.
* Labor felhasználó - lab-felhasználó minden labor-erőforrásokat, például a virtuális gépek, a házirendek és a virtuális hálózatok, megtekintheti, de nem módosíthatja a szabályzatokat vagy más felhasználók által létrehozott bármely virtuális gépeket.

Egyéni szerepkörök létrehozása a DevTest Labs megtekintéséhez tekintse meg a cikk [felhasználó engedélyeket adott laborszabályzatok](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Mivel hatókörök hierarchikus, amikor a felhasználók számára egy bizonyos hatókörben, automatikusan megkapják ezeket az engedélyeket, minden egyes alacsonyabb szintű hatókörben vonatkozik. Például ha egy felhasználó hozzá van rendelve a szerepkört az előfizetés tulajdonosa, majd hozzáféréssel rendelkeznek többek között az összes virtuális gép összes virtuális hálózatot és az összes labs egy adott előfizetés összes erőforrást. Ezért előfizetés tulajdonosaként automatikusan örökli a labor tulajdonosi szerepkörhöz. Azonban nem igaz ennek az ellenkezője. A lab tulajdonosa hozzáférés a laborokhoz, amely kisebb, mint az előfizetés szintjén hatókör. Ezért a labor tulajdonosa nem láthatja a virtuális gépek vagy a virtuális hálózatok vagy olyan erőforrások, amelyek a tesztkörnyezet kívül.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
Minden ebben a cikkben tárgyalt fogalmakat konfigurálható az Azure Resource Manager-sablonokat, amelyek lehetővé teszik az Azure-megoldás infrastruktúrájának/konfigurációval, és ismételten üzembe helyezni egy konzisztens állapotba.

[Struktúra és az Azure Resource Manager-sablonok szintaxisát](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) az Azure Resource Manager-sablon és a különböző szakaszokat a rendelkezésre álló tulajdonságok szerkezetét írja le.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
[DevTest Labs szolgáltatásban létrehozott tesztkörnyezet létrehozása](devtest-lab-create-lab.md)
