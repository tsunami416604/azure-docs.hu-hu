---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269351"
---
[Az Azure Files](../articles/storage/files/storage-files-introduction.md) azonosító-alapú hitelesítést támogatja SMB (Server Message Block) (előzetes verzió) révén [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/overview.md). A tartományhoz csatlakoztatott Windows virtuális gépek (VM) hozzáférhet az Azure-fájlmegosztások [Azure ad-ben](../articles/active-directory/fundamentals/active-directory-whatis.md) hitelesítő adatokat. 

Kezelheti az Azure Files megosztási szintű hozzáféréssel az identitás, például egy felhasználó, az Azure AD-csoport [szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md). Az Azure Files eléréséhez használt gyakori jogosultságkészletek építőelemekkel egyéni RBAC-szerepkörök határozhatja meg. Ha az egyéni RBAC szerepkör lehet hozzárendelni egy Azure AD identity, hogy identitás hozzáférést kap az Azure-fájlmegosztás szerint ezeket az engedélyeket.

Az előzetes verziójának részeként az Azure Files is támogatja a megőrzi, öröklődés és kényszerítése [NTFS DACL-ek](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) az összes fájlokat és könyvtárakat a fájlmegosztásban. Ha másol adatokat egy fájlmegosztás az Azure Files, vagy fordítva, megadhatja, hogy az NTFS DACL-ek karbantartása. Ily módon valósítható meg, biztonsági mentés használatával az Azure Files között, megőrizve az NTFS DACL-ek a helyszíni fájlmegosztások és a felhőalapú fájlmegosztását között. 

> [!NOTE]
> - Az Azure AD tartományi szolgáltatások authentication for SMB-hozzáférés nem támogatott Linux rendszerű virtuális gépekhez. Kizárólag Windows-alapú virtuális gépek támogatottak.
> - Az Azure AD tartományi szolgáltatások authentication for SMB-hozzáférés nem támogatott az Active Directory-tartományhoz csatlakozó gépről. Addig is érdemes lehet kihasználhatja [Azure Files Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) indítsa el az adatok migrálását az Azure Files és hozzáférés-vezérlés használatával a helyszíni tárolóból AD credentails kényszerítéséhez folytatásához AD tartományhoz csatlakozó gépek. 
> - Az Azure AD tartományi szolgáltatások authentication for SMB-hozzáférést csak 2018. szeptember 24. után létrehozott storage-fiókok esetében érhető el.
> - Az Azure AD tartományi szolgáltatások authentication SMB-hozzáférés és állandó NTFS DACL nem támogatott az Azure File Sync szolgáltatásbeli által felügyelt Azure-fájlmegosztások. 
