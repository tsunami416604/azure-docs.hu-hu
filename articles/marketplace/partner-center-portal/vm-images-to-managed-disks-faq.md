---
title: Virtuálisgép-lemezképeket helyezünk át felügyelt lemezes tárhelyre az Azure Piactéren
description: A gyorsabb, megbízhatóbb tárolás és az új piactéri funkciók és -képességek támogatása érdekében a piactéri virtuálisgép-lemezképeket a felügyelt lemezes tárolásba mozgatjuk.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 17b418ab82cc88b4368efe247b06c0c8bbb27453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285112"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Virtuálisgép-(VM) lemezképeket helyezünk át az Azure Piactérről a felügyelt lemezes tárolásra

A gyorsabb, megbízhatóbb tárolás és az új piactéri funkciók és -képességek támogatása érdekében a piactéri virtuálisgép-lemezképeket a felügyelt lemezes tárolásba mozgatjuk.

2020. január 2-tól kezdődően a virtuálisgép-lemezképeket fázisokban áthelyezzük a felügyelt lemezes tárolásba. Az első fázisban csak az okat az előző 90 napban új központi telepítéseket vagy virtuális gépeket futtató lemezképeket helyezünk át. Mielőtt áthelyeznénk a képeket, e-mailt küldünk, amelyben tudatjuk a kiadóval, hogy mely képek kerülnek áthelyezésre, és mikor kerülnek áthelyezésre.

A megjelenítőknek és a fogyasztóknak nem kell semmilyen műveletet végrehajtaniuk, és a felhasználókat ez nem érinti. A piactéri ajánlatok továbbra is elérhető, és az ügyfelek továbbra is képes telepíteni felügyelt virtuális gépek ezekből a lemezképekből, az áthelyezés során és után.

Ha bármilyen kérdése van, kérjük, [lépjen kapcsolatba velünk](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Gyakori kérdések

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>A virtuális gép-lemezképeim felhasználói kimaradást tapasztalnak?

A virtuális gép rendszerképek felhasználói nem tapasztal kimaradást. 

Az első fázisban csak azokat a virtuális gépi lemezképeket, amelyek nem rendelkeznek futó virtuális gépek. Mivel ezeka képek nem léteznek felhasználók, nem lesz semmilyen hatása. A következő fázisokban is, nem lesz hatással a felhasználók számára.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Mennyi ideig tart a folyamat befejezése?

Az áttelepítés akár 24 órát is igénybe vehet.

### <a name="do-i-need-to-take-any-action"></a>Kell-e valamit tennem?

Nem. A megjelenítőknek vagy a fogyasztóknak nem kell semmilyen műveletet végrehajtaniuk.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Frissítenem kell a rendszert, hogy a Felügyelt lemez tárolóba való áthelyezésük után más módon hívjam meg a Felhőportál API-jait?

Nem. A meglévő API-hívások továbbra is működni fognak.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Az összes virtuálisgép-lemezképáthelyezése egyszerre a felügyelt lemezre történik?

Az összes virtuálisgép-lemezképet ugyanazon a napon mozgatjuk. Értesítjük, amint elszállították őket.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kérhetem, hogy ütemezze a virtuális gép képeit egy későbbi időpontra?

Javasoljuk, hogy a képeket az ütemezett időpontra mozgassa. Azonban, ha aggályai vannak, kérjük, forduljon hozzánk, hogy átütemezze a költözést.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Közzétehetek frissítéseket a virtuálisgép-lemezképekhez az áthelyezés során?

A virtuális gép rendszerképeit nem lehet az áthelyezés során.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>A közzétételi folyamat megváltozik, miután a virtuális géplemez-lemezt áthelyezték a Felügyelt lemezre?

Nem, a közzétételi folyamat ugyanaz marad. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Áthelyezheti a közzétevő az ajánlatait a Felügyelt lemezre?

Nem, a közzétevők nem helyezhetik át ajánlataikat a Felügyelt lemezre. Várniuk kell, és a képeik automatikusan átkerülnek. Mielőtt bármilyen módosítást eszközhöz érnénk, értesítéseket küldünk a közzétevőnek.
