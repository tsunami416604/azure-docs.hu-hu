---
title: "Az Azure AD rétegzett biztonsági jelszó |} Microsoft Docs"
description: "Azt ismerteti, hogyan az Azure AD kikényszeríti a erős jelszavak és védje a felhasználók jelszavát a számítógépes bűnözők"
services: active-directory
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 683badcfb67dd9e98058d560a6b13d1a3474d3e9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Az Azure AD-jelszó biztonsági többrétegű megközelítése

Ez a cikk ismerteti, amelyek néhány ajánlott eljárás követésével egy olyan felhasználó nevében, vagy arra, hogy az Azure Active Directory (Azure AD) vagy a Microsoft Account rendszergazdaként.

 > [!NOTE]
 > **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md).
 >
 > Az Azure AD-rendszergazdák alaphelyzetbe állíthatja a felhasználói jelszavakat útmutatásának megfelelően történt a cikkben található [az Azure Active Directoryban a felhasználó jelszavának visszaállítása](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Jelszavakra vonatkozó követelmények

Az Azure AD a következő általános megközelítéseket tartalmazza a jelszavak védelme érdekében:

* Jelszóhosszra vonatkozó követelmények
* Összetettségi követelményeknek
* Normál és időszakos jelszólejárat

Jelszó alaphelyzetbe állítása, az Azure Active Directoryban kapcsolatos információkért lásd a témakör [az Azure AD az önkiszolgáló jelszó-változtatási az informatikai szakembereknek szóló](active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Az Azure AD-jelszó védelmet

Az Azure AD és a Microsoft-fiókrendszer megközelítések bizonyítása iparági használatával biztosíthatja a felhasználói és rendszergazdai jelszavakat, beleértve a biztonságos védelméről:

* Dinamikusan letiltott jelszavak
* Intelligens jelszózárolás

A jelszókezelés aktuális eredményei alapján kapcsolatos információkért lásd [jelszó útmutatást](http://aka.ms/passwordguidance).

### <a name="dynamically-banned-passwords"></a>Dinamikusan letiltott jelszavak

Az Azure AD és a Microsoft Accounts megakadályozhatja a jelszavas védelem által dinamikusan tiltó a gyakran használt jelszavakat. Az Azure AD Identity Protection-csapatnak rendszeresen elemzi tiltott jelszavak listája, meggátolja, hogy a felhasználók kiválasztása a gyakran használt jelszavakat. Ez a szolgáltatás az Azure AD és a Microsoft-fiókszolgáltatás ügyfeleinek érhető el.

Jelszavak létrehozása, ha fontos a rendszergazdák számára ösztönözzék a felhasználókat válassza ki a jelszó kifejezések, amely tartalmazza az betűket, számokat, karakterek vagy szavak egyedi kombinációja. Ez a megközelítés segít lehetetlenné felhasználói jelszavak majdnem sérült biztonságú, de jegyezze meg a felhasználók könnyebben.

#### <a name="password-breaches"></a>Jelszó megszegése

A Microsoft mindig dolgozik egy lépésben előre számítógépes-bűnözők marad.

Az Azure AD Identity Protection csapata folyamatosan elemzi a gyakran használt jelszavakat. A számítógépes bűnözők is hasonló stratégiákat használnak a támadásaikhoz, például [szivárványtáblát](https://en.wikipedia.org/wiki/Rainbow_table) hoznak létre a jelszókivonatok feltörése érdekében.

A Microsoft folyamatosan elemzi az [adatszivárgásokat](https://www.privacyrights.org/data-breaches) a tiltott jelszavak dinamikusan frissített listájának fenntartása érdekében, így a sebezhető jelszavak még azelőtt le lesznek tiltva, hogy valódi fenyegetést jelenthetnének az Azure AD ügyfelei számára. Az aktuális biztonsági erőfeszítéseinkről további információt a [Microsoft biztonsági intelligenciáról szóló jelentésében](https://www.microsoft.com/security/sir/default.aspx) talál.

### <a name="smart-password-lockout"></a>Intelligens jelszózárolás

Amikor az Azure AD érzékeli, hogy egy lehetséges számítógépes bűnöző megpróbál feltörni egy felhasználói jelszót, az Intelligens jelszózárolás segítségével zároljuk a felhasználói fiókot. Az Azure AD-t arra tervezték, hogy meghatározza az egyes bejelentkezési munkamenetekkel társított kockázatokat. Segítségével a lehető legfrissebb biztonsági adatokat, majd érvénybe lépni fiókzárolási szemantikáját, hogy számítógépes fenyegetéseket.

Ha a felhasználó le van tiltva az Azure AD ki, a képernyő hasonlít, amely a következő:

  ![Kizárva az Azure AD-ből](./media/active-directory-secure-passwords/locked-out-azuread.png)

Más Microsoft-fiók a képernyő hasonlít, amely a következő:

  ![Kizárva egy Microsoft-fiókból](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Jelszó alaphelyzetbe állítása, az Azure Active Directoryban kapcsolatos információkért lásd a témakör [az Azure AD az önkiszolgáló jelszó-változtatási az informatikai szakembereknek szóló](active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Ha Ön Azure AD-rendszergazda, javasolt a [Windows Hello](https://www.microsoft.com/windows/windows-hello) használata, hogy a felhasználók egyáltalán ne is hozhassanak létre hagyományos jelszavakat.
  >

## <a name="next-steps"></a>Következő lépések

* [Saját jelszó frissítése](active-directory-passwords-update-your-own-password.md)
* [Az Azure-identitáskezelés alapjai](fundamentals-identity.md)
* [A jelentés a jelszó-visszaállítási tevékenység](active-directory-passwords-reporting.md)
