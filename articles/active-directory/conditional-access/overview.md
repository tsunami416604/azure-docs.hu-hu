---
title: Mi a feltételes hozzáférés az Azure Active Directoryban?
description: Ismerje meg, hogyan feltételes hozzáférés középpontjában az új identitás vezérelte vezérlősík.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b044a4fd4e29bfe35abff7a4b36e5bae783328b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240911"
---
# <a name="what-is-conditional-access"></a>Mi az a feltételes hozzáférés?

A modern biztonsági határmost már túlnyúlik a szervezet hálózatán, hogy tartalmazza a felhasználó és az eszköz identitását. A szervezetek ezeket az identitásjeleket használhatják a hozzáférés-vezérlési döntéseik részeként. 

A feltételes hozzáférés az Azure Active Directory által használt eszköz a jelek egyesítéséhez, a döntések meghozatalához és a szervezeti szabályzatok kényszerítéséhez. A feltételes hozzáférés az új identitásvezérelt vezérlősík középpontjában áll.

![Fogalmi feltételes jel plusz döntés a végrehajtásra](./media/overview/conditional-access-signal-decision-enforcement.png)

Feltételes hozzáférési házirendek a legegyszerűbb, ha-majd utasítások, ha a felhasználó szeretne hozzáférni egy erőforrást, majd végre kell hajtson végre egy műveletet. Példa: A bérszámfejtési vezető szeretné elérni a bérszámfejtési alkalmazás, és szükséges, hogy többtényezős hitelesítéseléréséhez.

A rendszergazdáknak két elsődleges céllal kell szembenéznie:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A szervezet eszközeinek védelme

Feltételes hozzáférési házirendek használatával alkalmazhatja a megfelelő hozzáférési vezérlőket, ha szükséges, hogy a szervezet biztonságos, és maradjon ki a felhasználó útját, ha nem szükséges.

![Fogalmi feltételes hozzáférési folyamatfolyamat](./media/overview/conditional-access-overview-how-it-works.png)

A feltételes hozzáférési házirendek az első tényezős hitelesítés befejezése után lépnek érvénybe. Feltételes hozzáférés nem célja, mint egy szervezet első védelmi vonala forgatókönyvek, például a szolgáltatásmegtagadási (DoS) támadások, de használhatja az események jeleit a hozzáférés meghatározásához.

## <a name="common-signals"></a>Gyakori jelek

A feltételes hozzáférés által a politikai döntéshozatal során figyelembe vehetem a következő jelzéseket:

- Felhasználói vagy csoporttagság
   - A házirendek meghatározott felhasználókra és csoportokra célozhatók, így a rendszergazdák részletesen szabályozhatják a hozzáférést.
- IP-hely adatai
   - A szervezetek létrehozhatnak megbízható IP-címtartományokat, amelyek házirend-döntések meghozatalakor használhatók. 
   - A rendszergazdák teljes ország IP-tartományokat adhatnak meg, amelyekblokkolhatják vagy engedélyezhetik a forgalmat.
- Eszköz
   - Adott platformokeszközeivel rendelkező vagy adott állapottal jelölt felhasználók használhatók a feltételes hozzáférési házirendek érvényesítésekor.
- Alkalmazás
   - Az adott alkalmazásokelérését megkísérlő felhasználók különböző feltételes hozzáférési házirendeket indíthatnak el. 
- Valós idejű és számított kockázatészlelés
   - Jelek integráció az Azure AD Identity Protection lehetővé teszi a feltételes hozzáférési szabályzatok kockázatos bejelentkezési viselkedés azonosításához. A házirendek ezután jelszómódosítások vagy többtényezős hitelesítés végrehajtására kényszeríthetik a felhasználókat a kockázati szint csökkentése érdekében, vagy letilthatók a hozzáférésből, amíg a rendszergazda manuális műveletet nem hajt végre.
- Microsoft Cloud Alkalmazásbiztonság (MCAS)
   - Lehetővé teszi a felhasználói alkalmazások hozzáférésének és munkameneteinek valós idejű figyelését és ellenőrzését, növelve a felhőkörnyezethez való hozzáférés és a felhőkörnyezetben végrehajtott tevékenységek láthatóságát és szabályozását.

## <a name="common-decisions"></a>Közös határozatok

- Fájlhozzáférés
   - A legkorlátozóbb döntés
- Hozzáférés biztosítása
   - A legkevésbé korlátozó döntés hez továbbra is szükség lehet az alábbi lehetőségek közül:
      - Többtényezős hitelesítés megkövetelése
      - Az eszköz megfelelőként való megjelölése
      - Hibrid Azure AD-hez csatlakozott eszköz megkövetelése
      - Jóváhagyott ügyfélalkalmazás megkövetelése
      - Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)

## <a name="commonly-applied-policies"></a>Általánosan alkalmazott házirendek

Számos szervezet nek gyakori hozzáférési aggályai vannak, amelyeket a feltételes hozzáférési házirendek segíthetnek, például:

- Többtényezős hitelesítés megkövetelése felügyeleti szerepkörrel rendelkező felhasználók számára
- Többtényezős hitelesítés megkövetelése az Azure felügyeleti feladataihoz
- Bejelentkezések blokkolása az örökölt hitelesítési protokollokat használni próbáló felhasználók számára
- Megbízható helyek megkövetelése az Azure többtényezős hitelesítésének regisztrációhoz
- Hozzáférés letiltása vagy megadása meghatározott helyekről
- Kockázatos bejelentkezési viselkedések blokkolása
- Szervezet által felügyelt eszközök megkövetelése adott alkalmazásokhoz

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

Fedezze fel, hogy más szervezetek hogyan használják az Azure AD feltételes hozzáférést az automatikus hozzáférés-vezérlési döntések meghatározásához és megvalósításához. A következő kiemelt történetek bemutatják, hogyan elégíthetők ki ezek az ügyfelek igényei.

* [A Wipro a Microsoft felhőalapú biztonsági eszközeivel növeli a mobilos hatékonyságot az ügyfelek elköteleződésének javítása érdekében.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Az Azure AD feltételes hozzáférési szabályzatai lehetővé tették a vállalat számára, hogy dokumentumokat, erőforrásokat és alkalmazásokat osszon meg megbízható külső entitásokkal---akik használhatják saját hitelesítő adataikat--- miközben saját vállalati adatait is fenntarthatják.
* [Aramex szállítási korlátozott - A globális logisztikai és szállítási vállalat felhőhöz kapcsolódó irodát hoz létre identitás- és hozzáférés-kezelési megoldással.](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en) Az Aramex távoli alkalmazottai számára különösen nehéz volt biztosítani a biztonságos hozzáférést. A vállalat most feltételes hozzáférést alkalmaz, hogy ezek a távoli alkalmazottak a hálózaton kívülről is elérhessek SaaS-alkalmazásaikat. A feltételes hozzáférés szabály dönti el, hogy a többtényezős hitelesítés, amely csak a megfelelő személyek a megfelelő hozzáférést.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

A [Microsoft 365 Vállalati licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek is hozzáférhetnek a feltételes hozzáférés funkcióihoz. 

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférési házirend létrehozása darabonként](concept-conditional-access-policies.md)

A feltételes hozzáférés környezetben való megvalósításáról a [Feltételes hozzáférés telepítésének megtervezése az Azure Active Directoryban](plan-conditional-access.md)című témakörben olvashat.

[További információ az identitásvédelemről](../identity-protection/overview-v2.md)

[További információ a Microsoft Cloud App Security szolgáltatásról](/cloud-app-security/what-is-cloud-app-security)

[További információ a Microsoft Intune-ról](/intune/index)