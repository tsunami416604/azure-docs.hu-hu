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
ms.openlocfilehash: fbc5ca4d433be65d43ae535703cc7f765dda0a1e
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292687"
---
> [!NOTE]
> - Előzetes verziójának blobok és üzenetsorok az Azure AD-hitelesítés csak nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem érhetők el jelenleg. Ha az Azure AD-hitelesítés még nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokeneket az alkalmazásokban.
>
> - Az előzetes verzióban RBAC szerepkör-hozzárendelések eltarthat propagálása akár öt perc alatt.
>
> - OAuth-jogkivonatának blob és üzenetsor-műveletek engedélyezése, a HTTPS kell használnia.
>
> - Az Azure Portalon mostantól támogatja az Azure AD hitelesítő adatok használatával olvasása és írása a blob és üzenetsoradatot, részeként az előzetes kiadás.
> 
> - [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jelenleg használja a tárfiók kulcsának blob és üzenetsor adatok elérését.
>
> - Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../articles/storage/files/storage-files-active-directory-overview.md).



