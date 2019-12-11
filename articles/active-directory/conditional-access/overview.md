---
title: Mi a feltételes hozzáférés a Azure Active Directory?
description: Ismerje meg, hogy a feltételes hozzáférés hogyan áll az új Identity vezérelt vezérlési sík középpontjába.
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
ms.openlocfilehash: a4c85fbc2c1f926f89dbfe9f2a5ae47cc48edf73
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966277"
---
# <a name="what-is-conditional-access"></a>Mi az a feltételes hozzáférés?

A modern biztonsági területet mostantól a szervezet hálózatán kívül is kiterjesztheti a felhasználók és az eszközök azonosítására. A szervezetek a hozzáférés-vezérlési döntéseik részeként használhatják ezeket az identitási jeleket. 

A feltételes hozzáférés az Azure Active Directory által használt eszköz, amely lehetővé teszi a jelek összekapcsolását, a döntések meghozatalát és a szervezeti házirendek betartatását. A feltételes hozzáférés az új Identity vezérelt vezérlő síkja szívében található.

![Koncepcionális feltételes jel plusz döntés a kényszerítés beszerzéséhez](./media/overview/conditional-access-signal-decision-enforcement.png)

A feltételes hozzáférési szabályzatok a legegyszerűbben az IF-then utasítások, ha egy felhasználó szeretne hozzáférni egy erőforráshoz, végre kell hajtania egy műveletet. Példa: egy bérlista-kezelő szeretné elérni a bérlista-alkalmazást, és a többtényezős hitelesítés végrehajtásához szükséges.

A rendszergazdák két elsődleges céllal szembesülnek:

- A felhasználók hatékony munkájának támogatása bárhol és bármikor
- A szervezet eszközeinek védelmének biztosítása

A feltételes hozzáférési szabályzatok alkalmazásával a megfelelő hozzáférés-vezérlést alkalmazhatja a szervezet biztonságának biztosításához, és nem szükséges, hogy a felhasználó ne használja.

![Fogalmi feltételes hozzáférés folyamata](./media/overview/conditional-access-overview-how-it-works.png)

A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. A feltételes hozzáférés nem a szervezet első védelmi vonala, mint például a szolgáltatásmegtagadási (DoS) támadások, de ezekből az eseményekről származó jeleket is használhatnak a hozzáférés meghatározásához.

## <a name="common-signals"></a>Gyakori jelek

Azok a gyakori jelek, amelyekben a feltételes hozzáférés a szabályzat meghozatalakor a következő jeleket veszi figyelembe:

- Felhasználó vagy csoport tagsága
   - A házirendek célzott felhasználókat és csoportokat céloznak meg, amelyek lehetővé teszik a rendszergazdák számára a hozzáférés részletes szabályozását.
- IP-hely adatai
   - A szervezetek olyan megbízható IP-címtartományok hozhatók létre, amelyek szabályzati döntések elkészítésekor használhatók. 
   - A rendszergazdák az összes ország IP-tartományát megadhatják a forgalom blokkolásához vagy engedélyezéséhez.
- Eszköz
   - A feltételes hozzáférési szabályzatok kényszerítése esetén a megadott platformmal rendelkező vagy meghatározott állapotú eszközökkel rendelkező felhasználók is használhatók.
- Jelentkezés
   - Azok a felhasználók, akik egy adott alkalmazáshoz próbálnak hozzáférni, különböző feltételes hozzáférési házirendeket válthatnak ki. 
- Valós idejű és számított kockázati észlelés
   - A Azure AD Identity Protection lehetővé teszi az integrációt a feltételes hozzáférési házirendekkel a kockázatos bejelentkezési viselkedés azonosítása érdekében. A házirendek ezt követően kényszerítheti a felhasználókat a jelszó módosítására vagy a többtényezős hitelesítésre a kockázati szint csökkentése vagy a hozzáférés letiltása előtt, amíg a rendszergazda manuális beavatkozást nem végez.
- Microsoft Cloud App Security (MCAS)
   - Lehetővé teszi a felhasználói alkalmazások hozzáférésének és munkameneteinek valós idejű monitorozását és ellenőrzését, valamint a felhőalapú környezetben végrehajtott hozzáférés és a hozzájuk tartozó tevékenységek átláthatóságának növelését és felügyeletét.

## <a name="common-decisions"></a>Gyakori döntések

- Hozzáférés letiltása
   - Legszigorúbb döntés
- Hozzáférés biztosítása
   - A legkevésbé korlátozó döntés továbbra is megkövetelheti a következő lehetőségek közül egyet vagy többet:
      - Többtényezős hitelesítés megkövetelése
      - Eszköz megfelelőként való megjelölésének megkövetelése
      - Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése
      - Jóváhagyott ügyfélalkalmazás megkövetelése
      - Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)

## <a name="commonly-applied-policies"></a>Általánosan alkalmazott házirendek

Számos szervezet rendelkezik olyan általános hozzáféréssel, amelyekkel a feltételes hozzáférési szabályzatok segíthetnek, például:

- Többtényezős hitelesítés megkövetelése rendszergazdai szerepkörökkel rendelkező felhasználók számára
- Többtényezős hitelesítés megkövetelése az Azure felügyeleti feladatokhoz
- A régi hitelesítési protokollok használatát megkísérlő felhasználók bejelentkezésének letiltása
- Megbízható helyek megkövetelése az Azure Multi-Factor Authentication regisztrálásához
- Megadott helyekről való hozzáférés letiltása vagy megadása
- Kockázatos bejelentkezési viselkedések blokkolása
- Szervezet által felügyelt eszközök megkövetelése adott alkalmazásokhoz

## <a name="customer-case-studies"></a>Ügyfél-esettanulmányok

Ismerje meg, hogy más szervezetek hogyan használják az Azure AD feltételes hozzáférést az automatizált hozzáférés-vezérlési döntések definiálásához és megvalósításához. A következő Kiemelt történetek bemutatják, hogyan teljesítik ezeket az ügyfelek igényeit.

* [A Wipro a Microsoft Cloud Security eszközeivel fejleszti a mobil termelékenységet az ügyfelek bevonásának javítása érdekében.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Az Azure AD feltételes hozzáférési szabályzatai lehetővé tették a vállalat számára, hogy megosszák a megbízható külső entitásokkal rendelkező dokumentumokat, erőforrásokat és alkalmazásokat,---akik saját hitelesítő adataikat használhatják---a saját vállalati adatok feletti felügyelet fenntartása mellett.
* [A Aramex Delivery Limited – a globális logisztika és a szállítmányozási vállalat felhőalapú, identitás-és hozzáférés-kezelési megoldással rendelkező irodát hoz létre](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). A biztonságos hozzáférés biztosítása különösen nehéz volt a Aramex távoli alkalmazottaival. A vállalat mostantól feltételes hozzáférést alkalmaz arra, hogy ezek a távoli alkalmazottak a hálózaton kívülről férhessenek hozzá SaaS-alkalmazásaihoz. A feltételes hozzáférési szabály eldönti, hogy kikényszeríti-e Multi-Factor Authenticationt, és csak a megfelelő személyeket adja meg a megfelelő hozzáféréssel.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

A [Microsoft 365 vállalati verzió licenccel](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is. 

## <a name="next-steps"></a>Következő lépések

[Feltételes hozzáférési szabályzat létrehozása darab alapján](concept-conditional-access-policies.md)

Ha meg szeretné tudni, hogyan valósítható meg a feltételes hozzáférés a környezetben, tekintse meg [a feltételes hozzáférés Megtervezése Azure Active Directoryban](plan-conditional-access.md)című témakört.

[Tudnivalók az Identity Protectionről](../identity-protection/overview-v2.md)

[Tudnivalók a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)

[Tudnivalók a Microsoft Intune](https://docs.microsoft.com/intune/index)