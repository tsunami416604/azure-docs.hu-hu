---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854556"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md)használatával. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) hitelesítő adatok használatával férhetnek hozzá az Azure-fájlmegosztást.

[Szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md)használatával kezelheti Azure Files megosztási szintű hozzáférését egy identitáshoz, például egy felhasználóhoz vagy egy csoporthoz az Azure ad-ben. Meghatározhatja azokat az egyéni RBAC-szerepköröket, amelyek a Azure Files eléréséhez használt engedélyek közös készleteit tartalmazzák. Ha az egyéni RBAC szerepkört egy Azure AD-identitáshoz rendeli hozzá, az identitás hozzáférést kap az Azure-fájlmegosztás számára az engedélyek alapján.

A Azure Files a fájlmegosztás összes fájlján és könyvtárán is támogatja az [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) -fájlok megőrzését, öröklését és érvényesítését. Ha egy fájlmegosztás adatait átmásolja Azure Filesra, vagy fordítva, megadhatja, hogy az NTFS DACL-fájlok karbantartása megtörténjen. Így a biztonsági mentési forgatókönyvek Azure Files használatával valósíthatók meg, megőrizve az NTFS-DACL-t a helyszíni fájlmegosztás és a Felhőbeli fájlmegosztás között. 

> [!NOTE]
> - A Linux rendszerű virtuális gépek esetében az Azure AD DS-hitelesítés nem támogatott a Server Message Block (SMB) eléréséhez. Kizárólag Windows-alapú virtuális gépek támogatottak.
> - Az Azure AD DS-hitelesítés SMB-hozzáféréshez Active Directory tartományhoz csatlakoztatott gépek esetében nem támogatott. Az átmeneti környezetekben érdemes [Azure file Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) az adatok áttelepítésének megkezdéséhez Azure Files és a hozzáférés-vezérlés kényszerítéséhez Active Directory hitelesítő adatok használatával a helyszíni Active Directory tartományhoz csatlakoztatott gépekről. 
> - Az Azure AD DS-hitelesítés az SMB-hozzáféréshez csak a 2018. szeptember 24. után létrehozott Storage-fiókok esetében érhető el.
> - Az Azure AD DS hitelesítés az SMB-hozzáféréshez és az NTFS-DACL megőrzése nem támogatott a Azure File Sync által felügyelt Azure-fájlmegosztás esetén.
> - Az Azure AD DS-hitelesítés nem támogatja az Azure AD DS-ben létrehozott gépi fiókok hitelesítését.
