---
title: Az Azure foglalások Windows szoftverek díjait |} A Microsoft Docs
description: Ismerje meg, mely Windows szoftverek mérőszámok nem szerepelnek az Azure fenntartott VM-példány költségeket.
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
ms.date: 05/09/2018
ms.author: banders
ms.openlocfilehash: de2aee36f20bd5142f398de7edb301e53ab42cae
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902661"
---
# <a name="windows-software-costs-not-included-with-azure-reserved-vm-instances"></a>Windows szoftverek díjait nem tartalmazza az Azure Reserved VM Instances

Ha az Azure Hybrid Use Benefit nem rendelkezik a fenntartott VM-példányokon, majd díja az alábbi szakaszban felsorolt Windows szoftverek mérőszámokhoz tartozó.

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

Ezek a mérőszámok költsége Azure RateCard API-n keresztül kérheti le. A díjszabás lekérése egy azure-mérőszámok információkért lásd: [Azure-előfizetésben használt erőforrások kapcsolatos díja és metaadatok beolvasása](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>További lépések
Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).



