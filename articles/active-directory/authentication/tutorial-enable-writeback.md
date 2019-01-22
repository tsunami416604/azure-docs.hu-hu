---
title: Azure AD – jelszóvisszaíró engedélyezése
description: Az oktatóanyagban engedélyezzük a jelszóvisszaírást a felhőben kezdeményezett jelszómódosítások visszaírásához a helyszíni AD-be az Azure AD Connect részeként.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 6cbc4fa071a45e670dae3cc6be66272bc67767f7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437181"
---
# <a name="tutorial-enabling-password-writeback"></a>Oktatóanyag: A jelszóvisszaírás engedélyezése

Az oktatóanyagban engedélyezzük a jelszóvisszaírót hibrid környezetben. A jelszóvisszaíró használatával az Azure Active Directoryban (Azure AD) végrehajtott jelszómódosítások visszaszinkronizálhatók a helyszíni Active Directory Domain Services- (AD DS-) környezetbe. A jelszóvisszaíró az Azure AD Connect részeként engedélyezett biztonságos mechanizmus, amely a jelszómódosításokat az Azure AD-ből visszaküldi egy meglévő helyszíni címtárba. A jelszóvisszaíró belső működésével kapcsolatos további részleteket [a jelszóvisszaíró működését ismertető](concept-sspr-writeback.md) cikkben olvashat.

> [!div class="checklist"]
> * A jelszóvisszaírás engedélyezése az Azure AD Connectben
> * A jelszóvisszaírás engedélyezése az Új jelszó önkiszolgáló kérése (SSPR) szolgáltatásban

## <a name="prerequisites"></a>Előfeltételek

* Hozzáférés egy működő Azure AD-bérlőhöz, amely legalább egy próbaverziós licenccel rendelkezik.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók az Azure AD-bérlőn.
* Egy meglévő kiszolgáló, amely az [Azure AD Connect](../hybrid/how-to-connect-install-express.md) aktuális verziójához van konfigurálva.
* Az új jelszó önkiszolgáló kérésével (SSPR) kapcsolatos korábbi oktatóanyagok elvégzése.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>A jelszóvisszaírás engedélyezése az Azure AD Connectben

A jelszóvisszaíró engedélyezéséhez először engedélyeznünk kell a szolgáltatást arról a kiszolgálóról, amelyre az Azure AD Connectet telepítette.

1. A jelszóvisszaíró konfigurálásához és engedélyezéséhez jelentkezzen be az Azure AD Connect-kiszolgálóra, és indítsa el az **Azure AD Connect** konfigurációs varázslóját.
2. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
3. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
4. A **Csatlakozás az Azure AD szolgáltatáshoz** lapon adja meg egy globális rendszergazda hitelesítő adatait, majd kattintson a **Tovább** gombra.
5. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
6. A **Választható funkciók** lapon jelölje be a **Jelszóvisszaíró** melletti jelölőnégyzetet, és kattintson a **Tovább** gombra.
7. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
8. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

## <a name="enable-password-writeback-option-in-sspr"></a>A jelszóvisszaírás engedélyezése az SSPR-ben

A jelszóvisszaíró engedélyezése az Azure AD Connectben azonban csak a feladat egyik fele. Ha az SSPR-ben is engedélyezi a jelszóvisszaírót, azzal bezárja a kört, így a jelszavukat módosító vagy új jelszót igénylő felhasználók a helyszíni rendszerben is ugyanazt a jelszót használhatják majd.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
2. Lépjen az **Azure Active Directoryra**, kattintson a **Jelszó alaphelyzetbe állítása** gombra, majd válassza a **Helyszíni integráció** lehetőséget.
3. Állítsa a **Jelszavak visszaírása helyszíni címtárba** beállítást **Igen** értékre.
4. Állítsa a **Felhasználói fiókok feloldásának engedélyezése új jelszó kérése nélkül** beállítást **Igen** értékre.
5. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyeztük a jelszóvisszaírót az új jelszó önkiszolgáló kérési szolgáltatásában. Hagyja megnyitva az Azure Portal ablakát, és lépjen tovább a következő oktatóanyagra, amelyben további beállításokat konfigurálhat az új jelszó önkiszolgáló kéréséhez, mielőtt bevezetné a megoldás próbaverzióját.

> [!div class="nextstepaction"]
> [Az SSPR engedélyezése a Windows bejelentkezési képernyőjén](tutorial-sspr-windows.md)
