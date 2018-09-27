---
title: SUSE csomag kedvezményes és a használat – Azure foglalások |} A Microsoft Docs
description: Ismerje meg, hogyan SUSE terv kedvezmények SUSE szoftver virtuális gépeken.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: cwatson
ms.openlocfilehash: 62f75be44ed92528b48fc0f093f5966284662312
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393689"
---
# <a name="understand-how-the-suse-linux-enterprise-software-plan-discount-is-applied"></a>Megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftverfrissítési csomag kedvezmény

SUSE Linux csomagot vásárol, miután a rendszer automatikusan alkalmazza a kedvezményt az üzembe helyezett SUSE virtuális gépek (VM), amelyek megfelelnek a foglalást. A SUSE Linux-csomag magában foglalja a SUSE szoftvert futtat Azure virtuális gép költségét.

A megfelelő SUSE Linux-csomag megvásárlása, szüksége megtudhatja, hogy milyen SUSE virtuális gépek futtatása és a virtuális gépeken vcpu-k számát. Használja az alábbi szakaszok segítségével azonosíthatja a használati CSV-fájlból, mit szeretne vásárolni.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Példány mérete rugalmasan különböző méretű virtuális gépek vonatkozik kedvezmény

Például a Reserved VM Instances, SUSE megtervezése vásárlások rugalmasabbá példány mérete. Ez azt jelenti, hogy a kedvezmény érvényes, még ha telepít egy virtuális Gépet egy másik vCPU-számot. A kedvezmény a szoftvercsomag különböző méretekkel vonatkozik.

A kedvezmény a arány a következő táblázatokban függ. A arány a relatív erőforrás-igényű, hogy a csoportban minden mértékegységek hasonlítja össze. A arány attól függ, hogy a virtuális gép vcpu-k. Alapján számítja ki, hány Virtuálisgép-példányok beolvasása a SUSE Linux-csomag kedvezményt arány értéket használja.

Például ha vásárol egy csomag az SUSE Linux Enterprise Server for HPC Priority 3 vagy 4 vcpu-k a virtuális gép, a Foglalás arány, a 2. A kedvezmény a SUSE szoftver költségét mutatja be:

- 2 1 vagy 2 vcpu-k, az üzembe helyezett virtuális gépek
- 1-3 vagy 4 Vcpu, üzembe helyezett virtuális gép
- vagy 0.77 vagy hamarosan 77: % 5 vagy több vcpu-k rendelkező virtuális gépek.

Az 5 vagy több Vcpu arány, 2.6-os. Ezért a SUSE egy virtuális Gépet az 5 vagy több Vcpu-foglalás szoftverköltség, ami körülbelül 77 % csak része ismerteti.

## <a name="understand-your-suse-vm-usage-before-buying-a-suse-linux-plan"></a>A SUSE virtuális gépek használatának megértéséhez egy SUSE Linux-csomag megvásárlása előtt

Az alábbi táblázatokban a szoftverfrissítési csomagok vásárolhat egy foglalást, a kapcsolódó használati mérőszámok és a arányok minden.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Az Azure portal marketplace neve:

- SLES 12 SP3 HPC (prioritás)

|SUSE VIRTUÁLIS GÉPEK | Fogyasztásmérő azonosítója| Arány| A példában a virtuális gép mérete|
| -------| ------------------------| --- |--- |
|Az SLES for HPC 1-2 vcpu-k|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|Az SLES for HPC 3-4 vcpu-k|e531e1c0-09c9-4d83-B7D0-a2c6741faa22|2|D4s_v3|
|Az SLES for HPC 5 + vcpu-k|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server HPC standard

Az Azure portal marketplace neve:

- HPC SLES 12 SP3

|SUSE VIRTUÁLIS GÉPEK | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- | --- | ------------------------| --- | --- |
|Az SLES for HPC 1-2 vcpu-k |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|Az SLES for HPC 3-4 vcpu-k|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|Az SLES for HPC 5 + vcpu-k |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Az Azure portal marketplace nevei:

- SLES for SAP (prioritás) 15
- SLES for SAP 12 SP3 (prioritás)
- SLES for SAP 12 SP2 (prioritás)

|SUSE VIRTUÁLIS GÉPEK | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- | --- |
|Az SLES for SAP Priority 1-2 vcpu-k|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|Az SLES for SAP Priority 3-4 vcpu-k |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|Az SLES for SAP Priority 5 + vcpu-k |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server prioritás

Az Azure portal marketplace nevei:

- SLES 15 (PRIORITÁS)
- SLES 12 SP3 (prioritás)
- SLES 11 SP4 (prioritás)

|SUSE VIRTUÁLIS GÉPEK | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 – 4 vcpu-k |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 – 4 vcpu-k |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vcpu-k |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vcpu-k |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 core vcpu-k |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vcpu-k |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vcpu-k |c5228804-1de6-4BD4-a61c-501d9003acc8|3.2| |
|SLES 24 mag vcpu-k |-005 d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 vcpu-k |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 mag vcpu-k |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vcpu-k |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 mag vcpu-k |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 mag vcpu-k |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 maggal vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Az Azure portal marketplace nevei:

- SLES 15
- SLES 15 (szokásos)
- SLES 12 SP3 (Standard)

|SUSE VIRTUÁLIS GÉPEK | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- |--- |
|SLES 1-2 mag vcpu-k |4b2fecfc-B110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 mag vcpu-k |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + vcpu-k |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>További lépések

Foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az az Azure-foglalások SUSE szoftvercsomagok – alap](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.