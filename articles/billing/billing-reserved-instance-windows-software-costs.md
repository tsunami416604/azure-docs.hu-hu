---
title: Az Azure-ban foglalt szoftverek költségei | Microsoft Docs
description: Megtudhatja, hogy mely szoftver-mérőszámok nem szerepelnek az Azure Reserved VM instance costs szolgáltatásban.
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 52e2e2503303c2a7525a3a6c156f648c097b27dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478623"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>A Azure Reserved VM Instances nem tartalmazott szoftveres költségek

A virtuális gép fenntartott példánya és az SQL szolgáltatás számára fenntartott kapacitásra vonatkozó kedvezmények csak az infrastrukturális költségekre vonatkoznak, és nem a szoftver költségeire. Ha Windows rendszerű virtuális gépet használ, és nem rendelkezik Azure Hybrid Benefit a fenntartott virtuálisgép-példányokon, akkor a következő szakaszban felsorolt szoftverekért kell fizetnie. Az SQL-alapú központi telepítések esetén az IP-díjat a rendszer továbbra is külön fogyasztásmérővel terheli, ha Azure Hybrid Benefit nincs bejelölve.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>A foglalási díj nem tartalmazza a Windows-szoftverek mérőszámait

| Fogyasztásmérő azonosítója | MeterName a használati fájlban | A virtuális gép használja |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Foglalás – Windows példányai burst (1 mag) | B sorozat |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Foglalás – Windows példányai burst (2 mag) | B sorozat |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Foglalás – Windows példányai burst (4 mag) | B sorozat |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Foglalás – Windows példányai burst (8 mag) | B sorozat |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Foglalás – Windows példányai (1 mag) | Mind a B sorozat kivételével |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Foglalás – Windows példányai (2 mag) | Mind a B sorozat kivételével |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Foglalás – Windows példányai (4 mag) | Mind a B sorozat kivételével |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Foglalás – Windows példányai (6 mag) | Mind a B sorozat kivételével |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Foglalás – Windows példányai (8 mag) | Mind a B sorozat kivételével |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Foglalás – Windows példányai (12 mag) | Mind a B sorozat kivételével |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Foglalás – Windows példányai (16 mag) | Mind a B sorozat kivételével |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Foglalás – Windows példányai (20 mag) | Mind a B sorozat kivételével |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Foglalás – Windows példányai (24 mag) | Mind a B sorozat kivételével |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Foglalás – Windows példányai (32 mag) | Mind a B sorozat kivételével |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Foglalás – Windows példányai (40 mag) | Mind a B sorozat kivételével |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Foglalás – Windows példányai (64 mag) | Mind a B sorozat kivételével |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Foglalás – Windows példányai (72 mag) | Mind a B sorozat kivételével |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Foglalás – Windows példányai (128 mag) | Mind a B sorozat kivételével |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Foglalás – Windows példányai (256 mag) | Mind a B sorozat kivételével |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Foglalás – Windows példányai (96 mag) | Mind a B sorozat kivételével |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>A Cloud Services-szoftverek nem tartalmazzák a foglalási költségeket

| Fogyasztásmérő azonosítója | MeterName a használati fájlban |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU-licenc|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU-licenc|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU-licenc|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU-licenc|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU-licenc|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU-licenc|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU-licenc|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU-licenc|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU-licenc|

## <a name="rates-for-azure-meters"></a>Az Azure-fogyasztásmérők díjai

Ezen mérőszámok költségeit az Azure RateCard API-n keresztül érheti el. További információ az Azure-mérőszám díjszabásának beszerzéséről: az [Azure-előfizetésben használt erőforrások árának és metaadat](/previous-versions/azure/reference/mt219004(v=azure.100))-információinak beolvasása.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure-beli foglalásokról, tekintse meg a következő cikkeket:

- [Mik az Azure-foglalások?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure-beli foglalások kezelése](billing-manage-reserved-vm-instance.md)
- [A foglalási kedvezmény alkalmazási módjának megismerése](billing-understand-vm-reservation-charges.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
