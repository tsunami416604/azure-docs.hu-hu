---
title: Beágyazott virtualizálás engedélyezése sablonvirtuális gépen az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre sablon virtuális gépet több virtuális gép belsejében.  Más szóval engedélyezze a beágyazott virtualizálást egy sablon virtuális gépen az Azure Lab Servicesben.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502006"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Beágyazott virtualizálás engedélyezése sablonos virtuális gépen az Azure Lab Servicesben

Jelenleg az Azure Lab Services lehetővé teszi, hogy egy sablon virtuális gép egy laborban, és egy-egy példányt elérhetővé minden a felhasználók számára. Ha Ön hálózatépítő, biztonsági vagy informatikai órákat oktató professzor, előfordulhat, hogy minden egyes diákjának olyan környezetet kell biztosítania, amelyben több virtuális gép is beszélgethet egymással a hálózaton keresztül.

A beágyazott virtualizáció lehetővé teszi, hogy több virtuális gép környezetet hozzon létre a tesztkörnyezet sablonvirtuális gépén belül. A sablon közzététele biztosítja a labor minden felhasználó számára egy virtuális gép beállítása több virtuális gép belül.  Ez a cikk ismerteti, hogyan állíthatja be a beágyazott virtualizálást egy sablongépen az Azure Lab Services-ben.

## <a name="what-is-nested-virtualization"></a>Mi a beágyazott virtualizáció?

A beágyazott virtualizáció lehetővé teszi, hogy virtuális gépeket hozzon létre egy virtuális gépen belül. A beágyazott virtualizálás a Hyper-V-n keresztül történik, és csak Windows virtuális gépeken érhető el.

A beágyazott virtualizációról az alábbi cikkekben talál további információt:

- [Beágyazott virtualizálás az Azure-ban](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépben](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Megfontolandó szempontok

A beágyazott virtualizációval rendelkező tesztkörnyezet beállítása előtt az alábbiakat kell figyelembe venni.

- Új tesztkörnyezet létrehozásakor válassza **a Közepes (beágyazott virtualizálás)** vagy a **Nagy (beágyazott virtualizáció)** méretek lehetőséget a virtuális gép méretéhez. Ezek a virtuális gépméretek támogatják a beágyazott virtualizációt.
- Válasszon olyan méretet, amely jó teljesítményt nyújt mind a gazdagép, mind az ügyfél virtuális gépei számára.  Ne feledje, hogy a virtualizáció használatakor a választott méretnek nem csak egy gépnek, hanem a gazdagépnek, valamint az egyidejűleg futtatandó ügyfélgépeknek is megfelelőnek kell lennie.
- Az ügyfél virtuális gépei nem férhetnek hozzá az Azure-erőforrásokhoz, például az Azure virtuális hálózat DNS-kiszolgálóihoz.
- A gazdavirtuális gép telepítéséhez be kell állítani, hogy az ügyfélgép rendelkezhet-e internetkapcsolattal.
- Az ügyfélvirtuális gépek független gépekként vannak licencelve. A Microsoft operációs rendszerek és -termékek licenceléséről a [Microsoft licenceléséről a Microsoft licencelése](https://www.microsoft.com/licensing/default) című témakörben talál információt. A sablongép beállítása előtt ellenőrizze a használt egyéb szoftverek licencszerződéseit.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen

Ez a cikk feltételezi, hogy létrehozott egy tesztkörnyezet-fiókot és egy tesztkörnyezet.  Az új tesztkörnyezet-fiók létrehozásáról további információt a [Lab-fiók beállításához szükséges oktatóanyagcímű témakörben talál.](tutorial-setup-lab-account.md) A labor létrehozásáról további információt az [osztálytermi laboroktatói bemutató beállítása című](tutorial-setup-classroom-lab.md)témakörben talál.

>[!IMPORTANT]
>Válassza ki a **Nagy (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás)** a virtuális gép mérete a labor létrehozásakor.  A beágyazott virtualizáció egyébként nem fog működni.  

A sablongéphez való csatlakozásról olvassa el az [Osztálytermi sablon létrehozása és kezelése](how-to-create-manage-template.md)című témakört.

### <a name="using-script-to-enable-nested-virtualization"></a>Beágyazott virtualizálás engedélyezése parancsfájlhasználatával

A Windows Server 2016 vagy a Windows Server 2019 beágyazott virtualizálására vonatkozó automatikus telepítés használatához olvassa el a [Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen](how-to-enable-nested-virtualization-template-vm-using-script.md)az Azure Lab Services ben parancsfájl használatával című témakört. A Hyper-V szerepkör telepítéséhez a [Lab Services Hyper-V parancsfájljainak parancsfájljait](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) fogja használni.  A parancsfájlok hálózati kapcsolatot is beállítanak, hogy a Hyper-V virtuális gépek internet-hozzáféréssel rendelkezhessenek.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Beágyazott virtualizáció engedélyezése windowsos eszközökkel

A Windows Server 2016 vagy a Windows Server 2019 windowsos szerepkörökkel és felügyeleti eszközökkel történő beállítási beágyazott [virtualizálása című témakörben olvashat: A beágyazott virtualizálás engedélyezése egy sablonos virtuális gépen az Azure Lab Services ben.](how-to-enable-nested-virtualization-template-vm-ui.md)  Az utasítások azt is ismertetik, hogyan kell beállítani a hálózatot, hogy a Hyper-V virtuális gépek internet-hozzáféréssel rendelkezhessenek.
