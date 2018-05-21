---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 15cbfb9babe38ba6acaf4312735ab839af3f2d99
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>A klasszikusból Azure Resource Manager-alapú környezetbe való migrálásra vonatkozó gyakori kérdések

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Érinti ez a migrálási terv az Azure virtuális gépeken futó meglévő szolgáltatásaimat és alkalmazásaimat? 

Nem. A virtuális gépek (klasszikus modell) általánosan rendelkezésre álló, teljes mértékben támogatott szolgáltatások. Továbbra is használhatja ezeket az erőforrásokat, és akár ki is terjesztheti működését a Microsoft Azure-ban.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Mi történik a virtuális gépeimmel, ha nem tervezek migrálni a közeljövőben? 

A meglévő klasszikus API-kat és erőforrásmodellt nem tervezzük kivezetni. Szeretnénk könnyebbé tenni a migrálást, figyelembe véve a Resource Manager-alapú üzemi modellben elérhető fejlett szolgáltatásokat. Határozottan javasoljuk, hogy tekintse át a Resource Manager alatt az IaaS részeként elérhető [fejlesztett szolgáltatásokat](../articles/azure-resource-manager/resource-manager-deployment-model.md).

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Milyen hatással lesz a migrálási terv a meglévő eszközállományomra? 

Az eszközállomány a Resource Manager-alapú üzemi modellre való frissítése az egyik legjelentősebb változás, amellyel számolnia kell a migrálási tervben.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Milyen hosszú lesz a felügyeleti sík leállása? 

Ez a migrált erőforrások számától függ. A kisebb környezetek esetében (néhány tucat virtuális gépig) a teljes migrálás kevesebb mint egy órát tart majd. Nagyobb méretű környezetek esetében (virtuális gépek százai) a migrálás több óráig is eltarthat.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Visszaválthatok majd, miután a migrált erőforrások véglegesítve lettek a Resource Managerben? 

A migrálás bármikor megszakítható, amíg az erőforrások előkészített állapotban vannak. Az erőforrásoknak a véglegesítés művelettel való sikeres migrálását követően a visszaállás nem támogatott.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Visszafordíthatom a migrálást, ha a véglegesítési művelet meghiúsul? 

A migrálás nem szakítható meg, ha a véglegesítés művelet meghiúsul. Minden migrálási művelet, beleértve a véglegesítés műveletet is, idempotens. Ezért azt javasoljuk, hogy rövid idő elteltével próbálkozzon újra a művelettel. Ha továbbra is hibát tapasztal, hozzon létre egy támogatási jegyet, vagy írjon egy fórumbejegyzést ClassicIaaSMigration címkével a [VM fórumunkon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Kell új ExpressRoute-kapcsolatcsoportot beszereznem, ha az IaaS-t a Resource Manager alatt kell használnom? 

Nem. Nemrégiben lehetővé tettük [az ExpressRoute-kapcsolatcsoportok áthelyezését a klasszikusból a Resource Manager-alapú üzemi modellbe](../articles/expressroute/expressroute-move.md). Ha már rendelkezik ExpressRoute-kapcsolatcsoporttal, nem kell újat vásárolnia.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Mi a helyzet, ha szerepköralapú hozzáférés-vezérlési szabályzatokat konfiguráltam klasszikus IaaS-erőforrásaimhoz? 

A migrálás során az erőforrások át lesznek alakítva klasszikusból Resource Manager-alapú erőforrásokká. Ezért azt javasoljuk, hogy az RBAC-szabályzatok szükséges frissítését a migrálás befejezte utánra tervezze.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>I biztonsági másolatot készíteni a klasszikus virtuális gépek az adott tárolóban. Áttelepíthetem a virtuális gépeimet a klasszikus módból Resource Manager módba, hogy egy Recovery Services-tárolóban védjem őket?

<a name="vault">Amikor</a> helyez át egy virtuális Gépet a klasszikus Resource Manager módra, az áttelepítés előtt készített biztonsági másolatokat a rendszer nem telepíti át újonnan áttelepített erőforrás-kezelő virtuális géphez. Ha szeretné megtartani a klasszikus virtuális gépek biztonsági másolatokat, azonban kövesse az alábbi lépéseket az áttelepítés előtt. 

1. A Recovery Services-tároló, keresse meg a **védett elemek** fülre, és válassza ki a virtuális Gépet. 
2. Kattintson a [Védelem kikapcsolása](../articles/backup/backup-azure-manage-vms.md#stop-protecting-virtual-machines) parancsra. Hagyja a *Delete associated backup data* (Társított biztonsági mentési adatok törlése) beállítást **bejelöletlenül**.

> [!NOTE]
> Fizetnie kell biztonsági mentési példány költség keretein belül az adatok megőrzése mellett. Biztonsági másolatok adatforrásnak megőrzési tartomány megfelelően törlődnek. Legutóbbi biztonsági másolat azonban mindig tartani, amíg nem törli explicit módon biztonsági mentési adatokat. A virtuális gép és a védett elem a tárolóban lévő "A biztonsági mentési adatok törlése" eseményindítón a megőrzési tartomány ellenőrzése után a megőrzési időtartam alatt javasoljuk. 
>
>

A virtuális gép áttelepítéséhez a Resource Manager módra 

1. Törölje a biztonsági mentés/pillanatkép bővítményt a virtuális gépről.
2. Telepítse át a virtuális gépet a klasszikus módból a Resource Manager módba. A virtuális gépnek megfelelő tároló és hálózat adatait is mindenképpen telepítse át Resource Manager módba.

Továbbá, ha szeretne biztonsági másolatot készíteni az áttelepített virtuális gép, lépjen a virtuálisgép-felügyelet panel [biztonsági mentésének engedélyezése](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Ellenőrizhetem valahol, hogy az előfizetésem vagy az erőforrásaim esetében lehetséges-e a migrálás? 

Igen. A platform által támogatott migrálás esetében a migrálás előkészítésének első lépése annak ellenőrzése, hogy az adott erőforrások esetében lehetséges-e a migrálás. Ha az ellenőrzési művelet meghiúsul, értesítést kap a migrálást akadályozó összes tényezőről.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Mi történik, ha kvótahibát tapasztalok az IaaS-erőforrások migrálásra való előkészítése során? 

Javasoljuk, hogy szakítsa meg a migrálási folyamatot, és adjon fel egy támogatási jegyet a kvóta növelésére a régióban, ahová a virtuális gépeket migrálja. A kvótakérelem jóváhagyását követően tovább folytathatja a migrálási folyamat lépéseinek végrehajtását.

## <a name="how-do-i-report-an-issue"></a>Hogyan jelenthetem be a hibákat? 

A migrációval kapcsolatos problémáit vagy kérdéseit [VM fórumunkon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows) teheti közzé ClassicIaaSMigration címkével ellátva. Javasoljuk, hogy minden kérdését ezen a fórumon tegye fel. Ha rendelkezik támogatási szerződéssel, támogatási jegyet is feladhat.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Mit tehetek, ha nem tetszenek az erőforrások a platform által a migrálás során választott nevei? 

A migrálás az összes olyan erőforrás nevét megőrzi, amelyeknek kifejezetten adott nevet a klasszikus üzemi modellben. Egyes esetekben új erőforrások jönnek létre. Például: minden virtuális géphez létrejön egy hálózati adapter. A migrálás során létrehozott új erőforrások nevének megadása jelenleg nem támogatott. Ha szeretné, adja le szavazatait erre a szolgáltatásra az [Azure visszajelzési fórumon](http://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Migrálhatom az engedélyezési hivatkozásokkal rendelkező előfizetések között használt ExpressRoute-kapcsolatcsoportokat? 

Az előfizetések közötti engedélyezési hivatkozásokat használó ExpressRoute-kapcsolatcsoportok állásidő nélküli automatikus migrálása nem lehetséges. Az ezek manuális migrálására vonatkozóan vannak útmutatóink. A szükséges lépésekért és további információkért lásd: [ExpressRoute-kapcsolatcsoportok migrálása a klasszikusból a Resource Manager-alapú üzemi modellbe](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Az üzenet jelent *"VM összesített ügynök állapotát az jelentéskészítési nem kész. Ezért a virtuális gép nem migrálható. Győződjön meg arról, hogy a Virtuálisgép-ügynök van-e jelentés készként általános ügynök állapota"* vagy *"a virtuális gép tartalmazza a bővítményt a virtuális gép nem ügynökökről állapotú. Emiatt a virtuális gép nem telepíthető át."*

Ez az üzenet akkor jelenik meg, ha a virtuális gép nem rendelkezik kimenő internetkapcsolattal. A virtuálisgép-ügynök a kimenő kapcsolaton keresztül éri el az Azure-tárfiókot az ügynök állapotának öt percenkénti frissítéséhez.
