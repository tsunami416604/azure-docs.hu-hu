---
title: fájl belefoglalása
description: fájl belefoglalása
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608209"
---
### <a name="preview"></a>Hogyan részt a nyilvános előzetes verziója?

Kell üzembe helyezni a nyilvános előzetes verzióban való részvételhez mindenkor. Szereplő lépések segítségével [Ez a cikk](../articles/bastion/bastion-create-host-portal.md) egy új megerősített Azure-erőforrás létrehozásához. Jelenleg Ha fér hozzá, és ezzel a szolgáltatással, akkor kell használnia a [Azure portal – előzetes verzió](https://aka.ms/BastionHost) a normál Azure portál helyett.

### <a name="regions"></a>Mely régiókban érhetők el az előzetes verzióban?

Telepítheti és használhatja a megerősített ezek egyikét sem az erőforrás előzetesverzió-régióiban keresztül a [Azure Portalon – előzetes verzió hivatkozás](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>A megerősített erőforrás nem található az Azure Portalon. Mit tegyek?

Győződjön meg arról, hogy használja a [Azure Portalon – előzetes verzió hivatkozás](https://aka.ms/BastionHost), nem a normál Azure Portalon.

### <a name="publicip"></a>Kell egy nyilvános IP-címet a virtuális gépemen?

NEM kell egy nyilvános IP-címet az Azure virtuális gépen, amely az Azure megerősített szolgáltatással csatlakozik. A megerősített szolgáltatás megnyílik az RDP/SSH munkamenet és kapcsolat a virtuális gép magánhálózati IP-címét a virtuális gépet, a virtuális hálózaton belüli keresztül.

### <a name="rdpssh"></a>Szükségem van egy RDP vagy SSH-ügyfél?

Nincs szükség RDP- vagy SSH-ügyfélre ahhoz, hogy RDP/SSH-csatlakozást építsen ki az Azure-beli virtuális gépekhez az Azure Portalon. Használja a [Azure Portalon – előzetes verzió hivatkozás](https://aka.ms/BastionHost) előzetes félóránként rögzíti a portál elérésére. Ez lehetővé teszi, hogy RDP/SSH-csatlakozást hozzon létre a virtuális gépeihez a böngészőben.

### <a name="agent"></a>Az Azure-beli virtuális gépen futó ügynök van szükségem?

Sem a böngészőben, sem az Azure-beli virtuális gépen nem kell telepítenie ügynököt vagy más szoftvereket. A Bastion szolgáltatás ügynök nélküli, és nem szükséges hozzá semmilyen más szoftver az RDP/SSH-csatlakozáshoz.

### <a name="browsers"></a>Mely böngészőket támogatja?

A nyilvános előzetes során használja a Microsoft Edge böngésző vagy a Google Chrome Windows. Apple Mac gépek esetén használja a Google Chrome böngészőt. A Microsoft Edge Chromium is támogatva van mind Windows, mind Mac rendszereken.

### <a name="roles"></a>Azok a virtuális gép eléréséhez szükséges szerepköröket?

Annak érdekében, hogy a kapcsolat létrehozása a következő szerepkörök szükségesek:

* A virtuális gépen olvasói szerepköre
* A hálózati adapteren a virtuális gép magánhálózati IP-Címmel rendelkező olvasói szerepköre
* Az Azure megerősített erőforráson olvasói szerepköre

### <a name="previewbill"></a>Díjszabás – számítják fel az előzetes verzióra való részvételhez?

Csak díjköteles részben a nyilvános előzetes verzióban. Azonban nem létezik az üzemelő példány csatlakozik, nem biztosítunk szolgáltatói szerződést. További tájékoztatás a [díjszabási lapon](https://aka.ms/BastionHostPricing) olvasható.

### <a name="previewbill"></a>Miért kapok "a kapcsolat időkorlátja lejárt" hiba jelenik meg a megerősített munkamenet indítása előtt?

A munkamenet csak az Azure Portalról kell kezdeményezni. Jelentkezzen be az Azure Portalon, és kezdje újra a munkamenetet. Ha az URL-címet közvetlenül a egy másik böngésző-munkamenetet, vagy a lapot, ez a hiba várható. Ez segít, győződjön meg arról, hogy a munkamenet biztonságát és, hogy a munkamenet a csak az Azure Portalon keresztül érhetők el.
