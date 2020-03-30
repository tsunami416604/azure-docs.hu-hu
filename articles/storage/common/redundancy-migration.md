---
title: Tárfiók replikálásának módosítása
titleSuffix: Azure Storage
description: Ismerje meg, hogyan módosíthatja a meglévő tárfiók ban lévő adatok replikálásának módját.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337071"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Tárfiók replikálásának módosítása

Az Azure Storage mindig tárolja az adatok több példányát, hogy védve legyenak a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a tárfiók még a hibák ellenére is megfeleljen az [Azure Storage szolgáltatásiszintű szerződésének (SLA).](https://azure.microsoft.com/support/legal/sla/storage/)

Az Azure Storage a következő típusú replikációt kínálja:

- Helyileg redundáns tárolás (LRS)
- Zónaredundáns tárolás (ZRS)
- Georedundáns tárolás (GRS) vagy olvasási hozzáférésű georedundáns tárolás (RA-GRS)
- Geozónaredundáns tárolás (GZRS) vagy olvasási hozzáférésű geozónaredundáns tárolás (RA-GZRS) (előzetes verzió)

Az egyes beállítások áttekintését az [Azure Storage redundanciája](storage-redundancy.md)című témakörben találja.

## <a name="switch-between-types-of-replication"></a>Váltás a replikáció típusai között

A tárfiókot az egyik típusú replikációról bármely más típusra válthat, de egyes esetek egyszerűbbek, mint mások. Ha georeplikációt vagy olvasási hozzáférést szeretne hozzáadni vagy eltávolítani a másodlagos régióhoz, használhatja az Azure Portalt, a PowerShellt vagy az Azure CLI-t a replikációs beállítás frissítéséhez. Ha azonban módosítani szeretné az adatok replikálásának módját az elsődleges régióban, az LRS-ről a ZRS-re való áttéréssel vagy fordítva, manuális áttelepítést kell végrehajtania.

Az alábbi táblázat áttekintést nyújt az egyes replikációtípusokról a másikra való váltásról:

| Kapcsol | ... az LRS-hez | ... GRS/RA-GRS-re | ... a ZRS-hez | ... a GZRS/RA-GZRS-hez |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... innen: LRS</b> | N/A | Az Azure Portal, a PowerShell vagy a CLI használata az<sup>1</sup> replikációs beállítás módosításához | Kézi áttelepítés végrehajtása <br /><br />Élő áttelepítés kérése | Kézi áttelepítés végrehajtása <br /><br /> VAGY <br /><br /> Először váltson grs/RA-GRS-re, majd kérjen élő áttelepítést<sup>1</sup> |
| <b>... innen: GRS/RA-GRS</b> | Az Azure Portal, a PowerShell vagy a CLI használata a replikációs beállítás módosításához | N/A | Kézi áttelepítés végrehajtása <br /><br /> VAGY <br /><br /> Először váltson LRS-re, majd kérjen élő áttelepítést | Kézi áttelepítés végrehajtása <br /><br /> Élő áttelepítés kérése |
| <b>... innen: ZRS</b> | Kézi áttelepítés végrehajtása | Kézi áttelepítés végrehajtása | N/A | Az Azure Portal, a PowerShell vagy a CLI használata az<sup>1</sup> replikációs beállítás módosításához |
| <b>... innen: GZRS/RA-GZRS</b> | Kézi áttelepítés végrehajtása | Kézi áttelepítés végrehajtása | Az Azure Portal, a PowerShell vagy a CLI használata a replikációs beállítás módosításához | N/A |

<sup>1</sup> Egyszeri kimenő terhelést jelent.

> [!CAUTION]
> Ha a (RA)GRS vagy (RA-)GZRS-fiók hoz létre [egy fiókfeladat-átvételt,](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) akkor az új elsődleges régióban helyileg redundánsként van konfigurálva. Az ilyen LRS-fiókokra való élő áttérés zrs-re vagy GZRS-re nem támogatott. [Manuális áttelepítést](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)kell végrehajtania.

## <a name="change-the-replication-setting"></a>A replikációs beállítás módosítása

Az Azure Portalon, a PowerShellben vagy az Azure CLI-ben módosíthatja a tárfiók replikációs beállítását, feltéve, hogy nem módosítja az adatok replikálásának módját az elsődleges régióban. Ha az elsődleges régióLRS-ről az elsődleges régióban lévő ZRS-re, vagy fordítva, [manuális vagy](#perform-a-manual-migration-to-zrs) [élő áttelepítést](#request-a-live-migration-to-zrs)kell végrehajtania.

A tárfiók replikálásimódjának módosítása nem eredményez állásidőt az alkalmazások számára.

# <a name="portal"></a>[Portál](#tab/portal)

A tárfiók redundanciabeállításának módosításához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza a **Konfiguráció** beállítást.
1. Frissítse a **replikációs** beállítást.

![Képernyőkép a replikációs beállítás módosításáról a portálon](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

A PowerShell-fiók redundanciabeállításának módosításához hívja meg a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot, és adja meg a `-SkuName` paramétert:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiók redundanciabeállításának az Azure CLI-vel való módosításához `--sku` hívja meg az [az storage-fiók frissítési](/cli/azure/storage/account#az-storage-account-update) parancsát, és adja meg a paramétert:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Kézi áttelepítés végrehajtása a ZRS-be

Ha módosítani szeretné, hogyan replikálja a tárfiókban lévő adatok replikálása az elsődleges régióban, az LRS-ről a ZRS-re való áttéréssel vagy fordítva, akkor manuális áttelepítést hajthat végre. A manuális migrálás az élő migrálásnál nagyobb rugalmasságot biztosít. Ön szabályozhatja a manuális áttelepítés időzítését, ezért használja ezt a lehetőséget, ha az áttelepítést egy bizonyos dátumig kell végrehajtania.

Amikor manuális áttelepítést hajt végre lrs-ről ZRS-re az elsődleges régióban vagy fordítva, a céltárfiók georedundáns lehet, és a másodlagos régió olvasási hozzáféréséhez is konfigurálható. Egy LRS-fiókot például egy lépésben áttelepíthet egy GZRS- vagy RA-GZRS-fiókba.

A manuális áttelepítés alkalmazásleállást eredményezhet. Ha az alkalmazás magas rendelkezésre állást igényel, a Microsoft élő migrálási lehetőséget is biztosít. Az élő migrálás egy állásidő nélküli, helyszíni migrálás.

A manuális áttelepítés, az adatokat a meglévő tárfiókból egy új tárfiókba, amely zrs az elsődleges régióban. Manuális áttelepítés végrehajtásához az alábbi lehetőségek közül választhat:

- Adatok másolása egy meglévő eszköz, például az AzCopy, az Egyik Az Azure Storage-ügyfélkódtárak, vagy egy megbízható külső eszköz használatával.
- Ha ismeri a Hadoop vagy a HDInsight, csatolhatja mind a forrástárfiók és a céltárfiók fiók a fürthöz. Ezután párhuzamosítsa az adatmásolási folyamatot egy olyan eszközzel, mint a DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Élő áttelepítés kérése a ZRS-re

Ha a tárfiókot LRS-ről vagy GRS-ről zrs-re kell áttelepítenie az elsődleges régióban, alkalmazásállás nélkül, kérheti a Microsofttól az élő áttelepítést. Az élő áttelepítés során hozzáférhet a tárfiókban lévő adatokhoz, a tartósság és a rendelkezésre állás elvesztése nélkül. Az Azure Storage SLA az áttelepítési folyamat során megmarad. Az élő áttelepítéshez nincs adatvesztés társítva. A szolgáltatásvégpontok, a hozzáférési kulcsok, a megosztott hozzáférés-aláírások és az egyéb fiókbeállítások az áttelepítés után változatlanok maradnak.

A ZRS csak az általános célú v2-fiókokat támogatja, ezért a tárfiók frissítése előtt frissítse a ZRS-re irányuló élő áttelepítési kérelmet. További információ: [Frissítés általános célú v2-es tárfiókra.](storage-account-upgrade.md) A tárfióknak olyan adatokat kell tartalmaznia, amelyeket élő áttelepítéssel kell áttelepíteni.

Az élő áttelepítés csak LRS- vagy GRS-replikációt használó tárfiókok esetén támogatott. Ha fiókja RA-GRS-t használ, akkor a folytatás előtt módosítania kell a fiók replikációs típusát LRS-re vagy GRS-re. Ez a köztes lépés eltávolítja a másodlagos csak olvasható végpont által biztosított RA-GRS áttelepítés előtt.

Bár a Microsoft azonnal kezeli az élő migrálási kérelmet, a migrálás befejezésének időpontjára nem vállal garanciát. Ha egy adott időpontig szükséges az adatok ZRS-re való migrálása, a Microsoft a manuális migrálás végrehajtását javasolja. Minél több adat található a fiókban, általában annál tovább tart az adatok migrálása.

Manuális áttelepítést kell végrehajtania, ha:

- Az adatokat egy ZRS-tárfiókba szeretné áttelepíteni, amely a forrásfióktól eltérő régióban található.
- A tárfiók egy prémium szintű lapblob vagy blokk blob fiók.
- Adatokat szeretne áttelepíteni a ZRS-ről az LRS, GRS vagy RA-GRS-re.
- A tárfiók adatokat tartalmaz az archív rétegben.

Élő áttelepítést kérhet az [Azure Támogatási portálon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)keresztül. A portálon válassza ki a ZRS-re konvertálni kívánt tárfiókot.

1. **Új támogatási kérelem** kiválasztása
2. Töltse ki **az alapokat** a fiókadatai alapján. A **Szolgáltatás** szakaszban válassza **a Tárfiók kezelése** és a ZRS-re konvertálni kívánt erőforrás lehetőséget.
3. Válassza a **Tovább lehetőséget.**
4. Adja meg a következő értékeket a **Probléma** szakaszban:
    - **Súlyosság**: Hagyja az alapértelmezett értéket a hogy-van.
    - **Problématípusa**: Válassza az **Adatáttelepítés**lehetőséget.
    - **Kategória**: Válassza **az Áttelepítés zrs-be**lehetőséget.
    - **Cím**: Írjon be egy leíró címet, például **zrs-fiók áttelepítése**.
    - **Részletek**: Írja be a további részleteket a **Részletek** mezőbe, például szeretnék áttérni \_ \_ a ZRS-re a régió [LRS, GRS] területéről.
5. Válassza a **Tovább lehetőséget.**
6. Ellenőrizze, hogy a kapcsolattartási adatok helyesek-e a **Kapcsolattartási adatok** panelen.
7. Kattintson a **Létrehozás** gombra.

A támogatási személy felveszi Önnel a kapcsolatot, és minden szükséges segítséget megad.

> [!NOTE]
> Az élő áttelepítés jelenleg nem támogatott a prémium fájlmegosztások esetében. Jelenleg csak az adatok manuális másolása vagy áthelyezése támogatott.
>
> A GZRS-tárfiókok jelenleg nem támogatják az archív szintet. További részletekért tekintse meg az [Azure Blob storage: gyakori elérésű, ritka elérésű és archiválási hozzáférési szinteket.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)
>
> A felügyelt lemezek csak LRS-hez érhetők el, és nem telepíthetők át a ZRS-re. A szabványos SSD-kezelt lemezek pillanatképeit és képeit szabványos HDD-tárolón tárolhatja, és [választhat az LRS és a ZRS beállítások közül.](https://azure.microsoft.com/pricing/details/managed-disks/) A rendelkezésre állási készletekkel való integrációról az Azure által felügyelt lemezek – bevezetés című [témakörben talál](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)további információt.

## <a name="switch-from-zrs-classic"></a>Váltás a ZRS Classic-ról

> [!IMPORTANT]
> 2021. március 31-én a Microsoft elavultolja és áttelepíti a ZRS Classic fiókokat. További részleteket a ZRS Classic ügyfelei kapnak az eprecation előtt.
>
> Miután a ZRS általánosan elérhetővé válik egy adott régióban, az ügyfelek a továbbiakban nem hozhatnak létre ZRS Classic-fiókokat az adott régióban lévő Azure portalról. A Microsoft PowerShell és az Azure CLI használatával ZRS Classic-fiókok létrehozása egy lehetőség, amíg a ZRS Classic elavult. A ZRS elérhetőségéről az [Azure Storage redundanciája](storage-redundancy.md)című témakörben talál további információt.

A ZRS Classic aszinkron módon replikálja az adatokat egy-két régión belüli adatközpontok között. Előfordulhat, hogy a replikált adatok csak akkor érhetők el, ha a Microsoft kezdeményezi a feladatátvételt a másodlagos részére. A Klasszikus ZRS-fiók lrs-re, GRS-re vagy RA-GRS-re nem konvertálható. A Klasszikus ZRS-fiókok nem támogatják a metrikákat vagy a naplózást.

A ZRS Classic csak az általános célú V1 (GPv1) tárfiókok **blokkblobjaihoz** érhető el. A tárfiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](storage-account-overview.md)

A ZRS-fiók adatainak manuális áttelepítéséhez LRS, GRS, RA-GRS vagy ZRS Klasszikus fiókból, használja az alábbi eszközök egyikét: AzCopy, Azure Storage Explorer, PowerShell vagy Azure CLI. Saját áttelepítési megoldást is létrehozhat az Azure Storage-ügyfélkódtárak egyikével.

A ZRS Classic tárfiókot zrs-re is frissítheti az Azure Portal, a PowerShell vagy az Azure CLI használatával azokban a régiókban, ahol a ZRS elérhető.

# <a name="portal"></a>[Portál](#tab/portal)

Ha az Azure Portalon a ZRS-re szeretne frissíteni, keresse meg a fiók **konfigurációs** beállításait, és válassza a **Frissítés**lehetőséget:

![Frissítés a ZRS Classic-ról zrs-re a portálon](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Ha a PowerShell használatával szeretne ZRS-re frissíteni, hívja meg a következő parancsot:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával szeretne ZRS-re frissíteni, hívja meg a következő parancsot:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Az adatok replikálásának módosításával kapcsolatos költségek

Az adatok replikálásának módosításával kapcsolatos költségek a konverziós útvonaltól függenek. A legköltségesebb Azure Storage-redundancia ajánlatok közé tartozik az LRS, zrs, GRS, RA-GRS, GZRS és RA-GZRS.

Például az *LRS-ről* bármely más típusú replikációra való áttérés további költségeket von maga után, mert kifinomultabb redundanciaszintre kerül. *GrS-be* vagy RA-GRS-be való áttelepítés esetén a kimenő sávszélesség-díj merül fel, mivel az adatok (az elsődleges régióban) replikálódnak a távoli másodlagos régióba. Ez a díj a kezdeti beállításkor egyszeri költség. Az adatok másolása után nincsenek további áttelepítési díjak. A sávszélesség-díjakról az [Azure Storage díjszabási oldalán](https://azure.microsoft.com/pricing/details/storage/blobs/)talál.

Ha grs-ről LRS-re telepíti át a tárfiókot, nincs további költség, de a replikált adatok törlődnek a másodlagos helyről.

> [!IMPORTANT]
> Ha áttelepíti a tárfiókot RA-GRS-ről GRS-re vagy LRS-re, akkor a fiók ra-GRS-ként kerül számlázásra további 30 nappal az konvertálás dátumától számítva.

## <a name="see-also"></a>Lásd még

- [Az Azure Storage redundanciája](storage-redundancy.md)
- [Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése](last-sync-time-get.md)
- [Magas rendelkezésre állású alkalmazások tervezése olvasási hozzáférésű georedundáns tárolással](storage-designing-ha-apps-with-ragrs.md)
