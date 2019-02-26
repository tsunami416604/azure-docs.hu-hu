---
title: Az Azure azonnali visszaállítási képesség
description: Azonnali visszaállítási képesség és a VM vonatkozó gyakori kérdések az Azure backup stack, Resource Manager üzemi modell
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: sogup
ms.openlocfilehash: 63a0e5bd3db6251e361d9dd03acb538556e811ca
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804986"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Továbbfejlesztett biztonsági mentés, és állítsa vissza a teljesítmény az Azure Backup azonnali visszaállítási képesség

> [!NOTE]
> Vannak átnevezés, hogy a felhasználók visszajelzései alapján **virtuális gép biztonsági mentési vermének v2 verziójára** való **azonnali visszaállítása** elkerülése érdekében az Azure Stack-funkciókkal.

Az új modellre azonnali visszaállítása a következő szolgáltatás fejlesztéseket biztosítja:

* Lehetővé teszi a biztonsági mentési feladatot, amely elérhető a helyreállítás befejezéséhez a tárolóban való adatátvitel várakozás nélkül részeként készített pillanatkép használata. Ez csökkenti a várakozási idő a pillanatképek visszaállításának előtt a tárolóba másolja.
* Csökkenti a biztonsági mentési és helyreállítási idő szerint helyileg, a pillanatképek megőrzik alapértelmezés szerint két napig. Az alapértelmezett tároló nem 1 és 5 nap között bármilyen érték konfigurálható.
* Támogatja a lemez mérete 4 TB-ig.
* Standard SSD-lemezeket támogatja.
*   Használhat egy nem felügyelt virtuális gép eredeti tárfiókokban (lemezenként) visszaállítása során. Ez a lehetőség létezik, akkor is, ha a virtuális gép lemezei tárfiókokban vannak elosztva. Felgyorsítja a visszaállítási műveletek számos különböző Virtuálisgép-konfigurációk



## <a name="whats-new-in-this-feature"></a>Ez a szolgáltatás újdonságai

A biztonsági mentési feladat jelenleg két fázisból áll:

1.  Virtuális gép pillanatképének elkészítése.
2.  Átvitele az Azure Recovery Services-tárolót egy virtuális gép pillanatképét.

Helyreállítási pont számít jönnek létre csak azt követően végezhető el az 1. és 2 fázisait. Ez a frissítés részeként egy helyreállítási pont jön létre, amint elkészült a pillanatkép és a helyreállítási pont pillanatkép típusa visszaállítási folyamatot használja a visszaállítás végrehajtásához használható. A helyreállítási pont típusa "pillanatkép" segítségével azonosíthatja a helyreállítási pont, az Azure Portalon, és miután a pillanatkép átkerülnek a tárolóba, a helyreállítási pont típusa "pillanatkép és tár" változik.

![Virtuális gép biztonsági másolat verem Resource Manager üzemi modell – tárolási és a tárolóban lévő biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-flow.png)

Alapértelmezés szerint a pillanatfelvételek megmaradnak két napig. Ez a funkció lehetővé teszi a visszaállítások művelet van ezeket a pillanatképeket cutting le a helyreállítási időt. Ez csökkenti az idő átalakításához, és adatokat másol vissza a tárolót a storage-fiókját a felhasználó nem felügyelt lemez forgatókönyvek felügyelt lemez számára szükséges, a Recovery Services-adatok nem felügyelt lemezeket hoz létre.

## <a name="feature-considerations"></a>Funkciók kapcsolatos megfontolások

* A pillanatképek és a lemezek a helyreállítási pont létrehozása növelése érdekében, és visszaállítási műveletek felgyorsítása érdekében tárolja. Ennek eredményeképpen látni fogja, amelyek megfelelnek a pillanatképekhez, ebben az időszakban végrehajtott tárolási költségeket.
* Növekményes pillanatképek lapblobként tárolt. Nem felügyelt lemezeket használó összes felhasználó a saját helyi tárfiókban tárolt pillanatképek díjat számítunk fel. Mivel a visszaállítási pont gyűjtemények által felügyelt virtuális gép biztonsági mentéseinek használt blobpillanatképeket használja az alapul szolgáló tárolási szinten, a felügyelt lemezek látni fogja a blob-pillanatkép díjszabás megfelelő költségeket és a növekményes.
* Prémium szintű tárfiókok esetén az azonnali helyreállítási pontok száma legfeljebb 10 TB-os felé készített pillanatképeket lefoglalt terület.
* Lehetővé teszi a visszaállítási igényeinek megfelelően pillanatképek megőrzésének konfigurálása kap. A követelmény, attól függően beállíthatja a pillanatkép megőrzési legalább egy napot a biztonsági mentési házirend panel, kövesse az alábbi utasításokat. Ez segíthet csökkenthetők a költségek pillanatképek megőrzésének, ha nem végezhet helyreállítást gyakran.
* Ez a frissítés után az azonnali helyreállítás egy irányt frissítése, már nem válthat vissza.


>[!NOTE]
>Ez azonnali és visszaállítását a frissítés minden ügyfélnél pillanatkép megőrzési időtartama (**új és meglévő is megtalálható a**) két nap alapértelmezett értékre állítja. Azonban meg az időtartamot a követelmény 1-5 nap között bármilyen érték alapján.


## <a name="cost-impact"></a>A Cost gyakorolt hatás

A növekményes pillanatképek vannak a virtuális gép tárfiókban tárolja, az azonnali helyreállítás használt. Növekményes pillanatkép azt jelenti, hogy a pillanatkép által elfoglalt hely megegyezik a pillanatkép létrehozása után írt lapok által elfoglalt terület. Számlázás továbbra is a rendszer a GB-onként használt elfoglalt hely által a pillanatkép és a GB-onkénti ár azonos módon a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Az azonnali helyreállítás frissítése

Ha használja az Azure Portalon, látni fogja értesítést a tároló irányítópultjának. Ezt az értesítést a nagyméretű lemezek támogatása és a biztonsági mentés és visszaállítás sebességének fejlesztései vonatkozik.
Azonnali visszaállítani a frissítéskor képernyő megnyitásához, válassza ki a szalagcímet.

![A virtuális gép biztonsági másolat verem Resource Manager üzemi modell – értesítés a támogatás megszűnéséről biztonsági mentési feladat](./media/backup-azure-vms/instant-rp-banner.png)

Kattintson a **frissítése** az alábbi képernyőképen látható módon:

![A virtuális gép biztonsági másolat verem Resource Manager üzemi modell – biztonsági mentési feladat frissítése](./media/backup-azure-vms/instant-rp.png)

Másik lehetőségként megnyithatja **tulajdonságok** beolvasni a tároló oldalán a **frissítése** lehetőség **virtuális gép biztonsági másolat verem**.

![Biztonsági mentési feladat, a virtuális gép biztonsági másolat verem – Tulajdonságok lap](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="configure-snapshot-retention-using-azure-portal"></a>Az Azure portal használatával pillanatképek megőrzésének konfigurálása
A jelenleg elérhető **USA nyugati középső Régiója**, **Dél-India** és **Kelet-Ausztrália**.

A frissített felhasználókhoz, az Azure Portalon látható egy új funkció a mezőt a **virtuális gép biztonsági mentési szabályzat** panel a **azonnali visszaállítása** szakaszban. Módosíthatja a pillanatkép megőrzési időtartamot a **virtuális gép biztonsági mentési szabályzat** panel az összes virtuális gép az adott biztonsági mentési szabályzathoz társított.

![Azonnali visszaállítási képesség](./media/backup-azure-vms/instant-restore-capability.png)

## <a name="upgrade-to-instant-restore-using-powershell"></a>Frissítse az azonnali helyreállításához PowerShell-lel

Ha szeretné önkiszolgáló, és frissítse az azonnali helyreállításához, futtassa a következő parancsmagokat egy emelt szintű PowerShell terminál:

1.  Jelentkezzen be az Azure-fiókjával:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Regisztrálni kívánt előfizetés kiválasztásához:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Ez az előfizetés regisztrálása:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Frissítse az azonnali helyreállításához parancssori felület használatával

Futtassa a következő parancsokat egy rendszerhéjból:

1.  Jelentkezzen be az Azure-fiókjával:

    ```
    az login
    ```

2.  Regisztrálni kívánt előfizetés kiválasztásához:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Ez az előfizetés regisztrálása:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Győződjön meg arról, hogy a frissítés sikeres

### <a name="powershell"></a>PowerShell
Egy rendszergazda jogú PowerShell terminálból futtassa a következő parancsmagot:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>parancssori felület
Egy rendszerhéjból futtassa a következő parancsot:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Ugyanakkor "Regisztrálva", az előfizetés frissül az azonnali visszaállítása.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Mik azok az azonnali helyreállítás költség következményei?
A pillanatképek és a lemezek a helyreállítási pont létrehozása felgyorsíthatja és visszaállítási műveletek tárolódnak. Ennek eredményeképpen látni fogja, amelyek megfelelnek a kiválasztott virtuális gép biztonsági mentési szabályzat részeként pillanatkép megőrzési tárolási költségeket.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>A prémium szintű Storage-fiókok hajtsa végre az azonnali helyreállítási pontok készített pillanatképeket foglalhat el a 10 TB-os pillanatképekre vonatkozó korlát?
Igen, prémium szintű tárfiókok esetén az azonnali helyreállítási pontok készített pillanatképeket foglalhat el pillanatkép lefoglalt terület 10 TB.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hogyan működik a pillanatkép megőrzési ideje az ötnapos időszakban?
Minden nap egy új pillanatképet készít, majd öt egyéni növekményes pillanatképek vannak. A pillanatkép mérete attól függ, hogy a legtöbb esetben körülbelül 2 – 7 % adatváltozás.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Nem egy azonnali helyreállítás pillanatkép, egy növekményes pillanatkép vagy a teljes pillanatkép?
Az azonnali helyreállítás funkció részeként készített pillanatkép növekményes pillanatképek.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hogyan kiszámíthatja a hozzávetőleges növekedése miatt az azonnali helyreállítás funkció?
Ez a forgalom a virtuális gép függ. Egy stabil állapotot is feltételezik, költség növekedése van = pillanatkép megőrzési időtartam napi adatváltozással GB-onként VM tárolási költségeket.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Ha a helyreállítás típusának visszaállítási pont "Pillanatkép és tár" és a visszaállítási művelet elvégezzem, melyik helyreállítási típust használja?
Ha a helyreállítás típusának "pillanatkép és tár", a helyi pillanatkép, amely lesz sokkal gyorsabban összehasonlítva a tárolóból végzett visszaállítási automatikusan végezhető visszaállítás.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Mi történik, ha kiválasztom a megőrzési időszak visszaállítási pont (2. szint) kisebb, mint a pillanatkép (1. szint) megőrzési időszak?
Az új modell nem engedélyezi a törlése a visszaállítási pont (– 2. szint), kivéve, ha a rendszer törli a pillanatképét, (1. szint). Azt javasoljuk, hogy visszaállítási pont (– 2. szint) megőrzési idő nagyobb, mint a pillanatkép megőrzési időtartam ütemezése.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Miért van a pillanatkép meglévő után megőrizni a biztonsági mentési szabályzat időszak?
Ha a helyreállítási pont van állítva a pillanatkép és a legújabb helyreállítási pont Védettként érhető el, amíg a következő sikeres biztonsági mentés megtartására. Ez a a tervezett GC szabályzatának megfelelően még ma, hogy a jelen kell lennie minden esetben abban az esetben az összes biztonsági mentés további a sikertelen a virtuális gép probléma miatt legalább egy legújabb helyreállítási pont Védettként építjük. A normál esetben RPs törlődnek a legfeljebb 24 órán át lejártuk után.
