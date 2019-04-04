---
title: Foglalást a szoftverek díjait az Azure-hoz |} A Microsoft Docs
description: Ismerje meg, melyik szoftver mérőszámok nem szerepelnek az Azure fenntartott VM-példány költségeket.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 340cba65a1faac247678cd187f106157ba566f3e
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918722"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Az Azure Reserved VM Instances nem tartalmazza a szoftverek díjait.

Ha nem rendelkezik az Azure Hybrid Benefit értékelem a fenntartott VM-példányokon, majd díja az alábbi szakaszban felsorolt szoftver mérőszámokhoz tartozó.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows szoftverek mérőszámok nem szerepel a fenntartás költsége

| Fogyasztásmérő azonosítója | A használati fájlban MeterName | Virtuális gép által használt |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Foglalás-Windows fenntartott példányok periódusai (1 mag) | B sorozat |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Foglalás-Windows fenntartott példányok periódusai (2 mag) | B sorozat |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Foglalás-Windows fenntartott példányok periódusai (4 mag) | B sorozat |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Foglalás-Windows fenntartott példányok periódusai (8 mag) | B sorozat |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Foglalás-Windows fenntartott példányok (1 mag) | Mindenhol, kivéve a B sorozat |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Foglalás-Windows fenntartott példányok (2 mag) | Mindenhol, kivéve a B sorozat |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Foglalás-Windows fenntartott példányok (4 mag) | Mindenhol, kivéve a B sorozat |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Foglalás-Windows fenntartott példányok (6 mag) | Mindenhol, kivéve a B sorozat |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Foglalás-Windows fenntartott példányok (8 mag) | Mindenhol, kivéve a B sorozat |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Foglalás-Windows fenntartott példányok (12 mag) | Mindenhol, kivéve a B sorozat |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Foglalás-Windows fenntartott példányok (16 mag) | Mindenhol, kivéve a B sorozat |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Foglalás-Windows fenntartott példányok (20 mag) | Mindenhol, kivéve a B sorozat |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Foglalás-Windows fenntartott példányok (24 mag) | Mindenhol, kivéve a B sorozat |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Foglalás-Windows fenntartott példányok (32 mag) | Mindenhol, kivéve a B sorozat |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Foglalás-Windows fenntartott példányok (40 mag) | Mindenhol, kivéve a B sorozat |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Foglalás-Windows fenntartott példányok (64 mag) | Mindenhol, kivéve a B sorozat |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Foglalás-Windows fenntartott példányok (72 mag) | Mindenhol, kivéve a B sorozat |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Foglalás-Windows fenntartott példányok (128 mag) | Mindenhol, kivéve a B sorozat |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Foglalás-Windows fenntartott példányok (256 mag) | Mindenhol, kivéve a B sorozat |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Foglalás-Windows fenntartott példányok (96 mag) | Mindenhol, kivéve a B sorozat |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>A felhőszolgáltatások szoftver mérőszámai nem szerepel a fenntartás költsége

| Fogyasztásmérő azonosítója | A használati fájlban MeterName |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU licenc|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU licenc|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU-licenc|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU-licenc|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU-licenc|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU License|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU License|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU-licenc|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80-as vCPU-licenc|

## <a name="rates-for-azure-meters"></a>Az Azure-mérőszámok díjszabása

Ezek a mérőszámok költsége Azure RateCard API-n keresztül kérheti le. A díjszabás lekérése egy azure-mérőszámok információkért lásd: [Azure-előfizetésben használt erőforrások kapcsolatos díja és metaadatok beolvasása](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>További lépések
Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok a foglalást az Azure-hoz?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure-foglalások kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
