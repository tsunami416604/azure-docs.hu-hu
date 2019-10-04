---
title: A találgatásos támadások megelőzése az Azure AD Smart fiókzárolási használatával – Azure Active Directory
description: A Azure Active Directory Smart zárolásával megvédheti szervezetét a jelszavak kitalálása céljából indított támadásokkal szemben.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a762009a7aaf1a965333ac573efe55d792c3f04b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125004"
---
# <a name="azure-active-directory-smart-lockout"></a>Intelligens zárolás Azure Active Directory

Az intelligens zárolás segíti a felhasználók jelszavainak kiszűrését, illetve a találgatásos kényszerítési módszerek használatát. Felismeri az érvényes felhasználóktól érkező bejelentkezéseket, és a támadók és más ismeretlen forrásoktól eltérően kezeli őket. Az intelligens zárolás kizárja a támadókat, miközben lehetővé teszi, hogy a felhasználók továbbra is hozzáférjenek a fiókjához, és hatékonyak legyenek.

Alapértelmezés szerint az intelligens zárolás 10 sikertelen kísérlet után egy percen belül zárolja a fiókot a bejelentkezési kísérletekből. A fiók minden további sikertelen bejelentkezési kísérlet után ismét zárolja a műveletet, és a későbbi próbálkozások során egy percen belül megszakad.

Az intelligens zárolási szolgáltatás az utolsó három rossz jelszó-kivonatot követi, így elkerülhető, hogy a zárolási számláló ugyanazon a jelszónál legyen növelve. Ha valaki többször is ugyanazt a jelszót adja meg, akkor ez a viselkedés nem eredményezi a fiók zárolását.

 > [!NOTE]
 > A kivonatoló követés funkció nem érhető el olyan ügyfelek esetében, amelyeknél engedélyezve van az átmenő hitelesítés, mert a hitelesítés a helyszínen nem a felhőben történik.

A AD FS 2016-es és AF FS 2019-et használó összevont telepítések a [AD FS extranetes zárolás és az extranetes intelligens zárolás](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)használatával is engedélyezhetik a hasonló előnyöket.

Az intelligens zárolás mindig az összes Azure AD-ügyfélre vonatkozik, ezekkel az alapértelmezett beállításokkal a biztonság és a használhatóság megfelelő kombinációját kínálja. Az intelligens zárolási beállítások testre szabása a szervezetre jellemző értékekkel a felhasználóknak fizetett Azure AD-licenceket igényel.

Az intelligens zárolás használata nem garantálja, hogy egy valódi felhasználó soha nem lesz zárolva. Amikor az intelligens zárolás zárol egy felhasználói fiókot, igyekszünk a legjobbat választani, hogy ne zárolja a valódi felhasználót. A zárolási szolgáltatás megkísérli biztosítani, hogy a rossz szereplők ne férhessenek hozzá valódi felhasználói fiókhoz.  

* Az egyes Azure Active Directory adatközpontok egymástól függetlenül figyelik a zárolást. A felhasználók (threshold_limit * datacenter_count) száma sikertelen lesz, ha a felhasználó minden adatközpontot elér.
* Az intelligens zárolás ismerős helyet és ismeretlen helyet használ a rossz színész és a valódi felhasználó közötti különbségtételhez. A nem ismerős és ismerős helyszínek külön zárolási számlálókkal is rendelkeznek.

Az intelligens zárolás integrálható hibrid környezetekkel, jelszó-kivonatolási szinkronizálással vagy átmenő hitelesítéssel, hogy megvédje a helyszíni Active Directory fiókokat a támadók számára. Ha intelligens zárolási szabályzatokat állít be az Azure AD-ben, akkor a támadások kiszűrhetők a helyszíni Active Directory elérése előtt.

[Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)használatakor meg kell győződnie arról, hogy:

* Az Azure AD zárolási küszöbértéke **kisebb** , mint az Active Directory fiókzárolás küszöbértéke. Állítsa be úgy az értékeket, hogy a Active Directory fiókzárolás küszöbértéke legalább kettő vagy háromszor hosszabb legyen, mint az Azure AD zárolási küszöbértéke. 
* Az Azure AD zárolási időtartamának hosszabbnak kell lennie, mint a fiókzárolás visszavonása Active Directory az időtartam után. Vegye figyelembe, hogy az Azure AD időtartama másodpercben van megadva, míg az AD időtartama percben van megadva. 

Ha például azt szeretné, hogy az Azure AD-számlálója nagyobb legyen, mint az AD, akkor az Azure AD 120 másodperc (2 perc), míg a helyszíni AD 1 percre (60 másodpercre) van beállítva.

> [!IMPORTANT]
> Jelenleg a rendszergazda nem tudja feloldani a felhasználók felhőalapú fiókjait, ha azokat az intelligens zárolási funkció kizárta. A rendszergazdának várnia kell, amíg lejár a zárolás időtartama. A felhasználó azonban az önkiszolgáló jelszó-visszaállítás (SSPR) használatával egy megbízható eszközről vagy helyről oldhatja fel a zárolást.

## <a name="verify-on-premises-account-lockout-policy"></a>Helyszíni fiókzárolási házirend ellenőrzése

A helyszíni Active Directory fiókzárolási házirend ellenőrzéséhez kövesse az alábbi utasításokat:

1. Nyissa meg a Csoportházirend felügyeleti eszközt.
2. Szerkessze a szervezet fiókzárolási házirendjét (például az **alapértelmezett tartományi házirendet**) tartalmazó csoportházirendet.
3. Tallózás a **számítógép-konfigurációs** > **házirendek** > **Windows-beállítások** > **biztonsági beállítások** > **fiók szabályzatok** > fiókzárolási **Házirend**.
4. Ellenőrizze a fiókzárolás küszöbértékét, és állítsa alaphelyzetbe a fiókzárolás számlálóját az értékek **után** .

![A helyszíni Active Directory fiókzárolási házirend módosítása](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD intelligens zárolási értékek kezelése

A szervezeti követelmények alapján előfordulhat, hogy az intelligens zárolási értékeket testre kell szabni. Az intelligens zárolási beállítások testre szabása a szervezetre jellemző értékekkel a felhasználóknak fizetett Azure AD-licenceket igényel.

A szervezet intelligens zárolási értékeinek vizsgálatához vagy módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a **Azure Active Directory** > **hitelesítési módszerek** > **jelszavas védelméhez**.
1. Állítsa bea zárolási küszöbértéket attól függően, hogy az első zárolás előtt hány sikertelen bejelentkezés engedélyezett a fiókon. Az alapértelmezett érték 10.
1. Állítsa a **zárolás időtartamát másodpercben**, az egyes zárolások hosszára másodpercben. Az alapértelmezett érték 60 másodperc (egy perc).

> [!NOTE]
> Ha az első bejelentkezés a zárolás után is meghiúsul, a fiók ismét leáll. Ha egy fiók többször is zárolva van, a zárolás időtartama növekszik.

![Az Azure AD intelligens zárolási szabályzatának testreszabása a Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Hogyan állapítható meg, hogy az intelligens zárolási funkció működik-e

Az intelligens zárolási küszöbérték kiváltása esetén a következő üzenet jelenik meg a fiók zárolásakor:

**A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később, és ha még mindig problémája van, forduljon a rendszergazdához.**

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan tilthatja be a hibás jelszavakat a szervezetben az Azure AD használatával.](howto-password-ban-bad.md)
* [Az önkiszolgáló jelszó-visszaállítás konfigurálásával engedélyezheti a felhasználóknak a saját fiókjaik feloldását.](quickstart-sspr.md)
