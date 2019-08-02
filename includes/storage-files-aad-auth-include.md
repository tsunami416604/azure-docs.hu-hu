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
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570601"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitás-alapú hitelesítést a Server Message Block (SMB) (előzetes verzió) használatával [Azure Active Directory Domain Services (AD DS)](../articles/active-directory-domain-services/overview.md)segítségével. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) hitelesítő adatok használatával férhetnek hozzá az Azure-fájlmegosztást.

[Szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md)használatával kezelheti Azure Files megosztási szintű hozzáférését egy identitáshoz, például egy felhasználóhoz vagy egy csoporthoz az Azure ad-ben. Meghatározhatja azokat az egyéni RBAC-szerepköröket, amelyek a Azure Files eléréséhez használt engedélyek közös készleteit tartalmazzák. Ha az egyéni RBAC szerepkört egy Azure AD-identitáshoz rendeli hozzá, az identitás hozzáférést kap az Azure-fájlmegosztás számára az engedélyek alapján.

Az előzetes verzió részeként a Azure Files az [NTFS-DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) -fájlok megőrzését, öröklését és érvényesítését is támogatja egy fájlmegosztás összes fájlján és könyvtárán. Ha egy fájlmegosztás adatait átmásolja Azure Filesra, vagy fordítva, megadhatja, hogy az NTFS DACL-fájlok karbantartása megtörténjen. Így a biztonsági mentési forgatókönyvek Azure Files használatával valósíthatók meg, megőrizve az NTFS-DACL-t a helyszíni fájlmegosztás és a Felhőbeli fájlmegosztás között. 

> [!NOTE]
> - A Linux rendszerű virtuális gépek esetében az Azure AD DS-hitelesítés nem támogatott a Server Message Block (SMB) eléréséhez. Kizárólag Windows-alapú virtuális gépek támogatottak.
> - Az Azure AD DS-hitelesítés SMB-hozzáféréshez Active Directory tartományhoz csatlakoztatott gépek esetében nem támogatott. Az átmeneti környezetekben érdemes [Azure file Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) az adatok áttelepítésének megkezdéséhez Azure Files és a hozzáférés-vezérlés kényszerítéséhez Active Directory hitelesítő adatok használatával a helyszíni Active Directory tartományhoz csatlakoztatott gépekről. 
> - Az Azure AD DS-hitelesítés az SMB-hozzáféréshez csak a 2018. szeptember 24. után létrehozott Storage-fiókok esetében érhető el.
> - Az Azure AD DS hitelesítés az SMB-hozzáféréshez és az NTFS-DACL megőrzése nem támogatott a Azure File Sync által felügyelt Azure-fájlmegosztás esetén.
