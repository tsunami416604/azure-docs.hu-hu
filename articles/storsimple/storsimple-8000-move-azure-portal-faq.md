---
title: Helyezze át a klasszikus Azure portállal kapcsolatos gyakori kérdések |} Microsoft Docs
description: Helyezze át a StorSimple eszközökhöz a klasszikus Azure portálra gyakran feltett kérdésekre adott válaszok biztosít.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785179"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Helyezze át a StorSimple Device Manager szolgáltatás a klasszikus Azure-portálon: gyakori kérdések (GYIK)

## <a name="overview"></a>Áttekintés

Kérdések és válaszok előfordulhat, hogy a StorSimple Device Manager szolgáltatás fut a klasszikus Azure portálon az Azure-portálhoz áthelyezésekor a következők:

Kérdések és válaszok a következő kategóriákba vannak rendezve:

* Helyezze át a StorSimple eszköz kezelő szolgáltatás
* Helyezze át a storage-fiókok
* Azure Resource Manager használatával alapú parancsmagok
* StorSimple adatokat kezelő szolgáltatással
* Egyéb rendelkezések

## <a name="moving-storsimple-device-manager-service"></a>Helyezze át a StorSimple eszköz kezelő szolgáltatás

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Egyszer kell áthelyezett Azure-portálon továbbra is létrehozhatók a StorSimple Manager szolgáltatás a klasszikus portálon?

Nem. Miután áttelepítette a StorSimple Manager szolgáltatás az Azure portálra, a klasszikus portálon nem hozható létre a új szolgáltatás. Emellett [a klasszikus portálon nem lesz elérhető a 2018 január 8](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>A klasszikus portálon futtató több StorSimple kezelők van. Kiválaszthatja a munkaterületek áthelyezése az Azure-portálon?

Nem. Nem választható, mely StorSimple kezelők áthelyezése az Azure-portálon. Az előfizetéshez tartozó összes StorSimple kezelőt kerülnek.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Az áttelepítés a szolgáltatást az új Azure-portálon kezdeményezett I. Érdemes a StorSimple Manager is törli a klasszikus portálon? 

Nem. Ne próbálja meg törölni a szolgáltatást a klasszikus portálon áthelyezett. Várja meg az áttelepítés befejezéséhez, amennyiben az a StorSimple-kezelők az új portál került át, akkor nem törölnie kell szolgáltatások a klasszikus portálon. Végül a klasszikus portálon elavult.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Átnevezheti a StorSimple eszköz Manager szolgáltatás az Azure-portálon?

Nem. A szolgáltatás neve nem változtatható meg, miután a szolgáltatás létrehozása az Azure portálon. A kívánt viselkedést eredményező beállítást egyéb entitások, például az eszközök, a kötetek, a kötettárolók és a biztonsági mentési házirendek is igaz.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Létrehozhatók a 8010-es/8020-as modellt StorSimple felhő készülékek Azure Resource Manager üzembe helyezési modellben?

Igen. Létrehozhat új 8010-es/8020-as modellt StorSimple felhő készülékek az Azure Resource Manager üzembe helyezési modellben. Az alapul szolgáló virtuális gépeknek a virtuális e felhő készülékek megtalálhatók a Resource Manager üzembe helyezési modellben.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Amikor azt át az előfizetések az Azure portálra, a StorSimple felhő készülékek az alapul szolgáló virtuális gépek is telepítse át az Azure Resource Manager üzembe helyezési modellel?

A StorSimple szolgáltatás áthelyezése nem függ a felügyeleti virtuális gépek a StorSimple felhő készülékek. Teljes kezelheti a virtuális gépek StorSimple felhő készülékek a klasszikus és az Azure-portálon még ma.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Kezelhetem 8010-es/8020-as modellt StorSimple meglévő klasszikus felhő készüléknek Azure-portálról?

Igen. A meglévő 8010-es/8020-as modellt felhő berendezések társított virtuális gépeket az Azure-portálon kezelheti.

Ha létrehozott StorSimple felhő készülékek modell 8010-es/8020-as modellt frissítés 3.0 fut, és újabb verziók esetén, nem érintettek a szolgáltatás az új Azure-portálra. Teljes körűen kezelhetik a felhő készülékek probléma nélkül kell lennie. 

Ha a klasszikus portálon előtt frissítés 3.0 verziót futtató felhő készülékek, majd csak korlátozott számú funkció érhető el. További információkért látogasson el a [3. frissítés előtti verziót futtató eszközök nem támogatott műveletek listájának](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

A felhőalapú berendezések nem frissíthetők. A szoftver legújabb verziójának segítségével hozzon létre egy új felhőalapú készülék, valamint a meglévő kötettárolók létrehozott új felhő készülékre majd feladatátvétel. További információkért látogasson el [feladatok átadása a felhő készülék](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Eszközöm a StorSimple 8000 series Update 2.0 fut. Új Azure-portálon a szolgáltatás szeretnék áttelepíteni. Az eszköz sikeresen csatlakozott, de úgy tűnik, hogy nem képes vagyok teljes körű felügyeletéhez az eszköz. Hogyan oldja meg ezt a viselkedést?

Az új Azure-portálon akkor támogatott, csak a StorSimple eszközök futtatása frissítés 3.0-s és újabb rendszer. Ha az eszköz futtatása frissítés 2.0, csak korlátozott számú funkció érhető el ehhez az eszközhöz. További információkért látogasson el a [3. frissítés előtti verziót futtató eszközök nem támogatott műveletek listájának](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Teljes mértékben felügyelhesse az eszközt, telepítse a legújabb frissítést az eszközön. További információkért látogasson el [telepítése frissítés 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>A StorSimple Manager szolgáltatásban csak az Azure-portálon áthelyezett. Az eszköz néhány riasztásait hivatkozva. Ez a viselkedés kapcsolódik az áthelyezés?

Nem. Az áthelyezés saját magát nem hibák vagy figyelmeztetések eredményez. Kövesse a riasztás megoldásához a riasztásokat.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>A StorSimple 5000/7000-es adatsorozat eszköz használom. Ezek is támogatottak az Azure-portálon?

Nem. A StorSimple 5000/7000-es adatsorozat eszközöket nem támogatja az Azure portálon.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>I vagyok tervezési adatainak áttelepítésére StorSimple 5000/7000-es adatsorozat eszközt a StorSimple 8000 series eszköz. Mi a szolgáltatás áthelyezése az Azure-portálhoz a hatása az adatáttelepítés? 

Áttelepítheti az adatokat a StorSimple 5000/7000-es adatsorozat eszközről a StorSimple 8000 series futtató eszközre, az Azure-portálon. Az adatáttelepítés 5000/7000-es sorozatból 8000 Series helyezhet kell [jelentkezni egy támogatási jegy Microsoft Support](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Előfizetések, storage-fiókok, erőforráscsoportok áthelyezése

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Áthelyezhető StorSimple Device Manager egy előfizetésből más előfizetések az Azure-portálon?

Nem. Egy előfizetés másik StorSimple Device Manager szolgáltatás áthelyezése nem támogatott. Az alábbi lépéseket álló kézi folyamat hajthatja végre:

* Telepítse át a StorSimple eszköz adatokat.
* Hajtsa végre az eszköz gyári beállításainak visszaállítását, ez a művelet törli az eszköz helyi adatokat.
* Az eszközt regisztrálni kell az új előfizetés, a StorSimple Device Manager szolgáltatás.
* Az adatok áttelepítését vissza az eszközt.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Telepítse át a tárfiók Azure Resource Manager üzembe helyezési modellben kell?

Nem. A klasszikus tárfiókokba teljes mértékben felügyelhető az Azure portálról. A StorSimple szolgáltatás az Azure-portálon helyezi át, a tárfiók továbbra is működnek, mint korábban.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Mi történik a tárfiókot, a szolgáltatás az Azure-portálon történő áttelepítése után?

Az áthelyezés a szolgáltatás nem kapcsolódik a storage-fiók kezelése. Klasszikus és Azure Resource Manager storage-fiókok teljes mértékben felügyelhető az Azure portálon. A szolgáltatás az Azure-portálon helyezi át, ha az eszköz továbbra is futtassa ezt a tárfiókot, és nincs hatással az adatok kell lennie.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Telepíthetek át StorSimple Device Manager az egyik erőforráscsoportból a másikba?

Nem. A StorSimple Device Manager létrehozott egy erőforráscsoportot az egy másik erőforráscsoportban nem helyezhető át.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Azure Resource Manager használatával alapú parancsmagok

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Az új Azure-portálon történő áthelyezését. Most már Azure klasszikus telepítési modell PowerShell-parancsmagok alapján a parancsfájlok nem működnek? Mit kell tennem?

A meglévő Azure klasszikus telepítési modell PowerShell-parancsmagok nem támogatottak az Azure portálon. Frissítse a parancsfájlok az Azure Resource Manageren keresztül eszközei kezeléséhez. A parancsfájlok frissítésével kapcsolatos további információkért látogasson el [az új Azure-portálon minták](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>I ebben az esetben helyezi át az Azure-portálon, és a szolgáltatásadat-titkosítási kulcs váltása szükséges. Ha az ezt a beállítást, az Azure-portálon?

A szolgáltatásadat-titkosítási kulcs váltása lehetőség nincs Azure-portálon. Az Azure-portálon helyettesítő módjáról további információkért látogasson el [módosítása a szolgáltatásadat-titkosítási kulcs](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Használom Windows PowerShell StorSimple parancsmagok a StorSimple eszköz ilyen kivonat műveletek végrehajtásához egy támogatási csomag. Ezek a parancsmagok az új Azure-portálon való áthelyezésekor érintett?

Nem. Az új Azure-portálra szolgáltatással kell nincs hatással a Windows PowerShell a helyszíni StorSimple eszköz (pedig nem érinti az áthelyezés) társított StorSimple-parancsmagokkal. Továbbra is ezen parancsmagok segítségével hozzon létre egy támogatási csomag probléma nélkül is az Azure-portálon. Csak az Azure klasszikus telepítési modell PowerShell-parancsmagok is hatással van az áthelyezés.

## <a name="moving-storsimple-data-manager-service"></a>StorSimple adatkezelő szolgáltatás áthelyezése

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>StorSimple adatkezelő szolgáltatást használom a klasszikus Azure portálon. Hogyan kell az áthelyezés folytatja?

Ha StorSimple adatkezelő szolgáltatást használja, akkor automatikusan helyezte az Azure-portálon.

## <a name="miscellaneous"></a>Egyéb rendelkezések

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>StorSimple Snapshot Manager használom a 8000 sorozat eszközhöz. Van bármilyen hatása a StorSimple Snapshot Manager az Azure-portálhoz áthelyezésekor?

Nem. Nincs hatással a StorSimple Snapshot Manager helyezheti a szolgáltatást az Azure-portálhoz. Az eszközön, és a StorSimple Snapshot Manager továbbra is működnek, mint korábban.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>A StorSimple eszköz, kötettárolók vagy kötetek is átnevezése?

Nem. Eszközök, a kötetek, a kötettárolók vagy a biztonsági mentési házirendek az Azure portálon nem nevezhető át.

## <a name="next-steps"></a>További lépések

További információ [műveletek előtt frissítés 5.0 verziót futtató eszközökön támogatott](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



