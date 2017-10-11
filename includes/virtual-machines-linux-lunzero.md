Amikor adatlemezt ad hozzá egy Linux virtuális gép, esetleg felmerülő problémákat, ha a lemez nem létezik: LUN 0. Ha egy lemez segítségével manuálisan hozzáadni a `azure vm disk attach-new` parancsot, és adja meg a LUN-t (`--lun`) ahelyett, hogy engedélyezi a megfelelő logikai meghatározásához az Azure platformon, mi gondoskodunk, hogy a lemez már létezik / LUN azonosítójú 0 van jelen. 

Vegye figyelembe a következő példa egy kódrészletet a kimenetét megjelenítő `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

A két adatlemezek lehet létrehozni a LUN 0 és LUN-1 (az első oszlop a `lsscsi` részletek kimeneti `[host:channel:target:lun]`). Mindkét lemeznek kell lennie a accessbile a virtuális Gépen belül. Ha az első lemezt lehet hozzáadni a logikai egység 1 és a második lemezről LUN 2 manuálisan kellett megadott, a virtuális Gépen belül nem jelenhet meg a lemezeket megfelelően.

> [!NOTE]
> Az Azure `host` érték 5 ezekben a példákban, de ez eltérhetnek attól függően, hogy a tároló típusa szerinti választja.
> 
> 

Ez lemez azonban nem egy Azure probléma, de a, amelyben a Linux kernel követi a SCSI-specifikációk módját. A Linux kernel a SCSI-buszhoz csatlakoztatott eszközöket keres, ha egy eszköz kell található LUN azonosítójú 0 ahhoz, hogy a rendszer folytatja a további eszközök keresése. Ilyen:

* Tekintse át a kimenetet a `lsscsi` ellenőrzése, hogy rendelkezik-e a lemez 0 LUN azonosítójú adatlemez hozzáadása után.
* Ha a lemez nem jelenik meg helyesen a virtuális Gépen belül, a lemez LUN azonosítójú 0 meglétének ellenőrzése.

