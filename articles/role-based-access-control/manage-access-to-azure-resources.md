---
title: Azure-erőforrások az Azure Active Directoryval való hozzáférés kezelése
description: További tudnivalók az Azure Active Directory különböző funkciókat használ az Azure erőforrásokhoz való hozzáférés kezelésének módjai.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: f8f8af1380dc47a4a97845d9d47ac17bd4bba3e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Azure-erőforrások az Azure Active Directoryval való hozzáférés kezelése

Felhőalapú erőforrások identitások és hozzáférések felügyeletéhez, akkor egy a felhőt használó bármely szervezet kritikus függvény. Azure Active Directory (Azure AD) az identitás- és hozzáférés a Microsoft Azure rendszerhez.  

A támogató használata előtt a beállítást, az Azure AD területek, tekintse meg a következő videót: "Az Azure felhőbe SSO, a szerepkörök alapján hozzáférés-vezérlés és a feltételes hozzáférés zárolása." Azt hogy további tudnivalók:

- Gyakorlati tanácsok a helyszíni Active Directoryval az egyszeri bejelentkezés az Azure-portálon való konfigurálásához.
- Használja az Azure RBAC erőforrásokhoz előfizetések a minden részletre kiterjedő hozzáférés-vezérléshez.
- Erős hitelesítés szabályok Azure AD feltételes hozzáférés használatának kényszerítése.
- A felügyelt Szolgáltatásidentitás, ahol Azure-erőforrások automatikusan érheti el az Azure-szolgáltatásokat, és a fejlesztők API-kulcsokat és titkos kulcsok kezeléséhez nincs szükség fogalmát.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>A szolgáltatás területek
Az Azure AD az Azure erőforrásokhoz való hozzáférés kezelése a következő lehetőségeket biztosítja:

|||
|---|---|
| [Azure AD-bérlőt és -előfizetések közötti kapcsolat](rbac-and-directory-admin-roles.md) | További tudnivalók az Azure AD egy a megbízható felhasználók és csoportok az Azure-előfizetéssel. |
| [Szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) | A felhasználók, csoportok vagy szolgáltatásnevekről rendelt szerepkörök részletes hozzáféréskezelést kínálnak. |
| [Az RBAC privileged Identity Management](pim-azure-resource.md) | Vezérlő magas kiemelt szerepköröket just-in-time hozzárendelésével emelt szintű hozzáférés. |
| [Feltételes hozzáférés az Azure felügyeleti](conditional-access-azure-management.md) | Engedélyezi vagy letiltja a hozzáférést az Azure felügyeleti végpontok feltételes hozzáférés szabályzatainak beállítása. |
| [Felügyelt Szolgáltatásidentitás (MSI)](../active-directory/pp/msi-overview.md) | Adjon az Azure-erőforrások, például virtuális gépek saját identitás, így nem szükséges hitelesítő adatok be a kódját. |

 