---
title: Tesztkörnyezet-fiókok konfigurálása a Azure Lab Servicesban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre labor-fiókot, megtekintheti az összes Lab-fiókot, vagy törölhet egy labor-fiókot Azure Lab Servicesban.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443421"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Tesztkörnyezet-fiókok konfigurálása Azure Lab Services 
Azure Lab Services a labor-fiók a felügyelt labor típusok, például a tanterem Labs tárolója. A rendszergazda létrehoz egy Azure Lab Services, és hozzáférést biztosít a labor tulajdonosai számára, akik a fiókban létrehozhatnak laborokat. 

Ez a cikk bemutatja, hogyan hajthatja végre a következő feladatokat: 

- Címtartomány megadása a virtuális gépek számára a tesztkörnyezetben
- Virtuális gépek automatikus leállításának beállítása a kapcsolat bontásakor

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Címtartomány megadása a virtuális gépek számára a tesztkörnyezetben
A következő eljárás a virtuális gépekhez tartozó címtartomány megadásának lépéseit írja le a laborban. Ha frissíti a korábban megadott tartományt, a módosított címtartomány csak a módosítás után létrehozott virtuális gépekre vonatkozik. 

Íme néhány korlátozás a címtartomány megadásához, amelyet érdemes szem előtt tartani. 

- Az előtagnak kisebbnek vagy egyenlőnek kell lennie, mint 23. 
- Ha egy virtuális hálózat a labor-fiókhoz van társítva, a megadott címtartomány nem lehet átfedésben a társ virtuális hálózatból származó címtartományból.

1. A **labor-fiók** lapon válassza a bal oldali menü **Labs-beállítások** elemét.
2. A **címtartomány** mezőben határozza meg a laborban létrehozandó virtuális gépek címtartomány-tartományát. A címtartomány legyen az osztály nélküli tartományok közötti útválasztás (CIDR) jelölése (például: 10.20.0.0/23). A laborban található virtuális gépek ebben a címtartományból lesznek létrehozva.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Címtartomány konfigurálása](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállítása a kapcsolat bontásakor
A távoli asztali kapcsolat leválasztása után engedélyezheti vagy letilthatja a Windows Lab virtuális gépek (sablon vagy tanuló) automatikus leállítását. Azt is megadhatja, hogy a virtuális gépek mennyi ideig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt.

![Automatikus leállítás beállítása Lab-fiókban](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Ez a beállítás a labor-fiókban létrehozott összes laborra vonatkozik. A labor tulajdonosa felülbírálhatja ezt a beállítást a tesztkörnyezet szintjén. A labor-fiókban a beállítás módosítása csak a módosítás után létrehozott laborokra lesz hatással.

Ha szeretné megtudni, hogy a labor tulajdonosa hogyan állíthatja be ezt a beállítást a labor szintjén, tekintse meg [ezt a cikket.](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet helyének engedélyezése a labor Creator számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának összekötése egy társ virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
