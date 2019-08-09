---
title: 'Azure Active Directory Domain Services: Jelszóházirend | Microsoft Docs'
description: A jelszó-szabályzatok ismertetése a felügyelt tartományokban
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 30f4558339bbfddd2296cd1cb918c6ef8999b67e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879195"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Jelszó-és fiókzárolási házirendek a felügyelt tartományokban
Ez a cikk a felügyelt tartományok alapértelmezett jelszavas házirendjeit ismerteti. Emellett ismerteti a szabályzatok konfigurálásának módját is.

## <a name="fine-grained-password-policies-fgpp"></a>Részletes jelszóházirendek (FGPP)
A részletes jelszóházirendek használatával számos jelszóházirend adható meg egyetlen tartományon belül. A FGPP lehetővé teszi, hogy különböző korlátozásokat alkalmazzon a jelszó-és fiókzárolási házirendekhez egy adott tartományban lévő felhasználók különböző csoportjaira. Például szigorú jelszó-beállításokat alkalmazhat a Kiemelt fiókok lehetőségre.

A FGPP használatával a következő beállításokat állíthatja be:
* Jelszó minimális hossza
* Korábbi jelszavak
* A jelszavaknak meg kell felelniük a bonyolultsági követelményeknek
* Jelszó minimális kora
* Jelszó maximális kora
* Fiókzárolási házirend
    * Fiókzárolás időtartama
    * Sikertelen bejelentkezési kísérletek száma engedélyezett
    * Sikertelen bejelentkezési próbálkozások számának alaphelyzetbe állítása
    
A FGPP csak a közvetlenül az Azure AD DSban létrehozott felhasználókat érinti. A jelszó-bonyolultsági beállítások nem érintik a Felhőbeli felhasználókat és a tartományi felhasználókat, akik az Azure AD-ben szinkronizálva vannak az Azure AD DS felügyelt tartományba. A FGPP a csoportok társítása az Azure AD DS felügyelt tartományon keresztül történik, és az elvégzett módosítások a következő felhasználói bejelentkezéskor lesznek alkalmazva. A házirend módosítása nem oldja fel a már kizárt felhasználói fiók zárolását.

## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Alapértelmezett részletes jelszóházirend-beállítások a felügyelt tartományokban
Az alábbi képernyőfelvételen a Azure AD Domain Services felügyelt tartományon konfigurált alapértelmezett részletes jelszóházirendek láthatók.

![Alapértelmezett részletes jelszóházirendek](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nem módosíthatja vagy törölheti az alapértelmezett beépített, részletes jelszóházirend-házirendet. Az "HRE DC rendszergazdák" csoport tagjai létrehozhatnak egyéni FGPP, és konfigurálhatók a felülbíráláshoz (elsőbbséget élveznek) az alapértelmezett beépített FGPP.
>
>

## <a name="password-policy-settings"></a>Jelszóházirend beállításai
A felügyelt tartományokban a következő jelszóházirendek vannak konfigurálva alapértelmezés szerint:
* Jelszó minimális hossza (karakter): 7
* Jelszó maximális kora (élettartam): 90 nap
* A jelszavaknak meg kell felelniük a bonyolultsági követelményeknek

### <a name="account-lockout-settings"></a>Fiókzárolási beállítások
Felügyelt tartományban a következő fiókzárolási házirendek vannak konfigurálva alapértelmezés szerint:
* Fiókzárolás időtartama: 30
* Sikertelen bejelentkezési kísérletek száma engedélyezett: 5
* Sikertelen bejelentkezési kísérletek számának visszaállítása a következő után: 30 perc

A felhasználói fiókokat a rendszer 30 percen belül kizárja, ha 2 percen belül öt érvénytelen jelszót használ. A fiókok automatikus zárolása 30 perc után automatikusan megtörténik.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Egyéni részletes jelszóházirendek (FGPP) létrehozása felügyelt tartományon
Létrehozhat egy egyéni FGPP, és alkalmazhatja azt a felügyelt tartomány meghatározott csoportjaira. Ez a konfiguráció hatékonyan felülbírálja a felügyelt tartományhoz konfigurált alapértelmezett FGPP.

> [!TIP]
> Csak az **"HRE DC rendszergazdák"** csoport tagjai rendelkeznek az egyéni, részletes jelszóházirendek létrehozásához szükséges engedélyekkel.
>
>

Emellett egyéni részletes jelszóházirendek is létrehozhatók és alkalmazhatók a felügyelt tartományon létrehozott egyéni szervezeti egységekre.

A következő okok miatt konfigurálhat egyéni FGPP:
* Egy másik fiókzárolási házirend beállításához.
* A jelszó alapértelmezett élettartamának beállítása a felügyelt tartományhoz.

Egyéni FGPP létrehozása a felügyelt tartományon:
1. Jelentkezzen be a felügyelt tartomány felügyeletéhez használt Windows rendszerű virtuális gépre (legalább Windows Server 2012 R2-nek kell lennie). Ha még nem rendelkezik ilyennel, kövesse az utasításokat [egy Azure ad Domain Services tartomány kezeléséhez](manage-domain.md).
2. Indítsa el a Active Directory felügyeleti központt a virtuális gépen.
3. Kattintson a tartománynévre (például: "contoso100.com").
4. A rendszertároló megnyitásához kattintson duplán a **System (rendszeren** ) elemre.
5. Kattintson duplán a **jelszóbeállítás-tároló**elemre.
6. Megjelenik az alapértelmezett beépített FGPP a **AADDSSTFPSO**nevű felügyelt tartományhoz. Ez a beépített FGPP nem módosítható. Létrehozhat azonban egy új egyéni FGPP, amely felülbírálja az alapértelmezett FGPP.
7. A jobb oldali **feladatok** panelen kattintson az **új** elemre, majd a **Jelszó beállításai**lehetőségre.
8. A **jelszó-beállítások létrehozása** párbeszédpanelen határozza meg az egyéni FGPP tartozó egyéni jelszó beállításait. Ne felejtse el megfelelően beállítani a sorrendet az alapértelmezett FGPP felülbírálásához.

   ![Egyéni FGPP létrehozása](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Ne felejtse el kitörölni a védelem véletlen törlési beállítását.** Ha ez a beállítás be van jelölve, a FGPP nem menthető.
   >
   >

9. A **közvetlenül**a alkalmazásban kattintson a **Hozzáadás** gombra. A **felhasználók vagy csoportok kiválasztása** párbeszédpanelen kattintson a **helyszínek** gombra.

   ![Felhasználók és csoportok kijelölése](./media/how-to/fgpp-applies-to.png)

10. A **helyszínek** párbeszédpanelen bontsa ki a tartománynevet, majd kattintson a **AADDC Users (felhasználók**) elemre. Most kiválaszthat egy csoportot a beépített felhasználók szervezeti egységből, amelyre alkalmazni kívánja a FGPP.

    ![Válassza ki azt a szervezeti egységet, amelyhez a csoport tartozik](./media/how-to/fgpp-container.png)

11. Írja be a csoport nevét, és kattintson a Névellenőrzés gombra a csoport meglétének ellenőrzéséhez.

    ![Válassza ki a FGPP alkalmazni kívánt csoportot](./media/how-to/fgpp-apply-group.png)

12. A csoport neve **közvetlenül** a (z) szakaszban jelenik meg. A módosítások mentéséhez kattintson az **OK** gombra.

    ![FGPP alkalmazva](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Egyéni jelszóházirendek alkalmazása felhasználói fiókokhoz egyéni szervezeti egységben:** A részletes jelszóházirendek csak csoportokra alkalmazhatók. Egyéni jelszóházirend csak egyéni szervezeti egység felhasználói számára történő konfigurálásához hozzon létre egy csoportot, amely tartalmazza az adott szervezeti egységben lévő felhasználókat.
>
>

## <a name="next-steps"></a>További lépések
* [Tudnivalók a részletes jelszóházirendek Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Részletes jelszóházirendek konfigurálása az AD felügyeleti központban](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
