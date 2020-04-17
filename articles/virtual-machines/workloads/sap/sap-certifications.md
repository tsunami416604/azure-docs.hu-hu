---
title: Microsoft Azure-tanúsítványok az SAP-hoz | Microsoft dokumentumok
description: Frissített listája az aktuális konfigurációk és az SAP-tanúsítványok az Azure platformon.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: e803da238ebdd728e772a15b0f29ae36d401a699
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536403"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>A Microsoft Azure-on futó SAP-tanúsítványok és konfigurációk

Az SAP és a Microsoft régóta dolgozik együtt egy olyan erős partnerségben, amely kölcsönös előnyökkel jár ügyfelei számára. A Microsoft folyamatosan frissíti platformját, és új minősítési adatokat küld az SAP-nak annak érdekében, hogy a Microsoft Azure a legjobb platform az SAP-munkaterhelések futtatásához. Az alábbi táblázatok ismertetik az Azure által támogatott konfigurációkat és a növekvő SAP-tanúsítványok listáját. Ez a lista egy áttekintő lista, amely itt-ott eltérhet a hivatalos SAP listáktól. A részletes adatok hozadékát a [Cikk tartalmazza: Milyen SAP-szoftverek támogatottak az Azure-telepítésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="sap-hana-certifications"></a>SAP HANA tanúsítványok
Hivatkozások:

- [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) natív Azure-beli virtuális gépek és HANA nagy példányok SAP HANA-támogatás.

| SAP-termék | Támogatott operációs rendszer | Azure-ajánlatok |
| --- | --- | --- |
| SAP HANA Developer Edition (beleértve az SQLODBC, csak ODBO-Windows, ODBC, JDBC illesztőprogramok, HANA stúdió és HANA adatbázisból álló HANA ügyfélszoftvert) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D sorozatú Virtuálisgép-család |
| Üzleti One a HANA-n | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | A GS5 ellenőrzött elérhetősége. Teljes támogatás az M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA az Azure-ban (nagy példányok) [SAP HANA minősítésű IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Lakosztály a HANA,OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagy példány) [SAP HANA minősített IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagy példány) [SAP HANA minősített IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA az Azure-ban (nagy példányok) <br /> [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Ne feledje, hogy az SAP az [SAP HANA tanúsítvánnyal rendelkező IaaS-platformokban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) a "fürtözés" kifejezést használja a "horizontális felskálázás" szinonimájaként, és NEM a magas rendelkezésre állású "fürtözés" kifejezéssel

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver tanúsítványok
A Microsoft Azure a következő SAP-termékekhez rendelkezik, a Microsoft és az SAP teljes körű támogatásával.
Hivatkozások:

- [1928533 – SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533) az összes SAP NetWeaver alapú alkalmazáshoz, beleértve az SAP TREX, az SAP LiveCache és az SAP Content Server alkalmazásokat. És az összes adatbázis, kivéve az SAP HANA.


| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gép típusok |
| --- | --- | --- | --- |
| SAP Business Suite szoftver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP ASE |A5–A11, D11–D14, DS11–DS14, DS11_v2 DS15_v2, GS1–GS5, D2s_v3 D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 M416ms_v2 M416ms_v2 M416ms_v2, |
| SAP Üzleti All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP ASE |A5–A11, D11–D14, DS11–DS14, DS11_v2 DS15_v2, GS1–GS5, D2s_v3 D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 M416ms_v2 M416ms_v2 M416ms_v2, |
| SAP BusinessObjects ÜZLETI INTELLIGENCIA | Windows |N/A |A5–A11, D11–D14, DS11–DS14, DS11_v2 DS15_v2, GS1–GS5, D2s_v3 D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 M416ms_v2 M416ms_v2 M416ms_v2, |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (csak Windows és Oracle Linux), DB2, SAP ASE |A5–A11, D11–D14, DS11–DS14, DS11_v2 DS15_v2, GS1–GS5, D2s_v3 D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M64ls, M32ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2 M416ms_v2 M416ms_v2 M416ms_v2 M416ms_v2, |

## <a name="other-sap-workload-supported-on-azure"></a>Az Azure-ban támogatott egyéb SAP-munkaterhelés

| SAP-termék | Vendég operációs rendszer | RDBMS | Virtuális gép típusok |
| --- | --- | --- | --- |
| SAP Business One az SQL Server en | Windows  | SQL Server | Minden NetWeaver tanúsítvánnyal rendelkező virtuálisgép-típus<br /> [SAP megjegyzés #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows és Linux | | Minden NetWeaver által hitelesített virtuálisgép-típus<br /> SAP Megjegyzés #2451795 |
| SAP Üzleti objektumok BI platformja | Windows és Linux | | SAP megjegyzés #2145537 |
| SAP adatszolgáltatások 4.2 | | | SAP Megjegyzés #2288344 |
| SAP Hybris Kereskedelmi Platform  | Windows | SQL Server, Oracle | Minden NetWeaver tanúsítvánnyal rendelkező virtuálisgép-típus <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP Hybris Kereskedelmi Platform  | SLES 12 vagy újabb | SAP HANA | Minden NetWeaver tanúsítvánnyal rendelkező virtuálisgép-típus <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
| SAP Hybris Kereskedelmi Platform  | RHEL 7 vagy újabb | SAP HANA | Minden NetWeaver tanúsítvánnyal rendelkező virtuálisgép-típus <br /> [Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud) |
