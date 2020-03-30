---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383898"
---
## <a name="what-is-the-time-required-for-migration"></a>Mennyi idő szükséges az áttelepítéshez?

A migráció megtervezése és végrehajtása nagyban függ az architektúra összetettségétől, és néhány hónapig is eltarthat.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Mi az új ügyfél definíciója az IaaS virtuális gépeken (klasszikus)?

Azok az ügyfelek, akik nem rendelkeztek IaaS virtuális gépekkel (klasszikus) az előfizetéseikben a Febrauary 2020 hónapjában (egy hónappal az aprecáció megkezdése előtt), új ügyfélnek minősülnek. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Mi a definíciója egy meglévő ügyféli virtuális gépek (klasszikus)?

Azok az ügyfelek, akik 2020 februárjában aktívan vagy leállították az IaaS virtuális gépeket (Klasszikus) az előfizetéseikben, meglévő ügyfélnek minősülnek. Csak ezek az ügyfelek kap március 1-ig, 2023 áttelepíteni a virtuális gépek az Azure Service Manager az Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Miért jelenik meg a "NewClassicVMCreationNotAllowedForSubscription" hibaüzenete?

A kivonási folyamat részeként IaaS virtuális gép (klasszikus) már nem érhető el az új ügyfelek számára. Új ügyfélként azonosítottuk Önt, ezért a működése nem volt engedélyezve. Javasoljuk, hogy az [Azure virtuális gépek et használja az ARM használatával.](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) Ha nem tudja használni az Azure virtuális gépek et ARM használatával, forduljon az ügyfélszolgálathoz az előfizetés engedélyezési listájához.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Érinti ez a migrálási terv az Azure virtuális gépeken futó meglévő szolgáltatásaimat és alkalmazásaimat? 

2023. március 1-ig az IaaS virtuális gépek (klasszikus) esetén nem. Az IaaS virtuális gépek (klasszikus) teljes mértékben támogatott szolgáltatások általánosan elérhető. Továbbra is használhatja ezeket az erőforrásokat, és akár ki is terjesztheti működését a Microsoft Azure-ban. 2023. március 1-jén ezek a virtuális gépek teljes mértékben kilesznek vonva, és az aktív vagy lefoglalt virtuális gépek leállnak & feloldva. Nem lesz hatással más klasszikus erőforrások, például a Cloud Services (Classic), storage-fiókok (klasszikus), stb.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Mi történik a virtuális gépeimmel, ha nem tervezek migrálni a közeljövőben? 

2023. március 1-jén az IaaS virtuális gépek (Klasszikus) teljes mértékben megszűnnek, és az aktív vagy lefoglalt virtuális gépek leállnak & felszabadított állapotban lesznek. Az üzleti hatások megelőzése érdekében erősen kommentáljuk az áttelepítés megtervezését még ma, és 2023. Nem adtuk elavultnak a meglévő klasszikus API-kat, a Felhőszolgáltatásokat és az erőforrásmodellt. Szeretnénk könnyebbé tenni a migrálást, figyelembe véve a Resource Manager-alapú üzemi modellben elérhető fejlett szolgáltatásokat. Azt javasoljuk, hogy kezdje el tervezni ezeket az erőforrásokat az Azure Resource Managerbe. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Milyen hatással lesz a migrálási terv a meglévő eszközállományomra? 

Az eszközállomány a Resource Manager-alapú üzemi modellre való frissítése az egyik legjelentősebb változás, amellyel számolnia kell a migrálási tervben.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Milyen hosszú lesz a felügyeleti sík leállása? 

Ez a migrált erőforrások számától függ. A kisebb környezetek esetében (néhány tucat virtuális gépig) a teljes migrálás kevesebb mint egy órát tart majd. Nagyobb méretű környezetek esetében (virtuális gépek százai) a migrálás több óráig is eltarthat.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Visszaválthatok majd, miután a migrált erőforrások véglegesítve lettek a Resource Managerben? 

A migrálás bármikor megszakítható, amíg az erőforrások előkészített állapotban vannak. Az erőforrásoknak a véglegesítés művelettel való sikeres migrálását követően a visszaállás nem támogatott.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Visszafordíthatom a migrálást, ha a véglegesítési művelet meghiúsul? 

A migrálás nem szakítható meg, ha a véglegesítés művelet meghiúsul. Minden migrálási művelet, beleértve a véglegesítés műveletet is, idempotens. Ezért azt javasoljuk, hogy rövid idő elteltével próbálkozzon újra a művelettel. Ha továbbra is hibával szembesül, hozzon létre egy támogatási jegyet.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Kell új ExpressRoute-kapcsolatcsoportot beszereznem, ha az IaaS-t a Resource Manager alatt kell használnom? 

Nem. Nemrégiben lehetővé tettük [az ExpressRoute-kapcsolatcsoportok áthelyezését a klasszikusból a Resource Manager-alapú üzemi modellbe](../articles/expressroute/expressroute-move.md). Ha már rendelkezik ExpressRoute-kapcsolatcsoporttal, nem kell újat vásárolnia.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Mi a helyzet, ha szerepköralapú hozzáférés-vezérlési szabályzatokat konfiguráltam klasszikus IaaS-erőforrásaimhoz? 

A migrálás során az erőforrások át lesznek alakítva klasszikusból Resource Manager-alapú erőforrásokká. Ezért azt javasoljuk, hogy az RBAC-szabályzatok szükséges frissítését a migrálás befejezte utánra tervezze.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Biztonsági másolatot tettem a klasszikus virtuális gépeimről egy páncélteremben. Áttelepíthetem a virtuális gépeimet a klasszikus módból Resource Manager módba, hogy egy Recovery Services-tárolóban védjem őket?

Amikor egy virtuális gép klasszikus erőforrás-kezelő módból Erőforrás-kezelő módba kerül, az áttelepítés előtt készített biztonsági mentések nem kerülnek át az újonnan áttelepített Erőforrás-kezelő virtuális gépre. Azonban ha meg szeretné tartani a klasszikus virtuális gépek biztonsági mentéseit, kövesse az alábbi lépéseket az áttelepítés előtt. 

1. A Recovery Services tárolóban lépjen a **Védett elemek** fülre, és válassza a virtuális gép. 
2. Kattintson a Védelem kikapcsolása parancsra. Hagyja a *Delete associated backup data* (Társított biztonsági mentési adatok törlése) beállítást **bejelöletlenül**.

> [!NOTE]
> Az adatok megőrzéséig a biztonsági másolat példányának költségét számítjuk fel. A biztonsági másolatok metszése a megőrzési tartományszerint lesz. Az utolsó biztonsági másolat azonban mindig megmarad, amíg explicit módon nem törli a biztonsági mentési adatokat. Javasoljuk, hogy ellenőrizze a virtuális gép megőrzési tartományát, és indítsa el a "Biztonsági mentési adatok törlése" a védett elem a tárolóban, ha a megőrzési tartomány vége. 
>
>

A virtuális gép Erőforrás-kezelő módba való áttelepítéséhez 

1. Törölje a biztonsági mentés/pillanatkép bővítményt a virtuális gépről.
2. Telepítse át a virtuális gépet a klasszikus módból a Resource Manager módba. A virtuális gépnek megfelelő tároló és hálózat adatait is mindenképpen telepítse át Resource Manager módba.

Továbbá, ha biztonsági másolatot szeretne készíteni az áttelepített virtuális gépről, lépjen a Virtuálisgép-kezelés panelre a [biztonsági mentés engedélyezéséhez.](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Ellenőrizhetem valahol, hogy az előfizetésem vagy az erőforrásaim esetében lehetséges-e a migrálás? 

Igen. A platform által támogatott migrálás esetében a migrálás előkészítésének első lépése annak ellenőrzése, hogy az adott erőforrások esetében lehetséges-e a migrálás. Ha az ellenőrzési művelet meghiúsul, értesítést kap a migrálást akadályozó összes tényezőről.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Mi történik, ha kvótahibát tapasztalok az IaaS-erőforrások migrálásra való előkészítése során? 

Javasoljuk, hogy szakítsa meg a migrálási folyamatot, és adjon fel egy támogatási jegyet a kvóta növelésére a régióban, ahová a virtuális gépeket migrálja. A kvótakérelem jóváhagyását követően tovább folytathatja a migrálási folyamat lépéseinek végrehajtását.

## <a name="how-do-i-report-an-issue"></a>Hogyan jelenthetem be a hibákat? 

A migrációval kapcsolatos problémáit vagy kérdéseit [VM fórumunkon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows) teheti közzé ClassicIaaSMigration címkével ellátva. Javasoljuk, hogy minden kérdését ezen a fórumon tegye fel. Ha rendelkezik támogatási szerződéssel, támogatási jegyet is feladhat.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Mit tehetek, ha nem tetszenek az erőforrások a platform által a migrálás során választott nevei? 

A migrálás az összes olyan erőforrás nevét megőrzi, amelyeknek kifejezetten adott nevet a klasszikus üzemi modellben. Egyes esetekben új erőforrások jönnek létre. Például: minden virtuális géphez létrejön egy hálózati adapter. A migrálás során létrehozott új erőforrások nevének megadása jelenleg nem támogatott. Ha szeretné, adja le szavazatait erre a szolgáltatásra az [Azure visszajelzési fórumon](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Migrálhatom az engedélyezési hivatkozásokkal rendelkező előfizetések között használt ExpressRoute-kapcsolatcsoportokat? 

Az előfizetések közötti engedélyezési hivatkozásokat használó ExpressRoute-kapcsolatcsoportok állásidő nélküli automatikus migrálása nem lehetséges. Az ezek manuális migrálására vonatkozóan vannak útmutatóink. A szükséges lépésekért és további információkért lásd: [ExpressRoute-kapcsolatcsoportok migrálása a klasszikusból a Resource Manager-alapú üzemi modellbe](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>A következő üzenet jelenik *meg: "A virtuális gép a teljes ügynök állapotát nem készként jelenti. Ezért a virtuális gép nem telepíthető át. Győződjön meg arról, hogy a virtuális gép ügynök jelentési általános ügynök állapota ready"* vagy *"Virtuális gép tartalmazza bővítmény, amelynek állapota nem jelentik a virtuális gép. Ezért ez a virtuális gép nem telepíthető át."*

Ez az üzenet akkor jelenik meg, ha a virtuális gép nem rendelkezik kimenő internetkapcsolattal. A virtuálisgép-ügynök a kimenő kapcsolaton keresztül éri el az Azure-tárfiókot az ügynök állapotának öt percenkénti frissítéséhez.
