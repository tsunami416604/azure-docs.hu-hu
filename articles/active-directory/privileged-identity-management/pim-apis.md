---
title: A PIM-hez Microsoft Graph API-k (előzetes verzió) – Azure AD | Microsoft Docs
description: Információt nyújt a Microsoft Graph API-k használatáról Azure AD Privileged Identity Management (PIM) (előzetes verzió).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6da6bffbc54bfa6e9c39ddace665eb7cfec58614
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638663"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Privileged Identity Management API-k Microsoft Graph (előzetes verzió)

Az összes Privileged Identity Management feladatot a Azure Active Directory [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview) -k használatával hajthatja végre. Ez a cikk a Privileged Identity Management Microsoft Graph API-k használatára vonatkozó fontos fogalmakat ismerteti.

A Microsoft Graph API-kkal kapcsolatos részletekért tekintse meg a [Azure ad PRIVILEGED Identity Management API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> A Microsoft Graph/Beta verziója alatti API-k előzetes verzióban érhetők el, és változhatnak. Az API-k üzemi alkalmazásokban való használata nem támogatott.
>
> Bár az összes PIM-művelet támogatott a Graph API parancsain keresztül, az aktiválási kérések jóváhagyásához egy másik rendszer használható. A jóváhagyásra Graph API jelenleg fejlesztés alatt áll, és a következő néhány hónapban lesz közzétéve.

## <a name="required-permissions"></a>Szükséges engedélyek

A Privileged Identity Management Microsoft Graph API-k meghívásához a következő engedélyek **közül egyet vagy többet** kell megadnia:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Ahhoz, hogy az alkalmazások meg tudják hívni a Privileged Identity Management Microsoft Graph API-jait, rendelkeznie kell a szükséges engedélyekkel. A szükséges engedélyek megadásának legegyszerűbb módja az [Azure ad-beli engedélyezési keretrendszer](../develop/consent-framework.md)használata.

### <a name="set-permissions-in-graph-explorer"></a>Engedélyek beállítása a Graph Explorerben

Ha a Graph Explorer segítségével teszteli a hívásokat, megadhatja az engedélyeket az eszközben.

1. Jelentkezzen be a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer) globális rendszergazdaként.

1. Kattintson az **engedélyek módosítása**elemre.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer.png)

1. Jelölje be a jelölőnégyzeteket a felvenni kívánt engedélyek mellett. a `PrivilegedAccess.ReadWrite.AzureAD` még nem érhető el a Graph Explorerben.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson az **engedélyek módosítása** elemre az engedély módosításának alkalmazásához.

## <a name="next-steps"></a>Következő lépések

- [Azure AD Privileged Identity Management API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
