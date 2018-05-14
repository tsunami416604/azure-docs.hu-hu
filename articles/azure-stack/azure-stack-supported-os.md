---
title: Támogatott vendég operációs rendszerek Azure verem |} Microsoft Docs
description: A vendég operációs rendszerek használható Azure veremben.
services: azure-stack
documentationcenter: ''
author: Brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: d90ac726ae689cf299ee41d4e7ff4c17769e7455
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure-veremben támogatott vendég operációs rendszerek

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="windows"></a>Windows

Az Azure verem támogatja a Windows operációs rendszereiről a következő táblázatban:

| Operációs rendszer | Leírás | Elérhető a piactéren |
| --- | --- | --- | --- | --- | --- |
| A Windows Server verzió 1709 | 64 bites | A tárolók Core |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter Core tárolók adatközpont |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját rendszerkép kapcsolása |
| Windows 10 *(lásd az 1. megjegyzés)* | 64 bites, Pro és Enterprise | Saját rendszerkép kapcsolása |

***1. Megjegyzés:*** *telepítheti a Windows 10-es ügyfél operációs rendszereket Azure veremben, rendelkeznie kell [Windows felhasználói licencelési](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) vagy a vételi keresztül egy minősített több-Bérlős szolgáltató ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Piactéren elérhető rendszerkép fizetési,-akkor-használható vagy BYOL (EA/SPLA) licencelési érhetők el. Mindkét szolgáltatás egyetlen Azure veremben példányra használata nem támogatott. A telepítés során Azure verem esetében a vendégügynök megfelelő verzióját a lemezképpel.

 Datacenter kiadások érhetők el a piactérre letöltése; az ügyfelek a saját kiszolgáló képek, többek között a többi kiadás is kapcsolja. A Windows ügyfél képek nem érhetők el, a piactéren.

## <a name="linux"></a>Linux

Linux terjesztésekről tulajdonosaként elérhető a piactéren tartalmazza a szükséges Windows Azure Linux ügynök (WALA). Ha később saját rendszerkép Azure verem, kövesse az útmutatást a [Azure verem lemezképek hozzáadása Linux](azure-stack-linux.md).

> [!NOTE]
> Egyéni lemezképek kell kialakítani, nyilvános WALA legújabb verziójával. Előfordulhat, hogy Azure verem 2.2.18 régebbi verziói nem működnek majd megfelelően.
>
> [felhő inicializálás](https://cloud-init.io/) Azure veremben jelenleg nem támogatott.

| Disztribúció | Leírás | Közzétevő | Piactér |
| --- | --- | --- | --- | --- | --- |
| CentOS-alapú 6.9. | 64 bites | Az engedélyezetlen Wave | Igen |
| 7.4 centOS-alapú | 64 bites | Az engedélyezetlen Wave | Igen |
| ClearLinux | 64 bites | ClearLinux.org | Igen |
| Tároló Linux |  64 bites | CoreOS | Stable |
| Debian 8 "Jessie" | 64 bites | credativ |  Igen |
| Debian 9 "Stretch" | 64 bites | credativ | Igen |
| Red Hat Enterprise Linux 7.x | 64 bites | Red Hat |Saját rendszerkép kapcsolása |
| SLES 11SP4 | 64 bites | SUSE | Igen |
| SLES 12SP3 | 64 bites | SUSE | Igen |
| Ubuntu 14.04-es lts verzió | 64 bites | Canonical | Igen |
| Ubuntu 16.04-es lts verzió | 64 bites | Canonical | Igen |
| Ubuntu 18.04-es lts verzió | 64 bites | Canonical | Igen |

Más Linux terjesztésekről lehet, hogy a jövőben támogatja.

Red Hat Enterprise Linux-támogatás információkért tekintse meg [Red Hat és Azure verem: gyakran ismételt kérdések](https://access.redhat.com/articles/3413531).
