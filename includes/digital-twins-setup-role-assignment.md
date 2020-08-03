---
author: baanders
description: fájl belefoglalása a hozzáférési engedélyek lépéshez az Azure digitális Twins beállítása beállításban
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408298"
---
Az Azure Digital Twins [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) használatával szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez azt jelenti, hogy ahhoz, hogy egy felhasználó adatsíkon hívásokat hajtson végre az Azure-beli digitális Twins-példányon, a felhasználónak hozzá kell rendelnie egy, a megfelelő engedélyekkel rendelkező szerepkört.

Az Azure Digital Twins esetében ez a szerepkör az _**Azure digitális Twins tulajdonosa (előzetes verzió)**_. További információ a szerepkörökről és a biztonságról [*: az Azure Digital Twins-megoldások biztonsága*](../articles/digital-twins/concepts-security.md).

Ebből a szakaszból megtudhatja, hogyan hozhat létre szerepkör-hozzárendelést egy felhasználó számára az Azure Digital Twins-példányban, az adott felhasználó e-mail-címét használva az Azure AD-bérlőn az Azure-előfizetésében. A szervezete szerepköreitől függően saját maga is beállíthatja ezt az engedélyt, vagy beállíthat egy olyan személy nevében, aki az Azure Digital Twins-példányt fogja kezelni.

### <a name="assign-the-role"></a>A szerepkör kiosztása

Ahhoz, hogy a felhasználó engedélyeket nyújtson egy Azure Digital Twins-példány kezeléséhez, hozzá kell rendelnie az _**Azure Digital Twins tulajdonos (előzetes verzió)**_ szerepkört a példányon belül.

> [!NOTE]
> Vegye figyelembe, hogy ez a szerepkör eltér az Azure AD *tulajdonosi* szerepkörtől, amely az Azure Digital Twins-példány hatókörében is kiosztható. Ez két különböző felügyeleti szerepkör, és az Azure AD- *tulajdonos* nem biztosít hozzáférést az *Azure Digital Twins-tulajdonossal (előzetes verzió)* biztosított adatközpont-funkciókhoz.