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
ms.date: 07/12/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: f293adc6a25ef9e6ed916043c40233f9dd7bfbc1
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171282"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-tanúsítványok és a Microsoft Azure-on futó konfigurációk

SAP és a Microsoft rendelkezik, amely kölcsönös előnyt kínál az ügyfeleknek erős partnerséget együttműködése hosszú előzményeit. A Microsoft folyamatosan frissíti a hozzá tartozó platform és SAP új hitelesítő adatok elküldése a Microsoft Azure biztosítása érdekében a legjobb platform az SAP számítási feladatok futtatására. A következő táblák vázlat Azure támogatott konfigurációk és növekvő SAP-tanúsítványok listája. 

## <a name="sap-hana-certifications"></a>SAP HANA-minősítések
Hivatkozások:

- [Az SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) támogatása az SAP HANA natív Azure virtuális gépekre és nagyméretű HANA-példányok számára.

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA Developer Edition (HANA-ügyfélszoftver komponensekkel SQLODBC, ODBO – Windows csak, ODBC, JDBC-illesztőprogramok, HANA studio és HANA database) | Red Hat Enterprise Linux, a SUSE Linux Enterprise | A D-sorozat Virtuálisgép-család |
| Üzleti egy on HANA | SUSE Linux Enterprise | DS14_v2 <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, a SUSE Linux Enterprise | Szabályozott elérhetőség a gs5 esetén. Teljes körű támogatás M64s, M64ms, M128s, M128ms, az SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, a SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, az SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise BW-hez, OLAP-hoz | Red Hat Enterprise Linux, a SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, az SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| AZ SAP BW/4 HANA | Red Hat Enterprise Linux, a SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, az SAP HANA az Azure-ban (nagyméretű példányok) <br /> [Az SAP HANA Certified IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Vegye figyelembe, hogy az SAP használja-e a kifejezés "Fürtszolgáltatás" [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) , szinonima "scale-out", pedig nem fürtözés"magas rendelkezésre állás

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-minősítések
A Microsoft Azure az alábbi SAP termékekhez rendelkezik minősítéssel és teljes körű támogatással a Microsoft és az SAP részéről.
Hivatkozások:

- [1928533 - Azure-beli SAP alkalmazások: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533) minden SAP NetWeaver-alapú alkalmazásokhoz, többek között az SAP TREX SAP LiveCache és SAP-kiszolgálóhoz. És az összes adatbázishoz, kivéve a SAP HANA.


| SAP-termék | A vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business Suite | Windows, SUSE Linux Enterprise, a Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 – A11, D11 – D14, DS11 – DS14, DS11_v2 DS15_v2, GS1 – GS5, D2s_v3 D64s_v3, E2s_v3 E64s_v3, hogy M128ms M64s, hogy a |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, a Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 – A11, D11 – D14, DS11 – DS14, DS11_v2 DS15_v2, GS1 – GS5, D2s_v3 D64s_v3, E2s_v3 E64s_v3, hogy M128ms M64s, hogy a |
| SAP BusinessObjects BI | Windows |– |A5 – A11, D11 – D14, DS11 – DS14, DS11_v2 DS15_v2, GS1 – GS5, D2s_v3 D64s_v3, E2s_v3 E64s_v3, hogy M128ms M64s, hogy a |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, a Red Hat Enterprise Linux, Oracle Linux |Az SQL Server, Oracle (Windows és csak az Oracle Linux), a DB2, SAP ASE |A5 – A11, D11 – D14, DS11 – DS14, DS11_v2 DS15_v2, GS1 – GS5, D2s_v3 D64s_v3, E2s_v3 E64s_v3, hogy M128ms M64s, hogy a |

## <a name="other-sap-workload-supported-on-azure"></a>Más Azure-on támogatott SAP munkaterhelés

| SAP-termék | A vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business egy on SQL Server | Windows  | SQL Server | Az összes NetWeaver minősítéssel rendelkező virtuális gépek típusai<br /> [SAP-Jegyzetnek #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP CSATORNÁNKÉNT 10.01 MS SP08 | Windows és Linux | | Összes NetWeaver minősítéssel rendelkező virtuális gép típusa<br /> SAP-Jegyzetnek #2451795 |
| SAP Business objektumok BI platform | Windows és Linux | | SAP-Jegyzetnek #2145537 |
| SAP Data Services 4.2 | | | SAP-Jegyzetnek #2288344 |
| Az SAP Hybris Commerce Platform 5.x és 6.x | Windows | SQL Server, Oracle | Az összes NetWeaver minősítéssel rendelkező virtuális gépek típusai<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
