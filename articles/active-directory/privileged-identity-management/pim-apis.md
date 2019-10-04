---
title: A PIM-hez Microsoft Graph API-k (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: Információt nyújt a Microsoft Graph API-k használatáról Azure AD Privileged Identity Management (PIM) (előzetes verzió).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c7f42d536880f2578c62c6c4866b21be1cc9dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804555"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>Microsoft Graph a PIM-hez készült API-k (előzetes verzió)

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) által a Azure Portal használatával elvégezhető feladatok többsége a [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview)-k használatával is végrehajtható. Ez a cikk néhány fontos fogalmat ismertet a PIM Microsoft Graph API-k használatakor. A Microsoft Graph API-kkal kapcsolatos részletekért tekintse meg a [Azure ad PRIVILEGED Identity Management API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> A Microsoft Graph/Beta verziója alatti API-k előzetes verzióban érhetők el, és változhatnak. Az API-k üzemi alkalmazásokban való használata nem támogatott.

## <a name="required-permissions"></a>Szükséges engedélyek

A PIM Microsoft Graph API-k meghívásához a következő engedélyek **közül egyet vagy többet** kell megadnia:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Ahhoz, hogy az alkalmazások meghívja a PIM Microsoft Graph API-jait, rendelkeznie kell a szükséges engedélyekkel. A szükséges engedélyek megadásának legegyszerűbb módja az [Azure ad-beli engedélyezési keretrendszer](../develop/consent-framework.md)használata.

### <a name="set-permissions-in-graph-explorer"></a>Engedélyek beállítása a Graph Explorerben

Ha a Graph Explorer segítségével teszteli a hívásokat, megadhatja az engedélyeket az eszközben.

1. Jelentkezzen be a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer) globális rendszergazdaként.

1. Kattintson az **engedélyek módosítása**elemre.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer.png)

1. Jelölje be a jelölőnégyzeteket a felvenni kívánt engedélyek mellett. `PrivilegedAccess.ReadWrite.AzureAD`még nem érhető el a Graph Explorerben.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson az **engedélyek módosítása** elemre az engedély módosításának alkalmazásához.

## <a name="next-steps"></a>További lépések

- [Azure AD Privileged Identity Management API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
