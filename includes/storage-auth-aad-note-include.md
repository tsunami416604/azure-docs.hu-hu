---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372665"
---
> [!NOTE]
> - RBAC szerepkör-hozzárendelések propagálása akár két percet is igénybe vehet.
>
> OAuth-jogkivonatának blob és üzenetsor-műveletek engedélyezése, a HTTPS kell használnia.
>
> - Az Azure Portalon mostantól támogatja az Azure AD hitelesítő adatok használatával olvasása és írása a blob és üzenetsoradatot, részeként az előzetes kiadás. Az Azure Portallal blob és üzenetsor adatok eléréséhez, a felhasználó hozzá kell rendelni az Azure Resource Manager **olvasó** szerepkör, a megfelelő szerepkört a blob- vagy várólista hozzáférés mellett. További információkért lásd: [hozzáférést biztosítani Azure-tárolók és az Azure Portalon az RBAC üzenetsorok](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jelenleg használja a tárfiók kulcsának blob és üzenetsor adatok elérését. Ha a kulcs nem érhető el, majd az Azure AD engedélyezési szolgál hozzáférés a blobokhoz. Az Azure AD engedélyezési jelenleg nem támogatott sorra. 
>
> - Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../articles/storage/files/storage-files-active-directory-overview.md).