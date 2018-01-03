---
title: "Vendég operációs rendszer támogatása, Azure verem |} Microsoft Docs"
description: "A vendég operációs rendszerek használható Azure veremben."
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: af524b5f40fb79648d91deb54186826902aa8cd2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure-veremben támogatott vendég operációs rendszerek

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="windows"></a>Windows
Azure verem a következő Windows vendég operációs rendszereket támogatja. A piactéren képek letölthetők Azure verem. A Windows ügyfél képek nem érhetők el a piactéren.

A telepítés során Azure verem biztosítja, hogy a vendégügynök megfelelő verzióját a kép be a nézetmodellbe van.

| Operációs rendszer | Leírás | Közzétevő | Operációs rendszer típusa | Piactér |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 bites | Microsoft | Windows | Adatközpont |
| Windows Server 2012 | 64 bites | Microsoft | Windows | Adatközpont |
| Windows Server 2012 R2 | 64 bites | Microsoft | Windows | Adatközpont |
| Windows Server 2016 | 64 bites | Microsoft | Windows | Datacenter, Datacenter Core tárolók adatközpont |
| Windows 7 | 64 bites, Pro és Enterprise | Microsoft | Windows | Nem |
| Windows 8.1 | 64 bites, Pro és Enterprise | Microsoft | Windows | Nem |
| Windows 10 | 64 bites, Pro és Enterprise | Microsoft | Windows | Nem |

## <a name="linux"></a>Linux

Az itt felsorolt Linux terjesztésekről tartalmazza a szükséges Windows Azure Linux ügynök (WALA). 

> [!NOTE]
> Mint 2.2.3 régebbi WALA verzióival készült képek *nem* támogatott és nem valószínű, hogy telepíteni.

| Disztribúció | Leírás | Közzétevő | Piactér |
| --- | --- | --- | --- | --- | --- |
| Tároló Linux |  64 bites | CoreOS | Stable |
| CentOS-alapú 6.9. | 64 bites | Az engedélyezetlen Wave | Igen |
| CentOS-alapú 7.3 | 64 bites | Az engedélyezetlen Wave | Igen |
| 7.4 centOS-alapú | 64 bites | Az engedélyezetlen Wave | Igen |
| Debian 8 "Jessie" | 64 bites | credativ |  Igen |
| Debian 9 "Stretch" | 64 bites | credativ | Igen |
| Oracle Linux | 64 bites | Oracle | Nem |
| Red Hat Enterprise Linux 7.x | 64 bites | Red Hat | Nem |
| SLES 11SP4 | 64 bites | SUSE | Igen |
| SLES 12SP3 | 64 bites | SUSE | Igen |
| Ubuntu 14.04-es lts verzió | 64 bites | Canonical | Igen |
| Ubuntu 16.04-es lts verzió | 64 bites | Canonical | Igen |

Más Linux terjesztésekről lehet, hogy a jövőben támogatja.




