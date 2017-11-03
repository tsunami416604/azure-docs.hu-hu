---
title: "Azure fájlszinkronizálás (előzetes verzió) telepítése |} Microsoft Docs"
description: "Útmutató: Azure fájlszinkronizálás kezdetétől a végéig telepítése."
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
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Azure fájlszinkronizálás (előzetes verzió) telepítése
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Erősen ajánlott olvasási [Azure fájlok központi telepítésének tervezésében](storage-files-planning.md) és [Azure fájlszinkronizálás központi telepítésének tervezésében](storage-sync-files-planning.md) előtt a jelen útmutató lépéseit követve.

## <a name="prerequisites"></a>Előfeltételek
* A Storage-fiókok és az Azure fájlmegosztás, amely Azure fájlszinkronizálás telepíteni szeretné ugyanabban a régióban. További információkért lásd:
    - [Régiónkénti elérhetőség](storage-sync-files-planning.md#region-availability) Azure fájlszinkronizálás,
    - [Hozzon létre egy Tárfiókot](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) vonatkozó részletes utasításokat a Storage-fiók létrehozásával és
    - [Fájlmegosztás létrehozása](storage-how-to-create-file-share.md) vonatkozó részletes utasításokat a fájlmegosztás létrehozásához.
* Legalább egy támogatott Windows Server vagy Windows Server-fürt Azure fájlszinkronizálás-vel való szinkronizálásának. Lásd: [együttműködés a Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) további információt a Windows Server támogatott verziói.

## <a name="deploy-the-storage-sync-service"></a>A tároló szinkronizálási szolgáltatás telepítése 
A tárolási Sync szolgáltatás nem Azure fájlszinkronizálás képviselő legfelső szintű Azure-erőforrás. Tárolási szinkronizálási szolgáltatás telepítése, navigáljon a [Azure-portálon](https://portal.azure.com/), majd keresse meg a Azure fájlszinkronizálás. Ha az "Azure fájl Sync (előzetes verzió)" a keresési eredmények, válassza ki a "Létrehozás" gombra, nyissa meg a "központi telepítése tárolási Sync" lapon állítsa.

Az eredményül kapott panel kéri a következő információkat:

- **Név**: a tároló szinkronizálási szolgáltatást (előfizetésenként) egy egyedi nevet.
- **Előfizetés**: az előfizetés, amelyben a tároló szinkronizálási szolgáltatás létrehozásához. Attól függően, hogy a szervezet konfigurációs stratégia előfordulhat, hogy egy vagy több előfizetés elérésére. Egy Azure-előfizetés számlázási minden felhőszolgáltatás (például az Azure-fájlokat) a legalapvetőbb tárolója.
- **Erőforráscsoport**: erőforráscsoport egy olyan logikai csoport az Azure-erőforrások, például a Storage-fiók vagy egy tároló szinkronizálási szolgáltatás. Előfordulhat, hogy hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az Azure-fájl szinkronizálás (javasoljuk, használjon erőforráscsoportok logikailag a szervezete számára, például a HR-erőforrások vagy egy adott projekt erőforrások csoportosítása erőforrások elkülöníthetők tárolóként) .
- **Hely**: A régió, amelyben Azure fájlszinkronizálás telepíteni szeretné. Csak a támogatott régiók érhetők el ebben a listában.

A "Központi telepítése tárolási Sync" űrlap befejezését követően kattintson a "Létrehozás" gombra a tároló szinkronizálási szolgáltatás telepítése.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Windows Server rendszerű kiszolgálók előkészítése Azure fájlszinkronizálás való használatra
Ha valaha is, amellyel Azure fájlszinkronizálás, beleértve a csomópontok egy feladatátvevő fürtben használni kívánt kiszolgálón kövesse az alábbi lépéseket:

Minden olyan kiszolgálóra, beleértve a csomópontok egy feladatátvevő fürtben kíván Azure fájlszinkronizálás használni, kövesse az alábbi lépéseket:

1. Tiltsa le az Internet Explorer fokozott biztonsági beállításai. Ez csak a kezdeti kiszolgáló regisztrálása a szükséges, és lehet újra engedélyezve, miután a kiszolgáló rendelkezik már regisztrálva van.
    1. Nyissa meg a Kiszolgálókezelőt.
    2. Kattintson a **helyi kiszolgáló**:  
        ![A bal oldalon a Kiszolgálókezelő felhasználói felület "helyi kiszolgáló"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Jelölje ki a kapcsolatot a **Internet Explorer fokozott biztonsági beállítások** a Tulajdonságok alárendelt panelen:  
        ![Az "Internet Explorer fokozott biztonsági beállításai" a kiszolgáló-kezelő felhasználói felületének](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Válassza ki **ki** rendszergazdák és felhasználók az Internet Explorer fokozott biztonsági beállításai előugró ablakban:  
        ![Az Internet Explorer fokozott biztonsági beállításai pop-ablak "Ki" a kijelölt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Győződjön meg arról, hogy futtatja legalább PowerShell 5.1.\* (PowerShell 5.1 az alapértelmezett érték a Windows Server 2016-os). Ellenőrizheti a PowerShell 5.1 futtatja. \* $PSVersionTable objektum PSVersion tulajdonságának megtekintésével:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Ha a PSVersion kisebb, mint 5.1. \*, mint lesz a Windows Server 2012 R2 legtöbb telepítés esetében, ha letölti és telepíti könnyedén frissíthet [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). A megfelelő csomag letöltése és telepítése a Windows Server 2012 R2 **Win8.1AndW2K12R2 KB-os\*\*\*\*\*\*\*-x64.msu**.

3. [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Mindig ajánlott az Azure PowerShell-modulok legújabb verzióját használja.

## <a name="install-the-azure-file-sync-agent"></a>Az Azure fájl Sync-ügynök telepítése
Az Azure fájlszinkronizálás ügynök egy letölthető csomagot, mely lehetővé teszi, hogy a Windows Server szinkronizálni kell egy Azure-fájl megosztása nem. Az ügynök tölthető le: a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Miután letöltötte, kattintson duplán az MSI-csomagot az Azure fájlszinkronizálás ügynök telepítésének indításához.

> [!Important]  
> Szeretne használni az Azure fájlszinkronizálás feladatátvevő fürttel, ha az Azure fájlszinkronizálás ügynököt kell telepíteni a fürt minden csomópontján.

Azure fájlszinkronizálás ügynököt kell telepíteni a telepítőcsomagot viszonylag gyorsan túl sok további kérdések nélkül. A következőket javasoljuk:
- Hagyja alapértelmezett telepítési útvonalon `C:\Program Files\Azure\StorageSyncAgent`) egyszerűbbé teheti a hibaelhárítás és a kiszolgáló.
- Microsoft Update-hez naprakészen Azure fájlszinkronizálás engedélyezése. Minden frissítés, beleértve a szolgáltatás-frissítéseket és gyorsjavításokat, az Azure fájlszinkronizálás Agent megtörténik a Microsoft Update szolgáltatásból. Ajánlott mindig a legfrissebb Azure fájlszinkronizálás véve. Ellenőrizze a [Azure fájlszinkronizálás házirend](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) további információt.

Az Azure fájlszinkronizálás ügynöktelepítés lezárásakor a kiszolgáló regisztrációs felhasználói felület fog automatikus indítását. Ellenőrizze a következő szakaszban arról, hogyan regisztrálja ezt a kiszolgálót Azure fájl szinkronizálással.

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server regisztrálása tároló szinkronizálási szolgáltatás
A Windows-kiszolgáló regisztrálása a tárolási szinkronizálási szolgáltatás kapcsolatot hoz létre megbízhatósági – a kiszolgáló (vagy a fürt) és a tároló szinkronizálási szolgáltatást. A kiszolgáló regisztrációs felhasználói felület kell automatikus indítását a Azure fájl Sync-ügynök telepítése után, de ha nem, manuálisan is megnyithatja azt a helyéről: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Ha a kiszolgáló regisztrációs felhasználói felületén meg nyitva, kattintson **bejelentkezési** megkezdéséhez.

Bejelentkezés, után kéri a következő információkat:

![Egy a kiszolgáló regisztrációs felhasználói felületének képernyőképe](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-előfizetés**: a tároló szinkronizálási szolgáltatást tartalmazó előfizetés (lásd: [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service) fent). 
- **Erőforráscsoport**: a tároló szinkronizálási szolgáltatást tartalmazó erőforráscsoportot.
- **Tároló szinkronizálási szolgáltatás**: a tároló szinkronizálási szolgáltatás, amellyel a regisztrálni kívánt nevét.

Miután kiválasztotta a megfelelő információkkal a az vetett időszakosan megszakadó, kattintson a **regisztrálása** befejezéséhez a kiszolgáló regisztrálása. A regisztrációs folyamat részeként kéri egy további bejelentkezés.

## <a name="create-a-sync-group"></a>Szinkronizálási csoport létrehozása
A szinkronizálás csoport adja meg a sync-topológiát a fájlokat. A szinkronizálási csoporton belüli végpontok folyamatosan szinkronban vannak egymással. A szinkronizálás csoport tartalmaznia kell a felhő legalább egy végpontot, amely egy Azure fájlmegosztás jelöli, és egy kiszolgáló végpont, amely a Windows Server egy elérési utat képvisel. Szinkronizálási csoport létrehozásához keresse meg a tároló szinkronizálási szolgáltatás a a [Azure-portálon](https://portal.azure.com/), és kattintson a **+ szinkronizálású csoport**:

![Hozzon létre új szinkronizálási csoportot az Azure-portálon](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Az eredményül kapott ablaktáblán egy felhőalapú végponttal szinkronizálási csoport létrehozása a következő információkat kéri:

- **Csoportnév szinkronizálása**: létrehozni a szinkronizálási csoport nevét. Ez a név a tároló szinkronizálási szolgáltatás belül egyedieknek kell lenniük, de bármilyen nevet, amely logikai meg.
- **Előfizetés**: a tároló szinkronizálási szolgáltatást telepítette az előfizetés [a tároló szinkronizálási szolgáltatás telepítése](#deploy-the-storage-sync-service) felett.
- **A tárfiók**: kattintson a "Jelölje ki a tárfiók" pop megnyílik egy további ablaktáblán, amely lehetővé teszi, akkor válassza ki a tárfiókot, amellyel szinkronizálni szeretné Azure fájlmegosztás tartalmazó.
- **Az Azure fájlmegosztás**: az Azure-fájlmegosztás, amellyel szinkronizálni kívánt nevét.

Egy kiszolgáló végpont hozzáadásához keresse meg az újonnan létrehozott szinkronizálási csoportot, és kattintson a "Kiszolgáló végpont hozzáadása".

![Új kiszolgáló végpont hozzáadása a szinkronizálási ablakban](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Az eredményül kapott "kiszolgáló végpont hozzáadása" ablaktáblán van szükség a kiszolgáló-végpont létrehozása a következő információkat:

- **Kiszolgáló regisztrálva**: a kiszolgáló vagy fürt hozhat létre a kiszolgáló végpont nevét.
- **Elérési út**: a szinkronizálási csoport részeként szinkronizálnia kell a Windows Server elérési.
- **A felhő Tiering**: engedélyezni vagy letiltani a felhő rétegezését egy kapcsolóhoz, mely lehetővé teszi, hogy a ritkán használt vagy nem érhető el a fájlok helyezhető el a az Azure Fileshoz.
- **Szabad hely**: az ellenőrzésben szereplő szabad tárhely a köteten, amelyen a kiszolgáló végpont található lefoglalni. Például ha a kötet szabad terület méretének 50 %-os köteten található egyetlen kiszolgáló végpont van beállítva, körülbelül fél adatmennyiség fog helyezhető el a az Azure Fileshoz. Vegye figyelembe, hogy függetlenül attól, hogy a felhő rétegezéséhez engedélyezve van, az Azure fájlmegosztás mindig rendelkezik az adatok teljes másolata a szinkronizálási csoportban.

Kattintson a "Létrehozás" gombra a kiszolgáló végpont hozzáadása. A fájlok most folyamatosan szinkronban a Azure fájlmegosztás és a Windows Server között. 

> [!Important]  
> Felhő vagy a szinkronizálási csoport végpontját módosításokat végezheti el, és a fájlok a többi végpont a szinkronizálási csoportban történő szinkronizálását. Ha módosítja a felhő végpontnak (Azure fájlmegosztás) közvetlenül, vegye figyelembe, hogy módosításokat először kell egy Azure fájlszinkronizálás módosítása észlelési feladat, amely csak a Felhőbeli végpont initatiated 24 óránként egyszer észlelhetők. Tekintse meg a [Azure fájlok gyakran ismételt kérdések](storage-files-faq.md#afs-change-detection) további információt.

## <a name="next-steps"></a>Következő lépések
- [Az Azure File a szinkronizálási kiszolgáló végpont hozzáadása](storage-sync-files-server-endpoint.md)
- [A kiszolgáló Azure fájlszinkronizálás regisztrálása vagy regisztrációjának törléséhez](storage-sync-files-server-registration.md)