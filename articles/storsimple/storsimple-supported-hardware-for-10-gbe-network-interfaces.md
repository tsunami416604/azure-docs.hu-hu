---
title: StorSimple 10 GbE hardverének felületek |} A Microsoft Docs
description: A támogatott kisebb méretet moduláris (SFP) adó, kábelek és kapcsolók 10 GbE hálózati adapterek ismerteti a StorSimple eszközön.
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
ms.openlocfilehash: 8303195f0f3228ee145cbba9e322ea4e5e4c1264
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726966"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>A StorSimple eszköz 10 GbE hálózati adapterek támogatott hardver
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti, amely együttműködik a Microsoft Azure StorSimple-eszköz a kiegészítő hardvert.

## <a name="list-of-devices-tested-by-microsoft"></a>A Microsoft tesztelte eszközök listája
A Microsoft tesztelte a következő kis méretformátumhoz moduláris (SFP) adó, kábelek és kapcsolók, győződjön meg arról, hogy ezek az eszközök a optimálisan működni. (Az alábbi táblázatok frissíti, új hardver lett tesztelve.)

### <a name="sfp-transceivers"></a>SFP+ Transceivers
| Fordítás | Modell |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kábelek
| S. Nem. | Fordítás | Modell |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>Kapcsolók
| S. Nem. | Fordítás | Modell |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>A mező tesztelt eszközök listája
Ebben a szakaszban az eszközöket, amelyek sikeresen telepítették a mezőben a StorSimple-ügyfelek listáját tartalmazza. Ezek a nem Microsoft által tesztelt, de valószínűleg a StorSimple-eszköz használata.

| Paraméter | Érték |
| --- | --- |
| Győződjön meg arról, a kapcsolóhoz |Juniper |
| Kapcsoló modell |ex4550-32F |
| Kapcsoló operációs rendszer verziója |JunOS 12.3R9.4 |
| Panel modell |Készítse elő portok (PIC 0) |
| Adó tétele |Juniper |
| Adó modell |Cikkszám 740-021308 <br></br> Cikkszám 740-030658 |
| Adó belső vezérlőprogram verziója |Rev 01 verzió 0,0 (jelentett) |
| A modell kábeles |A nyomtatott kétoldalas átkötés LC/LC 50/125µ, OM3, LSZH |
| A StorSimple-modell |8600 |
| A StorSimple szoftver verziója |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>OEM-szolgáltató (Mellanox) által tesztelt eszközök listája
Mellanox tesztelte a következő kis méretformátumhoz moduláris (SFP) adó, kábelek és kapcsolók, győződjön meg arról, hogy ezek a Mellanox hálózati felületek, például a 10 GbE hálózati adapterek a StorSimple eszközön a optimálisan működni.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kábelek és a modulok Mellanox által támogatott
Az alábbi táblázat a kábelek és a modulok Mellanox által támogatott. Ezek a nem Microsoft által tesztelt, de valószínűleg a StorSimple-eszköz használata.

| S. Nem. | Sebesség | Modell | Leírás | Fordítás |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |passzív rézeres kábellel SFP + 10 Gb/s-1m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |passzív rézeres kábellel SFP + 10 Gb/s 2m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |passzív rézeres kábellel SFP + 10 Gb/s a 3m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |passzív rézeres kábellel SFP + 10 Gb/s 5m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP + kábel |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP + kábel |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP + kábel |Cisco |
| 8. |10 GbE |J9281B HP X242 10 G |Az SFP + 1 millió SFP + Rézeres kábellel közvetlen csatolása |HP |
| 9. |10 GbE |HP BLc 455883-B21 |10Gb SR SFP+ Opt |HP |
| 10. |10 GbE |HP BLc 455886-B21 |10Gb LR SFP+ Opt |HP |
| 11. |10 GbE |HP BLc 487649-B21 |0,5 m 10GbE SFP + réz kábel |HP |
| 12. |10 GbE |HP BLc 487652-B21 |Réz SFP + 1 millió 10 gbe-kábellel |HP |
| 13. |10 GbE |HP BLc 487655-B21 |A 3m 10GbE SFP + réz kábel |HP |
| 14. |10 GbE |HP BLc 487658-B21 |SFP + 7m 10GbE réz kábel |HP |
| 15. |10 GbE |HP BLc 537963-B21 |SFP + 5m 10GbE réz kábel |HP |
| 16. |10 GbE |AP784A HP |a 3m C sorozatú rackbe passzív Rézeres SFP + kábel |HP |
| 17. |10 GbE |AP785A HP |5m C sorozatú rackbe passzív Rézeres SFP + kábel |HP |
| 18. |10 GbE |AP818A HP |1m – B sorozat aktív Rézeres SFP + kábel |HP |
| 19. |10 GbE |AP819A HP |a 3m – B sorozat aktív Rézeres SFP + kábel |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR Transceiver |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 GbE |J9283B HP |X242 10 G SFP + SFP + 3 m DAC kábel |HP |
| 23. |10 GbE |J9285B HP |X242 10 G SFP + SFP + 7 m DAC kábel |HP |
| 24. |10 GbE |JD095B HP |X240 10 G SFP + SFP + 0,65 m DAC kábel |HP |
| 25. |10 GbE |JD096B HP |X240 10 G SFP + SFP + 1,2 millió DAC kábel |HP |
| 26. |10 GbE |JD097B HP |X240 10 G SFP + SFP + 3 m apa kábel |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP To SFP+ Adapter |Mellanox technológiák |
| 28. |10 GbE |MC2309124-006 Mt |A 10 Gb/s 24awg QSFP passzív Rézeres kábellel 1 x SFP+ 7 p |Mellanox technológiák |
| 29. |10 GbE |MC2309124-007 Mt |A 10 Gb/s 24awg QSFP passzív Rézeres kábellel 1 x SFP+ 7 p |Mellanox technológiák |
| 30. |10 GbE |MC2309130-003 Mt |Passzív Rézeres kábellel 1 x SFP+ QSFP 10 Gb/s 30awg a 3 m |Mellanox technológiák |
| 31. |10 GbE |MC2309130-00A Mt |A 10 Gb/s 30awg QSFP passzív Rézeres kábellel 1 x SFP+ 0,5 m |Mellanox technológiák |
| 32. |10 GbE |MC3309124-005 Mt |Passzív réz kábelezése x SFP+ 10 Gb/s-1 24awg 5 m |Mellanox technológiák |
| 33. |10 GbE |MC3309124-007 Mt |Passzív réz kábelezése x SFP+ 10 Gb/s-1 24awg 7 p |Mellanox technológiák |
| 34. |10 GbE |MC3309130-003 Mt |Passzív réz kábelezése x SFP+ 10 Gb/s-1 30awg 3 m |Mellanox technológiák |
| 35. |10 GbE |MC3309130-00A Mt |Passzív réz kábelezése x SFP+ 10 Gb/s-1 30awg 0,5 m |Mellanox technológiák |

### <a name="switches-supported-by-mellanox"></a>Mellanox által támogatott kapcsolók
Az alábbi táblázat felsorolja a Mellanox által támogatott kapcsolókat. Ezek a nem Microsoft által tesztelt, de valószínűleg a StorSimple-eszköz használata.

| S. Nem. | Sebesség | Modell | Leírás | Fordítás |
| --- | --- | --- | --- | --- |
| 1. |10GbE |516733-B21 |HP ProCurve 6120XG darab 10 gbe Ethernet panel kapcsoló |HP |
| 2. |10GbE |538113-B21 |HP 10 gbe átmenő modul (PTM) |HP |
| 3. |10GbE |EN4093 |IBM PureFlex rendszer Fabric EN4093 10 gigabites méretezhető kapcsoló modul |IBM |
| 4. |1 gbe |3020 |Cisco katalizáló 3020 1 gbe kapcsoló panel |Cisco |
| 5. |1 gbe |3020X |Cisco katalizáló 3020 X 1 gbe kapcsoló panel |Cisco |
| 6. |1 gbe |438030-B21 |HP 1 gbe kapcsoló modul - GbE2c 2/3. rétegbeli Ethernet panel Váltás |HP |
| 7. |1 gbe |6120G |HP ProCurve 6120G/XG 1 gbe kapcsoló panel |HP |

## <a name="next-steps"></a>További lépések
[További információ a StorSimple hardverkonfiguráción összetevők és állapot](storsimple-monitor-hardware-status.md).

