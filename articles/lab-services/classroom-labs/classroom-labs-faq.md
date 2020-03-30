---
title: Tantermi laborok az Azure Lab Services ben – gyakori kérdések | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Lab Services tantermi laborjaival kapcsolatos gyakori kérdésekre.This article provides answers to frequentfrequent) about classroom labs in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443499"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Tantermi laborok az Azure Lab Servicesben – gyakori kérdések (GYAKORI KÉRDÉSEK)
Válaszok az Azure Lab Services tantermi laborokkal kapcsolatos leggyakoribb kérdéseire. 

## <a name="quotas"></a>Kvóták

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>A kvóta felhasználónként, hetente vagy a labor teljes időtartamára vonatkozik? 
A tesztkörnyezethez beállított kvóta minden tanulóra vonatkozik a tesztteljes időtartamára. És a [virtuális gépek ütemezett futási ideje](how-to-create-schedules.md) nem számít bele a felhasználónak kiosztott kvótába. A kvóta a tanuló által a virtuális gépekre fordított ütemezési órákon kívül töltött időre vonatkozik.  A kvótákról a [Kvóták beállítása felhasználókszámára című](how-to-configure-student-usage.md#set-quotas-for-users)témakörben talál további információt.

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Ha a professzor bekapcsolja a diák virtuális gép, ez befolyásolja a hallgatói kvóta? 
Nem. Nem, nem az. Amikor a professzor bekapcsolja a tanuló virtuális gép, ez nem befolyásolja a kvótát kiosztott a hallgató. 

## <a name="schedules"></a>Ütemezések

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>A laborban lévő összes virtuális gép automatikusan elindul, ha be van állítva egy ütemezés? 
Nem. Nem minden virtuális gép. Csak a virtuális gépek, amelyek hozzá vannak rendelve a felhasználók ütemezés szerint. A felhasználóhoz nem rendelt virtuális gépek nem indulnak el automatikusan. Ez szándékosan van. 

## <a name="lab-accounts"></a>Tesztkörnyezetfiókok

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Miért nem tudok labort létrehozni a címtartomány hiánya miatt? 
Az osztálytermi laborok laborvirtuális gépeket hozhat létre egy ip-címtartományon belül, amelyet a laborfiók létrehozásakor az Azure Portalon adhat meg. Ha meg van adva egy címtartomány, minden egyes tesztkörnyezet, amely a tesztkörnyezet virtuális gépei 512 IP-címének kivan adva után jön létre. A tesztkörnyezet-fiók címtartományának elég nagynak kell lennie ahhoz, hogy a laborfiók alatt létrehozni kívánt összes labort el tudja helyezni. 

Ha például /19 - 10.0.0.0/19 blokkja van, ez a címtartomány 8192 IP-címet és 16 labst (8192/512 = 16 labs) fogad. Ebben az esetben a labor létrehozása sikertelen a 17.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Milyen porttartományokat nyissak meg a szervezet tűzfalbeállításán, hogy RDP/SSH-n keresztül csatlakozzak a Lab virtuális gépekhez?

A portok: 49152-65535. Az osztálytermi laborok egy terheléselosztó mögött ülnek. Minden tesztkörnyezet egyetlen nyilvános IP-címmel rendelkezik, és a laborban minden virtuális gép egyedi porttal rendelkezik. 

Az Azure Portalon az egyes virtuális gépek privát IP-címe is látható a labor kezdőlapjának **virtuálisgép-készletlapján.** Ha újra közzétesz egy tesztkörnyezetet, a tesztkörnyezet nyilvános IP-címe nem változik, de a tesztkörnyezetben lévő egyes virtuális gépek privát IP-címe és portszáma változhat. További információ a következő cikkben: [Az Azure Lab Services tűzfalbeállításai.](how-to-configure-firewall-settings.md)

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Milyen nyilvános IP-címtartományt nyissak meg a szervezet tűzfalbeállításaiban, hogy RDP/SSH-n keresztül csatlakozzak a Lab virtuális gépekhez?
Tekintse meg [az Azure IP-tartományok és szolgáltatáscímkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519), amely a nyilvános IP-címtartomány adatközpontok az Azure-ban. Megnyithatja azoknak a régióknak az IP-címeit, ahol a tesztkörnyezet-fiókok találhatók.

## <a name="virtual-machine-images"></a>Virtuális gép képei

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Laborkészítőként miért nem engedélyezhetek további képbeállításokat a virtuálisgép-képek legördülő menüjében egy új tesztkörnyezet létrehozásakor?

Amikor egy rendszergazda hozzáadja Önt tesztkörnyezet-létrehozóként egy tesztkörnyezet-fiókhoz, megkapja a laborok létrehozására vonatkozó engedélyeket. De nem rendelkezik az engedélyekkel a tesztkörnyezet-fiókon belüli beállítások szerkesztéséhez, beleértve az engedélyezett virtuálisgép-lemezképek listáját is. További képek engedélyezéséhez forduljon a laborfiók rendszergazdájához, vagy kérje meg a rendszergazdát, hogy vegye fel Önt közreműködői szerepkörként a tesztkörnyezet-fiókba. A közreműködői szerepkör megadja az engedélyeket a virtuális gép lemezképlistájának szerkesztéséhez a laborfiókban.

## <a name="users"></a>Felhasználók

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Hány felhasználó lehet egy tantermi laborban?
Egy tantermi laborban legfeljebb 400 felhasználót adhat hozzá. 

## <a name="blog-post"></a>Blogbejegyzést
Iratkozzon fel az [Azure Lab Services blogjára.](https://azure.microsoft.com/blog/tag/azure-lab-services/)

## <a name="update-notifications"></a>Értesítések frissítése
Iratkozzon fel a [Lab Services frissítéseire,](https://azure.microsoft.com/updates/?product=lab-services) hogy értesüljön a Lab Services új funkcióiról.

## <a name="general"></a>Általános kérdések
### <a name="what-if-my-question-isnt-answered-here"></a>Mi van, ha a kérdésemre nem kap választ?
Ha kérdése nem szerepel itt, tudassa velünk, hogy segíthessünk megtalálni a választ.

- Tegye fel kérdését a GYIK végén. 
- Ha szélesebb közönséget szeretne elérni, tegyen fel egy kérdést az [Azure Lab Services – Stack Overflow fórumon.](https://stackoverflow.com/questions/tagged/azure-lab-services) 
- A szolgáltatáskérések esetén küldje el kéréseit és ötleteit az [Azure Lab Services – User Voice szolgáltatásba.](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)

