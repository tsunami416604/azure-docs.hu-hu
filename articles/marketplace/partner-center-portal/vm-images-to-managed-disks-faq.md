---
title: Virtuális gépek (VM) lemezképeit áthelyezjük a felügyelt lemezes tárolóba az Azure Marketplace-en
description: Az új Piactéri funkciók és képességek gyorsabb, megbízhatóbb tárolásának és támogatásának biztosításához a piactéren futó virtuális gépek lemezképeit a felügyelt lemezes tárhelyre helyezi át.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: iqshahmicrosoft
ms.author: iqshah
ms.openlocfilehash: 814a4012ace95e84e75bcb4071c549822b2b0633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182428"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Virtuális gépek (VM) lemezképeit áthelyezjük az Azure Marketplace-en a felügyelt lemezes tárolásra

Az új Piactéri funkciók és képességek gyorsabb, megbízhatóbb tárolásának és támogatásának biztosításához a piactéren futó virtuális gépek lemezképeit a felügyelt lemezes tárhelyre helyezi át.

2020. január 2-án kezdődően a virtuálisgép-lemezképeket áthelyezjük a felügyelt lemezes tárolásba fázisokban. Az első fázisban a rendszerképeket csak új telepítések és a virtuális gépek az előző 90 napban való futtatása után fogjuk áthelyezni. A lemezképek áthelyezését megelőzően egy e-mailt küldünk, amely lehetővé teszi, hogy a közzétevő tudja, hogy mely képek lesznek áthelyezve.

A közzétevőknek vagy a fogyasztóknak semmilyen műveletet nem kell elvégezniük, és a felhasználók nem lesznek érintettek. A Piactéri ajánlatok elérhetők maradnak, és az ügyfelek továbbra is telepíthetik a felügyelt virtuális gépeket ezekről a rendszerképekről, az áthelyezés során és azt követően.

Ha bármilyen kérdése van, vegye [fel velünk a kapcsolatot](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Gyakori kérdések

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>A VM-lemezképek felhasználói áramszünetet tapasztalnak?

A virtuálisgép-lemezképek felhasználói nem fognak kiesést tapasztalni. 

Az első fázisban csak olyan virtuálisgép-lemezképeket helyezünk át, amelyek nem rendelkeznek futó virtuális gépekkel. Mivel ezeknek a lemezképeknek nincsenek felhasználói, nem lesz hatása. A következő fázisok esetében nem lesz hatással a felhasználókra.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Mennyi ideig tart a folyamat befejezése?

Az áttelepítés elvégzése akár 24 óráig is eltarthat.

### <a name="do-i-need-to-take-any-action"></a>Bármilyen műveletet végre kell hajtani?

Nem. A közzétevőknek vagy a fogyasztóknak semmilyen műveletet nem kell elvégezniük.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Kell-e frissíteni a rendszerem a Cloud Portal API-k más módon történő meghívásához, miután áthelyezték őket a felügyelt lemezes tárolóba?

Nem. A meglévő API-hívások továbbra is működni fognak.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>A virtuálisgép-lemezképek egy időben lettek áthelyezve a felügyelt lemezre?

Az összes virtuálisgép-lemezképet ugyanarra a napra helyezzük át. A rendszer értesítést küld, amint áthelyezték őket.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kérhetem a VM-lemezképek áthelyezését egy későbbi időpontra?

Javasoljuk, hogy a rendszerképeket az ütemezett dátumon helyezze át. Ha azonban probléma merül fel, lépjen kapcsolatba velünk, hogy átütemezze az áthelyezést.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Közzétehetem a virtuálisgép-lemezképek frissítéseit az áthelyezés során?

A virtuálisgép-lemezképek frissítései nem hozhatók létre az áthelyezés során.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>A közzétételi folyamat megváltozik a virtuális gép lemezképének felügyelt lemezre való áthelyezése után?

Nem, a közzétételi folyamat változatlan marad. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>A közzétevő áthelyezheti az ajánlatait a felügyelt lemezre?

Nem, a közzétevők nem helyezhetik át az ajánlatokat a felügyelt lemezre. Meg kell várniuk, és a rendszer automatikusan áthelyezi a lemezképeket. A módosítások elvégzése előtt értesítést küldünk a közzétevőnek.
