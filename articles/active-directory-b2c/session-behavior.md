---
title: Munkamenet-viselkedés konfigurálása – Azure Active Directory B2C | Microsoft dokumentumok
description: Munkamenet-viselkedés konfigurálása az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186811"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Munkamenet-viselkedés konfigurálása az Azure Active Directory B2C szolgáltatásában

Ez a funkció a következők részletes vezérlését biztosítja [felhasználónkénti áramlási alapon:](user-flow-overview.md)

- Az Azure AD B2C által kezelt webalkalmazás-munkamenetek élettartama.
- Egyszeri bejelentkezési (SSO) viselkedés több alkalmazás és a felhasználói folyamatok az Azure AD B2C bérlőben.

Ezek a beállítások nem érhetők el a jelszó-visszaállításfelhasználói folyamatokhoz.

Az Azure AD B2C támogatja az [OpenID Connect hitelesítési protokollt](openid-connect.md) a webes alkalmazásokba való biztonságos bejelentkezés engedélyezéséhez. A következő tulajdonságokkal kezelheti a webalkalmazás-munkameneteket:

## <a name="session-behavior-properties"></a>Munkamenet viselkedésének tulajdonságai

- **Webapp-munkamenet élettartama (perc)** – Az Azure AD B2C munkamenet-cookie-jának a felhasználó böngészőjében tárolt élettartama sikeres hitelesítés esetén.
    - Alapértelmezett = 1440 perc.
    - Minimum (bezárólag) = 15 perc.
    - Maximum (bezárólag) = 1440 perc.
- **Webapp-munkamenet időmeghosszabbítása** – Ha ez a kapcsoló **Abszolút**értékre van állítva, a felhasználónak újra hitelesítenie kell magát a **webalkalmazás munkamenetének élettartama (perc)** által megadott időszak letelte után. Ha ez a kapcsoló **gördülő** (az alapértelmezett beállítás), a felhasználó mindaddig bejelentkezve marad, amíg a felhasználó folyamatosan aktív a webalkalmazásban.
- **Egyszeri bejelentkezési konfiguráció** Ha több alkalmazás és felhasználói folyamatok a B2C bérlő, kezelheti a felhasználói interakciók közöttük az **egyszeri bejelentkezési konfigurációs** tulajdonság használatával. A tulajdonságot az alábbi beállítások egyikére állíthatja be:
    - **Bérlő** – Ez a beállítás az alapértelmezett. Ezzel a beállítással lehetővé teszi, hogy több alkalmazás és a felhasználói folyamatok a B2C-bérlő ugyanazt a felhasználói munkamenetet. Ha például egy felhasználó bejelentkezik egy alkalmazásba, a felhasználó zökkenőmentesen bejelentkezhet egy másikba, a Contoso Pharmacy-be is, amikor hozzáfér hozzá.
    - **Alkalmazás** – Ez a beállítás lehetővé teszi, hogy a felhasználói munkamenet et kizárólag egy alkalmazáshoz tartsa fenn, függetlenül más alkalmazásoktól. Ha például azt szeretné, hogy a felhasználó jelentkezzen be a Contoso Gyógyszertárba (ugyanazzal a hitelesítő adatokkal), még akkor is, ha a felhasználó már be van jelentkezve a Contoso Shopping- ba, ugyanazon a B2C-bérlőn lévő másik alkalmazásba.
    - **Házirend** – Ez a beállítás lehetővé teszi, hogy a felhasználói munkamenet et kizárólag egy felhasználói folyamat számára tartsa fenn, függetlenül az azt használó alkalmazásoktól. Ha például a felhasználó már bejelentkezett, és végrehajtott egy többtényezős hitelesítési (MFA) lépést, a felhasználó több alkalmazás magasabb biztonsági részeihez is hozzáférést kaphat, feltéve, hogy a felhasználói folyamathoz kötött munkamenet nem jár le.
    - **Letiltva** – Ez a beállítás arra kényszeríti a felhasználót, hogy a szabályzat minden végrehajtásakor végigfusson a teljes felhasználói folyamaton.

A következő használati esetek engedélyezve vannak a következő tulajdonságok használatával:

- A megfelelő webalkalmazás-munkamenet-élettartam beállításával megfelelt az iparág biztonsági és megfelelőségi követelményeinek.
- A hitelesítés kényszerítése egy meghatározott időszak után a felhasználó és a webalkalmazás magas biztonsági szintű részével folytatott interakció során.

## <a name="configure-the-properties"></a>A tulajdonságok konfigurálása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD B2C-bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget.
5. Nyissa meg a korábban létrehozott felhasználói folyamatot.
6. Válassza **a Tulajdonságok lehetőséget.**
7. Konfigurálja **a webalkalmazás munkamenetének élettartamát (perc),** **a webalkalmazás munkamenetének időmegmaradását**, **egyszeri bejelentkezési konfigurációját**, és szükség szerint **adja meg az azonosító jogkivonatot a kijelentkezési kérelmekben.**

    ![Munkamenet-viselkedés tulajdonságbeállításai az Azure Portalon](./media/session-behavior/session-behavior.png)

8. Kattintson a **Mentés** gombra.
