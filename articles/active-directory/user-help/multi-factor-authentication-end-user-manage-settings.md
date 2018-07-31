---
title: Kétlépéses ellenőrzés beállításait – Azure Active Directory kezelése |} A Microsoft Docs
description: Kezelése, beleértve a kapcsolattartási adatok módosítása, vagy az eszközök konfigurálása az Azure multi-factor Authentication használatát.
services: active-directory
keywords: többtényezős hitelesítés ügyfél, a hitelesítési probléma, a korrelációs azonosító
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.openlocfilehash: c3fd74731dbed2c2f36d97b3cb42b383f8e4ca0f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345089"
---
# <a name="manage-your-settings-for-two-step-verification"></a>A kétlépéses ellenőrzés beállításainak kezelése
Ez a cikk a kétlépéses ellenőrzés vagy a multi-factor Authentication hitelesítés beállításainak frissítésével kapcsolatos kérdésekre ad választ. Ha a fiókjához regisztrálással kapcsolatos problémák merülnek fel, tekintse meg [problémákat tapasztal a kétlépéses ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md) hibaelhárítási segítséget.

## <a name="where-to-find-the-settings-page"></a>Itt találja a beállítások lap
Függően a cég hogyan állította be az Azure multi-factor Authentication van néhány olyan helyen, ahol módosíthatja a beállítások például a telefonszámát.

Ha a céges ügyfélszolgálat küld egy adott URL-cím vagy a lépéseket, kezelhetik a kétlépéses ellenőrzést, kövesse ezeket az utasításokat. Ellenkező esetben a Mindenki más az alábbi utasítások alapján működik. Ha kövesse az alábbi lépéseket, de nem látható, ugyanazokkal a beállításokkal, ez azt jelenti, hogy a munkahelyi vagy iskolai testre szabott saját portálon. Az Azure multi-factor Authentication-portálra mutató hivatkozást, kérje a rendszergazda.

**Ugrás a további biztonsági ellenőrzési lapot**

- Nyissa meg a következőt: https://aka.ms/MFASetup.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Ha a hivatkozásra kattintva nem működik az Ön számára, akkor is elérhető a **további biztonsági ellenőrzés** lap az alábbi lépéseket:

1. Jelentkezzen be [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. Válassza ki a fiók neve jobb felső sarkában, majd válassza ki **profil**.

3. Válassza ki **további biztonsági ellenőrzés**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. A további biztonsági ellenőrzési lapot betölti a beállításokkal.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Szeretnék megváltoztathatják a telefonszámukat, vagy egy másodlagos szám hozzáadása
Fontos, egy másodlagos hitelesítő telefonszám konfigurálása.  Mivel az elsődleges telefonszámukat és a mobilalkalmazás valószínűleg a azonos telefonon, a másodlagos telefonszámot az egyetlen módja fogja tudni visszaszerezheti fiókját, ha a telefonja elveszett vagy ellopták.

> [!NOTE]
> Ha nem fér hozzá az elsődleges telefonszám, és nem a fiókjába első segítségre van szüksége, tekintse meg a [problémákat tapasztal a kétlépéses ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md) cikk további segítséget itt találhat.  

**Az elsődleges telefonszám módosítása:**  

1. Az a **további biztonsági ellenőrzés** lapon jelölje ki a szövegmezőt az aktuális telefonszámát és szerkeszthetik az új telefonszámot.  
2. Kattintson a **Mentés** gombra.  
3. Ha ez a telefonszám a számát, amelyet az ellenőrzési módszert használ, azt mentése előtt ellenőriznie kell az új számot.  

**Egy másodlagos telefonszámot hozzáadása:**  

1. A további biztonsági ellenőrzési lapon jelölje be a jelölőnégyzetet a **alternatív hitelesítési telefon.**  
2. Adja meg a másodlagos telefonszámot a szövegmezőben.  
3. Válassza ki **mentése** és a módosítások befejeződtek.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>A megbízhatóként megjelölt eszközön újra kétlépéses ellenőrzés megkövetelése

A szervezet beállításoktól függően előfordulhat, a jelölőnégyzet arról, hogy a "Ne jelenjen meg többé a **X** nap" a böngészőben a kétlépéses ellenőrzés végrehajtásakor. Ha ezt a jelölőnégyzetet és majd az eszköz elvesztése vagy úgy, hogy a fiók biztonsága sérül, az összes eszközre kell visszaállítani a kétlépéses ellenőrzést.

1. Válassza ki a további biztonsági ellenőrzési lapot **visszaállítási többtényezős hitelesítés a korábban megbízhatónak minősített eszközökön**.
2. Amikor legközelebb bejelentkezik bármilyen eszközön, a rendszer kéri a kétlépéses ellenőrzés végrehajtásához.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Hogyan régi eszközömet a Microsoft Authenticator törlése és áthelyezése egy új?
Távolítsa el az alkalmazást az eszközről, vagy az eszköz alaphelyzetbe állítása, az aktiválást a háttér nem távolítja el. További információkért lásd: [Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

## <a name="next-steps"></a>További lépések
* Első hibaelhárítási tippek és súgó [problémákat tapasztal a kétlépéses ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)
* Állítsa be a [alkalmazásjelszók](multi-factor-authentication-end-user-app-passwords.md) kétlépéses ellenőrzést nem támogató alkalmazások.
