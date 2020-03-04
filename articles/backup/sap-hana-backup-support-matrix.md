---
title: A SAP HANA biztonsági mentésének támogatási mátrixa
description: Ebben a cikkben megismerheti a támogatott forgatókönyveket és korlátozásokat, amikor az Azure Backup használatával biztonsági másolatot készít SAP HANA adatbázisokról az Azure-beli virtuális gépeken.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255978"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure-beli virtuális gépeken tárolt SAP HANA-adatbázisok biztonsági mentésére vonatkozó támogatási mátrix

A Azure Backup támogatja SAP HANA adatbázisok biztonsági mentését az Azure-ba. Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentésének Azure Backup használatával összefoglalja a támogatott forgatókönyveket és korlátozásokat.

> [!NOTE]
> A napló biztonsági mentésének gyakorisága mostantól legalább 15 percet is igénybe vehet. A naplók biztonsági mentései csak az adatbázis sikeres teljes biztonsági mentésének befejeződése után kezdődnek.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

| **Forgatókönyv**               | **Támogatott konfigurációk**                                | **Nem támogatott konfigurációk**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topológia**               | Csak az Azure Linux rendszerű virtuális gépeken futó SAP HANA                    | HANA Large instances (HLI)                                   |
| **Térségek**                   | **GA**<br> **Amerikai** Egyesült Államok, USA középső régiója, USA 2. keleti régiója, az USA keleti régiója, az USA északi középső régiója, az USA déli középső régiója, USA 2. nyugati régiója, USA nyugati középső régiója, Nyugat-Kanada <br> **Ázsia és a csendes-óceáni térség** – Ausztrália középső régiója, Ausztrália középső régiója 2, Kelet-Ausztrália, Délkelet-Ausztrália, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-korea, Kelet-Ázsia, Délkelet-Ázsia, Közép-India, Dél-India, Nyugat-india, Kelet-Kína, Észak-Kína, Kína készletek, Észak-Kína 2 <br> **Európa** – Nyugat-Európa, Észak-Európa, Közép-franciaország, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Észak-Németország, Középnyugat-Németország, Észak-Svájc, Nyugat-Svájc, központi Észak-Svájc <br> **Afrika/Me** – Dél-Afrika, Észak-Afrika, Nyugat-Európa, Észak-Európa, Egyesült Arab Emírségek középső régiója  <BR>  **Azure Government régiók** | Dél-Franciaország, Közép-Németország, Északkelet-Németország, US Gov IOWA |
| **OPERÁCIÓSRENDSZER-verziók**            | SLES: 12 SP2, SP3 vagy SP4; SLES 15 SP1                              | RHEL                                                |
| **HANA-verziók**          | SDC on HANA 1. x, MDC on HANA 2. x < = SPS04 Rev 46       | -                                                            |
| **HANA-telepítések**       | SAP HANA egyetlen Azure-beli virtuális gépen – csak vertikális felskálázással. <br><br> A magas rendelkezésre állású központi telepítések esetében a két különböző gép csomópontjait különálló, külön adatláncú csomópontként kezeli a rendszer.               | Vertikális felskálázás <br><br> Magas rendelkezésre állású telepítések esetén a biztonsági mentés automatikusan nem végzi el a feladatátvételt a másodlagos csomópontra. A biztonsági mentés konfigurálását külön kell elvégezni az egyes csomópontokon.                                           |
| **HANA-példányok**         | Egyetlen SAP HANA példány egyetlen Azure-beli virtuális gépen – csak vertikális felskálázás | Több SAP HANA példány egyetlen virtuális gépen                  |
| **HANA-adatbázisok típusai**    | Önálló adatbázis tároló (SDC) 1. x, több adatbázisból álló tárolón (MDC) a 2. x számítógépen | MDC a HANA 1. x-ben                                              |
| **HANA-adatbázis mérete**     | 2 – TB teljes biztonsági mentési méret a HANA által jelentett módon                   |                                                              |
| **Biztonsági másolatok típusai**           | Teljes, különbözeti és naplózott biztonsági másolatok                          | Növekményes, Pillanatképek                                       |
| **Visszaállítási típusok**          | A támogatott visszaállítási típusokról a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzésben tájékozódhat. |                                                              |
| **Biztonsági mentési korlátok**          | Akár 2 TB teljes biztonsági mentési méret SAP HANA példányban         |                                                              |
| **Speciális konfigurációk** |                                                              | SAP HANA + dinamikus rétegek <br>  Klónozás a láma használatával        |

------

> [!NOTE]
> A SAP HANA natív ügyfelek (SAP HANA Studio/cockpit/DBA pilótafülke) által végzett biztonsági mentési és visszaállítási műveletek jelenleg nem támogatottak.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) felmerülő gyakori problémákat
