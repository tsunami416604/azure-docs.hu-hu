---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095591"
---
> [!IMPORTANT]
> - Előzetes verziójának blobok és üzenetsorok az Azure AD-hitelesítés csak nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem érhetők el jelenleg. Ha az Azure AD-hitelesítés még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban.
>
> - Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.
>
> - Az Azure AD-hitelesítést kell a HTTPS PROTOKOLLT használja a blob és üzenetsor-műveletek hívásakor.
>
> - Az előzetes kiadásban az Azure Portalon nem használja az Azure AD hitelesítő adatait a blob és üzenetsor-adatok olvasását és írását. Ehelyett a portál továbbra is támaszkodik a hozzáférési kulcsát. Megtekintéséhez, vagy a portálon a blob- vagy várólista adatok frissítése, a felhasználó hozzá kell rendelni egy RBAC-szerepkör, amely magában foglalja a [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), amely engedélyt ad meghívásához [- Storage-fiókok Kulcsok listázását](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). A blobok és üzenetsorok közreműködő és olvasó szerepkörök jelenleg nem tartalmazzák a **listkeys műveletének** művelet részeként az előzetes kiadás, és ezért nem biztosítható a hozzáférés az Azure Portalon keresztül. Ismerkedés az identitásalapú hozzáférés blob és üzenetsor az adatokat az előzetes verzióban, használja a PowerShell vagy az Azure CLI.
