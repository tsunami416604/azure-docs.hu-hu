---
title: SAP Hana az Azure-ban (nagyméretű példányok) hitelesítő |} A Microsoft Docs
description: Az SAP HANA az Azure-ban (nagyméretű példányok) minősítése.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1346018d99a1c26290ad8928d9794f3b55050910
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028110"
---
# <a name="certification"></a>Tanúsítvány

Mellett a NetWeaver hitelesítő SAP használatához az SAP Hana, SAP HANA támogatásához egyes infrastruktúrákat, például az Azure IaaS speciális minősítés.

Alapvető fontosságú SAP-Jegyzetnek NetWeaver, valamint a párhuzamossági SAP HANA-minősítési program [SAP Megjegyzés #1928533 – SAP alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok](https://launchpad.support.sap.com/#/notes/1928533).

A minősítési rekordokat az SAP Hana az Azure-ban (nagyméretű példányok) egység megtalálható a [SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) hely. 

Az SAP HANA az Azure-ban (nagyméretű példányok) típusú hitelesített IaaS-platformon hely, az SAP HANA említett biztosítja a Microsoft és ügyfeleink helyezheti üzembe az alkalmazásokat nagy SAP Business Suite, az SAP BW, S/4 HANA, BW/4HANA vagy egyéb SAP HANA számítási feladatok Azure-beli SAP. A megoldás az SAP-HANA jóváhagyott dedikált hardver stamp alapul ([SAP HANA szabott data center integráció – TDI](https://scn.sap.com/docs/DOC-63140)). Ha futtatja az SAP HANA TDI-konfigurált megoldást, a hardveres infrastruktúrát használható, az összes, az SAP HANA-alapú alkalmazások (például SAP Business Suite az SAP HANA, SAP BW on SAP HANA, az S4/HANA és BW4/HANA).

Virtuális gépeken futó SAP HANA képest, ez a megoldás előnye van. Sokkal nagyobb memória kötetek biztosítja. Ez a megoldás engedélyezéséhez kell tennie a következő főbb szempontok megismerése:

- A SAP alkalmazás réteget és az SAP alkalmazások a szokásos Azure-beli hardveres stampek tárolt virtuális gépek futnak.
- Ügyfél a helyszíni infrastruktúra, adatközpontok, és alkalmazás-KözpontiTelepítés csatlakozik a cloud platform (ajánlott) expressroute-on keresztül, vagy virtuális magánhálózati (VPN). Az Active Directory és DNS is kibővíti az Azure-bA.
- Az SAP HANA-adatbázispéldányt HANA számítási futtatja az SAP HANA az Azure-ban (nagyméretű példányok). A nagyméretű szolgáltatáspéldányban be az Azure networking, csatlakoztatva van, így a HANA-példány a nagyméretű HANA-példányt futtató virtuális gépeken futó szoftver használhatják.
- Hardver-vagy SAP HANA az Azure-ban (nagyméretű példányok) dedikált hardverre, a SUSE Linux Enterprise Server vagy a Red Hat Enterprise Linux előtelepített IaaS megadott. Virtuális gépek, a további frissítések és az operációs rendszer karbantartási feladata a.
- HANA vagy SAP HANA nagyméretű HANA-példány egységeinek futtatásához szükséges minden olyan további összetevők telepítését a feladata. Minden folyamatban lévő műveletek megfelelő és az SAP HANA az Azure felügyeleti is rendelkezésre állnak a felhasználó felelőssége.
- Mellett a megoldások az itt leírtak szerint az Azure-előfizetéshez az Azure-ban (nagyméretű példányok) SAP Hana-hoz csatlakozó más összetevők is telepíthető. Példák olyan összetevők, amelyek lehetővé teszik a kommunikációt a, vagy közvetlenül az SAP HANA, helyettesítő kiszolgálókkal, például adatbázis-RDP kiszolgálók, az SAP HANA Studio, a SAP Data Services, az SAP BI forgatókönyvek esetén, vagy hálózati figyelési megoldások.
- Azure-ban, mint a nagyméretű HANA-példány biztosít magas rendelkezésre állású és vész-helyreállítási funkciók támogatása.

**Következő lépések**
- Tekintse meg [HLI rendelkezésre álló termékváltozatok](hana-available-skus.md) 