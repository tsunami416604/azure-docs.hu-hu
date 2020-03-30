---
title: Az Azure storage használata az SQL Server biztonsági mentéséhez és visszaállításához | Microsoft dokumentumok
description: Ismerje meg, hogyan biztonsági másolatot az SQL Server ről az Azure Storage-ba. Az SQL-adatbázisok Azure Storage-ba történő biztonsági mentésének előnyeit ismerteti.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101894"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Az Azure Storage használata az SQL Server biztonsági mentéséhez és helyreállításához
## <a name="overview"></a>Áttekintés
Az SQL Server 2012 SP1 CU2 szervizcsomaggal kezdve az SQL Server biztonsági mentéseit közvetlenül az Azure Blob storage szolgáltatásba is írhat. Ezzel a funkcióval biztonsági másolatot kaphat az Azure Blob szolgáltatásról egy helyszíni SQL Server-adatbázissal vagy egy Azure virtuális gépen lévő SQL Server-adatbázissal. A felhőbe való biztonsági mentés a rendelkezésre állás előnyeit, a korlátlan georeplikált külső tárhelyet, valamint az adatok felhőbe és a felhőből történő egyszerű áttelepítését kínálja. A BIZTONSÁGI mentési vagy visszaállítási utasításokat a Transact-SQL vagy az SMO használatával állíthatja ki.

Az SQL Server 2016 új képességeket vezet be; a [fájlpillanatkép biztonsági mentésének](https://msdn.microsoft.com/library/mt169363.aspx) segítségével szinte azonnali biztonsági mentéseket és hihetetlenül gyors visszaállításokat végezhet.

Ez a témakör ismerteti, hogy miért érdemes az Azure storage-t használni az SQL biztonsági mentések, majd ismerteti az érintett összetevőket. A cikk végén található erőforrások segítségével hozzáférhet a forgatókönyvekhez, és további információkat is elindíthat ja a szolgáltatás SQL Server biztonsági másolatokkal való használatához.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Az Azure Blob Service sql server biztonsági mentések esetén való használatának előnyei
Az SQL Server biztonsági mentésekor számos kihívással kell szembenéznie. Ezek a kihívások közé tartozik a tárolás kezelése, a tárolási hiba kockázata, a külső tárhelyhez való hozzáférés és a hardverkonfiguráció. A kihívások közül sokat az Azure Blob store szolgáltatás SQL Server biztonsági mentések használatával oldanak meg. Vegye figyelembe a következő előnyöket:

* **Egyszerű használat:** A biztonsági mentések Azure blobokban való tárolása kényelmes, rugalmas és könnyen elérhető külső beállítás lehet. Az SQL Server biztonsági mentései számára külső tárhely létrehozása olyan egyszerű lehet, mint a meglévő parancsfájlok/feladatok módosítása a **BIZTONSÁGI másolat URL-szintaxisának** használatához. A telephelyen kívüli tárolásnak általában elég messze kell lennie az éles adatbázis helyétől ahhoz, hogy megakadályozza az egyetlen katasztrófát, amely hatással lehet mind a telephelyen kívüli, mind az éles adatbázis helyeire. Az [Azure-blobok földrajzi replikálására](../../../storage/common/storage-redundancy.md)választva rendelkezik egy további védelmi réteggel katasztrófa esetén, amely az egész régiót érintheti.
* **Biztonsági mentés archiválása:** Az Azure Blob Storage szolgáltatás jobb alternatívát kínál a gyakran használt szalagos beállítással a biztonsági mentések archiválására. A szalagos tárolás hoz szükség lehet fizikai szállításra egy külső létesítménybe, és intézkedéseket az adathordozó védelmére. A biztonsági mentések azure Blob Storage-ban való tárolása azonnali, magas rendelkezésre állású és tartós archiválási lehetőséget biztosít.
* **Felügyelt hardver**: Az Azure-szolgáltatások kal nem áll fenn hardverkezelés többletterhelése. Az Azure-szolgáltatások kezelik a hardvert, és georeplikációt biztosítanak a redundancia és a hardverhibák elleni védelem érdekében.
* **Korlátlan tárhely:** Az Azure-blobok közvetlen biztonsági mentésének engedélyezésével gyakorlatilag korlátlan tárhelyhez férhet hozzá. Azt is megteheti, hogy egy Azure virtuálisgép-lemez biztonsági mentése a gép mérete alapján korlátozza a biztonsági mentést. Az Azure virtuális gépekhez biztonsági mentéshez csatolható lemezek száma korlátozva van. Ez a korlát 16 lemez egy extra nagy példány, és kevesebb a kisebb példányok.
* **Biztonsági mentés elérhetősége:** Az Azure-blobokban tárolt biztonsági mentések bárhonnan és bármikor elérhetők, és könnyen elérhetők a helyszíni SQL Server vagy egy Azure virtuális gépen futó másik SQL Server-kiszolgálóra való visszaállításhoz anélkül, hogy adatbázis-csatolásra/leválasztásra, illetve a virtuális merevlemez letöltésére és csatolására lenne szükség.
* **Költség**: Csak a használt szolgáltatásért kell fizetni. Lehet költséghatékony, mint egy off-site és biztonsági archívum lehetőséget. További információkért tekintse meg az [Azure díjkalkulátorát](https://go.microsoft.com/fwlink/?LinkId=277060 "Díjkalkulátor")és az [Azure díjszabási cikkét.](https://go.microsoft.com/fwlink/?LinkId=277059 "Árképzési cikk")
* **Tárolási pillanatképek:** Ha az adatbázisfájlok egy Azure blobban vannak tárolva, és az SQL Server 2016-ot használja, [a fájlpillanatkép biztonsági mentésével](https://msdn.microsoft.com/library/mt169363.aspx) szinte azonnali biztonsági mentést és hihetetlenül gyors visszaállításokat végezhet.

További információt az [SQL Server biztonsági másolat és visszaállítás az Azure Blob Storage Szolgáltatással című témakörben talál.](https://go.microsoft.com/fwlink/?LinkId=271617)

A következő két szakasz bemutatja az Azure Blob storage szolgáltatást, beleértve a szükséges SQL Server-összetevőket. Fontos megérteni az összetevők és azok kölcsönhatását a biztonsági mentés és visszaállítás sikeres használatához az Azure Blob storage szolgáltatásból.

## <a name="azure-blob-storage-service-components"></a>Az Azure Blob Storage szolgáltatás összetevői
A következő Azure-összetevők az Azure Blob storage-szolgáltatásbiztonsági mentésekor használatosak.

| Összetevő | Leírás |
| --- | --- |
| **Tárfiók** |A tárfiók az összes tárolási szolgáltatás kiindulópontja. Az Azure Blob Storage-szolgáltatás eléréséhez először hozzon létre egy Azure Storage-fiókot. Az Azure Blob storage szolgáltatásról az Azure Blob Storage Szolgáltatás használata című témakörben talál további [információt.](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Konténer** |Egy tároló blobok készletének csoportosítását biztosítja, és korlátlan számú blobot tárolhat. SQL Server biztonsági mentés írásához egy Azure Blob szolgáltatás, legalább a gyökértároló létre kell hoznia. |
| **Blob** |Bármilyen típusú és méretű fájl. A blobok címezhetőek a következő URL-formátumban: **https://[storage account].blob.core.windows.net/[container]/[blob]**. A lapblobokról a [Blokk- és lapblobok ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx) című témakörben talál további információt. |

## <a name="sql-server-components"></a>SQL Server-összetevők
A következő SQL Server-összetevők az Azure Blob storage szolgáltatásbiztonsági mentésekor használatosak.

| Összetevő | Leírás |
| --- | --- |
| **Url** |Az URL-cím egy egyedi biztonságimásolat-fájl hoz megadott egységes erőforrás-azonosítót (URI). Az URL-cím az SQL Server biztonságimásolat-fájl helyének és nevének megadására szolgál. Az URL-címnek egy tényleges blobra kell mutatnia, nem csak egy tárolóra. Ha a blob nem létezik, jön létre. Ha egy meglévő blob van megadva, backup sikertelen lesz, kivéve, ha a > FORMAT beállítás meg van adva. Az alábbi példa a BACKUP parancsban megadott URL-címet mutat be: **http[s]://[storageaccount]blob.core.windows.net/[container]/[FILENAME.bak]**. Https ajánlott, de nem szükséges. |
| **Megbízólevél** |Az Azure Blob storage-szolgáltatáshoz való csatlakozáshoz és hitelesítéshez szükséges adatok hitelesítő adatként tárolódnak.  Annak érdekében, hogy az SQL Server biztonsági mentéseket írjon egy Azure Blobba, vagy visszaállíthassa azt, létre kell hozni a SQL Server hitelesítő adatait. További információt az SQL Server hitelesítő adatai című [témakörben talál.](https://msdn.microsoft.com/library/ms189522.aspx) |

> [!NOTE]
> Az Sql Server 2016 frissítve lett a blokkblobok támogatásához. További részletekért olvassa el [az oktatóanyag: A Microsoft Azure Blob storage szolgáltatás használata SQL Server 2016-os adatbázisokkal.](https://msdn.microsoft.com/library/dn466438.aspx)
> 
> 

## <a name="next-steps"></a>További lépések
1. Hozzon létre egy Azure-fiókot, ha még nem rendelkezik ilyen. Ha az Azure-t értékeli, fontolja meg az [ingyenes próbaverziót.](https://azure.microsoft.com/free/)
2. Ezután menjen végig az alábbi oktatóanyagok egyikén, amelyek végigvezetik a tárfiók létrehozásán és a visszaállítás végrehajtásán.
   
   * **SQL Server 2014**: [Oktatóanyag: SQL Server 2014 Biztonsági másolat készítésés visszaállítása a Microsoft Azure Blob Storage Service szolgáltatásba](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Oktatóanyag: A Microsoft Azure Blob storage szolgáltatás használata SQL Server 2016-adatbázisokkal](https://msdn.microsoft.com/library/dn466438.aspx)
3. Tekintse át az SQL Server biztonsági másolat és visszaállítás segédprogrammal kezdődő további dokumentációt [a Microsoft Azure Blob Storage Service szolgáltatással.](https://msdn.microsoft.com/library/jj919148.aspx)

Ha bármilyen problémája van, olvassa el az SQL Server biztonsági másolat című témakört az [URL-lel kapcsolatos gyakorlati tanácsok és hibaelhárítás című témakörben.](https://msdn.microsoft.com/library/jj919149.aspx)

Az SQL Server további biztonsági mentési és visszaállítási beállításairól az [SQL Server biztonsági mentése és visszaállítása az Azure virtuális gépeken című témakörben található.](virtual-machines-windows-sql-backup-recovery.md)

