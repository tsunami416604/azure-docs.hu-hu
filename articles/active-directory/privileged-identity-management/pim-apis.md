---
title: A Microsoft Graph API-k a PIM szolgáltatásra (előzetes verzió) |} A Microsoft Docs
description: A Microsoft Graph API-k az Azure Active Directory Privileged Identity Management (PIM) (előzetes verzió) használatát ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d853579335653987321fded154eb9fb0f8dbdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000285"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>A Microsoft Graph API-k a PIM szolgáltatásra (előzetes verzió)

Az Azure AD Privileged Identity Management (PIM) az Azure portal használatával is elvégezheti a feladatok többsége is végrehajtható a [Microsoft Graph API-k](https://developer.microsoft.com/graph/docs/concepts/overview). A cikk néhány fontos fogalmakat, a Microsoft Graph API-k használata a PIM szolgáltatásra. A Microsoft Graph API-k kapcsolatos információkért tekintse meg a [az Azure AD Privileged Identity Management API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> A /beta verzióban a Microsoft Graph API-k előzetes verzióként érhetők el, és változhatnak. Éles üzemi alkalmazások pedig a következő API-k használata nem támogatott.

## <a name="required-permissions"></a>Szükséges engedélyek

A Microsoft Graph API-k hívása a PIM szolgáltatásra, rendelkeznie kell **egy vagy több** a következő engedélyekkel:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Az alkalmazások a Microsoft Graph API-k hívása a PIM szolgáltatásra a szükséges engedélyekkel kell rendelkezniük. Adja meg a szükséges engedélyekkel a legegyszerűbb módja az, hogy használja a [Azure ad-ben hozzájárulási keretrendszer](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>A Graph Explorer engedélyeinek beállítása

A Graph Explorer használatával tesztelje a hívásokat, ha az eszköz az engedélyeket is megadhat.

1. Jelentkezzen be a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) globális rendszergazdaként.

1. Kattintson a **engedélyek módosítása**.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer.png)

1. Adja hozzá a jelölők mellett a felvenni kívánt engedélyeket. `PrivilegedAccess.ReadWrite.AzureAD` még nem áll rendelkezésre a Graph Explorer.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson a **módosítási hozzáférést** a engedélyeinek módosítása a alkalmazni.

## <a name="next-steps"></a>További lépések

- [Azure AD Privileged Identity Management API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
