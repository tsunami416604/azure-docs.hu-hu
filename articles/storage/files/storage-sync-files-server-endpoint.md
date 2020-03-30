---
title: Azure File Sync kiszolgálóvégpont hozzáadása/eltávolítása | Microsoft dokumentumok
description: Ismerje meg, mit kell figyelembe venni az Azure Files központi telepítésének tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255105"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Azure File Sync kiszolgálóvégpont hozzáadása/eltávolítása
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótárává alakítja. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A *kiszolgálóvégpont* a *regisztrált kiszolgáló*egy adott helyét jelöli , például egy kiszolgálóköteten lévő mappát vagy a kötet gyökerét. Több kiszolgálóvégpont is létezhet ugyanazon a köteten, ha a névterek nem fedik át egymást (például F:\sync1 és F:\sync2). A felhőrétegezési házirendeket minden kiszolgálóvégponthoz külön-külön konfigurálhatja. Ha egy meglévő fájlkészlettel rendelkező kiszolgálóhelyet ad hozzá egy szinkronizálási csoporthoz, ezek a fájlok egyesülnek a szinkronizálási csoport más végpontjain már megtalálható fájlokkal.

Az Azure File Sync központi [telepítéséről](storage-sync-files-deployment-guide.md) további információt talál az Azure File Sync végpontok között történő központi telepítéséről.

## <a name="prerequisites"></a>Előfeltételek
Kiszolgálói végpont létrehozásához először meg kell győződnie arról, hogy a következő feltételek teljesülnek: 
- A kiszolgálón telepítve van az Azure File Sync ügynök, és regisztrálva van. Az Azure File Sync Agent telepítésére vonatkozó utasítások a [Register/unregister a server with Azure File Sync](storage-sync-files-server-registration.md) cikkben találhatók. 
- Győződjön meg arról, hogy egy tárolószinkronizálási szolgáltatás telepítve van. A Storage Sync Service üzembe helyezéséről az [Azure File Sync üzembe helyezése.](storage-sync-files-deployment-guide.md) 
- Győződjön meg arról, hogy egy szinkronizálási csoport telepítve van. További információ a [szinkronizálási csoport létrehozásáról.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Győződjön meg arról, hogy a kiszolgáló csatlakozik az internethez, és hogy az Azure elérhető. A 443-as portot használjuk a szerver és a szolgáltatás közötti kommunikációhoz.

## <a name="add-a-server-endpoint"></a>Kiszolgálói végpont felvétele
Kiszolgálóvégpont hozzáadásához keresse meg a kívánt szinkronizálási csoportot, és válassza a "Kiszolgálóvégpont hozzáadása" lehetőséget.

![Új kiszolgálói végpontok hozzáadása a szinkronizálási csoport panelen](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

A **kiszolgálóvégpont hozzáadása**csoportban a következő adatokra van szükség:

- **Regisztrált kiszolgáló**: Annak a kiszolgálónak vagy fürtnek a neve, amelyen a kiszolgálóvégpontot létre szeretné hozni.
- **Elérési út**: A Windows Server szinkronizálási csoport részeként szinkronizálandó elérési útja.
- **Felhőrétegezés**: A felhőrétegezés engedélyezésére vagy letiltására irányuló kapcsoló. Ha engedélyezve van, a felhőrétegezés *rétegezi* a fájlokat az Azure-fájlmegosztások. Ez az adatkészlet teljes másolata helyett a helyszíni fájlmegosztásokat gyorsítótárlá alakítja, hogy segítsen a kiszolgáló helyhatékonyságának kezelésében.
- **Kötetszabad terület:** a kiszolgálóvégpont ot lefoglalandó szabad terület. Ha például a kötet szabad területe 50%-ra van állítva egy egyetlen kiszolgáló-végpontot rendelkező köteten, az adatok összegének nagyjából a fele rétegzett lesz az Azure Files számára. Függetlenül attól, hogy a felhőrétegezés engedélyezve van-e, az Azure-fájlmegosztás mindig rendelkezik a szinkronizálási csoportban lévő adatok teljes másolatával.

A kiszolgálóvégpont hozzáadásához válassza a **Létrehozás** gombot. A szinkronizálási csoport névterében lévő fájlok szinkronban maradnak. 

## <a name="remove-a-server-endpoint"></a>Kiszolgálóvégpont eltávolítása
Ha szeretné, hogy hagyja abba az Azure File Sync egy adott kiszolgálóvégpont, eltávolíthatja a kiszolgáló végpont. 

> [!Warning]  
> Ne próbálja meg elhárítani a szinkronizálási, felhőszintű rétegezéssel vagy az Azure File Sync bármely más aspektusával kapcsolatos problémákat a kiszolgálóvégpont eltávolításával és újbóli létrehozásával, kivéve, ha egy Microsoft-mérnök kifejezetten utasítja. A kiszolgálóvégpont eltávolítása romboló művelet, és a kiszolgálóvégponton belüli rétegzett fájlok nem lesznek "újracsatlakoztatva" az Azure-fájlmegosztáson lévő helyükre a kiszolgálóvégpont újbóli létrehozása után, ami szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálóvégpont-névtéren kívül létező rétegzett fájlok véglegesen elveszhetnek. Rétegzett fájlok létezhetnek a kiszolgáló végpontján belül, még akkor is, ha a felhőrétegezés soha nem volt engedélyezve.

Annak érdekében, hogy a kiszolgálóvégpont eltávolítása előtt az összes rétegzett fájl visszahívása megtörtént, tiltsa le a felhőrétegezést a kiszolgálóvégponton, majd hajtsa végre a következő PowerShell-parancsmagást a kiszolgálóvégpont-névtérben lévő összes rétegzett fájl visszahívásához:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
A `-Order CloudTieringPolicy` megadott fájlok először a legutóbb módosított fájlokat hívja vissza.
Egyéb választható, de hasznos paraméterek:
* `-ThreadCount`meghatározza, hogy hány fájl hívható vissza párhuzamosan.
* `-PerFileRetryCount`meghatározza, hogy milyen gyakran kísérelnek meg visszahívást egy jelenleg letiltott fájlra.
* `-PerFileRetryDelaySeconds`meghatározza a visszahívási kísérletek újrapróbálkozása közötti időt másodpercben, és mindig az előző paraméterrel együtt kell használni.

> [!Note]  
> Ha a kiszolgálót üzemeltető helyi köteten nincs elegendő szabad terület `Invoke-StorageSyncFileRecall` az összes rétegzett adat visszahívásához, a parancsmag sikertelen lesz.  

A kiszolgálóvégpont eltávolítása:

1. Nyissa meg azt a tárolószinkronizálási szolgáltatást, ahol a kiszolgáló regisztrálva van.
2. Keresse meg a kívánt szinkronizálási csoportot.
3. Távolítsa el a kívánt kiszolgálóvégpontot a Storage Sync szolgáltatás szinkronizálási csoportjában. Ez úgy valósítható meg, hogy a szinkronizálási csoport ablaktáblájában a jobb gombbal a megfelelő kiszolgálóvégpontra kattint.

    ![Kiszolgálóvégpont eltávolítása szinkronizálási csoportból](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>További lépések
- [Kiszolgáló regisztrálása/regisztrációjának megszüntetése az Azure File Sync segítségével](storage-sync-files-server-registration.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
