---
title: "Az Azure storage használata az SQL Server biztonsági másolat és visszaállításáról |} Microsoft Docs"
description: "Útmutató: biztonsági mentés SQL Server Azure Storage. SQL-adatbázis biztonsági másolatának Azure Storage előnyeinek bemutatása."
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: d3df6b25fe524c500cf1a1333ac136e8a29d1484
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Az Azure Storage használata az SQL Server biztonsági mentése és visszaállítása
## <a name="overview"></a>Áttekintés
SQL Server 2012 SP1 CU2 verziótól kezdődően most írhat SQL Server biztonsági mentés közvetlenül az Azure Blob storage szolgáltatásban. Ez a funkció segítségével biztonsági másolat és az Azure Blob szolgáltatást, amely egy helyi SQL Server-adatbázist vagy egy Azure virtuális gépen SQL Server-adatbázis visszaállítása. A felhőalapú biztonsági mentés előnyökkel rendelkezésre állását, korlátlan georeplikált telephelytől távoli tároló és az áttelepítést a és a könnyű a felhőből. Transact-SQL vagy SMO biztonsági MENTÉSI vagy VISSZAÁLLÍTÁSI utasításokat adhatnak ki.

SQL Server 2016 vezet be az új képességek; használhat [fájlpillanatkép biztonsági mentés](http://msdn.microsoft.com/library/mt169363.aspx) meglepően gyors visszaállítások és szinte azonnali biztonsági mentés végrehajtásához.

Ez a témakör azt ismerteti, miért érdemes választani az Azure storage SQL biztonsági mentés, és majd az érintett összetevők ismertetése. A cikk végén biztosított források segítségével forgatókönyvek és a további információk az SQL Server biztonsági másolat a szolgáltatás használatának megkezdéséhez.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Az Azure Blob szolgáltatás az SQL Server biztonsági mentések használatának előnyei
Több, amikor az SQL Server biztonsági mentéséről szembesülhetnek akadályok merülnek fel. Ilyen kihívások közé tartozik a tárolási felügyeleti, tárolási hibája, hozzáférhet a telephelytől távoli tárhely és hardverkonfiguráció kockázatát. Ezek a kihívások számos szolgáltatással az Azure Blob tároló SQL Server biztonsági mentés tárgyalja. Vegye figyelembe a következő előnyöket biztosítja:

* **Könnyű használat**: a biztonsági mentések tárolására az Azure-blobokat lehet egy kényelmes, rugalmas, és könnyen elérhetők a telephelytől távoli lehetőséget. Távoli tároló létrehozása, lehet, hogy az SQL Server biztonsági mentések lehető legkönnyebben módosítja a meglévő parancsfájlok és feladatok használata a **biztonsági MENTÉS URL-CÍMRE való** szintaxis. Távoli tároló általában kell feldolgozásáig helyről az éles adatbázis, amely hatással van mindkét a helyszínen és az éles adatbázis helyen egyetlen katasztrófa megelőzése érdekében. Ha [földrajzi-replikálás az Azure-blobok](../../../storage/common/storage-redundancy.md), egy további védelmi réteg biztosítása legyen katasztrófahelyzet esetén, amely hatással lehet a teljes régióban van.
* **Biztonsági mentési archív**: az Azure Blob Storage szolgáltatást biztosít a gyakran használt szalagra elemet kell archiválnia a biztonsági mentések jobb megoldás. A szalagos tárolás lehet szükség a fizikai szállítására egy külső létesítmény és az adathordozó védelmére. A biztonsági mentések tárolására az Azure Blob Storage biztosít egy pillanat alatt elérheti, magas rendelkezésre állású, és a tartós archiválás lehetőséget.
* **Hardver felügyelt**: nincs nem növeli a hardver-kezelés az Azure-szolgáltatásokhoz. Azure-szolgáltatások kezelése a hardvert, és adja meg a georeplikáció redundancia és a hardver meghibásodása elleni védelem.
* **Korlátlan tárterület**: egy Azure-blobokat közvetlen biztonsági mentés engedélyezése, hogy hozzáfér a gyakorlatilag korlátlan tárterület. Azt is megteheti biztonsági mentés van egy Azure virtuálisgép-lemez mérete alapján korlátok. Összekapcsolhatja azt a biztonsági mentésekhez Azure virtuális gép lemezeinek száma korlátozva van. Ezt a határt egy extra nagy példány 16 lemezek és kisebb példányok kevesebb.
* **Biztonsági mentés a rendelkezésre állási**: Azure-blobokat tárolt biztonsági bárhonnan és bármikor érhetők el, és egy helyi SQL Server vagy egy másik SQL Server fut egy Azure virtuális gépen, nincs szükség adatbázis csatlakoztatási/leválasztási vagy letöltése és csatolni a virtuális Merevlemezt visszaállítást a könnyen elérhető.
* **Költség**: csak a használt szolgáltatás után kell fizetnie. Költséghatékony helyszínen és a biztonsági mentés archív lehetőségként lehet. Tekintse meg a [Azure árképzési Számológép](http://go.microsoft.com/fwlink/?LinkId=277060 "Díjkalkulátor"), és a [Azure díjszabása cikk](http://go.microsoft.com/fwlink/?LinkId=277059 "árazás cikk") további információt.
* **Storage-pillanatfelvételekkel**: Ha adatbázisfájlokat tárolja az Azure blob, és az SQL Server 2016 használ, [fájlpillanatkép biztonsági mentés](http://msdn.microsoft.com/library/mt169363.aspx) meglepően gyors visszaállítások és szinte azonnali biztonsági mentés végrehajtásához.

További részletekért lásd: [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage Service](http://go.microsoft.com/fwlink/?LinkId=271617).

A következő két szakasz az Azure Blob storage szolgáltatásban, többek között a szükséges SQL Server-összetevők telepítéséhez. Fontos megérteni az összetevők és kapcsolatukat sikeres biztonsági mentése és visszaállítása az Azure Blob storage szolgáltatásból.

## <a name="azure-blob-storage-service-components"></a>Az Azure Blob Storage szolgáltatás-összetevők
A következő Azure összetevők használt biztonsági másolatot az Azure Blob storage szolgáltatásban.

| Összetevő | Leírás |
| --- | --- |
| **Tárfiók** |A tárfiók kerül a kiindulási pont az összes tároló-szolgáltatáshoz. Egy Azure Blob Storage szolgáltatás eléréséhez hozzon létre egy Azure Storage-fiókot. Azure Blob storage szolgáltatással kapcsolatos további információkért lásd: [az Azure Blob Storage szolgáltatás használata](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Tároló** |Egy tároló blobok blobkészletek csoportosítását, és korlátlan számú BLOB tárolhatja. Egy SQL Server írni az Azure Blob szolgáltatásban történő biztonsági mentés rendelkeznie kell legalább a legfelső szintű tároló létrehozása. |
| **A BLOB** |A fájl típusát és méretét. Blobok olyan a következő URL-formátummal: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Lapblobokat kapcsolatos további információkért lásd: [Understanding Block és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-összetevők
A következő SQL Server-összetevőt az Azure Blob storage szolgáltatásban való biztonsági mentéshez használt.

| Összetevő | Leírás |
| --- | --- |
| **URL-CÍME** |Egy URL-címet adja meg egy egységes erőforrás-azonosító (URI) egy egyedi biztonságimásolat-fájl. Az URL-cím használatával adja meg a helyét és az SQL Server biztonságimásolat-fájl nevét. Az URL-cím egy tényleges blob, nem csak egy tárolót kell mutatnia. Ha a blob nem létezik, akkor jön létre. Ha egy meglévő blob van megadva, biztonsági MENTÉS sikertelen lesz, kivéve, ha a > WITH FORMAT kapcsolót. Az alábbiakban egy példa az URL-CÍMRE, a biztonsági MENTÉS parancsban lehet megadni: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS ajánlott, de nem kötelező. |
| **Hitelesítő adatok** |A csatlakozás és az Azure Blob storage szolgáltatásban való hitelesítéshez szükséges adatokat a hitelesítő adatok tárolja.  Ahhoz, hogy az SQL Server biztonsági mentések írni az Azure Blob vagy a visszaállítási belőle létre kell hozni egy SQL Server hitelesítő adatot. További információkért lásd: [SQL-kiszolgáló hitelesítési](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Ha másolja, majd töltse fel a biztonságimásolat-fájl az Azure Blob storage szolgáltatásban, kell használnia egy blob laptípus tárolási lehetőségként Ha tervezi használni ezt a fájlt a visszaállítási műveletekhez. Egy blob blokktípus való VISSZAÁLLÍTÁSA egy hibával meghiúsul.
> 
> 

## <a name="next-steps"></a>Következő lépések
1. Az Azure-fiók létrehozása, ha még nem rendelkezik. Ha az Azure értékelése, vegye figyelembe a [ingyenes próbaverzió](https://azure.microsoft.com/free/).
2. Folytassa a következő oktatóprogramot kínál, amelyek végigvezetik a tárfiók létrehozásához, és a visszaállítási folyamat végrehajtása egyikével.
   
   * **SQL Server 2014**: [oktatóanyag: SQL Server 2014 biztonsági mentéséhez és visszaállításához a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [oktatóanyag: a Microsoft Azure Blob storage szolgáltatás használata az SQL Server 2016-adatbázisok](https://msdn.microsoft.com/library/dn466438.aspx)
3. Tekintse át a dokumentációt kezdve [SQL Server biztonsági másolat és helyreállítás a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj919148.aspx).

Ha bármilyen problémába ütközik, tekintse át a témakör [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás](https://msdn.microsoft.com/library/jj919149.aspx).

Más SQL Server biztonsági mentési és visszaállítási eljárások alkalmazhatók, lásd: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md).

