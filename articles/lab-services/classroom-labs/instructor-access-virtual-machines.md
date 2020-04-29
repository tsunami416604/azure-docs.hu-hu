---
title: A Azure Lab Servicesban lévő virtuális gépekhez való hozzáférés oktatója
description: Ez a cikk bemutatja, hogyan érhetik el az oktatók a tanulói virtuális gépeket az oktató nézetből. Egy tanítási asszisztens például egy osztály oktatója, de más osztályok tanulója is.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641939"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Virtuális gépek elérése tanulóként az oktató nézetből
Ez a cikk bemutatja, hogyan érhetik el az oktatók a virtuális gépeket tanulóként részt vevő osztályokba. 

Itt talál egy olyan forgatókönyvet, amelyben ez a funkció segít. Az oktatási asszisztens az egyik osztály oktatója, de más osztályok tanulói. A Teaching Instructor pedig az oktatók nézetből szeretné megtekinteni és elérni a tanuló virtuális gépeket, amelyek a saját laborokat jelenítik meg. 

## <a name="access-vms-from-instructor-view"></a>Virtuális gépek elérése az oktató nézetből

1. Jelentkezzen be a [Azure Lab Services webhelyére](https://labs.azure.com). Láthatja a saját laboratóriumait. Ezek a laborok olyan laborok, amelyeket Ön hozott létre saját maga vagy a rendszergazda által a tulajdonosként hozzárendelt laborokban. További információ: [további tulajdonosok hozzáadása meglévő laborhoz](how-to-add-user-lab-owner.md)
2. Ha tanulóként részt vevő osztályokba kíván hozzáférni a virtuális gépekhez, válassza a számítógép ikont a jobb felső sarokban. Győződjön meg arról, hogy az Ön által tanulóként elérhető virtuális gépek jelennek meg. A következő példában a felhasználó a Python Lab tanítási asszisztense, de a Java Lab tanulója. Így a felhasználó a Java laborból látja a virtuális gépet a legördülő listából. A felhasználó elindíthatja a virtuális gépet, és csatlakozhat hozzá. 
    
    ![Tanuló virtuális gépek elérése](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Kapcsolódás virtuális géphez](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Kapcsolódás virtuális géphez RDP használatával Mac gépen](connect-virtual-machine-mac-rdp.md)
- [A távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-use-remote-desktop-linux-student.md)
