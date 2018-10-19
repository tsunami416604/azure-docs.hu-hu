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
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430357"
---
> [!NOTE]
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
>
> - Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../articles/storage/files/storage-files-active-directory-overview.md).



