---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2dc0f2dea752dd44ab2805a0d6fc0e5517f5c6c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48831704"
---
[Az Azure Files](../articles/storage/files/storage-files-introduction.md) azonosító-alapú hitelesítést támogatja SMB (Server Message Block) (előzetes verzió) révén [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/active-directory-ds-overview.md). A tartományhoz csatlakoztatott Windows virtuális gépek (VM) hozzáférhet az Azure-fájlmegosztások [Azure ad-ben](../articles/active-directory/fundamentals/active-directory-whatis.md) hitelesítő adatokat. 

Az Azure AD akkor hitelesíti az identitás, például egy felhasználó, csoport vagy az egyszerű szolgáltatás [szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md). Az Azure Files eléréséhez használt gyakori jogosultságkészletek építőelemekkel egyéni RBAC-szerepkörök határozhatja meg. Ha az egyéni RBAC szerepkör lehet hozzárendelni egy Azure AD identity, hogy identitás hozzáférést kap az Azure-fájlmegosztás szerint ezeket az engedélyeket.

Az előzetes verziójának részeként az Azure Files is támogatja a megőrzi, öröklődés és kényszerítése [NTFS DACL-ek](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) az összes fájlokat és könyvtárakat a fájlmegosztásban. Ha másol adatokat egy fájlmegosztás az Azure Files, vagy fordítva, megadhatja, hogy az NTFS DACL-ek karbantartása. Ily módon valósítható meg, biztonsági mentés használatával az Azure Files között, megőrizve az NTFS DACL-ek a helyszíni fájlmegosztások és a felhőalapú fájlmegosztását között. 

> [!NOTE]
> SMB-n keresztül az Azure AD-hitelesítés nem támogatott Linux rendszerű virtuális gépek előzetes kiadásban. Csak a Windows Server virtuális gépek támogatottak.

> SMB-n keresztül az Azure AD-hitelesítés nem támogatott vagy AD-vel az Azure-fájlokhoz fér hozzá a helyszíni gépek vagy az AAD-hitelesítő adatokat.

> Az Azure AD-hitelesítés csak 2018. szeptember 24. után létrehozott storage-fiókok esetében érhető el.
