---
title: Egy Azure fájlszinkronizálás (előzetes verzió) kiszolgáló végpont hozzáadása |} Microsoft Docs
description: További tudnivalók az Azure-fájlok központi telepítésének tervezése során megfontolandó tényezőkről.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: wgries
ms.openlocfilehash: 93331dd936a6d7b30ca18743d2079900421b2620
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738479"
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Egy Azure fájlszinkronizálás (előzetes verzió) kiszolgáló végpont hozzáadása
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ennek érdekében a Windows-kiszolgálók átalakítása a Azure fájlmegosztás gyors gyorsítótárába. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A *végpontját* egy adott helyen jelöli meg a *regisztrált kiszolgáló*, például egy kiszolgáló vagy a kötet gyökerében az egyik mappájába. Több kiszolgáló végpont ugyanazon a köteten létezhet, ha a névterek nem átfedésben lévő (például F:\sync1 és F:\sync2). Beállíthatja felhő rétegezési egyenként az egyes kiszolgáló-végpont esetében. Ha egy kiszolgáló-végpontként szinkronizálási csoport hozzáadása egy kiszolgálón található fájlokat egy meglévő állítja be, ezeket a fájlokat egyesül többi végpont a szinkronizálási csoportban található egyéb fájlok.

Lásd: [Azure fájlszinkronizálás (előzetes verzió) telepítése](storage-sync-files-deployment-guide.md) fájlszinkronizálás Azure-végpontok telepítéséről információk.

## <a name="prerequisites"></a>Előfeltételek
Kiszolgáló-végpont létrehozása, akkor előbb ellenőrizze, hogy a következő feltételek teljesülnek: 
- A kiszolgáló Azure fájl Sync-ügynök van telepítve, és regisztrálva van. Az Azure File szinkronizálási Agent telepítési lépéseit megtalálhatók a [Register/való regisztrációt megszüntetni a kiszolgáló Azure fájlszinkronizálás (előzetes verzió)](storage-sync-files-server-registration.md) cikk. 
- Győződjön meg arról, hogy a tároló szinkronizálási szolgáltatás van telepítve. Lásd: [Azure fájlszinkronizálás (előzetes verzió) telepítése](storage-sync-files-deployment-guide.md) talál részletes tárolási szinkronizálási szolgáltatás telepítése. 
- Győződjön meg arról, hogy a szinkronizálási csoport van telepítve. Megtudhatja, hogyan [hozzon létre egy szinkronizálási csoportot](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Győződjön meg arról, hogy a kiszolgáló csatlakozik-e az internethez, és hogy elérhető Azure. Az összes kommunikáció a kiszolgáló és a szolgáltatás között a 443-as portot használjuk.

## <a name="add-a-server-endpoint"></a>A kiszolgáló végpont hozzáadása
Vegyen fel egy kiszolgáló végpontot, keresse meg a kívánt szinkronizálású csoport, és válassza a "Kiszolgáló végpont hozzáadása".

![Új kiszolgáló végpont hozzáadása a szinkronizálási ablakban](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

A következő adatszolgáltatás **server végpont hozzáadása**:

- **Regisztrált kiszolgáló**: a kiszolgáló vagy a kiszolgáló-végpont létrehozása a fürt nevét.
- **Elérési út**: a szinkronizálási csoport részeként szinkronizálnia kell a Windows Server elérési.
- **A felhő Tiering**: engedélyezni vagy letiltani a felhő egy kapcsoló rétegezéséhez. Ha engedélyezve van, a felhő rétegezési lesz *réteg* fájlok az Azure fájlmegosztások. Helyszíni fájlmegosztások ez alakít át egy gyorsítótár ahelyett, hogy az adatkészlet, teljes másolata terület hatékonyságát, a kiszolgáló kezeléséhez.
- **Szabad hely**: lefoglalni a köteten, a kiszolgáló végpont elérhető szabad terület mennyisége. Például ha a kötet szabad terület értéke 50 %-os köteten található egy egykiszolgálós végpontot, körülbelül fél adatmennyiséget fog helyezhető el a az Azure Fileshoz. Függetlenül attól, hogy a felhő rétegezéséhez engedélyezve van, az Azure fájlmegosztások mindig rendelkezik az adatok teljes másolata a szinkronizálási csoportban.

Válassza ki **létrehozása** a kiszolgáló végpont hozzáadása. A fájlok egy szinkronizálási csoport egy adott névtéren belül most folyamatosan szinkronban. 

## <a name="remove-a-server-endpoint"></a>Távolítsa el a kiszolgáló végpont
Ha a felügyelni kívánt Azure fájlszinkronizálás használatával egy adott kiszolgáló végpont felhagyni, eltávolíthatja a kiszolgáló végpont. 

> [!Warning]  
> Ne kísérelje meg elhárítása szinkronizálási, rétegezéséhez felhő vagy bármely más szempontja, hogy Azure fájlszinkronizálás eltávolításával és újbóli létrehozása a kiszolgáló végpont, kivéve, ha explicit módon utasításainak a Microsoft mérnöke. A kiszolgáló végpont eltávolítása egy felülíró műveletet, és rétegzett fájlok belül a kiszolgáló végpont nem "újra létrehozza" az Azure fájlmegosztás helyükre után a kiszolgáló végpont jön létre újra, amelyek szinkronban hibát fog okozni. Vegye figyelembe azt is, lehet, hogy a kiszolgáló végpont névtér kívül megtalálható rétegzett fájlok végleg elvesznek. Rétegzett fájlok belül a kiszolgáló végpont még akkor is, ha lehetséges, hogy léteznek felhő rétegezéséhez soha nem volt engedélyezve.

Győződjön meg arról, hogy minden rétegzett fájlok előtt van-e tartani, mielőtt eltávolítaná a kiszolgáló végpont, tiltsa le a kiszolgáló végpont rétegezéséhez felhő, és majd hajtható végre a következő PowerShell-parancsmag előhívja a kiszolgáló végpont névtéren belül minden rétegzett fájlok:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Ha a helyi köteten, amelyen a kiszolgáló nem rendelkezik elég szabad hely a rétegzett adatainak visszaírásához, a `Invoke-StorageSyncFileRecall` parancsmag futtatása sikertelen.  

A kiszolgáló végpont eltávolítása:

1. Nyissa meg a tároló szinkronizálási szolgáltatást, ha a kiszolgáló regisztrálva van-e.
2. Nyissa meg a kívánt szinkronizálású csoport.
3. Távolítsa el a kiszolgáló végpont felügyelni a szinkronizálási csoport a tároló szinkronizálási szolgáltatás. Ehhez kattintson a jobb gombbal a kívánt kiszolgálóra végpont a szinkronizálási ablakban.

    ![A kiszolgáló végpont szinkronizálási csoportból történő eltávolítása](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>További lépések
- [Regisztrálása vagy a kiszolgáló regisztrációját az Azure fájlszinkronizálás (előzetes verzió)](storage-sync-files-server-registration.md)
- [Egy Azure fájlszinkronizálás központi telepítésének tervezése](storage-sync-files-planning.md)