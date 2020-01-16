---
title: Szoftvercsomag-kedvezmény – Azure | Microsoft Docs
description: Megtudhatja, hogyan történik a szoftvercsomag-kedvezmények alkalmazása a virtuális gépeken található szoftvereken.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: f3d0c123fcbd16f3a4125ad74f0502863b6ce6d1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995259"
---
# <a name="azure-software-plan-discount"></a>Azure-szoftvercsomag-kedvezmények

Az Azure-beli SUSE- és RedHat-szoftvercsomagok üzembe helyezett virtuális gépekre alkalmazható foglalások. A szoftvercsomag-kedvezmény a foglalással egyező, üzembe helyezett virtuális gépek szoftverhasználatára érvényes.

Egy virtuális gép leállításakor a rendszer automatikusan egy másik egyező virtuális gépre alkalmazza a kedvezményt, ha van ilyen. A szoftvercsomagok a szoftver virtuális gépen történő futtatásának költségét fedezik. Az egyéb (pl. számítási, tárolási és hálózatkezelési) költségek számlázása külön történik.

A megfelelő csomag megvásárlásához ismernie kell a saját virtuálisgép-használatát és azt, hogy a virtuális gépek hány virtuális processzorral rendelkeznek. Az alábbi szakaszok segítségével meghatározhatja a használati adatok alapján, hogy melyik csomagot érdemes megvásárolnia.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>A RedHat rendszerű virtuális gépek használatának áttekintése vásárlás előtt

Keresse meg a termék nevét a használati adatok alapján, és vásárolja meg az azonos típusú és méretű RedHat-csomagot.

Ha például a használati adatok alapján Önnek a **Red Hat Enterprise Linux – 1–4 virtuális processzorral rendelkező virtuális gép licence** termék szükséges, akkor a **Red Hat Enterprise Linuxot** vásárolja meg egy **1–4 virtuális processzorral rendelkező virtuális géphez**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>A SUSE rendszerű virtuális gépek használatának áttekintése vásárlás előtt

Keresse meg a termék nevét a használati adatok alapján, és vásárolja meg az azonos típusú és méretű SUSE-csomagot.

Ha például a használati adatok alapján Önnek a **SUSE Linux Enterprise Server – 2–4 virtuális processzorral rendelkező virtuális gép támogatása** termék szükséges, akkor a **SUSE Linux Enterprise Server Priorityt** vásárolja meg egy **2–4 virtuális processzorral rendelkező virtuális géphez**.

![Példa a megvásárolandó termék kiválasztására](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>A kedvezmény különböző virtuálisgép-méretekre érvényes a SUSE-csomagok esetében

A Reserved VM Instances-csomagokhoz hasonlóan a SUSE-csomagok is biztosítják a példányok méretrugalmasságát. Ez azt jelenti, hogy a kedvezmény akkor is érvényes, ha eltérő számú virtuális processzorral rendelkező virtuális gépet helyez üzembe. A kedvezmény különböző virtuálisgép-méretekre érvényes a szoftvercsomagban.

A kedvezmény mértéke az alábbi táblázatokban szereplő arányoktól függ. Az arány a csoportok fogyasztásmérőinek relatív lábnyomát hasonlítja össze. Az arány a virtuális gépek virtuális processzoraitól függ. Az arány használatával kiszámíthatja, hány virtuálisgép-példány kapja meg a SUSE Linux-csomaggal járó kedvezményt.

Ha például SUSE Linux Enterprise Server for HPC Priority-csomagot vásárol egy 3 vagy 4 virtuális processzorral rendelkező virtuális géphez, akkor az adott foglalás aránya 2. A kedvezmény az alábbiak esetében fedezi a SUSE szoftver költségét:

- 2 üzembe helyezett virtuális gép 1 vagy 2 virtuális processzorral;
- 1 üzembe helyezett virtuális gép 3 vagy 4 virtuális processzorral;
- vagy egy olyan virtuális gép 0,77 része vagy 77%-a, amely 5 vagy több virtuális processzorral rendelkezik.

5 vagy több virtuális processzor esetén az arány 2,6. Így egy 5 vagy több virtuális processzorral rendelkező virtuális gépet tartalmazó SUSE-foglalás a szoftverköltségnek csak egy részét, kb. 77%-át fedezi.

Az alábbi táblázatok azokat a szoftvercsomagokat ismertetik, amelyekhez foglalást vásárolhat, a csomagok kapcsolódó fogyasztásmérőit és arányait.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Azure Portal Marketplace-en szereplő név:

- SLES 12 SP3 for HPC (Priority)

|SUSE rendszerű virtuális gép | MeterId| Arány| Példa virtuálisgép-méretre|
| -------| ------------------------| --- |--- |
|SLES for HPC, 1–2 virtuális processzor|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC, 3–4 virtuális processzor|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC, 5 vagy több virtuális processzor|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Azure Portal Marketplace-en szereplő név:

- SLES 12 SP3 for HPC

|SUSE rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- | --- | ------------------------| --- |
|SLES for HPC, 1–2 virtuális processzor |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC, 3–4 virtuális processzor|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES for HPC, 5 vagy több virtuális processzor |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Azure Portal Marketplace-en szereplő nevek:

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|SUSE rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority, 1–2 virtuális processzor|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority, 3–4 virtuális processzor |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority, 5 vagy több virtuális processzor |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Azure Portal Marketplace-en szereplő nevek:

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|SUSE rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- |--- |
|SLES, 1 virtuális processzor|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES, 2–4 virtuális processzor |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES, 2–4 virtuális processzor |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES, 6 virtuális processzor |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES, 8 virtuális processzor |e11331a8-fd32-4e71-b60e-4de2a818c67a|3,2|D8s_v3|
|SLES, 12 magos virtuális processzor |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3,2||
|SLES, 16 virtuális processzor |bb21066f-fe46-46d3-8006-b326b1663e52|3,2| D16s_v3|
|SLES, 20 virtuális processzor |c5228804-1de6-4bd4-a61c-501d9003acc8|3,2| |
|SLES, 24 magos virtuális processzor |-005d-4075-ac11-822ccde9e8f6|3,2| ND24s|
|SLES, 32 virtuális processzor |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3,2| D32s_v3|
|SLES, 40 magos virtuális processzor |a161d3d3-0592-4956-9b64-6829678b6506|3,2||
|SLES, 64 virtuális processzor |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3,2| D64s_v3|
|SLES, 72 magos virtuális processzor |93329a72-24d7-4faa-93d9-203f367ed334|3,2|F72s_v2|
|SLES, 96 magos virtuális processzor |2018c3a8-ff13-41f8-b64d-9558c5206547|3,2||
|SLES, 128 magos virtuális processzor |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3,2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure Portal Marketplace-en szereplő nevek:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE rendszerű virtuális gép | MeterId | Arány|Példa virtuálisgép-méretre|
| ------- |------------------------| --- |--- |
|SLES, 1–2 magos virtuális processzor |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES, 3–4 magos virtuális processzor |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES, 5 vagy több virtuális processzor |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

A foglalással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Előre fizetés Azure Reservationsbeli SUSE-szoftvercsomagokért](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
