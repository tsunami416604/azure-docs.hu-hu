---
title: Hely kiválasztásának engedélyezése a laborkészítőnek az Azure Lab Servicesben
description: Ez a cikk azt ismerteti, hogy a tesztkörnyezet-fiók rendszergazdája hogyan engedélyezheti a tesztkörnyezet-létrehozóknak, hogy helyeket válasszanak a laborjaikhoz.
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
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444366"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>A laborkészítő nek engedélyezze a labor helyének kiválasztását az Azure Lab Servicesben
Az Azure Lab Services-ben a laborfiók tulajdonosa engedélyezheti, hogy a laborkészítők (oktatók) válasszanak ki egy helyet az általuk létrehozott laborhoz. Ez a hely eltérhet a tesztkörnyezet-fiók helyétől. A hely az Azure-régiók egy csoportja. Az Egyesült Államok helye például olyan régiók csoportja, mint az USA keleti régiója, USA nyugati régiója és így tovább. 

Ön, mint a laborfiók tulajdonosa, kiválaszthatja a **Labor létrehozója, hogy válasszon labor helybeállítást,** amikor létrehoz egy tesztkörnyezet-fiókot, és miután létrehozta a tesztkörnyezet-fiók (vagy egy meglévő tesztkörnyezet-fiók). 

## <a name="at-the-time-of-lab-account-creation"></a>A tesztkörnyezet-fiók létrehozásakor
Laborfiók létrehozásakor ez a beállítás látható az első képernyőn (**Alapismeretek** lap). 

![A beállítás engedélyezése a tesztkörnyezet létrehozásakor](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Ez a beállítás le van tiltva, ha a **Speciális** lapon a tesztkörnyezet-fiókhoz partner virtuális hálózatot választ.  

![Ha a társ virtuális hálózata engedélyezve van](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>A tesztkörnyezet-fiók létrehozása után
A tesztkörnyezet-fiók létrehozása után az alábbi lépésekkel engedélyezheti vagy tilthatja le ezt a beállítást: 

1. A **Laborfiók** lapon válassza a bal oldali menü **Lab-beállítások parancsát.**
2. Válassza a **Labor létrehozója, hogy válasszon labor helyét** beállítást, ha azt szeretné, hogy a labor létrehozója, hogy válasszon egy helyet a labor. Ha le van tiltva, a laborok automatikusan létre ugyanazon a helyen, ahol a laborfiók létezik. 
    
    Ez a mező le van tiltva, ha virtuális hálózatot választ a **Társ virtuális hálózati** mezőhöz. Ez azért van, mert a laborok a laborfiókban kell lennie ugyanabban a régióban, mint a laborfiók számára a társ virtuális hálózat erőforrásainak eléréséhez. 
1. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Labor beállításai](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>A virtuális hálózat és a hely kiválasztása nem engedélyezett
Ebben a forgatókönyvben még nem engedélyezte a **Labor létrehozója a labor helyének kiválasztását.** 

![Nincs laborhely](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Ezután a tesztkörnyezet alkotói (oktatók) nem látnak lehetőséget a tesztkörnyezet helyének kiválasztására. Látni fogják az óránkénti árat minden méretopcióhoz, amely elérhető számukra. Amikor létrehoznak egy tesztkörnyezetet, egy Azure-régióban jön létre, amely ugyanazon a helyen található, mint az Azure-régióban, ahol a laborfiók található. Ha például a laborfiók **az USA nyugati részén**található, akkor előfordulhat, hogy a labor az USA déli középső **részén** jön létre, de nem **a Kelet-Kanadában.** Nem garantálunk semmit a régióban, amit választunk, eltekintve attól, hogy a helyszínen van. Ha egy méret jelenleg korlátozott, akkor a tesztkörnyezet létrehozója megjelenik egy jelölőnégyzet, ahol láthatja a méreteket, amelyeket általában támogatunk, de jelenleg nem érhetők el. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>A virtuális hálózatban és a helyben a kijelölés nem engedélyezett
Ebben a forgatókönyvben a **labor létrehozója a labor helyének kiválasztásához** beállítás le van tiltva, mert kiválasztott egy társ virtuális hálózatot a laborfiókhoz. Ezután a laborkészítők ugyanazt a képernyőt fogják látni, mint az előző lehetőségnél. Mivel az összes virtuális gépnek ugyanabban az Azure-régióban kell lennie, mint a virtuális hálózatnak, a tesztkörnyezet ugyanabban az Azure-régióban jön létre, amelyben a virtuális hálózat található. Ha az adott régió egy méretre van korlátozva, a méret nem érhető el. 

## <a name="location-selection-is-enabled"></a>A hely kiválasztása engedélyezve van
Ha a **Labor létrehozója számára a laborhely kiválasztásának engedélyezése**lehetőséget választja, a laboralkotók (oktatók) egy lehetőséget látnak egy hely kiválasztására a tesztkörnyezet létrehozásakor. 

![Labor helyének választása](../media/allow-lab-creator-pick-lab-location/location-selection.png)

A laborkészítők láthatják az összes olyan hely ártartományát, ahol a méret van, és kiválaszthatják a helyet. A labor jön létre minden Olyan Azure-régióban, amely leképezi az adott helyre.

Ha egy hely korlátozott, akkor alapértelmezés szerint nem jelenik meg a listában. Bontsa ki a legördülő listát, és válassza **a Nem elérhető helyek megjelenítése ekkora méretben**lehetőséget. 

![Nem elérhető helyek megjelenítése](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Költségek
Korábban a díjszabás a tesztkörnyezethez kiválasztott virtuális gép méretén alapult. Most, az ár alapján kombinációja operációs rendszer (OS), méret, és helyét. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A labor hálózatának csatlakoztatása egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
- [Tesztkörnyezet egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)