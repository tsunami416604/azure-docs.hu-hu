---
title: MFA-kiszolgáló konfigurálása – Azure Active Directory
description: Ismerje meg, hogyan konfigurálhatja az Azure MFA-kiszolgáló beállításait a Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53763e62e8d7aa5b77ae451ef6427c1e09bff9f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742697"
---
# <a name="configure-mfa-server-settings"></a>MFA-kiszolgáló beállításainak konfigurálása

Ez a cikk segítséget nyújt az Azure MFA-kiszolgáló beállításainak kezelésében a Azure Portal.

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk. Azok a meglévő ügyfelek, akik aktiválták az MFA-kiszolgálót a július 1. előtt, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

A következő MFA-kiszolgáló beállításai érhetők el:

| Funkció | Leírás |
| ------- | ----------- |
| Kiszolgáló beállításai | Az MFA-kiszolgáló letöltése és az aktiválási hitelesítő adatok előállítása a környezet inicializálásához |
| [Egyszeri Mellőzés](#one-time-bypass) | Lehetővé teszi a felhasználók számára a hitelesítést a többtényezős hitelesítés végrehajtása nélkül korlátozott ideig. |
| [Gyorsítótárazási szabályok](#caching-rules) |  A gyorsítótárazás elsődlegesen akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, miközben az első kérelem még folyamatban van. Ez a funkció lehetővé teszi, hogy a következő kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen megtörtént az első ellenőrzés. |
| Kiszolgáló állapota | Tekintse meg a helyszíni MFA-kiszolgálók állapotát, beleértve a verziószámot, az állapotot, az IP-címet és az utolsó kommunikációs időt és dátumot. |

## <a name="one-time-bypass"></a>Egyszeri Mellőzés

Az egyszeri Mellőzés funkció lehetővé teszi, hogy a felhasználó egyetlen alkalommal hitelesítse magát a többtényezős hitelesítés végrehajtása nélkül. A Mellőzés ideiglenes, és a megadott számú másodperc elteltével lejár. Olyan helyzetekben, amikor a Mobile alkalmazás vagy telefon nem kap értesítést vagy telefonhívást, engedélyezheti az egyszeri mellőzést, hogy a felhasználó hozzáférhessen a kívánt erőforráshoz.

Egyszeri Mellőzés létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a **biztonsági**  >  **MFA**  >  **egyszeri mellőzése** elemet.
1. Válassza a **Hozzáadás** elemet.
1. Ha szükséges, válassza ki a replikálási csoportot a mellőzéshez.
1. Adja meg a felhasználónevet `username\@domain.com` . Adja meg azt a másodpercet, ameddig a megkerülésnek utolsónak kell lennie, és a Mellőzés oka.
1. Válassza a **Hozzáadás** elemet. Az időkorlát azonnal érvénybe lép. A felhasználónak be kell jelentkeznie, mielőtt az egyszeri Mellőzés lejár.

Ugyanezen az ablakban is megtekintheti az egyszeri mellőzési jelentést.

## <a name="caching-rules"></a>Gyorsítótárazási szabályok

Beállíthat egy időszakot, amely lehetővé teszi a hitelesítési kísérleteket a felhasználó hitelesítése után a _gyorsítótárazási_ funkció használatával. A felhasználó további hitelesítési kísérletei a megadott időszakon belül automatikusan sikeresek lesznek.

A gyorsítótárazás elsődlegesen akkor használatos, ha a helyszíni rendszerek, például a VPN, több ellenőrzési kérelmet küldenek, miközben az első kérelem még folyamatban van. Ez a funkció lehetővé teszi, hogy a következő kérelmek automatikusan sikeresek legyenek, miután a felhasználó sikeresen megtörtént az első ellenőrzés.

>[!NOTE]
> A gyorsítótárazási funkció nem használható a bejelentkezések Azure Active Directory (Azure AD) szolgáltatásba való használatra.

A gyorsítótárazás beállításához hajtsa végre a következő lépéseket:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **MFA**  >  **gyorsítótárazási szabályait**.
1. Válassza a **Hozzáadás** elemet.
1. Válassza ki a **gyorsítótár típusát** a legördülő listából. Adja meg a **gyorsítótárazási másodpercek** maximális számát.
1. Ha szükséges, válasszon ki egy hitelesítési típust, és adjon meg egy alkalmazást.
1. Válassza a **Hozzáadás** elemet.

## <a name="next-steps"></a>További lépések

Az MFA-kiszolgáló további konfigurációs lehetőségei az MFA-kiszolgáló webkonzolján is elérhetők. [A magas rendelkezésre állás érdekében az Azure MFA-kiszolgálót is konfigurálhatja](howto-mfaserver-deploy-ha.md).
