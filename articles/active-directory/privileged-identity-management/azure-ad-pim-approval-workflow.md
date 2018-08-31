---
title: Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek |} A Microsoft Docs
description: Megtudhatja, hogyan jóváhagyja vagy elutasítja az Azure AD Privileged Identity Management (PIM) az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189158"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek

A Privileged Identity Management jóváhagyást kér az aktiválási szerepkörök konfigurálása, és válasszon egy vagy több felhasználót, vagy delegált jóváhagyók csoportot.

## <a name="view-pending-approvals-requests"></a>függőben lévő jóváhagyások (kérelmek) megtekintése

Delegált jóváhagyónak Ha a kérelem jóváhagyásra váró kap e-mail-értesítések. Szeretné megtekinteni ezeket a kérelmeket a PIM-portál, az irányítópult (az új navigáció) jelölje ki a bal oldali navigációs sávon a "Függőben lévő jóváhagyási kérelmek" fülre.

![](media/azure-ad-pim-approval-workflow/image023.png)

Itt láthatja a függőben lévő jóváhagyási kérelmek listáját:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Hagyja jóvá vagy utasítsa a kérelmek szerepkör jogosultságszint-emelési kérés (egyetlen és/vagy tömeges)

Válassza ki a kívánt jóváhagyása vagy elutasítása kérelmeket, és kattintson a gombra a művelet sáv, amely megfelel a döntéseiben:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Adja meg a jóváhagyási vagy elutasítási indoklása

Ekkor megnyílik egy új panel jóváhagyja vagy elutasítja a több kérés egyszerre. Adja meg a döntést, és kattintson jóváhagyása (vagy elutasítása) az alsó vagy a panelen:

![](media/azure-ad-pim-approval-workflow/image029.png)

A folyamat befejeződése után az állapotjelzőben díjainak hozott döntés (ebben a példában a döntést a jóváhagyása):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>További lépések

- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
- [A PIM e-mail-értesítések](pim-email-notifications.md)
