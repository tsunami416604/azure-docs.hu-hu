---
title: 'Az Azure Active Directory Domain Services: Jelszóházirend |} A Microsoft Docs'
description: A felügyelt tartományok jelszóházirendeket ismertetése
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: maheshu
ms.openlocfilehash: 868d902baac294fc5d7eb093f41159f6eedd109c
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986286"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Jelszó- és fiókzárolási házirendet a felügyelt tartományok
Ez a cikk bemutatja a felügyelt tartomány alapértelmezett jelszóházirendeket. Emellett ismerteti, hogyan konfigurálhatja ezeket a szabályzatokat.

## <a name="fine-grained-password-policies-fgpp"></a>Finom részletes jelszóházirendek (FGPP)
Részletes jelszóházirendek segítségével számos jelszóházirendek belül egyetlen tartományt adja meg. Részletes Jelszóházirendek lehetővé teszi, hogy a jelszó- és fiókzárolási házirendet különböző korlátozásokat alkalmazhat a tartomány felhasználói más-más részhalmazához. Ha például a kiemelt jogosultságú fiókok szigorú jelszó-beállításokat is alkalmazhat.

Részletes Jelszóházirendek segítségével a következő jelszó beállításokat konfigurálhatja:
* Jelszó minimális hossza
* Az előző
* Jelszavak meg kell felelnie a bonyolultsági feltételeknek
* Jelszó minimális kora
* A jelszó maximális kora
* Fiókzárolási házirend
    * Fiókzárolás időtartama
    * Sikertelen bejelentkezési kísérletek engedélyezett száma
    * Alaphelyzetbe állítása sikertelen bejelentkezési kísérlet után száma


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Alapértelmezett részletes jelszóval szabályzatbeállítások egy felügyelt tartományon
Az alábbi képernyőképen azt mutatja be, az alapértelmezett finom jelszóházirendet konfigurálni egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

![Alapértelmezett részletes jelszóházirendet](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nem módosítja vagy törli az alapértelmezett beépített részletes jelszóházirendet. Az "AAD DC rendszergazdák" csoport tagjai egyéni FGPP létrehozását és konfigurálását, hogy a felülbírálás (elsőbbséget élveznek a) az alapértelmezett beépített FGPP.
>
>

## <a name="password-policy-settings"></a>Jelszóval szabályzatbeállítások
A felügyelt tartományban a következő jelszóházirendek alapértelmezés szerint vannak konfigurálva:
* Jelszó minimális hossza (karakter): 7
* A jelszó maximális kora (élettartam): 90 nap
* Jelszavak meg kell felelnie a bonyolultsági feltételeknek

### <a name="account-lockout-settings"></a>Fiókzárolási beállítások
A felügyelt tartományban a következő fiókzárolási házirendek alapértelmezés szerint vannak konfigurálva:
* Fiókzárolás időtartama: 30
* Sikertelen bejelentkezési kísérletek engedélyezett száma: 5
* Alaphelyzetbe állítása sikertelen bejelentkezési kísérlet után száma: 30 perc

Hatékony felhasználói fiókok zárolt 30 percig öt érvénytelen jelszavak használata 2 percen belül. Fiókok 30 perc után lesznek automatikusan feloldani.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Hozzon létre egy egyéni megadni a részletes jelszóházirend (FGPP-) egy felügyelt tartományon
Hozzon létre egy egyéni FGPP, és alkalmazza azt az adott csoportokhoz a felügyelt tartományban. Ez a konfiguráció felülbírálja a részletes Jelszóházirendek a felügyelt tartományhoz konfigurált alapértelmezett.

> [!TIP]
> Csak a tagjai a **"AAD DC rendszergazdák"** csoportot hozhat létre egyéni megadni a részletes jelszóházirendek engedélye.
>
>

Ezenkívül is hozzon létre egyéni finom részletes jelszóházirendek és azokat bármilyen egyéni szervezeti egységek hoz létre a felügyelt tartomány a alkalmazni.

Konfigurálhat egy egyéni FGPP a következő okok miatt:
* Egy másik fiókzárolási házirend beállítása.
* A felügyelt tartományhoz tartozó alapértelmezett jelszó élettartama beállításának konfigurálása.

Egy egyéni FGPP létrehozása a felügyelt tartomány:
1. Jelentkezzen be a felügyelt tartomány felügyeletéhez használja, a Windows virtuális gép. Ha még nincs fiókja, kövesse az utasításokat [felügyelt tartomány kezelése](active-directory-ds-admin-guide-administer-domain.md)
2. Indítsa el a **Active Directory felügyeleti központ** a virtuális gépen.
3. Kattintson a tartomány nevét (például "contoso100.com").
4. Kattintson duplán a **rendszer** a rendszertárolóhoz megnyitásához.
5. Kattintson duplán a **jelszóbeállítás-tároló**.
6. Megjelenik az alapértelmezett beépített FGPP a felügyelt tartományhoz tartozó nevű **AADDSSTFPSO**. A beépített FGPP nem módosítható. Ugyanakkor hozzon létre egy új egyéni felülbírálás FGPP az alapértelmezett FGPP.
7. Az a **feladatok** lehetőségre a jobb oldali panelen **új** kattintson **jelszóbeállítások**.
8. Az a **jelszóbeállítások létrehozása** párbeszédpanelen adja meg a alkalmazni az egyéni FGPP részeként egyéni jelszó-beállításokat. Ne felejtse el az alapértelmezett FGPP megfelelően állítsa be a sorrend.

  ![Hozzon létre egyéni részletes Jelszóházirendek](./media/how-to/custom-fgpp.png)

  > [!TIP]
  > **Ne feledje, hogy törölje a jelet a lehetőség a véletlen törlés elleni védelem.** Ha ezt a lehetőséget választja, a részletes Jelszóházirendek nem lehet menteni.
  >
  >

9. A **vonatkozik erre a**, kattintson a **Hozzáadás** gombra. Az a **felhasználók vagy csoportok kiválasztása** párbeszédpanelen kattintson a **helyek** gombra.

  ![Felhasználók és csoportok kijelölése](./media/how-to/fgpp-applies-to.png)

10. Az a **helyek** párbeszédpanelen bontsa ki a tartomány nevét, és kattintson a **AADDC felhasználók**. Most már a beépített felhasználók szervezeti Egységét, amelyre a alkalmazni a részletes Jelszóházirendek, válasszon ki egy csoportot.

  ![Válassza ki a szervezeti Egységhez adott csoporthoz tartozik](./media/how-to/fgpp-container.png)

11. Adja meg a csoport nevét, majd kattintson a **Névellenőrzés** gombra kattintva ellenőrizze a csoport létezik.

  ![Válassza ki a csoportot a alkalmazni a részletes Jelszóházirendek](./media/how-to/fgpp-apply-group.png)

12. A csoport neve jelenik meg **vonatkozik erre a** szakaszban. Kattintson a **OK** gombra a módosítások mentéséhez.

  ![Részletes Jelszóházirendek a alkalmazni](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Az egyénijelszó-szabályzatoknak az egyéni szervezeti felhasználói fiókok:** finom részletes jelszóházirendek csak csoportok alkalmazhatók. Csak az egyéni szervezeti felhasználók egyéni jelszó szabályzatának konfigurálása, hozzon létre egy csoportot, amely a felhasználót tartalmazza, amelynek.
>
>

## <a name="next-steps"></a>További lépések
* [Ismerje meg az Active Directory finom részletes jelszóházirendek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394)
* [Az AD felügyeleti központ segítségével megadni a részletes jelszóházirendek beállítása](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
