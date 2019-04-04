---
title: Red Hat foglalási terv kedvezményt és a használat – Azure |} A Microsoft Docs
description: Ismerje meg, hogy Red Hat terv kedvezmények Red Hat-szoftverek virtuális gépeken.
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
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917056"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Megismerheti, hogyan alkalmazza a a Red Hat Linux Enterprise szoftvert foglalási terv kedvezményt van az Azure-hoz

Red Hat Linux csomagot vásárol, miután a rendszer automatikusan alkalmazza a kedvezményt üzemelő Red Hat virtuális gépekre (VM), amelyek megfelelnek a foglalást. Egy Red Hat Linux-csomag magában foglalja a Red Hat szoftver futó Azure virtuális gép költségét.

A megfelelő Red Hat Linux-csomag megvásárlása, szüksége megtudhatja, hogy milyen Red Hat virtuális gépek futtatása és a virtuális gépeken vcpu-k számát. Használja az alábbi szakaszok segítségével azonosíthatja a használati CSV-fájlból, mit szeretne vásárolni.

## <a name="discount-applies-to-different-vm-sizes"></a>Különböző méretű virtuális gépek vonatkozik kedvezmény

Reserved VM Instances, például a Red Hat terv vásárlások példány mérete rugalmasságot kínálnak. Ez azt jelenti, hogy a kedvezmény érvényes, még ha telepít egy virtuális Gépet egy másik vCPU-számot. A kedvezmény a szoftvercsomag különböző méretekkel vonatkozik.

A kedvezmény a arány a következő táblázatokban függ. A arány a relatív erőforrás-igényű, hogy a csoportban minden mértékegységek hasonlítja össze. A arány attól függ, hogy a virtuális gép vcpu-k. Alapján számítja ki, hány Virtuálisgép-példányok beolvasása a Red Hat Linux-csomag kedvezményt arány értéket használja.

Például ha egy virtuális Gépet a 3 vagy 4 vcpu-k vásárol egy tervet a Red Hat Linux Enterprise Server, a Foglalás arány, a 2. A kedvezmény a Red Hat szoftver költségét mutatja be:

- 2 1 vagy 2 vcpu-k, az üzembe helyezett virtuális gépek
- 1-3 vagy 4 Vcpu, üzembe helyezett virtuális gép
- vagy 0.77 vagy hamarosan 77: % 5 vagy több vcpu-k rendelkező virtuális gépek.

Az 5 vagy több Vcpu arány, 2.6-os. Így a Red Hat vcpu-k 5 vagy több virtuális gép a Foglalás egy szoftverköltség, ami körülbelül 77 % csak része ismerteti.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Red Hat virtuális gép használatának megértéséhez, vásárlás előtt

Az alábbi táblázatokban a szoftverfrissítési csomagok vásárolhat egy foglalást, a kapcsolódó használati mérőszámok és a arányok minden.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Az Azure portal marketplace nevei:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9.
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (legújabb lvm)

|Red Hat VM | Fogyasztásmérő azonosítója| Arány| A példában a virtuális gép mérete|
| -------| ------------------------| --- |--- |
|1 – 4 vCPU VM-licenc|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1 – 4 vCPU VM-licenc|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1 – 4 vCPU VM-licenc|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 + vCPU VM-licenc|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 + vCPU VM-licenc|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|virtuális gépek licenceivel 44 vCPU|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|virtuális gépek licenceivel 60 vCPU|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux magas rendelkezésre ÁLLÁS az SAP-

Az Azure portal marketplace neve:

|Red Hat VM | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- | --- | ------------------------| --- | --- |
|1 – 4 vCPU VM-licenc |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 + vCPU VM-licenc|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux magas rendelkezésre állással

Az Azure portal marketplace nevei:

|Red Hat VM | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- | --- |
|1 – 4 vCPU VM-licenc|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 + vCPU VM-licenc|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Az Azure portal marketplace nevei:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 SAP-
- Red Hat Enterprise Linux 7,5-öt az SAP

|Red Hat VM | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- |--- |
|1 vCPU VM-licenc|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|virtuális gépek licenceivel 2 vCPU|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU VM-licenc|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|virtuális gépek licenceivel 6 vCPU|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|virtuális gépek licenceivel. 8 vCPU|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|virtuális gépek licenceivel 12 vCPU|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|virtuális gépek licenceivel 16 vCPU|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|virtuális gépek licenceivel 20 vCPU|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|virtuális gépek licenceivel 24 vCPU|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|virtuális gépek licenceivel 32 vCPU|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|virtuális gépek licenceivel 40 vCPU|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|virtuális gépek licenceivel 44 vCPU|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|virtuális gépek licenceivel 60 vCPU|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|virtuális gépek licenceivel 64 vCPU|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|virtuális gépek licenceivel 72 vCPU|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|virtuális gépek licenceivel 96 vCPU|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|virtuális gépek licenceivel 128 vCPU|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Az Azure portal marketplace nevei:

- Red Hat Enterprise Linux 6.7 az SAP Hana-hoz
- Red Hat Enterprise Linux 7.2 az SAP Hana-hoz
- Red Hat Enterprise Linux 7.3 az SAP Hana-hoz

|Red Hat VM | Fogyasztásmérő azonosítója | Arány|A példában a virtuális gép mérete|
| ------- |------------------------| --- |--- |
|1 vCPU VM-licenc|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|virtuális gépek licenceivel 2 vCPU|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU VM-licenc|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|virtuális gépek licenceivel 6 vCPU|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|virtuális gépek licenceivel. 8 vCPU|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|virtuális gépek licenceivel 12 vCPU|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|virtuális gépek licenceivel 16 vCPU|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|virtuális gépek licenceivel 20 vCPU|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|virtuális gépek licenceivel 24 vCPU|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|virtuális gépek licenceivel 32 vCPU|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|virtuális gépek licenceivel 40 vCPU|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|virtuális gépek licenceivel 44 vCPU|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|virtuális gépek licenceivel 60 vCPU|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|virtuális gépek licenceivel 64 vCPU|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|virtuális gépek licenceivel 72 vCPU|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|virtuális gépek licenceivel 96 vCPU|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|virtuális gépek licenceivel 128 vCPU|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>További lépések

Foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalást az Azure-hoz](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure foglalást a Red Hat szoftvercsomagok – alap](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure-foglalások kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
