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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565085"
---
[Az Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitásalapú hitelesítést a kiszolgálói üzenetblokkon (SMB) az [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (előzetes verzió) és [az Azure Active Directory tartományi szolgáltatások (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA) szolgáltatáson keresztül. Ez a cikk arra összpontosít, hogy az Azure Files hogyan használhatja ki a tartományi szolgáltatásokat akár a helyszíni, akár az Azure-ban, hogy támogassa az Azure Files identitásalapú hozzáférését az SMB-n keresztül. Ez lehetővé teszi, hogy könnyedén cserélje le a meglévő fájlkiszolgálók at Azure Files, és továbbra is használja a meglévő címtárszolgáltatás, fenntartása zökkenőmentes felhasználói hozzáférést megosztások. 

Az Azure Files érvényesíti az engedélyezést a felhasználói hozzáférés mind a megosztás, mind a könyvtár/fájl szinten. A megosztásszintű engedély-hozzárendelés hozzárendelhető az Azure AD-felhasználókhoz vagy a tipikus [szerepköralapú hozzáférés-vezérlési (RBAC)](../articles/role-based-access-control/overview.md) modellen keresztül felügyelt csoportokhoz. Az RBAC használatával a fájlhozzáféréshez használt hitelesítő adatoknak elérhetőnek kell lenniük, vagy szinkronizálva kell lenniük az Azure AD-vel. A beépített RBAC-szerepköröket, például a storage file data SMB Share Reader-t hozzárendelheti az Azure AD felhasználóihoz vagy csoportjaihoz, hogy olvasási hozzáférést biztosítson egy Azure-fájlmegosztáshoz.

A könyvtár/fájl szintjén az Azure Files ugyanúgy támogatja a [Windows dakta-k](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) megőrzését, öröklését és érvényesítését, mint bármely Windows-fájlkiszolgáló. Ha adatokat másol smb-n keresztül egy fájlmegosztásról az Azure Files-ba, vagy fordítva, megtarthatja a Windows DAK-okat. Akár kényszeríti az engedélyezést, akár nem, az Azure Files segítségével biztonsági másolatot készíthet az ACL-okról az adatokkal együtt. 
