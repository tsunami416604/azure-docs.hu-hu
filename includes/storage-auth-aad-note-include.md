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
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251876"
---
> [!NOTE]
> - Előzetes verziójának blobok és üzenetsorok az Azure AD-hitelesítés csak nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem érhetők el jelenleg. Ha az Azure AD-hitelesítés még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban.
>
> - Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.
>
> - OAuth-jogkivonatának blob és üzenetsor-műveletek engedélyezése, a HTTPS kell használnia.
>
> - Az Azure Portalon mostantól támogatja az Azure AD hitelesítő adatok használatával olvasása és írása a blob és üzenetsoradatot, részeként az előzetes kiadás. Az Azure Portallal blob és üzenetsor adatok eléréséhez, a felhasználó kell hozzárendelni az Azure Resource Manager-olvasó RBAC szerepkör, a blob és üzenetsor-hozzáférés megfelelő előzetes szerepkör mellett. További információkért lásd: [hozzáférést biztosítani Azure-tárolók és az Azure Portalon (előzetes verzió) az RBAC üzenetsorok](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jelenleg használja a tárfiók kulcsának blob és üzenetsor adatok elérését. OAuth-hozzáférés blobok esetében támogatott.
>
> - Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../articles/storage/files/storage-files-active-directory-overview.md).