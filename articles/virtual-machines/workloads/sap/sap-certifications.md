---
title: Az SAP a Microsoft Azure-minősítések |} A Microsoft Docs
description: Frissített listája aktuális konfigurációkat és a tanúsítvánnyal az SAP, az Azure platformon.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: a38d5b44aaca0aa28e7ecd32543d2b0563964b3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65797443"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-tanúsítványok és a Microsoft Azure-on futó konfigurációk

SAP és a Microsoft rendelkezik, amely kölcsönös előnyt kínál az ügyfeleknek erős partnerséget együttműködése hosszú előzményeit. A Microsoft folyamatosan frissíti a hozzá tartozó platform és SAP új hitelesítő adatok elküldése a Microsoft Azure biztosítása érdekében a legjobb platform az SAP számítási feladatok futtatására. A következő táblák vázlat Azure támogatott konfigurációk és növekvő SAP-tanúsítványok listája. 

## <a name="sap-hana-certifications"></a>SAP HANA-minősítések
Hivatkozások:

- [Az SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) támogatása az SAP HANA natív Azure virtuális gépekre és nagyméretű HANA-példányok számára.

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA Developer Edition (HANA-ügyfélszoftver komponensekkel SQLODBC, ODBO – Windows csak, ODBC, JDBC-illesztőprogramok, HANA studio és HANA database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | A D-sorozat Virtuálisgép-család |
| Üzleti egy on HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Szabályozott elérhetőség a gs5 esetén. M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, teljes körű támogatása <br /> SAP HANA az Azure-ban (nagyméretű példányok) [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, az SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise BW-hez, OLAP-hoz | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA az Azure-ban (nagyméretű példányok) [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Vegye figyelembe, hogy az SAP használja-e a kifejezés "Fürtszolgáltatás" [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) , szinonima "scale-out", pedig nem fürtözés"magas rendelkezésre állás

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-minősítések
A Microsoft Azure az alábbi SAP termékekhez rendelkezik minősítéssel és teljes körű támogatással a Microsoft és az SAP részéről.
Hivatkozások:

- [1928533 - SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533) minden SAP NetWeaver-alapú alkalmazásokhoz, többek között az SAP TREX SAP LiveCache és SAP-kiszolgálóhoz. És az összes adatbázishoz, kivéve a SAP HANA.


| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP BusinessObjects BI | Windows |– |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 to A11, D11 to D14, DS11 to DS14, DS11_v2 to DS15_v2, GS1 to GS5, D2s_v3 to D64s_v3, E2s_v3 to E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Más Azure-on támogatott SAP munkaterhelés

| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business egy on SQL Server | Windows  | SQL Server | Az összes NetWeaver minősítéssel rendelkező virtuális gépek típusai<br /> [SAP Note #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows és Linux | | Összes NetWeaver minősítéssel rendelkező virtuális gép típusa<br /> SAP Note #2451795 |
| SAP Business objektumok BI platform | Windows és Linux | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris Commerce Platform 5.x and 6.x | Windows | SQL Server, Oracle | Az összes NetWeaver minősítéssel rendelkező virtuális gépek típusai<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
