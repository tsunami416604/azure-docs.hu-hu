---
title: Csoportok & alkalmazások hozzáférési felülvizsgálatának befejezése - Azure AD
description: Megtudhatja, hogyan végezheti el a csoporttagok vagy az alkalmazások hozzáférésének hozzáférés-felülvizsgálatát az Azure Active Directory-hozzáférési felülvizsgálatokban.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932552"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférés-felülvizsgálatának befejezése az Azure AD-hozzáférés-felülvizsgálatokban

Rendszergazdaként [hozzon létre egy hozzáférés-áttekintést a csoportokról vagy alkalmazásokról,](create-access-review.md) és az ellenőrzők [elvégzik a hozzáférési felülvizsgálatot.](perform-access-review.md) Ez a cikk ismerteti, hogyan tekintheti meg a hozzáférési felülvizsgálat eredményeit, és hogyan alkalmazhatja az eredményeket.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda, felhasználói rendszergazda, biztonsági rendszergazda vagy biztonsági olvasó

További információt a [Licenckövetelmények című témakörben talál.](access-reviews-overview.md#license-requirements)

## <a name="view-an-access-review"></a>Hozzáférési ellenőrzés megtekintése

Nyomon követheti a folyamatot, amint az ellenőrzők befejezik az ellenőrzéseket.

1. Jelentkezzen be az Azure Portalra, és nyissa meg az [Identitáscégcég-kezelő lapot.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

1. A bal oldali menüben kattintson az **Access-ellenőrzések parancsra.**

1. A listában kattintson egy hozzáférési ellenőrzésre.

    A hozzáférési felülvizsgálatok sorozatának megtekintéséhez keresse meg a hozzáférési felülvizsgálatot, és megtalálja a közelgő eseményeket az Ütemezett ellenőrzések ben.

    Az **Áttekintés** lapon láthatja a folyamatot. A címtárban az ellenőrzés befejezéséig nem módosulnak a hozzáférési jogok.

    ![A hozzáférés-ellenőrzések előrehaladásának áttekintése](./media/complete-access-review/overview-progress.png)

1. Ha le szeretne állítani egy hozzáférési felülvizsgálatot, mielőtt elérne az ütemezett befejezési dátumot, kattintson a **Leállítás** gombra.

    Ha leállít egy felülvizsgálatot, az ellenőrzők a továbbiakban nem adhatnak választ. Az ellenőrzés leállítása után nem lehet újraindítani.

1. Ha már nem érdekli a hozzáférési felülvizsgálat, a Törlés gombra kattintva **törölheti** azt.

## <a name="apply-the-changes"></a>A módosítások alkalmazása

Ha **az automatikus eredmény alkalmazása az erőforrásra** engedélyezve volt, és a **befejezéskor beállításokban**megadott beállítások alapján az automatikus alkalmazás az ellenőrzés záró dátuma után vagy az ellenőrzés manuális leállításakor kerül végrehajtásra.

Ha **az automatikus alkalmazás az erőforrásra** nem volt engedélyezve az ellenőrzéshez, kattintson az **Alkalmaz** gombra a módosítások manuális alkalmazásához. Ha egy felhasználó hozzáférését megtagadták az ellenőrzés, amikor az **Alkalmaz gombra**kattint, az Azure AD eltávolítja a tagságvagy az alkalmazás-hozzárendelés.

![Hozzáférés-ellenőrzési módosítások alkalmazása](./media/complete-access-review/apply-changes.png)

A felülvizsgálat állapota **befejezettről** köztes állapotokra változik, például **az Alkalmazás** és végül az **alkalmazott eredmény**állapotra. A rendszer néhány percen belül elvárja, hogy a megtagott felhasználók ( ha vannak ilyenek ) törlődjenek a csoporttagságból vagy az alkalmazás-hozzárendelésből.

A konfigurált automatikus ellenőrzés alkalmazása, vagy **az Alkalmaz** lehetőség kiválasztása nincs hatással egy helyszíni címtárból vagy dinamikus csoportból származó csoportra. Ha módosítani szeretné a helyszíni adatokból származó csoportot, töltse le az eredményeket, és alkalmazza ezeket a módosításokat a csoport könyvtárbeli ábrázolására.

## <a name="retrieve-the-results"></a>Az eredmény lekérése

Az egyszeri hozzáférés felülvizsgálatának eredményeinek megtekintéséhez kattintson az **Eredmények** lapra. Csak a felhasználó hozzáférésének megtekintéséhez írja be annak a felhasználónak a megjelenítendő nevét vagy egyszerű felhasználónevét, akinek a hozzáférését ellenőrizték.

![Hozzáférés-ellenőrzés eredményeinek beolvasása](./media/complete-access-review/retrieve-results.png)

Az ismétlődő aktív hozzáférés-ellenőrzés előrehaladásának megtekintéséhez kattintson az **Eredmények** lapra.

Ismétlődő hozzáférési felülvizsgálat befejezett példányának eredményének megtekintéséhez kattintson az **Előzmények áttekintése**gombra, majd válassza ki az adott példányt a befejezett hozzáférés-ellenőrzési példányok listájából a példány kezdő és záró dátuma alapján. Ennek a példánynak az eredményei az **Eredmények** oldalon szerezhetők be.

A hozzáférési felülvizsgálat összes eredményének beolvasásához kattintson a **Letöltés** gombra. Az így kapott CSV fájl megtekinthető az Excel vagy más programok, amelyek megnyitják UTF-8 kódolt CSV fájlokat.

## <a name="remove-users-from-an-access-review"></a>Felhasználók eltávolítása a hozzáférési felülvizsgálatból

 Alapértelmezés szerint a törölt felhasználók 30 napig töröltek maradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket.  A felhasználók 30 nap után véglegesen törlődnek.  Emellett az Azure Active Directory portál használatával a globális rendszergazda explicit módon [véglegesen törölheti a nemrég törölt felhasználót,](../fundamentals/active-directory-users-restore.md) mielőtt ezt az időszakot elérné.  Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív hozzáférési felülvizsgálatokból.  A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="next-steps"></a>További lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-guest-access-with-access-reviews.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md)
