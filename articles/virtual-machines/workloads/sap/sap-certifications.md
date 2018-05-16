---
title: A Microsoft Azure tanúsítványaival kapcsolatban SAP |} Microsoft Docs
description: Frissített listáját a jelen konfigurációkat és az Azure platformon az SAP minősítései közül.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 1038f0c8a967c05ebf07849619268f22b1eb0c4b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-tanúsítványokról és a Microsoft Azure-on futó konfigurációk

SAP és a Microsoft rendelkezik egy erős együttműködve, az ügyfelek számára, a kölcsönös előnyök rendelkező együttesen működő hosszú ideje. Microsoft folyamatosan frissíti a platformját, és új hitelesítő adatait az SAP elküldése a Microsoft Azure biztosítása érdekében a legjobb platform az SAP-munkaterhelések futtatására. A következő táblák vázlat Azure támogatott konfigurációk és a növekvő számú SAP-tanúsítványok listája. 

## <a name="sap-hana-certifications"></a>SAP HANA-minősítések
Hivatkozások:

- [SAP HANA hitelesített IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) SAP HANA támogatja a natív Azure virtuális gépek és HANA nagy példányok esetében.

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA Developer Edition (beleértve a HANA ügyfélszoftver áll SQLODBC, ODBO-csak Windows, ODBC, JDBC-illesztőprogramokat, HANA studio és a HANA-adatbázis) | Red Hat Enterprise Linux SUSE Linux Enterprise | D sorozatú virtuális gép termékcsalád |
| Üzleti egy on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP HANA-S VAGY 4 | Red Hat Enterprise Linux SUSE Linux Enterprise | Ellenőrzött rendelkezésre állásának GS5, M64s, M64ms, M128s, M128ms, SAP HANA Azure (nagy példány) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux SUSE Linux Enterprise | GS5 nem éles környezetben, M64s, M64ms, M128s, M128ms, SAP HANA Azure (nagy példány) |
| HANA Enterprise BW-hez, OLAP-hoz | Red Hat Enterprise Linux SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA Azure (nagy példány) |
| SAP BW/4 HANA | Red Hat Enterprise Linux SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA Azure (nagy példány) |

Minden Azure virtuális gépeken, amennyiben SAP HANA méretezett hitelesített.

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-minősítések
A Microsoft Azure az alábbi SAP termékekhez rendelkezik minősítéssel és teljes körű támogatással a Microsoft és az SAP részéről.
Hivatkozások:

- [1928533 - SAP alkalmazások Azure: támogatott termékek és az Azure virtuális gép típusok](https://launchpad.support.sap.com/#/notes/1928533) összes SAP NetWeaver alapú alkalmazás, például a SAP TREX, SAP LiveCache és SAP-kiszolgálóhoz. És az összes adatbázis, kivéve a SAP HANA.


| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business Suite | A Windows, a SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, a GS5, a D64s_v3, a E64s_v3, hogy M128ms M64s E2s_v3 D2s_v3 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP Business All-in-One | A Windows, a SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, a GS5, a D64s_v3, a E64s_v3, hogy M128ms M64s E2s_v3 D2s_v3 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP BusinessObjects BI | Windows |N.A. |A A11, a D14, a DS14, a DS15_v2, a GS5, a D64s_v3, a E64s_v3, hogy M128ms M64s E2s_v3 D2s_v3 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP NetWeaver | A Windows, a SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, a GS5, a D64s_v3, a E64s_v3, hogy M128ms M64s E2s_v3 D2s_v3 GS1 DS11_v2 DS11 D11 a5 csomag |

## <a name="other-sap-workload-supported-on-azure"></a>Egyéb Azure támogatott SAP munkaterhelés

| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business egy on SQL Server | Windows  | SQL Server | Virtuálisgép-típusokon hitelesített összes NetWeaver |
| SAP CSATORNÁNKÉNT 10.01 MS SP08 | Windows és Linux | | Összes NetWeaver hitelesített VM típusa<br /> SAP Megjegyzés #2451795 |
| SAP Business objektumok BI platform | Windows és Linux | | SAP Megjegyzés #2145537 |
| SAP Data Services 4.2 | | | SAP Megjegyzés #2288344 |
| SAP Hybris Commerce Platform 5.x és 6.x | Windows | SQL Server, Oracle | Virtuálisgép-típusokon hitelesített összes NetWeaver<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
