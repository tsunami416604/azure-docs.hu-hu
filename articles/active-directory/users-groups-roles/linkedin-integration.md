---
title: Az Azure Active Directoryban a LinkedIn-integráció engedélyezése |} A Microsoft Docs
description: Azt ismerteti, hogyan engedélyezheti vagy tilthatja le a LinkedIn-integráció a Microsoft-alkalmazások az Azure Active Directoryban
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/22/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cccbeefd49f5374aeffcae07ac8c888f7a28690f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159998"
---
# <a name="linkedin-integration"></a>LinkedIn-integráció

Ebben a cikkben azt is megtudhatja, hogyan engedélyezheti vagy tilthatja le a LinkedIn-integráció az Azure Active Directory (Azure AD) felügyeleti központban a bérlő számára.

> [!IMPORTANT]
> A LinkedIn-integrációs beállításainak jelenleg tesszük elérhetővé az Azure AD-bérlők számára. Amikor bevezetné a bérlőjéhez, alapértelmezés szerint engedélyezve van.
> 
> Kivételek:
> * A beállítás nem érhető el az USA kormányzati szerveinek biztosított, a Microsoft Cloud németországi adatközpontjában, vagy az Azure és az Office 365 Kínában a 21Vianet által üzemeltetett Microsoft Cloudot használó ügyfelek számára.
> * A beállítás Németországban kiépített bérlők alapértelmezés szerint ki van kapcsolva. Vegye figyelembe, hogy a beállítás nem érhető el a Microsoft Cloud németországi adatközpontjában használó ügyfelek számára.
> * A beállítás a franciaországi kiépített bérlők alapértelmezés szerint ki van kapcsolva.

> Csak akkor, ha nincs engedélyezve működik az integráció *és* Ha engedélyezi a felhasználók számára, hogy engedélyt adjanak az alkalmazásoknak a céges adatok saját nevükben való eléréséhez. A jóváhagyás beállítással kapcsolatos további információkért lásd: [eltávolítása egy felhasználó hozzáférést egy alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Engedélyezheti vagy tilthatja le a LinkedIn-integráció az Azure Portalon a felhasználók számára

Engedélyezi, vagy tiltsa le a teljes bérlőn, vagy csak a kiválasztott felhasználók a LinkedIn-integráció a bérlőben.

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza ki **felhasználói beállítások**.
4. A **LinkedIn-integráció**:
  * Válassza ki **Igen** a bérlő összes felhasználója LinkedIn-integráció engedélyezése
  * Válassza ki **kijelölt** LinkedIn-integráció csak a kiválasztott bérlő felhasználók engedélyezése
  * Válassza ki **nem** letiltása az összes felhasználó számára a LinkedIn-integráció ![engedélyezése LinkedIn-integráció](./media/linkedin-integration/linkedin-integration.png)
5. Ha elkészült, válassza a beállítások mentéséhez **mentése**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Engedélyezi vagy letiltja a felhasználók számára a csoportházirend LinkedIn-integráció

1. Töltse le a [Office 2016 felügyeleti sablonfájlok (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Bontsa ki a **ADMX** fájlokat, és másolja őket a központi tárolóban.
3. Nyissa meg a Csoportházirend kezelése.
4. Hozzon létre egy csoportházirend-objektum a következő beállítást: **Felhasználói konfiguráció** > **felügyeleti sablonok** > **a Microsoft Office 2016** > **vegyes**  >  **Megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban**.
5. Válassza ki **engedélyezve** vagy **letiltott**.
  
 Állapot | Következmény
------ | ------
**Engedélyezve** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** Office 2016-beállítások a beállítás engedélyezve van. A szervezeti felhasználók az Office alkalmazások használhatja a munkahelyi LinkedIn-szolgáltatásokat.
 **Letiltva** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** az Office 2016 beállítások beállítás le van tiltva, és a végfelhasználók számára a beállítás nem módosítható. A szervezet felhasználói az Office 2016 alkalmazásaikban nem használható a munkahelyi LinkedIn-szolgáltatásokat.

A csoportházirend érvényes csak az Office 2016-alkalmazások a helyi számítógépeken. Felhasználói láthatják az Office 365 teljes profil kártyák munkahelyi LinkedIn-szolgáltatásokat, akkor is, ha a LinkedIn elkezdődnek Office 2016-alkalmazásokban.

## <a name="learn-more"></a>Részletek

* [LinkedIn integrálása a szervezet](linkedin-user-consent.md)

* [LinkedIn-adatokat és a szolgáltatások a Microsoft-alkalmazásba](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn-súgóközpont](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>További lépések

A következő hivatkozás használatával tekintse meg a jelenlegi LinkedIn integráció beállítása az Azure Portalon:

[Megtekintheti a jelenlegi LinkedIn-integráció beállítása az Azure Portalon](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
