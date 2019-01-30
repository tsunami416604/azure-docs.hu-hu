---
title: Az Azure File Sync-kiszolgálói végpont hozzáadása/eltávolítása |} A Microsoft Docs
description: Ismerje meg az Azure Files üzembe helyezésének tervezése során megfontolandó szempontokat.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 118ecc08604cbe05181dd3ba943fd73a34fd47f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212477"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Az Azure File Sync-kiszolgálói végpont hozzáadása/eltávolítása
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt nem átalakításával keletkező a Windows-kiszolgálók az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A *kiszolgálói végpont* jelöli egy adott helyen a egy *regisztrált kiszolgáló*, például az egyik mappájába, egy kiszolgáló vagy a kötet gyökerében. Több kiszolgálói végpontot létezhet ugyanazon a köteten, ha nincsenek átfedésben a névtereket (például F:\sync1 és F:\sync2). Külön-külön az egyes kiszolgálói végpontot a felhőalapú rétegezési házirendeket konfigurálhat. Egy kiszolgálón található fájlok meglévő készletének a kiszolgáló-végpontként való felvételekor egy szinkronizálási csoport, ezeket a fájlokat fogja egyesíthető más végpontok a szinkronizálási csoportban található egyéb fájlok.

Lásd: [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md) üzembe helyezése az Azure File Sync-teljes körű tájékoztatást.

## <a name="prerequisites"></a>Előfeltételek
Kiszolgálói végpont létrehozása, akkor előbb ellenőrizze, hogy a következő feltételek teljesülnek: 
- A kiszolgáló az Azure File Sync-ügynök van telepítve és regisztrálva van. Az Azure File Sync ügynök telepítésére vonatkozó utasítások megtalálhatók a [regisztrálása vagy regisztrációjának törlése a kiszolgáló az Azure File Sync](storage-sync-files-server-registration.md) cikk. 
- Győződjön meg arról, hogy a Társzinkronizálási szolgáltatás van telepítve. Lásd: [üzembe helyezése az Azure File Sync](storage-sync-files-deployment-guide.md) a Storage Sync Service telepítése részleteiért. 
- Győződjön meg arról, hogy a szinkronizálási csoport lett telepítve. Ismerje meg, hogyan [szinkronizálási csoport létrehozása a](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Győződjön meg arról, hogy a kiszolgáló csatlakozik-e az internethez, hogy az Azure érhető el. A kiszolgáló és a szolgáltatás közötti minden kommunikáció 443-as portot használjuk.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele
Kiszolgálói végpont hozzáadásához keresse meg a kívánt szinkronizálási csoporthoz, és válassza a "Kiszolgálói végpont felvétele".

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Az alábbi adatokat a szükséges **kiszolgálói végpont felvétele**:

- **Regisztrált kiszolgáló**: A kiszolgáló vagy a kiszolgálói végpont létrehozása a fürt neve.
- **Elérési út**: A Windows Server, a szinkronizálási csoport részeként kell szinkronizálni az elérési útja.
- **Felhőbeli Rétegezés**: Egy kapcsoló engedélyezheti vagy tilthatja le a felhőbeli rétegezés. Ha engedélyezve van, a felhő rétegezési lesz *szint* az Azure-fájlmegosztások fájlokat. Ez alakítja a helyszíni fájlmegosztások gyorsítótár ahelyett, hogy az adatkészlet, teljes másolata, amelyek segítségével kezelheti a hatékony területfelhasználásának a kiszolgálón.
- **Szabad terület a köteten**: a minimális szabad területet a köteten, amely a kiszolgálói végpont található fenntartására. Például ha a szabad terület a köteten egyetlen kiszolgálói végpont köteten található 50 %-ra van állítva, körülbelül felét adatmennyiség rétegezettek lesznek az Azure Files. Függetlenül attól, hogy felhőbeli rétegezés engedélyezve van, az Azure-fájlmegosztást az adatok teljes másolata mindig szerepel a szinkronizálási csoport.

Válassza ki **létrehozás** hozzáadása a kiszolgálói végpontot. A szinkronizálási csoport egy adott névtéren belül a fájlok mostantól folyamatosan szinkronban. 

## <a name="remove-a-server-endpoint"></a>Kiszolgálói végpont törlése
Ha ezt később is megteheti az Azure File Sync használatával egy adott kiszolgáló végponton lemondásához, eltávolíthatja a kiszolgálói végpontot. 

> [!Warning]  
> Ne kísérelje meg eltávolításával és újbóli létrehozása a kiszolgálói végpontot, kivéve, ha explicit módon tulajdonosaitól egy Microsoft-mérnök szinkronizálási, felhőbeli rétegezés vagy bármilyen más szempont az Azure File Sync hibáinak elhárítása. Kiszolgálói végpont eltávolítása egy destruktív művelet, és a kiszolgálói végpont található rétegzett fájlok nem "újra létrehozza" az Azure-fájlmegosztás a helyükre a kiszolgálói végpont újból létrejön, mely szinkronban hibák után. Azt is vegye figyelembe, előfordulhat, hogy a rétegzett fájlokhoz, amely a kiszolgálói végpont névtér kívüliek véglegesen elvesztek. Rétegzett fájlok a kiszolgálói végpont még akkor is, ha lehetséges, hogy megtalálhatóak a felhőbeli rétegezés nincs engedélyezve.

Győződjön meg arról, hogy az összes rétegzett fájlok idézni vannak-e a kiszolgálói végpont eltávolítása előtt, tiltsa le a felhőbeli rétegezést a kiszolgálói végpontot, és végre a következő PowerShell-parancsmag előhívja a kiszolgálói végpont névtérben lévő összes rétegzett fájlok:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Ha a kiszolgáló a helyi kötet nem rendelkezik elég szabad hely összes rétegzett adat előhívja a `Invoke-StorageSyncFileRecall` parancsmag futtatása sikertelen.  

A kiszolgálói végpont eltávolítása:

1. Keresse meg a Storage Sync Service, ahol a kiszolgáló regisztrálva van-e.
2. Keresse meg a kívánt szinkronizálási csoport.
3. A Storage Sync Service a szinkronizálási csoport távolítsa el a kiszolgálói végpontot, amennyit csak szeretne. A kattintson a jobb gombbal a megfelelő kiszolgálói végpontot, a szinkronizálási csoport panelen kell elvégezni.

    ![Kiszolgálói végpont eltávolítása a szinkronizálási csoport](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>További lépések
- [Regisztráció és a kiszolgáló regisztrációját az Azure File Sync](storage-sync-files-server-registration.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
