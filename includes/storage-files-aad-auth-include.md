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
ms.openlocfilehash: 8817f5c7ab53c5b0ebb0e8adf8c6f0f7b8a2ff42
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372322"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül [a helyszíni Active Directory tartományi szolgáltatások (AD DS) és a](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md)használatával. Ez a cikk azt ismerteti, hogy az Azure-fájlmegosztás hogyan használhatja a helyszíni vagy az Azure-beli tartományi szolgáltatásokat az Azure-fájlmegosztás SMB-alapú hozzáférésének támogatásához. Az identitás-alapú hozzáférés engedélyezése az Azure-fájlmegosztás lehetővé teszi a meglévő fájlkiszolgálók az Azure-fájlmegosztás nélküli cseréjét a meglévő címtárszolgáltatás cseréje nélkül, a megosztások zökkenőmentes felhasználói hozzáférésének fenntartása mellett. 

Azure Files érvényesíti a felhasználói hozzáférés engedélyezését mind a megosztás, mind a könyvtár/fájl szintjén. A megosztási szintű engedélyek hozzárendelése a [szerepköralapú hozzáférés-vezérlési (RBAC)](../articles/role-based-access-control/overview.md) modellen keresztül felügyelhető Azure Active Directory (Azure ad) felhasználókkal vagy csoportokkal végezhető el. A RBAC a fájlokhoz való hozzáféréshez használt hitelesítő adatokat elérhetővé kell tennie vagy szinkronizálni kell az Azure AD-vel. Az Azure AD-ben olyan beépített szerepköröket rendelhet hozzá, mint például a Storage file-adat SMB-megosztás olvasója az Azure AD-beli felhasználók vagy csoportok számára az Azure-fájlmegosztás olvasási hozzáférésének biztosításához.

A könyvtár/fájl szintjén a Azure Files [a Windows-fájlkiszolgálók ugyanúgy,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) mint bármely Windows-fájlkiszolgáló megőrzését, öröklését és kényszerítését támogatja. Dönthet úgy, hogy megtartja a Windows DACL-t, amikor az adatok másolása SMB-kapcsolaton keresztül történik a meglévő fájlmegosztás és az Azure-fájlmegosztás között. Függetlenül attól, hogy szeretné-e érvényesíteni az engedélyt, vagy sem, az Azure-fájlmegosztás használatával biztonsági mentést készíthet az ACL-ekkel az adataival együtt. 
