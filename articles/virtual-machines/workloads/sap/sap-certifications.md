---
title: "A Microsoft Azure tanúsítványaival kapcsolatban SAP |} Microsoft Docs"
description: "Frissített listáját a jelen konfigurációkat és az Azure platformon az SAP minősítései közül."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: e4d5c78299903659a18aa9b8d04495e215bcca0d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-tanúsítványokról és a Microsoft Azure-on futó konfigurációk

SAP és a Microsoft rendelkezik egy erős együttműködve, az ügyfelek számára, a kölcsönös előnyök rendelkező együttesen működő hosszú ideje. Microsoft folyamatosan frissíti a platformját, és új hitelesítő adatait az SAP elküldése a Microsoft Azure biztosítása érdekében a legjobb platform az SAP-munkaterhelések futtatására. Az alábbi táblázatok felsorolják a támogatott konfigurációk és növekvő tanúsítványok listája. 

## <a name="sap-hana-certifications"></a>SAP HANA-minősítések

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA Developer Edition (beleértve a HANA ügyfélszoftver áll SQLODBC, ODBO-csak Windows, ODBC, JDBC-illesztőprogramokat, HANA studio és a HANA-adatbázis) |Red Hat Enterprise Linux SUSE Linux Enterprise | D sorozatú virtuális gép termékcsalád |
| HANA One |Red Hat Enterprise Linux SUSE Linux Enterprise |DS14 v2 (az általános forgalomba hozatal időpontjától) |
| SAP HANA-S VAGY 4 |Red Hat Enterprise Linux SUSE Linux Enterprise |Ellenőrzött rendelkezésre állásának GS5, SAP HANA Azure (nagy példány) |
| Suite on HANA, OLTP |Red Hat Enterprise Linux SUSE Linux Enterprise |Nem éles környezetben, egy csomópontos rendszerekhez GS5 SAP HANA Azure (nagy példány) |
| HANA Enterprise BW-hez, OLAP-hoz |Red Hat Enterprise Linux SUSE Linux Enterprise |Egy csomópontra történő telepítés esetén GS5 SAP HANA Azure (nagy példány) |
| SAP BW/4 HANA |Red Hat Enterprise Linux SUSE Linux Enterprise |Egy csomópontra történő telepítés esetén GS5 SAP HANA Azure (nagy példány) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-minősítések
A Microsoft Azure az alábbi SAP termékekhez rendelkezik minősítéssel és teljes körű támogatással a Microsoft és az SAP részéről.

| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gépek típusa |
| --- | --- | --- | --- |
| SAP Business Suite |A Windows, a SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, hogy GS5 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, hogy GS5 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP BusinessObjects BI |Windows |N/A |A A11, a D14, a DS14, a DS15_v2, hogy GS5 GS1 DS11_v2 DS11 D11 a5 csomag |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, az Oracle (Windows, és csak az Oracle Linux), a DB2, SAP ASE |A A11, a D14, a DS14, a DS15_v2, hogy GS5 GS1 DS11_v2 DS11 D11 a5 csomag |
