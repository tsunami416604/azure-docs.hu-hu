---
title: "Az Azure személyes adatok védelme a titkosítás aktívan |} Microsoft Docs"
description: "Ez a cikk segít a személyes adatok védelme az Azure használatával több része"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 31e41f9befd9319115e5d147b473756486100c6e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Az Azure titkosítási technológiák: a titkosítás aktívan személyes adatok védelme

Ez a cikk segít megértéséhez, valamint Azure titkosítási technológiák segítségével inaktív adatok védelmét.

Az inaktív adatok titkosítása elengedhetetlen az ajánlott eljárás bizalmas vagy személyes adatok védelme és a megfelelőség és az adatok adatvédelmi követelményeinek megfelelően.
Titkosítását az célja, hogy megakadályozható, hogy a támadó a titkosítatlan hozzáférjenek az adatok biztosításával adattitkosítás a lemezen.

## <a name="scenario"></a>Forgatókönyv 

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a mediterrán és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani a műveleteket. Támogatás ezeket, több kisebb körutazás sorok Olaszország, német, Dánia és az Egyesült szerzett

A vállalat a Microsoft Azure használ a felhőben tárolt vállalati adatokat. Ez lehet, hogy olyan felhasználói és/vagy alkalmazotti információkat, mint például:

- címek
- telefonszámok
- azonosító számokat
- Hitelkártya-adatokhoz

A vállalati adatok azokat, hogy szükség lenne rá részlegek számára elérhető tétele közben kell védeni az ügyfél és az alkalmazottak adatok védelme. (például a bérszámfejtési és foglalások részlegek)

A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagok nyomon követéséhez a kapcsolatokat az aktuális és korábbi ügyfelek személyes információkat is tartalmaz.

### <a name="problem-statement"></a>Probléma leírása

A vállalati adatok ezen osztályok (például a bérszámfejtési és foglalások részlegek) igénylő elérhetővé tétele közben kell védeni az ügyfelek és az alkalmazottak személyes adatok védelme. A személyes adatok kívül a vállalat által felügyelt adatközpont tárolja, és nem a vállalat fizikai ellenőrzés alatt áll.

### <a name="company-goal"></a>Vállalati cél

Többrétegű védelemmel az olyan jellegű biztonsági stratégia részeként akkor a vállalat célja, hogy győződjön meg arról, hogy minden adatforrás, amely tartalmazza a személyes adatok titkosítva legyenek, beleértve a felhőben található. Ha illetéktelen személyek a személyes adatok eléréséhez, megjelenítéséhez, nem olvasható formátumban kell lennie. Titkosítás alkalmazásakor kell lennie, vagy az átlátszó – felhasználók és rendszergazdák könnyen.

## <a name="solutions"></a>Megoldások

Azure-szolgáltatások és több inaktív személyes adatok védelméhez titkosítással technológiák biztosítása.

### <a name="azure-key-vault"></a>Azure Key Vault

[Az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) biztonságitár biztosít az Azure-szolgáltatásokat az inaktív adatok titkosításához használt kulcsok és a javasolt kulcs tárolása és kezelése megoldás. Titkosítási kulcsok kezelése fontos tárolt adatok védelme.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Miként használható az Azure Key Vault kulcsok titkosító személyes adatok védelme?

Az Azure Key Vault használatához az Azure-fiók előfizetés kell létrehoznia. Szükség Azure PowerShell telepítése. A lépések az alábbiak PowerShell-parancsmagok használatával a következőket teheti:

1. Csatlakozás az előfizetésekhez

2. Kulcstartó létrehozása

3. Kulcs vagy titkos kód hozzáadása a kulcstartóhoz

4. Regisztrálja az Azure Active Directoryban a kulcstartót használó alkalmazások

5. Engedélyezi az alkalmazások, a kulcsok vagy titkos kulcsok használata

Hozzon létre egy kulcstartót, használja a New-AzureRmKeyVault PowerShell-parancsmagot is. A tároló nevére, erőforráscsoport-név és földrajzi helyet rendeli. A tároló neve fogjuk más parancsmagokkal keresztül kulcsok kezelése. A tárolót a REST API-n keresztül használó alkalmazások a tároló URI fogja használni.

Az Azure Key Vault biztosítható egy szoftveres védelemmel ellátott kulcs, vagy importálhatja a meglévő kulcs egy. PFX-fájlt. A tárolóban lévő titkos kulcsokat (jelszó) is tárolhatja.

Hozzon létre egy kulcsot a helyi HSM-ben is, és vigye át a HSM a Key Vault szolgáltatásban nélkül a kulcs elhagyná a HSM határait.

Az Azure Key Vault használatával részletes utasításokat kövesse a [Ismerkedés az Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

Az Azure Key Vault használt PowerShell-parancsmagok listáját lásd: [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>A Windows Azure lemez titkosítása

[Az Azure lemez titkosítása a Windows és Linux IaaS virtuális gépeket](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) Azure virtuális gépeken futó aktívan személyes adatok védelmét, és jól integrálható az Azure Key Vault. Használja az Azure Disk Encryption [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) lévő Linux mind az operációs rendszer és az adatlemezek titkosítására. Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016-os, és a Windows 8 és Windows 10-ügyfelek az Azure Disk Encryption támogatott.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Miként használható az Azure Disk Encryption személyes adatok védelme?

Azure Disk Encryption használatához az Azure-fiók előfizetés kell létrehoznia. Engedélyezi a lemez titkosítása a Windows Azure és a Linux virtuális gépek, tegye a következőket:

1. Az Azure lemez titkosítási Resource Manager-sablon, a PowerShell vagy a parancssori felület (CLI) használatával lemeztitkosítás engedélyezze és adja meg a titkosítási konfiguráció. 

2. Hozzáférést biztosít az Azure platformon, a titkosítási anyagot olvasni a kulcstartót.

3. Adjon meg egy Azure Active Directory (AAD) alkalmazás azonosítóját a kulcstartót a titkosítási kulcsokat tárol írni.

Azure frissíti a virtuális gép és a kulcstároló konfigurációs, és a titkosított virtuális gépet.

Beállításakor a kulcstartót Azure Disk Encryption támogatásához, hozzáadhat egy fő titkosítási kulcs-(KEK), a nagyobb biztonság, és támogatja a titkosított virtuális gépek biztonsági mentése.

![](media/protect-personal-data-at-rest/create-key.png)

Részletes utasítások az adott telepítési forgatókönyvek és a felhasználói élmény szerepelnek [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket.](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

[Az Azure Storage szolgáltatás titkosítási (SSE) inaktív adatok](https://docs.microsoft.com/azure/storage/storage-service-encryption) segítségével, és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni. Az Azure Storage automatikusan titkosítja az adatokat, 256 bites AES titkosítással előtt Storage megőrzése, és lekérése előtt visszafejti. A szolgáltatás az Azure-BLOB és a fájlok érhető el.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Hogyan használja a Storage szolgáltatás titkosítási személyes adatok védelme?

Ahhoz, hogy a Storage szolgáltatás titkosítási, tegye a következőket:

1. Jelentkezzen be az Azure portálon.

2. Válasszon egy tárfiókot.

3. Beállítások a Blob szolgáltatás szakaszban válassza ki a titkosítás.

4. A Fájlszolgáltatás szakaszban jelölje be a titkosítás.

A titkosítási beállítás gombra kattintva engedélyezheti vagy letilthatja a Storage szolgáltatás titkosítási.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Új adatok lesznek titkosítva. A meglévő fájlokat a tárfiókban lévő adatokat nem titkosított marad.

Miután engedélyezte a titkosítás, adatok másolása a tárfiókot, a következő módszerek valamelyikével:

1. Blobok vagy a fájlok másolása a [AzCopy parancssori segédprogram](https://docs.microsoft.com/azure/storage/storage-use-azcopy).

2. [Az SMB-fájlmegosztás csatlakoztatása](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows) , például Robocopy segédprogram segítségével másolja a fájlokat.

3. Adatok másolása a blob vagy a fájl a és a blob-tároló vagy közötti tárolási fiókok [Storage Ügyfélkódtáraival például .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Használja a [Tártallózó](https://docs.microsoft.com/azure/storage/storage-explorers) blobok feltöltése a tárfiókhoz a titkosítás engedélyezve van.

### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

Transzparens Data Encryption (TDE) egy olyan szolgáltatás, amellyel az adatbázis és a kiszolgáló szintjén az adatokat titkosíthatja az SQL Azure-ban. TDE engedélyezve van az újonnan létrehozott összes adatbázisra alapértelmezés szerint. TDE hajt végre, valós idejű i/o-titkosítási és visszafejtési adatainak és naplókönyvtárainak fájlt.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Hogyan használjam TDE személyes adatok védelme?

TDE konfigurálhatja az Azure portálon keresztül, a REST API használatával, vagy a PowerShell használatával. Egy meglévő adatbázist, az Azure portál használatával TDE engedélyezéséhez a következőkre:

1. Látogasson el az Azure portálon, a <https://portal.azure.com> és bejelentkezés az Azure rendszergazdai vagy közreműködői fiókját.

2. A bal oldali szalagcím kattintson a Tallózás gombra, és kattintson az SQL-adatbázisok.

3. A bal oldali ablaktáblán kijelölt SQL adatbázisok és kattintson a felhasználói adatbázisban.

4. Az adatbázis paneljén kattintson az összes beállítást.

5. Kattintson a beállítások panelről átlátható titkosítási rész átlátható titkosítási panel megnyitásához.

6. Az adatok titkosítása panelen áthelyeznie az adatokat titkosítási gombra, és kattintson Mentés (el a lap tetején) beállítás alkalmazása. A titkosítási állapottal fog hozzávetőleges az átlátható adattitkosítást előrehaladását.

![Adatok titkosítás engedélyezése](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

A cikk található útmutatást ahhoz, hogy TDE és visszafejtése TDE védett adatbázisok és egyéb információkat [átlátható adattitkosítást az Azure SQL Database.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Összefoglalás

A vállalat az Azure felhőben tárolt személyes adatok célját érhető el. Ehhez teljes kötetek védelméhez az Azure Disk Encryption használatával. Ebbe beletartozik az operációs rendszer fájljait és az adatfájlokat, amely személyes azonosításra alkalmas adatokat és más bizalmas adatokat. Az Azure Storage szolgáltatás titkosítási blobokat és fájlok tárolt személyes adatok védelmére használható. Az Azure SQL-adatbázisban tárolt adatokat az átlátható adattitkosítási védelmet nyújt az illetéktelen elérhetővé tegyék személyes információkat.

A az Azure-adatok titkosítására használt kulcs védelme érdekében a vállalat az Azure Key Vault használja. Ez leegyszerűsíti a kulcskezelési folyamatot, és lehetővé teszi, hogy a vállalat kulcsok feletti személyes adatok titkosítása az irányítást tarthat fenn.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Azure virtuális gép titkosítása](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Az Azure Data Lake Store az adatok titkosítása](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Az Azure Cosmos DB adatbázis titkosítását](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
