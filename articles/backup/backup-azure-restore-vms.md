---
title: "Biztonsági mentés alapján állítsa helyre a virtuális gépek |} Microsoft Docs"
description: "Megtudhatja, hogyan szeretné visszaállítani egy Azure virtuális géphez a helyreállítási pont"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "biztonsági másolat visszaállításával lehetséges; visszaállítása; helyreállítási pont;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: 5f6e5dd9d4fb96376762300856b594d772d84af8
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>Virtuális gépek visszaállítása az Azure-ban
> [!div class="op_single_selector"]
> * [Állítsa vissza a virtuális gépek Azure-portálon](backup-azure-arm-restore-vms.md)
> * [Állítsa vissza a virtuális gépek a klasszikus portál](backup-azure-restore-vms.md)
>
>

A következő lépések az Azure Backup-tárolóban tárolt biztonsági másolatból állítsa vissza a virtuális gép egy új virtuális géphez.

> [!IMPORTANT]
> A biztonsági mentési tárolókról mostantól lehetőség van Recovery Services-tárolókra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra. Után <br/> **2017. november 30**, többé nem tudnak PowerShell használatával létrehozni a biztonsági mentési tárolóból. <br/> Által **2017. November 30**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

## <a name="restore-workflow"></a>Munkafolyamat visszaállítása
### <a name="step-1-choose-an-item-to-restore"></a>1. lépés:, Válasszon ki egy elemet visszaállítása
1. Keresse meg a **védett elemek** fülre, és válassza ki a virtuális gépet egy új virtuális géphez visszaállítandó.

    ![Védett elemek](./media/backup-azure-restore-vms/protected-items.png)

    A **helyreállítási pont** oszlopa a **védett elemek** lap jelzi a virtuális gép helyreállítási pontok száma. A **legújabb helyreállítási pont** oszlopból kiderül, hogy a legutóbbi biztonsági mentés, amelyből a virtuális gépek visszaállítható idején.
2. Kattintson a **visszaállítása** megnyitásához a **visszaállítani az elemet** varázsló.

    ![Állítsa vissza egy elemet](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>2. lépés: Válassza ki a helyreállítási pont létrehozása
1. Az a **válasszon ki egy helyreállítási pontot** képernyő, visszaállíthatja a legújabb helyreállítási pontról, vagy egy előző pont időben. Az alapértelmezett beállítás a varázsló megnyitásakor kijelölt *legújabb helyreállítási pont*.

    ![Válasszon ki egy helyreállítási pontot](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Válasszon egy korábbi időpontbeli időpontban, válassza ki a **dátum kiválasztása** a legördülő lehetőséget, majd válassza ki a dátumot a naptárban parancsával a **naptár ikonra**. A vezérlőelemben található összes dátum, amely rendelkezik a helyreállítási pontok egy világos szürke árnyalatát kitöltődnek, és kijelölhetők-e a felhasználó által.

    ![Dátum kiválasztása](./media/backup-azure-restore-vms/select-date.png)

    Kattintson egy dátumra a naptárban, ha a helyreállítási pontok érhető el, hogy megjelenik-e a dátum a helyreállítási pontok az alábbi táblázatban. A **idő** oszlop, amely során létrehozott pillanatképen idejét jelzi. A **típus** oszlopban megjelenik a [konzisztencia](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) a helyreállítási pont. A táblázat fejlécéhez zárójelben adott napon a rendelkezésre álló helyreállítási pontok számát mutatja.

    ![Helyreállítási pontok](./media/backup-azure-restore-vms/recovery-points.png)
3. Válassza ki a helyreállítási pontot a **helyreállítási pontok** táblázatban, majd kattintson a Tovább nyílra kattintva nyissa meg a következő képernyőre.

### <a name="step-3-specify-a-destination-location"></a>3. lépés: Adja meg a kívánt rendeltetési helyet
1. Az a **válassza ki a példány visszaállítása** képernyőn adja meg a részleteket, ahol visszaállítani a virtuális gépet.

   * Adja meg a virtuális gép neve: az egy adott felhőalapú szolgáltatás, a virtuális gép nevének egyedinek kell lennie. Túlzott írása a meglévő virtuális gép nem támogatott.
   * Válasszon egy felhőalapú szolgáltatást a virtuális gép számára: Ez az megadása kötelező a virtuális gép létrehozása. Ha szeretné, vagy egy meglévő felhőalapú szolgáltatást használja, vagy hozzon létre egy új felhőalapú szolgáltatás.

        Bármilyen felhőszolgáltatás neve nek globálisan egyedinek kell lennie. Általában a felhőszolgáltatás neve lekérdezi egy nyilvánosan elérhető URL-címet [cloudservice] formájában társítva. cloudapp.net. Azure nem engedélyezi a hozzon létre egy új felhőalapú szolgáltatás, ha a név már használatban van. Ha egy új felhőalapú szolgáltatás létrehozása mellett dönt, akkor kap a neve megegyezik a virtuális gép –, amelyben a virtuális gép nevét kivételezett eset a hozzárendelt felhő szolgáltatás alkalmazandó elég egyedinek kell lennie.

        Jelenleg csak felhőszolgáltatások és virtuális hálózatok nem társított bármely affinitáscsoportok a visszaállítási példány részleteit jeleníti meg. [További](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Válasszon egy tárfiókot a virtuális gép számára: Ez az a virtuális gép létrehozása esetén kötelező. Választhat a meglévő tárfiókok ugyanabban a régióban, mint az Azure Backup-tárolóban. Storage-fiókok, amelyek a zóna redundáns vagy a prémium szintű storage típusa nem támogatott.

    Támogatott konfigurációval nem tárfiókok esetén hozzon létre egy tárfiókot, annak konfigurációja támogatott visszaállítási művelet indítása előtt.

    ![Válasszon virtuális hálózatot](./media/backup-azure-restore-vms/restore-sa.png)
3. A virtuális hálózat kiválasztása: A virtuális hálózathoz (VNET), a virtuális gép a virtuális gép létrehozása idején kell kiválasztani. A visszaállítási felhasználói felület használható az előfizetésen belüli összes Vnetek jeleníti meg. Nem kötelező, jelölje be a virtuális hálózat a visszaállított virtuális géphez – csatlakozni tudjanak a visszaállított virtuális gépet az interneten keresztül akkor is, ha a virtuális hálózat nem lesz alkalmazva lesz.

    Ha a kijelölt felhőalapú szolgáltatás virtuális hálózathoz társítva, akkor a virtuális hálózat nem módosítható.

    ![Válasszon virtuális hálózatot](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Válassza ki az alhálózatot: abban az esetben, ha a virtuális hálózat rendelkezik alhálózatokkal, alapértelmezés szerint az első alhálózat lesz kiválasztva. A kiválasztott alhálózatnak a legördülő lista lehetőségek közül választhat. Az alhálózati adatokat, nyissa meg a hálózatok a bővítmény a [portál kezdőlapján](https://manage.windowsazure.com/), és **virtuális hálózatok** , és válassza ki a virtuális hálózatot, és részletekbe menően tárhatják fel az alhálózati részletes konfigurálása.

    ![Válasszon alhálózatot](./media/backup-azure-restore-vms/select-subnet.png)
5. Kattintson a **Submit** ikonra a varázslóban a adatokat küldenek, és hozzon létre egy visszaállítási feladat.

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése
Adjon meg a helyreállítási varázsló összes információt és az elküldött után Azure biztonsági mentés megpróbálja hozzon létre egy feladatot, amely nyomon követheti a visszaállítási műveletet.

![Helyreállítási feladat létrehozása](./media/backup-azure-restore-vms/create-restore-job.png)

Ha a feladat létrehozása sikeres, megjelenik egy bejelentési értesítés arról, hogy létrejött-e a feladatot. Gombra kattintva kaphat további részleteket a **feladat megtekintése** gombot választja, az **feladatok** fülre.

![A visszaállítási feladat létrehozása](./media/backup-azure-restore-vms/restore-job-created.png)

A visszaállítási művelet befejeződése után lesz megjelölve a befejezettként **feladatok** fülre.

![A visszaállítási feladat befejezése](./media/backup-azure-restore-vms/restore-job-complete.png)

A virtuális gép visszaállítása után telepítse újra az eredeti virtuális gép meglévő bővítmények szeretne és [módosítása a végpontok](../virtual-machines/windows/classic/setup-endpoints.md) a virtuális gép az Azure portálon.

## <a name="post-restore-steps"></a>Visszaállítás utáni lépéseket
Ha például Ubuntu, Linux-alapú felhő-init terjesztési biztonsági okokból használ, jelszó le lesz tiltva visszaállítás utáni. Használjon a visszaállított virtuális Gépet a VMAccess bővítmény [a jelszó alaphelyzetbe állítása](../virtual-machines/linux/classic/reset-access.md). Azt javasoljuk, hogy ezek terjesztéseket SSH-kulcsok használatával elkerülheti a feladás egy vagy több helyreállítási jelszó alaphelyzetbe állítása.

## <a name="backup-for-restored-vms"></a>A visszaállított virtuális gépek biztonsági mentése
Ha már visszaállította VM ugyanazon felhőszolgáltatáshoz eredeti biztonsági másolatba mentett virtuális gép ugyanazzal a névvel, biztonságimásolat-készítés folytatódik a virtuális gép post visszaállításkor. Ha VM vissza egy másik felhőszolgáltatást vagy más nevet a visszaállított virtuális Géphez megadott rendelkezik, ennek tekintendők egy új virtuális Gépet, és visszaállított virtuális Géphez szeretne telepítő biztonsági mentés.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>A virtuális gépek helyreállítása Azure DataCenter vészhelyreállítás során
Azure biztonsági mentés lehetővé teszi, hogy helyreállítása biztonsági másolat a virtuális gépek a párosított adatközpont abban az esetben, ha az elsődleges adatközpont ahol virtuális gépek futnak a feladatait katasztrófa és Backup-tárolóban kell lennie a georedundáns konfigurálta. Ilyen esetben során ki kell választania egy tárfiókot, amely párosított adatközpontban található, és ki a visszaállítási folyamat ugyanaz marad. Azure biztonsági mentés párosított földrajzi számítási szolgáltatás segítségével hozza létre a visszaállított virtuális gépet. További információ [Azure Data center rugalmasság](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Tartomány a tartományvezérlő virtuális gépek helyreállítása
Tartományvezérlőn (DC) virtuális gépek biztonsági mentése támogatott lehetőség az Azure Backup szolgáltatással. Azonban ügyelni kell a visszaállítási folyamat során. A megfelelő helyreállítási folyamatot attól függ, hogy a tartomány struktúráját. A legegyszerűbb esetben egyetlen tartományvezérlő egyetlen tartományban van. Gyakrabban üzemi terhelés esetén a fog egyetlen tartományt a több tartományvezérlők, lehet, hogy az egyes tartományvezérlők a helyszíni. Végül előfordulhat, hogy több tartomány erdő.

Az Active Directory szempontjából az Azure virtuális gép olyan, mint bármely más virtuális gép modern támogatott hipervizor alkalmazáson. A fő különbség a helyszíni hipervizorok, hogy nincs virtuális gép konzol az Azure-ban érhető el. A konzol bizonyos forgatókönyvekben, például az operációs rendszer nélküli helyreállítást (BMR) típusú biztonsági mentéssel helyreállítása szükség. VM-visszaállítást a biztonsági mentési tárolóból azonban a teljes operációs rendszer nélküli Helyreállítás váltja fel. Active Directory helyreállító módjának (DSRM) is rendelkezésre áll, ezért az összes Active Directory-helyreállítási forgatókönyvek életképes. Háttér kapcsolatos további információkért tekintse meg [biztonsági mentése és visszaállítása a virtualizált tartományvezérlők szempontjai](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) és [tervezése az Active Directory-erdő helyreállítási](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Egyetlen tartományvezérlő egyetlen tartományban
A virtuális gép visszaállítása végezhető el (például bármely más virtuális gép) az Azure portál vagy a PowerShell használatával.

### <a name="multiple-dcs-in-a-single-domain"></a>Ugyanazon tartományba több tartományvezérlők
Ugyanabban a tartományban más tartományvezérlők elérhetők a hálózaton keresztül, a tartományvezérlő is visszaállítani, mint bármely virtuális gép. Az utolsó megmaradt tartományvezérlő abban a tartományban, vagy egy elkülönített hálózatot visszaállítás történik, ha egy erdő helyreállítási eljárást kell követni.

### <a name="multiple-domains-in-one-forest"></a>Több tartomány egy erdőben
Ugyanabban a tartományban más tartományvezérlők elérhetők a hálózaton keresztül, a tartományvezérlő is visszaállítani, mint bármely virtuális gép. Azonban minden más esetben egy erdő helyreállítási ajánlott.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>Virtuális gépek speciális hálózati konfigurációjának visszaállítása
Azure biztonsági mentés a következő speciális hálózati konfigurációk a virtuális gépek biztonsági mentését is támogatja.

* A terheléselosztó (belső és külső) virtuális gépek
* Virtuális gépek több foglalt IP-cím
* Több hálózati adapterrel rendelkező virtuális gépek

Ezek a konfigurációk engedélyezhetik a következő szempontok őket visszaállítása közben.

> [!TIP]
> PowerShell-alapú visszaállítási folyamat segítségével hozza létre újra a virtuális gépek post visszaállítási speciális hálózati konfigurációját.
>
>

### <a name="restoring-from-the-ui"></a>Visszaállítás a felhasználói felületen:
A felhasználói Felületről, visszaállítása közben **mindig válasszon új felhőszolgáltatást**. Vegye figyelembe, hogy mivel portál csak akkor kötelező paraméterek visszaállítási folyamat, virtuális gépek visszaállítani a felhasználói felület használata során elvész a rendelkeznek speciális hálózati konfigurációját. Ez azt jelenti, a helyreállítás virtuális gépek lesz normál virtuális gépek nélkül a terheléselosztót vagy több hálózati adapter vagy több foglalt IP-cím.

### <a name="restoring-from-powershell"></a>A PowerShell visszaállítása:
PowerShell jogosult a csupán a virtuális gépek lemezei állítsa vissza biztonsági másolatból, és a virtuális gép létrehozása. Ez akkor hasznos, ha a fent említett speciális hálózati konfigurációk igénylő virtuális gépek visszaállításakor.

Ahhoz, hogy teljesen hozza létre újra a virtuális gép post visszaállítási lemezeinek, kövesse az alábbi lépéseket:

1. Állítsa vissza a lemezeket a mentési tároló [Azure biztonsági mentési PowerShell](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Hozzon létre a virtuális gép konfigurációs terheléselosztóhoz szükséges, vagy több hálózati adapter/több foglalt IP-cím a PowerShell-parancsmagokkal és használja úgy, hogy a virtuális gép létrehozása szükséges konfiguráció.

   * Hozzon létre virtuális Gépet a felhőszolgáltatás [belső terheléselosztó](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Hozzon létre virtuális Gépet csatlakozni [internetre irányuló terheléselosztót](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * A virtuális gép létrehozása [több hálózati adapter](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * A virtuális gép létrehozása [több foglalt IP-címek](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Következő lépések
* [Kapcsolatos hibák elhárítása](backup-azure-vms-troubleshoot.md#restore)
* [Virtuális gépek kezelése](backup-azure-manage-vms.md)
