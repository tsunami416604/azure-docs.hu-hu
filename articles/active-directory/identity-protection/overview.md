---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Ismerje meg, hogy Azure AD Identity Protection lehetővé teszi a támadók számára a feltört identitások vagy eszközök kihasználása, illetve az olyan identitások vagy eszközök biztonságossá tételét, amelyekről korábban gyanúba esett vagy ismertek.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a542d3208d5871d88c966fffc65cf16e0fbeee
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335395"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Mi az Azure Active Directory Identity Protection?

Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection lehetővé teszi, hogy a szervezetek automatizált válaszokat konfiguráljanak a felhasználói identitásokkal kapcsolatos gyanús műveletekre.

## <a name="get-started"></a>Bevezetés

A Microsoft több mint egy évtizede biztosította a felhőalapú identitások védelmét. A Azure Active Directory Identity Protection a környezetében ugyanazokat a védelmi rendszereket használhatja, amelyeket a Microsoft az identitások védelmére használ.

A biztonsági rések túlnyomó többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. Az évek során a támadók egyre hatékonyabban hasznosítják a harmadik féltől származó szabálysértéseket és kifinomult adathalászat-támadásokat használnak. Amint egy támadó még az alacsony jogosultsági szintű felhasználói fiókokhoz is hozzáfér, viszonylag könnyen hozzáférhet a fontos vállalati erőforrásokhoz az oldalirányú mozgással.

Ennek következményeként a következőket kell tennie:

- Az összes identitás biztosítása a jogosultsági szintjétől függetlenül
- Proaktív módon megakadályozhatja a feltört identitások visszaélését

A feltört identitások feltárása nem egyszerű feladat. A Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikus használatot alkalmaz a potenciálisan feltört identitást jelző rendellenességek és gyanús incidensek észlelésére. Ezeknek az adatoknak a használatával az Identity Protection jelentéseket és riasztásokat hoz létre, amelyek lehetővé teszik az észlelt problémák kiértékelését, valamint a megfelelő kockázatcsökkentő vagy szervizelési műveletek elvégzését.

Azure Active Directory Identity Protection több, mint egy figyelési és jelentéskészítési eszköz. A szervezet identitásának megóvása érdekében olyan kockázatalapú házirendeket állíthat be, amelyek automatikusan válaszolnak az észlelt problémákra, amikor a rendszer elérte az adott kockázati szintet. Ezek a szabályzatok a Azure Active Directory és [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS) által biztosított egyéb feltételes hozzáférés-vezérlések mellett automatikusan letilthatják vagy elindíthatják az adaptív szervizelési műveleteket, beleértve a jelszavak alaphelyzetbe állítását és többtényezős hitelesítés kényszerítése.

### <a name="identity-protection-capabilities"></a>Identity Protection-képességek

**Biztonsági rések és kockázatos fiókok észlelése:**  

- Egyéni javaslatok nyújtása a biztonsági rések kiemelésével az általános biztonsági helyzet javításához
- A bejelentkezési kockázati szintek kiszámítása
- Felhasználói kockázati szintek kiszámítása

**Kockázati események kivizsgálása:**

- Értesítés küldése a kockázati eseményekről
- Kockázati események kivizsgálása a releváns és a környezetfüggő információk használatával
- Alapvető munkafolyamatok biztosítása a vizsgálatok nyomon követéséhez
- Egyszerű hozzáférés biztosítása a hibajavítási műveletekhez, például a jelszó-visszaállításhoz

**Kockázatalapú feltételes hozzáférési szabályzatok:**

- Házirend a kockázatos bejelentkezések enyhítéséhez a bejelentkezések blokkolásával vagy a többtényezős hitelesítési kihívások megkövetelésével
- Kockázatos felhasználói fiókok blokkolására vagy biztonságossá tételére vonatkozó szabályzat
- A többtényezős hitelesítéshez szükséges felhasználók regisztrálására szolgáló szabályzat

## <a name="identity-protection-roles"></a>Identity Protection-szerepkörök

Ha terheléselosztást szeretne végrehajtani a felügyeleti tevékenységek között az Identity Protection megvalósításán, több szerepkört is hozzárendelhet. A Azure AD Identity Protection 3 címtárbeli szerepkört támogat:

| Role | Elvégezhető | Nem hajtható végre |
| :-- | --- | --- |
| Globális rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz, a személyazonosság védelme| |
| Biztonsági rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz | A felhasználók jelszavainak alaphelyzetbe állítása |
| Biztonsági olvasó | Írásvédett hozzáférés az Identity Protectionhez | A személyazonosság védelme, a felhasználók szervizelése, a házirendek konfigurálása, a jelszavak alaphelyzetbe állítása |

További részletekért lásd: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>Észlelés

### <a name="vulnerabilities"></a>Biztonsági rések

Azure Active Directory Identity Protection elemzi a konfigurációt, és észleli azokat a biztonsági réseket, amelyek hatással lehetnek a felhasználó identitására. További részletekért tekintse meg a [Azure Active Directory Identity Protection által észlelt biztonsági réseket](vulnerabilities.md).

### <a name="risk-events"></a>Kockázati események

Azure Active Directory adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználó identitásával kapcsolatos gyanús műveletek észlelésére. A rendszer létrehoz egy rekordot minden észlelt gyanús művelethez. Ezeket a rekordokat kockázati eseménynek is nevezzük.  
További részletek: [Az Azure Active Directory kockázati eseményei](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Vizsgálat

Az Identity Protection szolgáltatással való utazás általában az Identity Protection irányítópulttal kezdődik.

![Szervizelés](./media/overview/1000.png "Szervizelés")

Az irányítópult hozzáférést biztosít a következőkhöz:

- Jelentések, például **a kockázatra megjelölt felhasználók**, **kockázati események** és **biztonsági rések**
- Beállítások, például **biztonsági házirendek**konfigurációja, **értesítések** és **multi-Factor Authentication-regisztráció**

Általában a vizsgálat kiindulási pontja, amely a kockázati eseményekkel kapcsolatos tevékenységek, naplók és egyéb kapcsolódó információk áttekintését mutatja be, hogy eldöntse, hogy szükséges-e szervizelési vagy kockázatcsökkentő lépések, és hogy az identitás milyen módon volt megsérült, és megértette, hogyan használták a feltört identitást.

A vizsgálati tevékenységeket összekapcsolhatja az [értesítések](notifications.md) Azure Active Directory a védelem küldése e-mailben.

## <a name="policies"></a>Házirendek

Az automatikus válaszok megvalósításához a Azure Active Directory Identity Protection három szabályzatot biztosít:

- [Multi-Factor Authentication regisztrációs szabályzat](howto-mfa-policy.md)
- [Felhasználói kockázati házirend](howto-user-risk-policy.md)
- [Bejelentkezési kockázati házirend](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>További lépések

- [Channel 9: Azure AD-és identitás-megjelenítés: Identity Protection – előzetes verzió](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Azure Active Directory Identity Protection engedélyezése](enable.md)
