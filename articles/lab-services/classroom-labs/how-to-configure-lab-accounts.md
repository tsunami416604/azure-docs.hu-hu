---
title: Laborfiókok konfigurálása az Azure Lab Servicesben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan hozhat létre egy tesztkörnyezet-fiókot, tekintse meg az összes tesztkörnyezet-fiókok, vagy törölje a tesztkörnyezet-fiók az Azure Lab Servicesben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284303"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Tesztkörnyezetfiókok konfigurálása az Azure Lab Servicesben 
Az Azure Lab Services-ben egy laborfiók egy tároló felügyelt labortípusok, például tantermi laborok. A rendszergazda beállít egy tesztkörnyezet-fiókot az Azure Lab Services szolgáltatással, és hozzáférést biztosít a labortulajdonosok, akik laborokat hozhat létre a fiókban. 

Ez a cikk a következő feladatok elvégzését mutatja be: 

- A tesztkörnyezetben lévő virtuális gépek címtartományának megadása
- Virtuális gépek automatikus leállításának konfigurálása leválasztáskor

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>A tesztkörnyezetben lévő virtuális gépek címtartományának megadása
A következő eljárás lépéseket, hogy adja meg a címtartományt a virtuális gépek a laborban. Ha frissíti a korábban megadott tartományt, a módosított címtartomány csak a módosítás után létrehozott virtuális gépekre vonatkozik. 

Az alábbiakban néhány korlátozást olvashat a címtartomány megadásához, amelyeket érdemes szem előtt tartania. 

- Az előtagnak legfeljebb 23-nak kell lennie. 
- Ha egy virtuális hálózat társviszonyban van a laborfiókkal, a megadott címtartomány nem fedheti át a társviszonyba nem létesített virtuális hálózat címtartományát.

1. A **Laborfiók** lapon válassza a bal oldali menü **Labs-beállítások elemét.**
2. A **Címtartomány** mezőhöz adja meg a laborban létrehozandó virtuális gépek címtartományát. A címtartománynak az osztály nélküli tartományok közötti útválasztás (CIDR) jelölésében kell lennie (például: 10.20.0.0/23). A tesztkörnyezetben lévő virtuális gépek ebben a címtartományban jönnek létre.
3. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Címtartomány konfigurálása](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállítása leválasztáskor
A távoli asztali kapcsolat lebontása után engedélyezheti vagy letilthatja a Windows lab virtuális gépek (sablonok vagy diákok) automatikus leállítását. Azt is megadhatja, hogy a virtuális gépek nek mennyi ideig kell várniuk a felhasználó újracsatlakozására, mielőtt automatikusan leállna.

![Automatikus leállítási beállítás a laborfiókban](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Ez a beállítás a laborfiókban létrehozott összes laborra vonatkozik. A tesztkörnyezet tulajdonosa felülbírálhatja ezt a beállítást a labor szintjén. A beállítás módosítása a laborfiókban csak a módosítás után létrehozott laborokra lesz hatással.

Ha többet szeretne tudni arról, hogy egy tesztkörnyezet tulajdonosa hogyan konfigurálhatja ezt a beállítást a tesztkörnyezet szintjén, olvassa el [ezt a cikket.](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának csatlakoztatása egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
