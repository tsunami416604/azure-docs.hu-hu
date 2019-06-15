---
title: Az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory hozzáférési csomagjára hozzáférés kérése
description: Ismerje meg, hogyan használhatja a saját hozzáférési portál igényelhet hozzáférést egy hozzáférés-csomagot az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541555"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Igényelhet hozzáférést egy hozzáférés-csomagot az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Jelentkezzen be a saját Access Portalra

Az első lépés, hogy jelentkezzen be a saját hozzáférés portálra, ahol hozzáférést kérhet az access csomagjára.

**Előfeltétel szerepkör:** Kérelmező

1. Keresse meg az e-mailt és a egy üzenetet a projekt vagy üzleti Manager dolgozik. Az e-mailnek tartalmaznia kell egy hivatkozást a hozzáférés-csomaghoz való hozzáféréssel kell rendelkeznie. A hivatkozás kezdődik:

    `https://myaccess.microsoft.com`

1. A hivatkozás megnyitásához.

1. Jelentkezzen be a saját hozzáférés-portálon.

    Győződjön meg arról, hogy a munkahelyi fiókok használhatók. Ha nem tudja biztosan, hogy ellenőrizze a projekt vagy üzleti Managerrel.

## <a name="request-an-access-package"></a>A kérelem egy hozzáférés-csomag

Ha a hozzáférési csomagot talált a saját hozzáférés-portálon, küldhet egy kérést.

**Előfeltétel szerepkör:** Kérelmező

1. Kattintson a pipa jelre a hozzáférés-csomagot.

    ![A hozzáférési portál – a hozzáférési csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kattintson a **hozzáférés kérése** a kérelem hozzáférési panel megnyitásához.

1. Ha a **üzleti indoklás** jelenik meg, írja be a hozzáférnének indoklását.

1. Ha **adott időszakra vonatkozó kérelem?** engedélyezve, jelölje be **Igen** vagy **nem**.

1. Ha szükséges, adja meg a kezdő dátum és záró dátuma.

    ![A hozzáférési portál – hozzáférés kéréséhez](./media/entitlement-management-shared/my-access-request-access.png)

1. Ha befejezte, kattintson a **küldés** elküldeni kérelmét.

1. Kattintson a **korábbi kérések** a kérelmek és az állapot megtekintéséhez.

    A hozzáférés csomag jóváhagyást igényel, ha a kérés most már egy függőben lévő jóváhagyási állapotban van.

## <a name="cancel-a-request"></a>Egy kérés visszavonása

Ha a hozzáférési kérelem elküldését és a kérés továbbra is a **jóváhagyásra váró** állapotba, lemondhatja a szolgáltatásokat a kérelmet.

**Előfeltétel szerepkör:** Kérelmező

1. Kattintson a saját hozzáférési portál, a bal oldali **korábbi kérések** a kérelmek és az állapot megtekintéséhez.

1. Kattintson a **nézet** megszakítja a kérelemhez hivatkozást.

1. Ha a kérés továbbra is a **jóváhagyásra váró** állapotba, kattinthat **megszakítási kérés** megszakítja a kérelmet.

    ![A hozzáférési portál – a megszakítási kérelmet.](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kattintson a **korábbi kérések** annak ellenőrzéséhez, hogy a kérelem meg lett szakítva.

## <a name="select-a-policy"></a>Szabályzat kiválasztása

A kért egy hozzáférés-csomag, amelynek a több szabályzat is vonatkozik a hozzáférést, ha a előfordulhat, hogy válasszon ki egy szabályzatot kéri. Például egy hozzáférési Csomagkezelő előfordulhat, hogy konfigurálja egy hozzáférési csomagot két szabályzatokkal belső munkatársra két csoportját. Az első házirend előfordulhat, hogy hozzáférést 60 napig és jóváhagyás szükséges. A második házirend előfordulhat, hogy 2 nap engedélyezni a hozzáférést, és nem a jóváhagyás szükséges. Ha ebben a forgatókönyvben, jelöljön ki a használni kívánt házirendet.

**Előfeltétel szerepkör:** Kérelmező

## <a name="next-steps"></a>További lépések

- [Hagyja jóvá vagy utasítsa a hozzáférési kérelmek](entitlement-management-request-approve.md)
- [Folyamat és az e-mailes értesítések kérése](entitlement-management-process.md)
