---
title: A Microsoft Azure virtuális gépének előfeltételei | Azure Piactér
description: A virtuális gép ajánlatának az Azure Piactéren való közzétételéhez szükséges előfeltételek listája.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277616"
---
# <a name="virtual-machine-prerequisites"></a>A virtuális gépek előfeltételei

Ez a cikk felsorolja mind a műszaki és üzleti követelmények, amelyeknek meg kell felelnie, mielőtt közzétehet egy virtuális gép ajánlat az [Azure Marketplace-en.](https://azuremarketplace.microsoft.com/marketplace/)  Ha még nem tette meg, tekintse át a [Virtuálisgép-ajánlat közzétételi útmutatóját.](../../marketplace-virtual-machines.md)


## <a name="technical-requirements"></a>Technikai követelmények

A virtuális gép (VM) megoldásközzétételének technikai előfeltételei egyszerűek:

- Aktív Azure-fiókkal kell rendelkeznie. Ha nem rendelkezik ilyentel, regisztrálhat a [Microsoft Azure webhelyen.](https://azure.microsoft.com)  
- A Windows vagy a Linux virtuális gép fejlesztésének támogatására konfigurált környezetnek kell lennie.  További információt a kapcsolódó virtuális gép dokumentációs webhelyén talál:
    - [Dokumentáció Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentáció Windows rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Üzleti követelmények

Az üzleti követelmények magukban foglalják az eljárási, szerződéses és jogi kötelezettségeket: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Regisztrált Cloud Marketplace Kiadónak kell lennie.  Ha még nincs regisztrálva, kövesse a ["Legyen felhőalapú piactér-közzétevővé"](https://docs.microsoft.com/azure/marketplace/become-publisher)című cikklépéseit.

    > [!NOTE]
    > Ugyanazzal a Microsoft Developer Center regisztrációs fiókkal kell bejelentkeznie a [Cloud Partner Portal](https://cloudpartner.azure.com)szolgáltatásba.
    > Az Azure Marketplace-ajánlatokhoz csak egy Microsoft-fiókkal kell rendelkeznie. Nem lehet jellemző az egyes szolgáltatásokra vagy ajánlatokra.
    
- A vállalatnak (vagy leányvállalatának) az Azure Piactér által támogatott országból/régióból történő értékesítésben kell lennie.  Ezen országok/régiók aktuális listáját a [Microsoft Azure Piactér részvételi szabályzatai című témakörben tetszetős témakörben található.](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)
- A terméknek olyan módon kell licencelnie, amely kompatibilis az Azure Marketplace által támogatott számlázási modellekkel.  További információt a [Számlázási lehetőségek az Azure Piactéren című témakörben talál.](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace) 
- Ön felelős azért, hogy a technikai támogatást üzletileg ésszerű módon elérhetővé tegye az ügyfelek számára. Ez a támogatás lehet ingyenes, fizetett, vagy a közösségi megközelítések.
- Ön felelős a szoftver és a harmadik féltől származó szoftverfüggőségek licencelésért.
- Olyan tartalmat kell megadnia, amely megfelel az ajánlatnak az Azure Marketplace-en és az Azure Portalon való felsorolásfeltételeinek. <!-- TD: Meaning/links? -->
- El kell fogadnia a [Microsoft Azure Piactér részvételi szabályzatának](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) és kiadói szerződésének feltételeit.
- Be kell tartania a [Microsoft Azure webhelyhasználati feltételeit](https://azure.microsoft.com/support/legal/website-terms-of-use/), a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement)és a [Microsoft Azure certified programszerződését.](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)


## <a name="next-steps"></a>További lépések

Miután teljesítette ezeket az előfeltételeket, [létrehozhatja a virtuális gép ajánlatát.](./cpp-create-offer.md)
