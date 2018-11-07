---
title: Azure storage használata az SQL Server biztonsági mentése és visszaállítása |} A Microsoft Docs
description: Útmutató az SQL Server biztonsági mentése az Azure Storage. SQL-adatbázis biztonsági másolatának az Azure Storage előnyeinek bemutatása.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 0fdf768008161fbb72e48dae937a4172222dbb63
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239746"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Azure Storage használata az SQL Server biztonsági mentés és visszaállítás
## <a name="overview"></a>Áttekintés
Az SQL Server 2012 SP1 CU2 kezdve most már írhat SQL Server-biztonságimásolatok közvetlenül az Azure Blob storage szolgáltatás. Ez a funkció segítségével akár biztonsági és a egy helyszíni SQL Server-adatbázisból vagy SQL Server-adatbázis egy Azure virtuális gépen az Azure Blob service visszaállítása. A felhőalapú biztonsági mentés területen biztosít előnyöket a rendelkezésre állási, korlátlan méretű telephelyen kívüli tárhelyet georeplikált és adatok, és a könnyű a felhőből. Biztonsági MENTÉSI vagy VISSZAÁLLÍTÁSI utasításokat kiadhatnak SMO vagy a Transact-SQL használatával.

Az SQL Server 2016 új képességek; jelennek meg használhat [fájlpillanatkép-biztonsági mentés](https://msdn.microsoft.com/library/mt169363.aspx) szinte azonnali biztonsági mentéseket és a rendkívül gyors helyreállítást végez.

Ez a témakör azt ismerteti, miért az Azure storage használatát az SQL biztonsági mentésekhez, és ezután ismerteti a következő összetevők kapnak szerepet. A cikk végén található források segítségével a forgatókönyvek és a további információk az SQL Server biztonsági másolat a szolgáltatás használatának megkezdéséhez.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>SQL-kiszolgáló biztonsági mentése az Azure Blob Service előnyei
Nincsenek több esetleges problémát is, amelyek biztonsági mentése az SQL Server között. Ezek a kihívások közé tartozik a storage kezelése, külső helyszínen lévő tárolási és hardveres konfigurációkra való hozzáférés a tárolási hibák. Ezek a kihívások számos az SQL Server biztonsági mentése az Azure Blob tároló szolgáltatással foglalkozik. Vegye figyelembe a következő előnyökkel jár:

* **Könnyű használat**: a biztonsági mentések tárolására az Azure-blobokban lehet egy kényelmes, rugalmas és könnyen elérhető külső helyszínen lévő beállítást. Tárolás külső helyszínen lévő létrehozása az SQL Server-biztonságimásolatok is lehet egyszerű módon, hogy módosítja a meglévő szkriptek és feladatok használata a **biztonsági MENTÉS URL-CÍMRE történő** szintaxist. Tárolás külső helyszínen lévő általában elég messze az éles adatbázis helyét, hogy egyetlen katasztrófa, ami hatással lehet a telephelyen kívül tárolt és az éles adatbázis helyén is kell lennie. Választva [georeplikáció az Azure-blobok](../../../storage/common/storage-redundancy.md), egy további, amelyek hatással lehetnek a teljes régió vészhelyzet védelmi réteget rendelkezik.
* **Biztonsági mentési, archiválási**: az Azure Blob Storage szolgáltatás egy jobb alternatívát nyújt a biztonsági mentések archiválása a gyakran használt szalagos lehetőség. A szalagos tárolás fizikai szállítása egy külső helyszínen lévő létesítményben, a mértékek az adathordozó védelme igényelhetnek. A biztonsági mentések tárolására az Azure Blob Storage biztosítja az azonnali, magas rendelkezésre állású, és a egy tartós archiválási lehetőséget.
* **Felügyelt hardveres**: nincs semmilyen hardver kezelése az Azure-szolgáltatásokkal járó többletterhelést. Azure-szolgáltatások kezelése a hardvert, és adja meg a georeplikáció a redundancia és a hardverhibák elleni védelem.
* **Korlátlan tárolási**: a közvetlen biztonsági mentést az Azure-blobok engedélyezésével fér hozzá gyakorlatilag korlátlan. Azt is megteheti biztonsági mentést egy Azure-beli virtuálisgép-lemez korlátozások vonatkoznak a mérete alapján. Az Azure virtuális gép biztonsági mentésekhez csatlakoztathat lemezeket száma korlátozva van. Ez a korlátozás egy nagyon nagy példány esetében 16 lemez és a kisebb méretű példányok kevesebb.
* **Biztonsági mentés a rendelkezésre állási**: az Azure-blobokban tárolt biztonsági másolatok bárhonnan és bármikor elérhető és könnyen elérhető lesz a visszaállítás vagy egy helyszíni SQL Server, vagy egy másik SQL Server futtatása az Azure virtuális gépként, feleslegessé téve adatbázis csatlakoztatási/leválasztási vagy letöltése, és a virtuális merevlemez csatlakoztatása.
* **Költség**: csak a használt szolgáltatás kell fizetnie. Költséghatékony telephelyen és a biztonsági mentési archív beállítás lehet. Tekintse meg a [Azure díjkalkulátorát](https://go.microsoft.com/fwlink/?LinkId=277060 "Díjkalkulátor"), és a [Azure díjszabását ismertető cikk](https://go.microsoft.com/fwlink/?LinkId=277059 "díjszabási cikk") további információt.
* **A pillanatképek tárolási**: Ha az adatbázis fájljai egy Azure-blobból, és használja az SQL Server 2016-ra, [fájlpillanatkép-biztonsági mentés](https://msdn.microsoft.com/library/mt169363.aspx) szinte azonnali biztonsági mentéseket és a rendkívül gyors helyreállítást végez.

További részletekért lásd: [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage szolgáltatás](https://go.microsoft.com/fwlink/?LinkId=271617).

A következő két szakasz vezeti be az Azure Blob storage szolgáltatás, beleértve a szükséges SQL Server-összetevőt. Fontos tudni, az összetevőket és a sikeres biztonsági mentését, és állítsa vissza az Azure Blob storage szolgáltatás kapcsolati.

## <a name="azure-blob-storage-service-components"></a>Az Azure Blob Storage szolgáltatás-összetevők
Az alábbi Azure-összetevők biztonsági mentése az Azure Blob storage szolgáltatást használják.

| Összetevő | Leírás |
| --- | --- |
| **Tárfiók** |A storage-fiók szolgál kiindulópontként a storage-szolgáltatások. Azure Blob Storage-szolgáltatás eléréséhez, először létre kell hoznia egy Azure Storage-fiókot. Azure Blob storage szolgáltatással kapcsolatos további információkért lásd: [az Azure Blob Storage szolgáltatás használata](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Tároló** |Egy tároló áll blobok csoportosítását biztosítja, és korlátlan számú BLOB tárolhatja. Írhat egy SQL Server egy Azure Blob service, a biztonsági mentési kell rendelkeznie legalább a legfelső szintű létrehozott tárolóba. |
| **Blob** |Bármilyen típusú és méretű fájl. Blobok legyenek a következő URL-formátummal: **https://[storage account].blob.core.windows.net/[container]/[blob]**. A Lapblobok kapcsolatos további információkért lásd: [Understanding Block és Lapblobok](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-összetevők
A következő SQL Server-összetevők biztonsági mentése az Azure Blob storage szolgáltatást használják.

| Összetevő | Leírás |
| --- | --- |
| **URL-cím** |Egy URL-címet adja meg egy egységes erőforrás-azonosító (URI) egy egyedi biztonsági mentési fájlt. Az URL-cím segítségével adja meg a hely és az SQL Server biztonságimásolat-fájl neve. Az URL-cím a tényleges blobba, nem csak egy tárolóban kell mutatnia. Ha a blob nem létezik, a rendszer létrehozza. Ha meglévő blobokhoz meg van adva, biztonsági MENTÉS sikertelen, kivéve, ha a > WITH FORMAT kapcsolót. Az alábbiakban egy példát az URL-címet kell megadni a BACKUP parancsból a: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS ajánlott, de nem kötelező. |
| **Hitelesítő adatok** |A csatlakozáshoz, és az Azure Blob storage szolgáltatás való hitelesítéshez szükséges adatokat a hitelesítő adat van tárolva.  Ahhoz, hogy a biztonsági másolatokat az Azure Blob vagy visszaállítási belőle, hogy az SQL Server, az SQL Server hitelesítő adatokat kell létrehozni. További információkért lásd: [SQL kiszolgálói hitelesítő adatok](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Ha másolja, és a egy biztonsági mentési fájl feltöltése az Azure Blob storage szolgáltatáshoz, kell használnia egy lap a blob típusa a tárolási lehetőségként Ha tervezi használni ezt a fájlt a visszaállítási műveleteket. VISSZAÁLLÍTÁS egy block blob írja be a hiba miatt sikertelen lesz.
> 
> 

## <a name="next-steps"></a>További lépések
1. Ha még nem rendelkezik egy Azure-fiók létrehozása. Ha éppen kipróbálja az Azure, fontolja meg a [az ingyenes próbaidőszak](https://azure.microsoft.com/free/).
2. Folytassa, amelyek végigvezetik egy storage-fiók létrehozása és a visszaállítás végrehajtása a következő oktatóanyagok egyikével.
   
   * **Az SQL Server 2014**: [oktatóanyag: SQL Server 2014 biztonsági mentése és visszaállítása a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **Az SQL Server 2016**: [oktatóanyag: a Microsoft Azure Blob storage szolgáltatás használata az SQL Server 2016-adatbázisok](https://msdn.microsoft.com/library/dn466438.aspx)
3. Tekintse át a további dokumentáció kezdve [SQL Server biztonsági mentés és helyreállítás a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj919148.aspx).

Ha bármilyen problémába ütközik, tekintse át a témakör [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás](https://msdn.microsoft.com/library/jj919149.aspx).

A többi SQL Server biztonsági mentési és visszaállítási lehetőségek, lásd: [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-backup-recovery.md).

