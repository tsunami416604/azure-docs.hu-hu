---
title: Munkamenet-viselkedés konfigurálása – Azure Active Directory B2C | Microsoft Docs
description: Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f5400b47c1e0b4657e40d2c57f8212711bbdaf3f
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927071"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban

Az [egyszeri bejelentkezés (SSO) munkamenet](session-overview.md) -kezelője Azure Active Directory B2C (Azure ad B2C) lehetővé teszi a rendszergazda számára, hogy a felhasználó már hitelesített hitelesítése után vezérelje a felhasználókkal való interakciót. A rendszergazda például megadhatja, hogy megjelenjen-e az identitás-szolgáltatók kiválasztása, vagy hogy meg kell-e adni a fiók adatait. Ez a cikk a Azure AD B2C egyszeri bejelentkezéses beállításainak konfigurálását ismerteti.

## <a name="session-behavior-properties"></a>Munkamenet viselkedési tulajdonságai

A webalkalmazás-munkamenetek kezeléséhez a következő tulajdonságokat használhatja:

- **Webalkalmazás-munkamenet élettartama (perc)** – sikeres hitelesítés után a felhasználó böngészőjében tárolt Azure ad B2C's-munkamenet-cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimum (inkluzív) = 15 perc.
    - Maximum (inkluzív) = 1440 perc.
- **Webalkalmazás-munkamenet időtúllépése** – a [munkamenet lejárati típusa](session-overview.md#session-expiry-type), *működés közbeni*vagy *abszolút*értéke. 
- **Egyszeri bejelentkezés konfigurálása** – az egyszeri bejelentkezés (SSO) viselkedési [hatóköre](session-overview.md#session-scope) több alkalmazás és felhasználói folyamat között a Azure ad B2C-bérlőben. 


## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőt tartalmazza.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza ki a **Tulajdonságok** elemet.
7. Konfigurálja a **webalkalmazás-munkamenet élettartamát (perc)**, a **webalkalmazás-munkamenet időkorlátját**, az **egyszeri bejelentkezés konfigurációját**, és szükség szerint adja **meg az azonosító tokent a kijelentkezési kérésekben** .

    ![A munkamenet viselkedési tulajdonságának beállításai a Azure Portalban](./media/session-behavior/session-behavior.png)

8. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

- További információ a [Azure ad B2C-munkamenetről](session-overview.md).