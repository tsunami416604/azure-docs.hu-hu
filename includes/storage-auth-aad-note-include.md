---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac7aa294dee25a6dea4ec2ee614f2b2d34d3a6bc
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49117677"
---
> [!IMPORTANT]
> - Előzetes verziójának blobok és üzenetsorok az Azure AD-hitelesítés csak nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem érhetők el jelenleg. Ha az Azure AD-hitelesítés még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban.
>
> - Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.
>
> - Az Azure AD-hitelesítést kell a HTTPS PROTOKOLLT használja a blob és üzenetsor-műveletek hívásakor.
>
> - Az Azure Portalon mostantól támogatja az írási és olvasási Blobadatok az előzetes kiadás részeként az Azure AD hitelesítő adataival.
> 
> - Az Azure portal jelenleg nem támogatja az Azure AD hitelesítő adataival sorban lévő adatok írására és olvasására. A storage-fiókkulcsok segítségével érhető el a sorban lévő adatok.
>
> - [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jelenleg használja a tárfiók kulcsának blob és üzenetsor adatok elérését.


