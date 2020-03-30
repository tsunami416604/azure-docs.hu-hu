---
title: A SAP HANA biztonsági mentésének támogatási mátrixa
description: Ebben a cikkben ismerje meg a támogatott forgatókönyvek és korlátozások, amikor az Azure biztonsági mentés segítségével az Azure HANA-adatbázisok biztonsági mentését az Azure virtuális gépeken.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252440"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure-beli virtuális gépeken tárolt SAP HANA-adatbázisok biztonsági mentésére vonatkozó támogatási mátrix

Az Azure Backup támogatja az SAP HANA-adatbázisok azure-beli biztonsági mentését. Ez a cikk összegzi a támogatott forgatókönyvek és korlátozások jelen, amikor az Azure Backup használatával az Sap HANA-adatbázisok biztonsági mentését az Azure virtuális gépeken.

> [!NOTE]
> A naplóbiztonsági mentés gyakorisága most már legalább 15 percre állítható be. A naplóbiztonsági mentések csak az adatbázis sikeres teljes biztonsági mentésének befejezése után kezdődnek meg.

## <a name="scenario-support"></a>Forgatókönyv támogatása

| **Forgatókönyv**               | **Támogatott konfigurációk**                                | **Nem támogatott konfigurációk**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topológia**               | Az SAP HANA csak az Azure Linux virtuális gépeken fut                    | HANA nagy példányok (HLI)                                   |
| **Geos között**                   | **Ga:**<br> **Amerika** – USA középső régiója, USA keleti régiója 2, USA keleti régiója, USA középső középső régiója, USA nyugati középső régiója 2, USA nyugati középső régiója, USA nyugati régiója, Kanada középső régiója, Kanada keleti régiója, Dél-Brazília <br> **Ázsia csendes-óceáni** – Ausztrália Közép-, Ausztrália Középső 2, Ausztrália Kelet, Ausztrália Délkelet, Japán Kelet, Japán Nyugat, Korea Central, Korea Dél-, Kelet-Ázsia, Délkelet-Ázsia, Közép-India, Dél-India, Nyugat-India, Kína Kelet-, Kína Észak-, Kína Kelet2, Kína Észak 2 <br> **Európa** – Nyugat-Európa, Észak-Európa, Franciaország Közép-, Egyesült Királyság déli, Egyesült Királyság nyugati, észak-németországi, németországi nyugati középső, észak- és északnyugati, nyugat-, észak-svájci középső <br> **Afrika / ME** - Dél-Afrika Észak, Dél-Afrika Nyugat, Egyesült Arab Emírségek Észak, Egyesült Arab Emírségek központi  <BR>  **Azure Government-régiók** | Franciaország Dél, Németország Közép-, Németország északkeleti, US Gov IOWA |
| **Operációs rendszer verziói**            | SLES 12 SP2, SP3 vagy SP4 szervizcsomaggal; SLES 15 SP1 szervizcsomaggal                              | RHEL                                                |
| **HANA verziók**          | SDC a HANA 1.x,MDC hana 2.x <= SPS04 Rev 46       | -                                                            |
| **HANA-telepítések**       | SAP HANA egyetlen Azure-beli virtuális gépen – csak a felskálázás. <br><br> A magas rendelkezésre állású központi telepítések esetén a két különböző gépen lévő mindkét csomópontot külön adatláncokkal rendelkező különálló csomópontokként kezeli a rendszer.               | Bővítés <br><br> Magas rendelkezésre állású telepítések esetén a biztonsági mentés nem feladatátvételt biztosít a másodlagos csomópontra automatikusan. A biztonsági mentés konfigurálását minden csomóponthoz külön kell elvégezni.                                           |
| **HANA-példányok**         | Egyetlen SAP HANA-példány egyetlen Azure virtuális számítógépen – csak a felskálázás | Több SAP HANA-példány egyetlen virtuális gépen                  |
| **HANA adatbázistípusok**    | Egyetlen adatbázis-tároló (SDC) ON 1.x, multi-database container (MDC) on 2.x | MDC a HANA 1.x-ben                                              |
| **HANA adatbázis mérete**     | 2-TB teljes biztonsági mentési méret a HANA jelentése szerint)                   |                                                              |
| **Biztonsági másolat típusai**           | Teljes, különbözeti és naplóbiztonsági mentések                          | Növekményes, Pillanatképek                                       |
| **Visszaállítási típusok**          | A támogatott visszaállítási típusokról az SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) megjegyzése című dokumentumban olvashat. |                                                              |
| **Biztonsági mentési korlátok**          | Sap HANA-példányonként legfeljebb 2 TB teljes biztonsági mentési méret         |                                                              |
| **Speciális konfigurációk** |                                                              | SAP HANA + dinamikus rétegezés <br>  Klónozás a LaMa-n keresztül        |

------

> [!NOTE]
> Az SAP HANA natív ügyfelek (SAP HANA Studio/ Cockpit/ DBA Cockpit) biztonsági mentési és visszaállítási műveletei jelenleg nem támogatottak.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [lehet biztonsági másolatot készíteni az Azure virtuális gépeken futó SAP HANA-adatbázisokról](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Megtudhatja, hogyan [állíthatja vissza az Azure virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan kezelheti az Azure Backup használatával biztonsági mentést készítő [SAP HANA-adatbázisokat](sap-hana-db-manage.md)
* Ismerje meg, hogyan [háríthatók el az SAP HANA-adatbázisok biztonsági mentésekor felmerülő gyakori problémák](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
