---
title: A Microsoft Azure virtuális gépekre vonatkozó előfeltételei | Azure piactér
description: A virtuálisgép-ajánlat Azure piactéren való közzétételéhez szükséges előfeltételek listája.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824418"
---
# <a name="virtual-machine-prerequisites"></a>Virtuális gépek előfeltételei

Ez a cikk a virtuálisgép-ajánlat [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/)való közzétételéhez szükséges technikai és üzleti követelményeket sorolja fel.  Ha még nem tette meg, tekintse át a [virtuális gép ajánlatának közzétételi útmutatóját](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technikai követelmények

A virtuálisgép-(VM-) megoldások közzétételének technikai előfeltételei egyszerűek:

- Aktív Azure-fiókkal kell rendelkeznie. Ha nem rendelkezik ilyennel, regisztrálhat a [Microsoft Azure webhelyre](https://azure.microsoft.com).  
- A Windows vagy Linux rendszerű virtuális gépek fejlesztésének támogatásához konfigurálnia kell egy környezetet.  További információkért lásd a társított VM-dokumentációs webhelyet:
    - [Linux rendszerű virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows rendszerű virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények magukban foglalják az eljárási, szerződéses és jogi kötelezettségeket: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- A felhőalapú piactéren regisztrált közzétevőnek kell lennie.  Ha még nincs regisztrálva, kövesse a cikk lépéseit a [Cloud Marketplace Publisherben](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > A [Cloud Partner Portal](https://cloudpartner.azure.com)való bejelentkezéshez ugyanazt a Microsoft Developer Center regisztrációs fiókot kell használnia.
    > Az Azure Marketplace-ajánlatokhoz csak egy Microsoft-fiók tartozhat. Nem lehet egyedi szolgáltatásokra vagy ajánlatokra jellemző.
    
- A vállalatnak (vagy annak leányvállalatának) az Azure piactér által támogatott értékesítési ország/régió területén kell lennie.  Ezen országok/régiók aktuális listája: [Microsoft Azure Marketplace részvételi szabályzatok](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- A terméknek olyan módon kell lennie, amely kompatibilis az Azure piactér által támogatott számlázási modellekkel.  További információ: [Számlázási lehetőségek az Azure Marketplace-](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace)en. 
- Ön felelős azért, hogy üzleti szempontból ésszerű módon biztosítson technikai támogatást az ügyfeleknek. Ez a támogatás ingyenes, fizetős vagy közösségi megközelítésekkel is elvégezhető.
- Ön felelős a szoftver és a harmadik féltől származó szoftverek függőségeinek licenceléséhez.
- Olyan tartalmat kell megadnia, amely megfelel az ajánlat feltételeinek, hogy megjelenjenek az Azure Marketplace-en és a Azure Portal. <!-- TD: Meaning/links? -->
- El kell fogadnia a [Microsoft Azure Marketplace részvételi szabályzatok](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) és a kiadói szerződés feltételeit.
- Be kell tartania a [Microsoft Azure webhely használati feltételeit](https://azure.microsoft.com/support/legal/website-terms-of-use/), a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement)és a [Microsoft Azure Certified program szerződését](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>További lépések

Miután teljesítette ezeket az előfeltételeket, [létrehozhat egy](./cpp-create-offer.md)virtuálisgép-ajánlatot.
