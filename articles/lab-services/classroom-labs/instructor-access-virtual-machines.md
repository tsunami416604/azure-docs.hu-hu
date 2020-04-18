---
title: Virtuális gépek elérése az Azure Lab Servicesben
description: Ez a cikk bemutatja, hogyan oktatók férhetnek hozzá a tanuló virtuális gépek az oktató nézetben. Például egy tanársegéd lehet egy osztály oktatója, de más osztályok tanulója is.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641939"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Virtuális gépek elérése diákként az oktatói nézetből
Ez a cikk bemutatja, hogyan oktatók férhetnek hozzá a virtuális gépek az osztályok, hogy részt vesznek a diákok. 

Itt van egy forgatókönyv, ahol ez a funkció segít. A tanársegéd egy osztály oktatója, de más osztályok tanulója. És a tanár oktató szeretné megtekinteni, és elérheti a tanuló virtuális gépek az oktató nézet, amely megmutatja a laborok saját. 

## <a name="access-vms-from-instructor-view"></a>Virtuális gépek elérése oktatónézetből

1. Jelentkezzen be az [Azure Lab Services webhelyére.](https://labs.azure.com) Látod a laborokat, amik a sajátjaid. Ezek a laborok lehetnek ön által létrehozott laborok, vagy a rendszergazdák által önhöz tulajdonosként hozzárendelt laborok. További információ: [További tulajdonosok hozzáadása meglévő laborhoz](how-to-add-user-lab-owner.md)
2. Ha a tanulóként részt vett osztályok virtuális gépeit szeretné elérni, válassza a számítógép ikonját a jobb felső sarokban. Győződjön meg arról, hogy diákként elérhető virtuális gépeket lát. A következő példában a felhasználó a Python-labor oktatási asszisztense, de a Java labor tanulója. Így a felhasználó látja a virtuális gép a Java laborban a legördülő listában. A felhasználó elindíthatja a virtuális gép, és csatlakozzon hozzá. 
    
    ![Diákvirtuális gépek elérése](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Kapcsolódás virtuális géphez](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Csatlakozás virtuális géphez asztali gép használatával Macen](connect-virtual-machine-mac-rdp.md)
- [Távoli asztal használata Linuxos virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)
