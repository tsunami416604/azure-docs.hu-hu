---
title: Hardver StorSimple 10 GbE interfészekhez | Microsoft dokumentumok
description: A SFP-eszközök 10 GbE hálózati csatolójának támogatott kis méretű form-factor pluggable (SFP) adó-vevőit, kábeleit és kapcsolóit ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965017"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>A StorSimple eszköz 10 GbE hálózati interfészének támogatott hardvere

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple-eszközzel működő kiegészítő hardverekkel kapcsolatos információkat tartalmazza.

## <a name="list-of-devices-tested-by-microsoft"></a>A Microsoft által tesztelt eszközök listája
A Microsoft a következő kis méretű, dugaszolható (SFP) adó-vevőket, kábeleket és kapcsolókat tesztelte annak érdekében, hogy azok optimálisan működjenek az eszközökkel. (Az új hardver tesztelése után a következő táblázatok frissülnek.)

### <a name="sfp-transceivers"></a>SFP+ adó-vevők
| Gyártmány | Modell |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kábelek
| S. Nem. | Gyártmány | Modell |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite között |N820-05M (OM3) |

### <a name="switches"></a>Switchek
| S. Nem. | Gyártmány | Modell |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>A terepen tesztelt eszközök listája
Ez a szakasz azoknak az eszközöknek a listáját tartalmazza, amelyeket a StorSimple-ügyfelek sikeresen telepítettek a mezőben. Ezeket a Microsoft nem tesztelte, de valószínűleg együttműködik a StorSimple eszközzel.

| Paraméter | Érték |
| --- | --- |
| Kapcsoló gyártménye |Juniper |
| Kapcsoló modell |ex4550-32F |
| Az operációs rendszer verziójának váltása |JunOS 12.3R9.4 |
| Penge modell |Beépített portok (PIC 0) |
| Adó-vevő gyártmánya |Juniper |
| Adó-vevő modell |Részszám: 740-021308 <br></br> Alkatrészszám 740-030658 |
| Adó-vevő belső vezérlőprogram verziója |Rev 01 0.0-s verzió (jelentett) |
| Kábelmodell |LSZH kétoldalas pulóver LC/LC 50/125μ, OM3, LSZH |
| StorSimple modell |8600 |
| StorSimple szoftververzió |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Az OEM-szolgáltató által tesztelt eszközök listája (Mellanox)
A Mellanox a következő kis méretű, dugaszolható (SFP) adó-vevőket, kábeleket és kapcsolókat tesztelte annak érdekében, hogy azok optimálisan működjenek a Mellanox hálózati interfészekkel, például a StorSimple eszköz 10 GbE hálózati interfészével.

### <a name="cables-and-modules-supported-by-mellanox"></a>A Mellanox által támogatott kábelek és modulok
Az alábbi táblázat a Mellanox által támogatott kábeleket és modulokat sorolja fel. Ezeket a Microsoft nem tesztelte, de valószínűleg együttműködik a StorSimple eszközzel.

| S. Nem. | Sebesség | Modell | Leírás | Gyártmány |
| --- | --- | --- | --- | --- |
| 1. |10 Gbe |CAB-SFP-SFP-1M |passzív rézkábel SFP+ 10 Gb/s 1m |Arista között |
| 2. |10 Gbe |CAB-SFP-SFP-2M |passzív rézkábel SFP+ 10 Gb/s 2m |Arista között |
| 3. |10 Gbe |CAB-SFP-SFP-3M |passzív rézkábel SFP+ 10 Gb/s 3m |Arista között |
| 4. |10 Gbe |CAB-SFP-SFP-5M |passzív rézkábel SFP+ 10 Gb/s 5m |Arista között |
| 5. |10 Gbe |Cisco SFP-H10GBCU1M |Cisco SFP+ kábel |Cisco |
| 6. |10 Gbe |Cisco SFP-H10GBCU3M |Cisco SFP+ kábel |Cisco |
| 7. |10 Gbe |Cisco SFP-H10GBCU5M |Cisco SFP+ kábel |Cisco |
| 8. |10 Gbe |J9281B HP X242 10G |SFP+ és SFP+ 1m közvetlen csatolás rézkábel |Hp |
| 9. |10 Gbe |455883-B21 HP BLc |10 Gb SR SFP+ opt |Hp |
| 10. |10 Gbe |455886-B21 HP BLc |10 Gb LR SFP+ opt |Hp |
| 11. |10 Gbe |487649-B21 HP BLc |SFP+ 0,5 m 10GbE rézkábel |Hp |
| 12. |10 Gbe |487652-B21 HP BLc |SFP+ 1m 10GbE rézkábel |Hp |
| 13. |10 Gbe |487655-B21 HP BLc |SFP+ 3m 10GbE rézkábel |Hp |
| 14. |10 Gbe |487658-B21 HP BLc |SFP+ 7m 10GbE rézkábel |Hp |
| 15. |10 Gbe |537963-B21 HP BLc |SFP+ 5m 10GbE rézkábel |Hp |
| 16. |10 Gbe |AP784A HP |3m C-sorozatú passzív réz SFP+ kábel |Hp |
| 17. |10 Gbe |AP785A HP |5m C-sorozatú passzív réz SFP+ kábel |Hp |
| 18. |10 Gbe |AP818A LE |1 m B-sorozatú aktív réz SFP+ kábel |Hp |
| 19. |10 Gbe |AP819A HP |3m B-sorozatú aktív réz SFP+ kábel |Hp |
| 20. |10 Gbe |J9150A LE |X132 10G SFP+ LC SR Adó-vevő |Hp |
| 21. |10 Gbe |J9151A LE |X132 10G SFP+ LC LR Adó-vevő |Hp |
| 22. |10 Gbe |J9283B LE |X242 10G SFP+ SFP+ 3m DAC kábel |Hp |
| 23. |10 Gbe |J9285B LE |X242 10G SFP+ SFP+ 7m DAC kábel |Hp |
| 24. |10 Gbe |JD095B LE |X240 10G SFP+ SFP+ 0,65 m DAC kábel |Hp |
| 25. |10 Gbe |JD096B LE |X240 10G SFP+ SFP+ 1,2 m DAC kábel |Hp |
| 26. |10 Gbe |JD097B LE |X240 10G SFP+ SFP+ 3m DAD kábel |Hp |
| 27. |10 Gbe |MAM1Q00A-QSA Mellanox |QSFP –SFP+ adapter |Mellanox Technológiák |
| 28. |10 Gbe |MC2309124-006 Mt |Passzív rézkábel 1x SFP+ A QSFP 10Gb /s 24awg 7m |Mellanox Technológiák |
| 29. |10 Gbe |MC2309124-007 Mt |Passzív rézkábel 1x SFP+ A QSFP 10Gb /s 24awg 7m |Mellanox Technológiák |
| 30. |10 Gbe |MC2309130-003 Mt |Passzív rézkábel 1x SFP+ A QSFP 10Gb / s 30awg 3m |Mellanox Technológiák |
| 31. |10 Gbe |MC2309130-00A Mt |Passzív rézkábel 1x SFP+ A QSFP 10Gb/s 30awg 0,5 m |Mellanox Technológiák |
| 32. |10 Gbe |MC3309124-005 Mt |Passzív rézkábel 1x SFP+ 10Gb/s 24awg 5m |Mellanox Technológiák |
| 33. |10 Gbe |MC3309124-007 Mt |Passzív rézkábel 1x SFP+ 10Gb/s 24awg 7m |Mellanox Technológiák |
| 34. |10 Gbe |MC3309130-003 Mt |Passzív rézkábel 1x SFP+ 10Gb/s 30awg 3m |Mellanox Technológiák |
| 35. |10 Gbe |MC3309130-00A Mt |Passzív rézkábel 1x SFP+ 10Gb/s 30awg 0,5 m |Mellanox Technológiák |

### <a name="switches-supported-by-mellanox"></a>A Mellanox által támogatott kapcsolók
Az alábbi táblázat a Mellanox által támogatott kapcsolókat sorolja fel. Ezeket a Microsoft nem tesztelte, de valószínűleg együttműködik a StorSimple eszközzel.

| S. Nem. | Sebesség | Modell | Leírás | Gyártmány |
| --- | --- | --- | --- | --- |
| 1. |10gbe |516733-B21 |HP ProCurve 6120xG 10GbE Ethernet blade kapcsoló |Hp |
| 2. |10gbe |538113-B21 |HP 10gbe átmenő modul (PTM) |Hp |
| 3. |10gbe |EN4093 |IBM PureFlex System Fabric EN4093 10 gigabites skálázható kapcsolómodul |IBM |
| 4. |1gbe között |3020 |Cisco Catalyst 3020 1GbE kapcsolópenge |Cisco |
| 5. |1gbe között |3020X |Cisco Catalyst 3020X 1GbE kapcsolópenge |Cisco |
| 6. |1gbe között |438030-B21 |HP 1GbE kapcsolómodul - GbE2c Layer 2/3 Ethernet blade switch |Hp |
| 7. |1gbe között |6120G |HP ProCurve 6120G/XG 1GbE kapcsolópanel |Hp |

## <a name="next-steps"></a>További lépések
[További információ a StorSimple hardverösszetevőkről és állapotról.](storsimple-monitor-hardware-status.md)

