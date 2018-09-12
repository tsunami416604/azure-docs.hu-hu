---
title: LinkedIn kapcsolatok integrációja az Azure Active Directoryban |} A Microsoft Docs
description: Azt ismerteti, hogyan engedélyezheti vagy tilthatja le a Microsoft-alkalmazások az Azure Active Directoryban a LinkedIn-fiókkapcsolatok
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392248"
---
# <a name="linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok

Ebben a cikkben azt is megtudhatja, hogyan engedélyezheti vagy tilthatja le a LinkedIn-fiókkapcsolatok a bérlő az Azure Active Directory (Azure AD) felügyeleti központban.

> [!IMPORTANT]
> A LinkedIn-fiókkapcsolatok beállítása jelenleg tesszük elérhetővé az Azure AD-bérlők számára. Amikor bevezetné a bérlőjéhez, alapértelmezés szerint engedélyezve van. 
> 
> Kivételek:
> * A beállítás nem érhető el az USA kormányzati szerveinek biztosított, a Microsoft Cloud németországi adatközpontjában, vagy az Azure és az Office 365 Kínában a 21Vianet által üzemeltetett Microsoft Cloudot használó ügyfelek számára.
> * A beállítás Németországban kiépített bérlők alapértelmezés szerint ki van kapcsolva. Vegye figyelembe, hogy a beállítás nem érhető el a Microsoft Cloud németországi adatközpontjában használó ügyfelek számára.
> * A beállítás a franciaországi kiépített bérlők alapértelmezés szerint ki van kapcsolva.

> Csak akkor, ha nincs engedélyezve működik az integráció *és* Ha engedélyezi a felhasználók számára, hogy engedélyt adjanak az alkalmazásoknak a céges adatok saját nevükben való eléréséhez. A jóváhagyás beállítással kapcsolatos további információkért lásd: [eltávolítása egy felhasználó hozzáférést egy alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Engedélyezi vagy letiltja a LinkedIn-fiókkapcsolatok a bérlő az Azure Portalon

Engedélyezheti vagy letilthatja a LinkedIn-fiókkapcsolatok a teljes bérlőn, vagy csak a kijelölt felhasználók a bérlőben.

1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-bérlő globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza ki **felhasználói beállítások**.
4. A **LinkedIn-fiókkapcsolatok**:
  * Válassza ki **Igen** ahhoz, hogy a bérlő összes felhasználója a LinkedIn-fiókkapcsolatok
  * Válassza ki **kijelölt** engedélyezéséhez LinkedIn-fiók csak a kiválasztott bérlő kapcsolatok felhasználók
  * Válassza ki **nem** letiltása az összes felhasználó LinkedIn-fiókkapcsolatok ![engedélyezése LinkedIn-fiókkapcsolatok](./media/linkedin-integration/linkedin-integration.png)
5. Ha elkészült, válassza a beállítások mentéséhez **mentése**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Engedélyezi vagy letiltja a LinkedIn-fiókkapcsolatok a bérlő csoportházirend használatával

1. Töltse le a [Office 2016 felügyeleti sablonfájlok (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Bontsa ki a **ADMX** fájlokat, és másolja őket a központi tárolóban.
3. Nyissa meg a Csoportházirend kezelése.
4. Hozzon létre egy csoportházirend-objektum a következő beállítással: **felhasználói konfiguráció** > **felügyeleti sablonok** > **a Microsoft Office 2016**  >  **Egyéb rendelkezések** > **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban**.
5. Válassza ki **engedélyezve** vagy **letiltott**.
  
 Állapot | Következmény
------ | ------
**Engedélyezve van** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** Office 2016-beállítások a beállítás engedélyezve van. A szervezeti felhasználók az Office alkalmazások használhatja a munkahelyi LinkedIn-szolgáltatásokat.
 **Letiltva** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** az Office 2016 beállítások beállítás le van tiltva, és a végfelhasználók számára a beállítás nem módosítható. A szervezet felhasználói az Office 2016 alkalmazásaikban nem használható a munkahelyi LinkedIn-szolgáltatásokat.

A csoportházirend érvényes csak az Office 2016-alkalmazások a helyi számítógépeken. Felhasználói láthatják az Office 365 teljes profil kártyák munkahelyi LinkedIn-szolgáltatásokat, akkor is, ha a LinkedIn elkezdődnek Office 2016-alkalmazásokban.

## <a name="learn-more"></a>Részletek

* [LinkedIn integrálása a szervezet](linkedin-user-consent.md)

* [LinkedIn-adatokat és a szolgáltatások a Microsoft-alkalmazásba](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn-súgóközpont](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>További lépések
A következő hivatkozás használatával tekintse meg az aktuális LinkedIn-fiókkapcsolatok beállítása az Azure Portalon:

[LinkedIn-fiókkapcsolatok konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 