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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565085"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül [Active Directory (ad)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (előzetes verzió) és [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA) használatával. Ez a cikk arra összpontosít, hogy a Azure Files hogyan használhatja ki a helyszíni vagy az Azure-beli tartományi szolgáltatásokat az SMB-alapú Azure Files identitáson alapuló hozzáférésének támogatásához. Így egyszerűen lecserélheti a meglévő fájlkiszolgálók Azure Files és továbbra is használhatja a meglévő címtárszolgáltatást, így zökkenőmentesen kezelheti a felhasználók hozzáférését a megosztásokhoz. 

Azure Files érvényesíti az engedélyezést a felhasználói hozzáféréshez a megosztáshoz és a könyvtárhoz/fájlhoz. A megosztási szintű engedélyek hozzárendelése a tipikus [szerepköralapú hozzáférés-vezérlési (RBAC)](../articles/role-based-access-control/overview.md) modell által felügyelt Azure ad-felhasználókhoz vagy-csoportokhoz rendelhető hozzá. A RBAC a fájlokhoz való hozzáféréshez használt hitelesítő adatokat elérhetővé kell tennie vagy szinkronizálni kell az Azure AD-vel. Az Azure AD-ben olyan beépített RBAC-szerepköröket rendelhet hozzá, mint például a Storage file-adatsmb-megosztás olvasója az Azure AD-beli felhasználókhoz vagy csoportokhoz olvasási hozzáférést biztosítva az Azure-fájlmegosztást.

A könyvtár/fájl szintjén a Azure Files [a Windows-fájlkiszolgálók ugyanúgy,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) mint bármely Windows-fájlkiszolgáló megőrzését, öröklését és kényszerítését támogatja. Ha egy fájlmegosztás SMB-kapcsolatát másolja Azure Filesre, vagy fordítva, megtarthatja a Windows-DACL-t. Függetlenül attól, hogy szeretné-e kikényszeríteni az engedélyezést vagy sem, kihasználhatja a Azure Filest az ACL-ek biztonsági mentési listáival együtt. 
