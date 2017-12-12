---
title: "Azure fájlszinkronizálás (előzetes verzió) telepítése |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítése Azure fájlszinkronizálás, így tovább."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 7d6cb91f97020ad60bd2ea74b24df76511956f38
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-azure-file-sync-preview"></a>Azure fájlszinkronizálás (előzetes verzió) telepítése
Sync szolgáltatás használatával Azure fájl (előzetes verzió) központosítása fájlmegosztások a szervezet Azure fájlokban, ugyanakkor változatlanul megőrizze a rugalmasság, a teljesítmény és a kompatibilitási egy helyszíni fájlkiszolgáló. Azure fájlszinkronizálás átalakítja a Windows Server az Azure fájlmegosztás gyors gyorsítótárába. Minden protokoll, amely a Windows Server helyileg, az adatok eléréséhez használhatja, többek között a ftps-t, SMB és NFS. Akkor is annyi gyorsítótárak világszerte szükség szerint.

Határozottan javasoljuk, hogy olvassa el [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md) és [Azure fájlszinkronizálás központi telepítésének tervezésében](storage-sync-files-planning.md) a jelen cikkben ismertetett lépések végrehajtása előtt.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure Storage-fiókot és egy Azure fájlmegosztás, amely Azure fájlszinkronizálás telepíteni szeretné ugyanabban a régióban. További információkért lásd:
    - [Régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability) az Azure-fájl szinkronizálás.
    - [Hozzon létre egy tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) a storage-fiók létrehozásáról részletes leírását.
    - [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) fájlmegosztás létrehozásáról részletes leírását.
* Legalább egy támogatott Windows Server vagy a Windows Server-fürt Azure fájlszinkronizálás-vel való szinkronizálásának példányát. Windows Server támogatott verzióival kapcsolatos további információkért lásd: [együttműködés a Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>A tároló szinkronizálási szolgáltatás telepítése 
A tároló szinkronizálási szolgáltatás az Azure-fájl szinkronizálás a legfelső szintű Azure-erőforrás. Tárolási szinkronizálási szolgáltatás telepítéséhez keresse fel a [Azure-portálon](https://portal.azure.com/), kattintson a *új* és az Azure-fájl szinkronizálás kereshet. A keresési eredmények között, válassza ki **Azure fájlszinkronizálás (előzetes verzió)**, majd válassza ki **létrehozása** megnyitásához a **tárolási szinkronizáló telepítése** lapon.

A megjelenő ablakban adja meg a következő adatokat:

- **Név**: a tároló szinkronizálási szolgáltatást (előfizetésenként) egy egyedi nevet.
- **Előfizetés**: az előfizetés, amelyen létrehozásához a tároló szinkronizálási szolgáltatást. Attól függően, hogy a szervezet konfigurációs stratégia lehetséges, hogy egy vagy több előfizetés elérésére. Azure-előfizetés számlázási minden felhőszolgáltatás (például az Azure-fájlokat) a legalapvetőbb tárolója.
- **Erőforráscsoport**: erőforráscsoport egy olyan logikai csoport az Azure-erőforrások, például a storage-fiók vagy egy tároló szinkronizálási szolgáltatás. Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az Azure-fájl szinkronizálás. (Javasoljuk erőforráscsoportok tárolóként különítenie erőforrásokat logikailag a szervezete számára, például a HR-erőforrások vagy egy adott projekt erőforrások csoportosítása.)
- **Hely**: A régió, ahol telepítendő Azure fájlszinkronizálás. Csak a támogatott régiók érhetők el ebben a listában.

Ha elkészült, válassza ki a **létrehozása** a tároló szinkronizálási szolgáltatás telepítése.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server használata Azure fájlszinkronizálás előkészítése
Minden Azure fájl szinkronizálással, beleértve a csomópontok egy feladatátvevő fürtben használni kívánt kiszolgálón végezze el a következő lépéseket:

1. Tiltsa le a **Internet Explorer fokozott biztonsági beállítások**. Ez azért szükséges, csak a kezdeti kiszolgáló regisztrálása. Engedélyezheti újra azt követően a kiszolgáló regisztrálva van.
    1. Nyissa meg a Kiszolgálókezelőt.
    2. Kattintson a **helyi kiszolgáló**:  
        !["Helyi kiszolgáló" a kiszolgáló-kezelő felhasználói felületén bal oldalán](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Az a **tulajdonságok** subpane, jelölje ki a kapcsolatot a **Internet Explorer fokozott biztonsági beállítások**.  
        ![A Kiszolgálókezelő felhasználói felületén a "Internet Explorer fokozott biztonsági beállítások" ablak](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Az a **Internet Explorer fokozott biztonsági beállításai** párbeszédpanelen jelölje ki **ki** a **rendszergazdák** és **felhasználók**:  
        ![Az Internet Explorer fokozott biztonsági beállításai pop-ablak "Ki" a kijelölt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Győződjön meg arról, hogy futtatja legalább PowerShell 5.1.\* (PowerShell 5.1 az alapértelmezett érték a Windows Server 2016-os). Ellenőrizheti, hogy futnak-e PowerShell 5.1. \* értékének megnézzük a **PSVersion** tulajdonsága a **$PSVersionTable** objektum:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Ha a PSVersion érték kisebb, mint 5.1. \*, mint lesz a Windows Server 2012 R2 legtöbb telepítés esetében, ha letölti és telepíti könnyedén frissíthet [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). A megfelelő csomag letöltése és telepítése a Windows Server 2012 R2 **Win8.1AndW2K12R2 KB-os\*\*\*\*\*\*\*-x64.msu**.

3. [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Azt javasoljuk, hogy az Azure PowerShell-modulok legújabb verzióját használja.

## <a name="install-the-azure-file-sync-agent"></a>Az Azure fájl Sync-ügynök telepítése
Az Azure fájlszinkronizálás ügynök csomag egy letölthető, amely lehetővé teszi a Windows Server az Azure fájlmegosztások szinkronizálhatóak. Az ügynököt a programot letöltheti a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). A letöltés befejeződése után kattintson duplán az MSI-csomagot az Azure fájlszinkronizálás ügynök telepítésének indításához.

> [!Important]  
> Ha szeretne használni az Azure fájlszinkronizálás feladatátvevő fürttel, az Azure fájlszinkronizálás ügynököt telepíteni kell a fürt minden csomópontján.

Az Azure fájlszinkronizálás ügynök telepítési csomag viszonylag gyorsan és túl sok további kérdések nélkül kell telepíteni. Azt javasoljuk, tegye a következőket:
- Hagyja meg az alapértelmezett telepítési útvonal (C:\Program Files\Azure\StorageSyncAgent), egyszerűbbé teheti a hibaelhárítás és a kiszolgáló.
- Engedélyezheti a Microsoft Update Azure fájlszinkronizálás naprakészen tartásához. Minden frissítés Azure fájlszinkronizálás ügynökkel, beleértve a szolgáltatás-frissítéseket és gyorsjavításokat, fordulhat elő, a Microsoft Update szolgáltatásból. Azt javasoljuk, hogy a legújabb frissítés telepítése az Azure fájl Sync. További információkért lásd: [Azure fájlszinkronizálás házirend](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Ha az Azure fájl Sync-ügynök telepítése befejeződött, a kiszolgáló regisztrációs felhasználói felülete automatikusan megnyílik. Megtudhatja, hogyan kell regisztrálni a kiszolgáló Azure-fájl szinkronizálással, a következő című szakaszában talál.

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server regisztrálása tároló szinkronizálási szolgáltatás
Windows-kiszolgáló regisztrálása a tárolási szinkronizálási szolgáltatás kapcsolatot hoz létre megbízhatósági kapcsolat a kiszolgáló (vagy a fürt) és a tároló szinkronizálási szolgáltatást. A kiszolgáló regisztrációs felhasználói felülete automatikusan meg kell az Azure fájl Sync-ügynök telepítése után. Ha nem, manuálisan is megnyithatja, a fájl helyéről: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Amikor megnyílik a regisztrációs kiszolgáló felhasználói Felületét, válassza ki a **bejelentkezési** megkezdéséhez.

Miután bejelentkezik, a következő információkat kéri:

![Egy a kiszolgáló regisztrációs felhasználói felületének képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: az előfizetés, amely tartalmazza a tároló szinkronizálási szolgáltatást (lásd: [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service)). 
- **Erőforráscsoport**: a tároló szinkronizálási szolgáltatást tartalmazó erőforráscsoportot.
- **Tároló szinkronizálási szolgáltatás**: a tároló szinkronizálási szolgáltatás, amellyel a regisztrálni kívánt nevét.

Miután kiválasztotta a megfelelő adatokat, válassza ki a **regisztrálása** befejezéséhez a kiszolgáló regisztrálása. A regisztrációs folyamat részeként kéri egy további bejelentkezés.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása
A szinkronizálás csoport adja meg a sync-topológiát a fájlokat. A szinkronizálási csoporton belüli végpontok tartják szinkronban vannak egymással. A szinkronizálás csoport tartalmaznia kell legalább egy felhő végpontot, amely jelöli az Azure fájlmegosztások, és egy kiszolgáló végpont, amely a Windows Server egy elérési utat képvisel. A szinkronizálás csoport létrehozásakor a [Azure-portálon](https://portal.azure.com/)nyissa meg a tároló szinkronizálási szolgáltatással, és válassza ki **+ szinkronizálású csoport**:

![Hozzon létre egy új szinkronizálási csoportot az Azure-portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

A megnyíló párbeszédpanelen írja be a felhő végponttal szinkronizálási csoport létrehozása a következő információkat:

- **Szinkronizálási csoportnév**: létrehozni a szinkronizálási csoport nevét. Ez a név a tároló szinkronizálási szolgáltatás belül egyedieknek kell lenniük, de bármilyen nevet, amely logikai meg.
- **Előfizetés**: az előfizetést, amelybe telepítette a tároló szinkronizálási szolgáltatás a [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service).
- **A tárfiók**: Ha **válasszon tárfiókot**, egy másik ablak jelenik meg, ahol kiválaszthatja a tárfiókot, amely rendelkezik a szinkronizálni kívánt Azure fájlmegosztás.
- **Az Azure fájlmegosztás**:, amellyel szinkronizálni kívánt Azure fájlmegosztás nevét.

Vegyen fel egy kiszolgáló végpontot, nyissa meg az újonnan létrehozott szinkronizálási csoporthoz, és válassza ki **server végpont hozzáadása**.

![Új kiszolgáló végpont hozzáadása a szinkronizálási ablakban](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Az a **server végpont hozzáadása** panelen adja meg a kiszolgáló-végpont létrehozása a következő információkat:

- **Regisztrált kiszolgáló**: a kiszolgáló vagy fürt, ahol szeretne létrehozni a kiszolgáló végpont nevét.
- **Elérési út**: A Windows Server elérési útját a szinkronizálási csoport részeként szinkronizálva.
- **A felhő Tiering**: engedélyezni vagy letiltani a felhő egy kapcsoló rétegezéséhez. A felhőalapú rétegezéséhez, a ritkán használt vagy nem érhető el fájlok is helyezhető el a az Azure Fileshoz.
- **Szabad hely**: a kiszolgáló végpont tároló kötet tartalékként lefoglalni kívánt szabad terület mennyisége. Például ha a kötet szabad terület értéke 50 %-os köteten, amely egy önálló kiszolgáló végponttal rendelkezik, körülbelül fél adatmennyiség többszintű az Azure Fileshoz. Függetlenül attól, hogy a felhő rétegezéséhez engedélyezve van, az Azure fájlmegosztások mindig rendelkezik az adatok teljes másolata a szinkronizálási csoportban.

A kiszolgáló végpont hozzáadásához válassza **létrehozása**. A fájlok vannak most szinkronban legyen az Azure fájlmegosztás és a Windows Server. 

> [!Important]  
> Felhő endpoint vagy a szinkronizálási csoport végpontját módosításokat végezheti el, és a fájlok szinkronizálta már a sync csoport végpontja. Ha módosítja a felhő végpontnak (Azure fájlmegosztás) közvetlenül, módosítások először kell egy Azure fájlszinkronizálás módosítás észlelése észlelhetők. A módosítás észlelése a felhőbeli végpont 24 óránként csak egyszer indítható. További információkért lásd: [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection).

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Elosztott fájlrendszer replikációs szolgáltatása (DFS-R) központi telepítés Azure fájlszinkronizálás áttelepítése
A DFS-R-telepítés át Azure fájlszinkronizálás:

1. Hozzon létre egy szinkronizálási lecseréli a DFS-R-topológia ábrázolására.
2. Indítsa el a kiszolgálónak a teljes adatkészletet a DFS-R topológia áttelepítéséhez. Telepítse az Azure fájlszinkronizálás ezen a kiszolgálón.
3. Regisztrálja a kiszolgálót, és át kell az első kiszolgáló kiszolgáló-végpont létrehozása. Ne engedélyezze a felhő rétegezéséhez.
4. Minden az adatok szinkronizálása az Azure fájlmegosztásba (felhővégpontnak) segítségével.
5. Telepíti és regisztrálja az Azure fájlszinkronizálás ügynököt minden olyan a többi DFS-R-kiszolgálójára.
6. Tiltsa le a DFS-R. 
7. Kiszolgáló-végpont létrehozása minden olyan a DFS-R-kiszolgálójára. Ne engedélyezze a felhő rétegezéséhez.
8. Győződjön meg arról, szinkronizálás befejeződött, és tesztelje a topológia a kívánt módon működjenek.
9. A DFS-r kivonása
10. A felhő rétegezési előfordulhat, hogy minden kiszolgáló végponton tetszés szerint engedélyezni kell.

További információkért lásd: [Azure fájlszinkronizálás együttműködési az elosztott fájlrendszer (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Következő lépések
- [Hozzáadni vagy eltávolítani egy Azure-beli fájl szinkronizáló kiszolgáló végpont](storage-sync-files-server-endpoint.md)
- [Regisztrálja, vagy a kiszolgáló regisztrációját az Azure fájlszinkronizálás](storage-sync-files-server-registration.md)
