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
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: ff3aea4e449e3d489b0c0f01345ecd9773c7d885
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure-veremben támogatott vendég operációs rendszerek

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="windows"></a>Windows
Az Azure verem támogatja a Windows operációs rendszerű vendégek felsorolt a következő táblázatban: a piactéren képek letölthetők Azure verem. A Windows ügyfél képek nem érhetők el a piactéren.

A telepítés során Azure verem esetében a vendégügynök megfelelő verzióját a lemezképpel.

| Operációs rendszer | Leírás | Elérhető a piactéren |
| --- | --- | --- | --- | --- | --- |
| A Windows Server verzió 1709 | 64 bites | A tárolók Core |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter Core tárolók adatközpont |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját rendszerkép kapcsolása |
| Windows 10 *(lásd az 1. megjegyzés)* | 64 bites, Pro és Enterprise | Saját rendszerkép kapcsolása |

***1. Megjegyzés:****telepítheti a Windows 10-es ügyfél operációs rendszereket Azure veremben, rendelkeznie kell [Windows felhasználói licencelési](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) vagy a vételi keresztül egy minősített több-Bérlős szolgáltató ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*

Piactéren elérhető rendszerkép fizetési,-akkor-használható vagy BYOL (EA/SPLA) licencelési érhetők el. Mindkét szolgáltatás egyetlen Azure veremben példányra használata nem támogatott. 

Csak a Datacenter kiadások érhetők el a piactéren; az ügyfelek a saját kiszolgáló képek, többek között a többi kiadás is kapcsolja.

## <a name="linux"></a>Linux

Az itt felsorolt Linux terjesztésekről tartalmazza a szükséges Windows Azure Linux ügynök (WALA).

> [!NOTE]   
> Egyéni lemezképek kell kialakítani, nyilvános WALA legújabb verziójával. Előfordulhat, hogy Azure verem 2.2.18 régebbi verziói nem működnek majd megfelelően.  
>
> [felhő inicializálás](https://cloud-init.io/) Azure veremben jelenleg nem támogatott.

| Disztribúció | Leírás | Közzétevő | Piactér |
| --- | --- | --- | --- | --- | --- |
| Tároló Linux |  64 bites | CoreOS | Stable |
| CentOS-alapú 6.9. | 64 bites | Az engedélyezetlen Wave | Igen |
| 7.4 centOS-alapú | 64 bites | Az engedélyezetlen Wave | Igen |
| ClearLinux | 64 bites | ClearLinux.org | Igen |
| Debian 8 "Jessie" | 64 bites | credativ |  Igen |
| Debian 9 "Stretch" | 64 bites | credativ | Igen |
| Red Hat Enterprise Linux (függőben) 7.x | 64 bites | Red Hat |Saját rendszerkép kapcsolása |
| SLES 11SP4 | 64 bites | SUSE | Igen |
| SLES 12SP3 | 64 bites | SUSE | Igen |
| Ubuntu 14.04-es lts verzió | 64 bites | Canonical | Igen |
| Ubuntu 16.04-es lts verzió | 64 bites | Canonical | Igen |

Más Linux terjesztésekről lehet, hogy a jövőben támogatja.
