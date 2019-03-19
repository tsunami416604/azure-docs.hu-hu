---
title: A Microsoft Azure virtuális gép előfeltételei |} A Microsoft Docs
description: Egy Virtuálisgép-ajánlat közzététele az Azure piactéren szükséges előfeltételek listája.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010845"
---
# <a name="virtual-machine-prerequisites"></a>A virtuális gép előfeltételei

Ez a cikk felsorolja mindkét a műszaki és üzleti követelményeknek, amelyek előtt meg kell felelnie egy Virtuálisgép-ajánlat, közzéteheti a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/).  Ha még nem tette meg, tekintse át a [virtuális gép ajánlat közzétételi útmutató](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technikai követelmények

A virtuális gép (VM) a megoldás közzététele műszaki előfeltételeinek magától értetődnek:

- Aktív Azure-fiókkal kell rendelkeznie. Ha nem rendelkezik egy, iratkozzon fel a [a Microsoft Azure site](https://azure.microsoft.com).  
- Rendelkeznie kell konfigurálni, hogy támogassa a Windows vagy Linux rendszerű virtuális gépek fejlesztési környezet.  További információkért tekintse meg a társított virtuális gép dokumentációs webhelyen:
    - [Linux rendszerű virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows virtuális gépek dokumentációja](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények eljárási szerződéses és jogi kötelezettségek tartalmazza: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Regisztrált Felhőbeli piactér kiadói kell lennie.  Ha nincs még regisztrálva, kövesse a cikkben található lépéseket [válnak a Felhőbeli piactér kiadói](../../become-publisher.md).

    > [!NOTE]
    > Jelentkezzen be a Microsoft Developer Center regisztrációs ugyanazt a fiókot kell használnia a [Cloud Partner Portalon](https://cloudpartner.azure.com).
    > Az Azure piactér-i ajánlatainak közzétételéhez egyetlen Microsoft-fiókkal kell rendelkeznie. Nem lehet adott egyes szolgáltatásokat vagy ajánlatokat.
    
- A vállalatnak (vagy az leányvállalatának) kell működnie egy értékesítési-a-ország az Azure piactér által támogatott.  Ezekben az országokban aktuális listáját lásd: [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- A termék úgy, hogy az Azure piactér által támogatott számlázási modellek kompatibilis licenccel kell rendelkezniük.  További információkért lásd: [számlázási lehetőségek az Azure Marketplace-en](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Ön felelős az ügyfelek számára elérhetővé tétele az műszaki támogatást, olyan üzletileg ésszerű módon. Ez a támogatás ingyenes, fizetős, vagy közösségi megközelítések keresztül lehet.
- Ön felelős a szoftver- és külső függőségek licencelése.
- Meg kell adnia a tartalmat, amely megfelel az Azure Marketplace-en és az Azure Portalon való megjelentetéséhez ajánlat feltételeit. <!-- TD: Meaning/links? -->
- El kell fogadnia a használati a [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) és Kiadókra vonatkozó szerződése.
- Be kell tartania a [a Microsoft Azure webhelyhasználati feltételek](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement), és [a Microsoft Azure Certified Program szerződését](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>További lépések

Miután az előfeltételek teljesülnek, [a Virtuálisgép-ajánlat létrehozása](./cpp-create-offer.md).
