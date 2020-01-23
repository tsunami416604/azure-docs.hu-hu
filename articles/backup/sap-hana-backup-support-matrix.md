---
title: A SAP HANA biztonsági mentésének támogatási mátrixa
description: Ebben a cikkben megismerheti a támogatott forgatókönyveket és korlátozásokat, amikor az Azure Backup használatával biztonsági másolatot készít SAP HANA adatbázisokról az Azure-beli virtuális gépeken.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a0a7c25ec718dcd6a903d2149a8b3930fb25941e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514291"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure-beli virtuális gépeken tárolt SAP HANA-adatbázisok biztonsági mentésére vonatkozó támogatási mátrix

A Azure Backup támogatja SAP HANA adatbázisok biztonsági mentését az Azure-ba. Ez a cikk az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentésének Azure Backup használatával összefoglalja a támogatott forgatókönyveket és korlátozásokat.

## <a name="onboard-to-the-public-preview"></a>Bevezetés a nyilvános előzetes verzióba

A nyilvános előzetes verzióra az alábbiak szerint kell bejelentkezni:

* A portálon regisztrálja előfizetési AZONOSÍTÓját a Recovery Services szolgáltatónak a [jelen cikkben](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)leírtak szerint.
* A PowerShell esetében futtassa ezt a parancsmagot. A műveletnek "regisztrálva" kell lennie.

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> A napló biztonsági mentésének gyakorisága mostantól legalább 15 percet is igénybe vehet. A naplók biztonsági mentései csak az adatbázis sikeres teljes biztonsági mentésének befejeződése után kezdődnek.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

| **Forgatókönyv**               | **Támogatott konfigurációk**                                | **Nem támogatott konfigurációk**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topológia**               | Csak az Azure Linux rendszerű virtuális gépeken futó SAP HANA                    | HANA Large instances (HLI)                                   |
| **Térségek**                   | Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Kanada, Közép-Kanada, Kelet-japán Kelet-Ázsia, Kelet-Ázsia USA keleti régiója, USA 2. keleti régiója, az USA nyugati középső régiója, az USA nyugati régiója, USA 2. nyugati régiója, az USA középső régiója, az USA déli középső régiója, Dél-India keleti régiója Dél-Korea Közép-Korea, Észak-Európa, Nyugat-Európa Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója | Ausztrália középső régiója, Ausztrália 2. középső Kelet-Kína, Észak-Kína, Kína készletek, Észak-Kína 2 Nyugat-India Franciaország középső régiója, Dél-Kanada Észak-Németország, Középnyugat-Németország Észak-Svájc, Nyugat-Svájc Dél-Afrika északi régiója, Dél-Afrika nyugati régiója, Észak-Európa, Egyesült Arab Emírségek l Azure Government régiók |
| **OPERÁCIÓSRENDSZER-verziók**            | SLES 12 SP2, SP3 vagy SP4 szervizcsomaggal           | SLES 15, RHEL                                                |
| **HANA-verziók**          | SDC on HANA 1. x, MDC on HANA 2. x < = SPS04 Rev 44           | -                                                            |
| **HANA-telepítések**       | SAP HANA egyetlen Azure-beli virtuális gépen – csak vertikális felskálázással               | Kiterjesztés                                                    |
| **HANA-példányok**         | Egyetlen SAP HANA példány egyetlen Azure-beli virtuális gépen – csak vertikális felskálázás | Több SAP HANA példány egyetlen virtuális gépen                  |
| **HANA-adatbázisok típusai**    | Önálló adatbázis tároló (SDC) 1. x, több adatbázisból álló tárolón (MDC) a 2. x számítógépen | MDC a HANA 1. x-ben                                              |
| **HANA-adatbázis mérete**     | 2 – TB teljes biztonsági mentési méret a HANA által jelentett módon |                                                              |
| **Biztonsági másolatok típusai**           | Teljes, különbözeti és naplózott biztonsági másolatok                           | Növekményes, Pillanatképek                                       |
| **Visszaállítási típusok**          | A támogatott visszaállítási típusokról a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzésben tájékozódhat. |                                                              |
| **Biztonsági mentési korlátok**          | Akár 2 TB teljes biztonsági mentési méret SAP HANA példányban  |                                                              |
| **Speciális konfigurációk** |                                                              | SAP HANA + dinamikus rétegek <br>  Klónozás a láma használatával            |

------

> [!NOTE]
> A SAP HANA natív ügyfelek (SAP HANA Studio/cockpit/DBA pilótafülke) által végzett biztonsági mentési és visszaállítási műveletek jelenleg nem támogatottak.



## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést SAP HANA Azure-beli virtuális gépeken futó adatbázisokról](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Ismerje meg, hogyan [állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Megtudhatja, hogyan [kezelheti SAP HANA-adatbázisok biztonsági mentését a Azure Backup használatával](sap-hana-db-manage.md)
* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot) felmerülő gyakori problémákat
