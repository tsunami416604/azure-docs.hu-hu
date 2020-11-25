---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026659"
---
A Key Vault legfeljebb 1024 hozzáférésiszabályzat-bejegyzést támogat, amelyek mindegyike külön engedélyeket biztosít az adott rendszerbiztonsági tag számára. Ennek a korlátozásnak a miatt javasoljuk, hogy a hozzáférési szabályzatokat felhasználók csoportjaihoz, ahol lehetséges, az egyes felhasználók helyett. A csoportok használata sokkal egyszerűbbé teszi a szervezeten belüli több személy engedélyeinek kezelését. További információ: [alkalmazás-és erőforrás-hozzáférés kezelése Azure Active Directory csoportok használatával](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

A Key Vault hozzáférés-vezérléssel kapcsolatos részletes információkért lásd [: Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](../articles/key-vault/general/overview-security.md#identity-and-access-management).