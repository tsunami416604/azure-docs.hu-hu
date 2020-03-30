---
title: AZ SAP HANA hitelesítése az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA tanúsítványa az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617231"
---
# <a name="certification"></a>Tanúsítvány

A NetWeaver minősítés mellett az SAP-nak speciális tanúsítványra van szüksége az SAP HANA számára bizonyos infrastruktúrákon, például az Azure IaaS-on való támogatásához.

Az alapvető SAP-feljegyzés a NetWeaveren, és bizonyos mértékig az SAP HANA minősítés az [SAP Note #1928533 – SAP-alkalmazások az Azure-ban: Támogatott termékek és Az Azure virtuálisgép-típusok.](https://launchpad.support.sap.com/#/notes/1928533)

Az SAP HANA az Azure-ban (nagy példányok) egységek az [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) webhelyen található. 

Az SAP HANA az Azure-beli (nagy példányok) típusok, az SAP HANA minősítésű IaaS-platformok webhely, a Microsoft és az SAP-ügyfelek számára lehetővé teszi a nagy SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA vagy más SAP HANA számítási feladatok az Azure-ban. A megoldás az SAP-HANA tanúsítvánnyal rendelkező dedikált hardverbélyegzőn[(SAP HANA szabott adatközpont-integráció – TDI)](https://scn.sap.com/docs/DOC-63140)alapul. Ha egy SAP HANA TDI-konfigurált megoldás, az összes SAP HANA-alapú alkalmazások (például az SAP Business Suite SAP HANA, SAP BW SAP HANA, S4/HANA és BW4/HANA) működik a hardveres infrastruktúra.

Az SAP HANA virtuális gépeken való futtatásához képest ez a megoldás előnyt élvez. Sokkal nagyobb memóriamennyiséget biztosít. A megoldás engedélyezéséhez a következő kulcsfontosságú szempontokat kell megértenie:

- Az SAP-alkalmazásréteg és a nem SAP-alkalmazások a szokásos Azure-hardverbélyegzőkben üzemeltetett virtuális gépeken futnak.
- Az ügyfelek helyszíni infrastruktúrája, adatközpontjai és alkalmazás-üzembe helyezései expressroute-on (ajánlott) vagy virtuális magánhálózaton (VPN) keresztül kapcsolódnak a felhőplatformhoz. Az Active Directory és a DNS is ki vannak bővítve az Azure-ba.
- Az SAP HANA-adatbázis-példány hana számítási feladatok sap hana az Azure-ban (nagy példányok) fut. A nagy példány bélyegzője csatlakozik az Azure-hálózatba, így a virtuális gépeken futó szoftver kommunikálhat a HANA nagy példányban futó HANA-példány.
- Az SAP HANA hardvere az Azure-ban (nagy példányok) dedikált hardver egy SUSE Linux Enterprise Server vagy Red Hat Enterprise Linux előtelepített IaaS-ben. A virtuális gépekhez is az operációs rendszer további frissítései és karbantartása az Ön felelőssége.
- Hana vagy az SAP HANA hana hana-egységekhana nagy példányon futtatásához szükséges további összetevők telepítése az Ön felelőssége. Az SAP HANA azure-beli minden folyamatban lévő művelete és felügyelete egyben az Ön felelőssége is.
- Az itt ismertetett megoldásokon kívül az Azure-előfizetésben más összetevőket is telepíthet, amelyek az Azure-beli SAP HANA-hoz (nagy példányok) csatlakoznak. Ilyenek például olyan összetevők, amelyek lehetővé teszik a kommunikációt az SAP HANA-adatbázissal, például a jump-kiszolgálók, az RDP-kiszolgálók, az SAP HANA Studio, az SAP Data Services SAP BI-forgatókönyvekhez vagy a hálózati figyelési megoldások.
- Az Azure-hoz hasonló hana nagy példány támogatja a magas rendelkezésre állású és vész-helyreállítási funkciókat.

**További lépések**
- [A HLI elérhető sk-jainak ajánlása](hana-available-skus.md) 