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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536581"
---
[Az Azure Files](../articles/storage/files/storage-files-introduction.md) támogatja az identitásalapú hitelesítést a kiszolgálói üzenetblokkon (SMB) a [helyszíni Active Directory tartományi szolgáltatásokon (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (előzetes verzió) és [az Azure Active Directory tartományi szolgáltatásokon (Azure AD DS)](../articles/active-directory-domain-services/overview.md)keresztül. Ez a cikk arra összpontosít, hogy az Azure fájlmegosztások hogyan használhatják a tartományi szolgáltatásokat, akár a helyszíni, akár az Azure-ban, hogy támogassák az Azure-fájlmegosztásokhoz való identitásalapú hozzáférést az SMB-n keresztül. Az identitásalapú hozzáférés engedélyezése az Azure-fájlmegosztásokhoz lehetővé teszi, hogy a meglévő fájlkiszolgálókat a meglévő címtárszolgáltatás cseréje nélkül cserélje le azure-fájlmegosztásokkal, így a megosztásokhoz való zökkenőmentes felhasználói hozzáférés fenntartása. 

Az Azure Files érvényesíti a felhasználói hozzáférés engedélyezését mind a megosztási, mind a könyvtár-/fájlszintekhez. A megosztásszintű engedély-hozzárendelés az Azure Active Directory (Azure AD) felhasználóin vagy a [szerepköralapú hozzáférés-vezérlési (RBAC)](../articles/role-based-access-control/overview.md) modellen keresztül felügyelt csoportokon hajtható végre. Az RBAC használatával a fájlhozzáféréshez használt hitelesítő adatoknak elérhetőnek kell lenniük, vagy szinkronizálva kell lenniük az Azure AD-vel. A beépített RBAC-szerepköröket, például a storage file data SMB Share Reader-t hozzárendelheti az Azure AD felhasználóihoz vagy csoportjaihoz, hogy olvasási hozzáférést biztosítson egy Azure-fájlmegosztáshoz.

A könyvtár/fájl szintjén az Azure Files ugyanúgy támogatja a [Windows dakta-k](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) megőrzését, öröklését és érvényesítését, mint bármely Windows-fájlkiszolgáló. A Windows DAK-ok megtartása az Adatok SMB-n keresztüli másolásakor a meglévő fájlmegosztás és az Azure-fájlmegosztások között. Akár kívánja érvényesíteni az engedélyezést, akár nem, az Azure-fájlmegosztások segítségével biztonsági másolatot kaphat az ACL-król az adatokkal együtt. 
