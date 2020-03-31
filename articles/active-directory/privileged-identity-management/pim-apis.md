---
title: Microsoft Graph API-k pim (előzetes verzió) számára – Azure AD | Microsoft dokumentumok
description: Tájékoztatást nyújt a Microsoft Graph API-k az Azure AD kiemelt identitáskezelés (PIM) (előzetes verzió) használatával kapcsolatban.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75638663"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph API-k kiemelt identitáskezeléshez (előzetes verzió)

Az Azure Active Directory Microsoft Graph [API-k](https://developer.microsoft.com/graph/docs/concepts/overview) használatával az összes kiemelt identitáskezelési feladatot elvégezheti. Ez a cikk a Kiemelt identitáskezelés Microsoft Graph API-k használatának fontos fogalmait ismerteti.

A Microsoft Graph API-król az [Azure AD kiemelt identitáskezelési API-hivatkozáscímű tekintse](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)meg.

> [!IMPORTANT]
> A Microsoft Graph /beta verzióalatti API-k előzetes verzióban vannak elérhetők, és változhatnak. Ezek az API-k éles alkalmazásokban való használata nem támogatott.
>
> Bár a PIM-műveletek et a Graph API-parancsaink támogatják, egy másik rendszert használunk az aktiválási kérelmek jóváhagyásához. Graph API jóváhagyásra jelenleg fejlesztés alatt áll, és fel kell szabadítani az elkövetkező néhány hónapban.

## <a name="required-permissions"></a>Szükséges engedélyek

A Kiemelt identitáskezelés Microsoft Graph API-k hívásához az alábbi engedélyek **közül legalább egynek** rendelkeznie kell:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Ahhoz, hogy az alkalmazások meghívja a Microsoft Graph API-k kiemelt identitáskezelés, rendelkezniük kell a szükséges engedélyekkel. A szükséges engedélyek megadásának legegyszerűbb módja az [Azure AD jóváhagyási keretrendszer](../develop/consent-framework.md)használata.

### <a name="set-permissions-in-graph-explorer"></a>Engedélyek beállítása a Graph Explorerben

Ha a Graph Explorer segítségével teszteli a hívásokat, megadhatja az engedélyeket az eszközben.

1. Jelentkezzen be a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) be globális rendszergazdaként.

1. Kattintson **az engedélyek módosítása gombra.**

    ![Graph Explorer - engedélyek módosítása](./media/pim-apis/graph-explorer.png)

1. Jelölje be a felvenni kívánt engedélyek melletti jelölőnégyzeteket. `PrivilegedAccess.ReadWrite.AzureAD`még nem érhető el a Graph Explorer programban.

    ![Graph Explorer - engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson **az Engedélyek módosítása gombra** az engedélymódosítások alkalmazásához.

## <a name="next-steps"></a>További lépések

- [Azure AD kiemelt identitáskezelési API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
