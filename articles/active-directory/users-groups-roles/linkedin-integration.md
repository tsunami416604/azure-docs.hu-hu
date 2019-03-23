---
title: A LinkedIn-fiókkapcsolatok – Azure Active Directory rendszergazdai jóváhagyás |} A Microsoft Docs
description: Azt ismerteti, hogyan engedélyezheti vagy tilthatja le a LinkedIn integrációs fiók kapcsolatok a Microsoft-alkalmazások az Azure Active Directoryban
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368124"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Járul hozzá az Azure Active Directory-szervezet LinkedIn-fiókkapcsolatok

A saját LinkedIn kapcsolatok bizonyos Microsoft-alkalmazások eléréséhez a szervezet lehetővé tehetik a felhasználóknak. Nincsenek adatok meg van osztva, amíg a felhasználók beleegyezik abba, hogy csatlakoztathassák a fiókjukat. A szervezet az Azure Active Directoryban (Azure AD) integrálható [felügyeleti központban](https://aad.portal.azure.com).

> [!IMPORTANT]
> A LinkedIn-fiókkapcsolatok beállítása jelenleg tesszük elérhetővé az Azure AD a szervezetek számára. Amikor bevezetné a szervezet számára, hogy alapértelmezés szerint engedélyezve van.
> 
> Kivételek:
> * A beállítás nem érhető el az USA kormányzati szerveinek biztosított, a Microsoft Cloud németországi adatközpontjában, vagy az Azure és az Office 365 Kínában a 21Vianet által üzemeltetett Microsoft Cloudot használó ügyfelek számára.
> * A beállítás Németországban kiépített bérlők alapértelmezés szerint ki van kapcsolva. Vegye figyelembe, hogy a beállítás nem érhető el a Microsoft Cloud németországi adatközpontjában használó ügyfelek számára.
> * A beállítás a franciaországi kiépített bérlők alapértelmezés szerint ki van kapcsolva.
>
> Ha a LinkedIn-fiókkapcsolatok engedélyezve vannak a szervezet számára, az a fiók kapcsolatok követően a felhasználók járul hozzá az alkalmazások hozzáférjenek a céges adatok saját nevükben való működik. A felhasználó hozzájárulási beállítással kapcsolatos további információkért lásd: [eltávolítása egy felhasználó hozzáférést egy alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok engedélyezése az Azure portal használatával

LinkedIn-fiókkapcsolatok csak a felhasználók számára szeretné engedélyezni a hozzáférést, a teljes szervezet a szervezet csak a kijelölt felhasználókra is engedélyezheti.

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure ad-ben a szervezet globális rendszergazdája.
2. Válassza ki **felhasználók**.
3. Az a **felhasználók** panelen válassza ki **felhasználói beállítások**.
4. A **LinkedIn-fiókkapcsolatok**, engedélyezése a felhasználók számára, hogy csatlakoztathassák a fiókjukat a LinkedIn kapcsolatok bizonyos Microsoft-alkalmazások eléréséhez. Nincsenek adatok meg van osztva, amíg a felhasználók beleegyezik abba, hogy csatlakoztathassák a fiókjukat.

  * Válassza ki **Igen** a szolgáltatást a szervezetében lévő összes felhasználó engedélyezése
  * Válassza ki **kijelölt** jóváhagyást a szervezet csak a kijelölt felhasználók
  * Válassza ki **nem** a szervezet felhasználói jóváhagyás visszavonása

    ![Integrálása a szervezet LinkedIn-fiókkapcsolatok](./media/linkedin-integration/linkedin-integration.png)

5. Ha elkészült, válassza ki a **mentése** a beállítások mentéséhez.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok engedélyezése csoportházirend használatával

1. Töltse le a [Office 2016 felügyeleti sablonfájlok (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Bontsa ki a **ADMX** fájlokat, és másolja őket a központi tárolóban.
3. Nyissa meg a Csoportházirend kezelése.
4. Hozzon létre egy csoportházirend-objektum a következő beállítást: **Felhasználói konfiguráció** > **felügyeleti sablonok** > **a Microsoft Office 2016** > **vegyes**  >  **Megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban**.
5. Válassza ki **engedélyezve** vagy **letiltott**.
  
   Állapot | Következmény
   ------ | ------
   **Engedélyezve** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** Office 2016-beállítások a beállítás engedélyezve van. A szervezet felhasználói az Office 2016 alkalmazásaikban használhatja a munkahelyi LinkedIn-szolgáltatásokat.
   **Letiltva** | A **megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban** az Office 2016 beállítások beállítás le van tiltva, és a végfelhasználók számára a beállítás nem módosítható. A szervezet felhasználói az Office 2016 alkalmazásaikban nem használható a munkahelyi LinkedIn-szolgáltatásokat.

A csoportházirend érvényes csak az Office 2016-alkalmazások a helyi számítógépeken. Ha a felhasználó letiltotta a LinkedIn-alkalmazásokban az Office 2016, Office 365-ben a munkahelyi LinkedIn-szolgáltatásokat továbbra is megjelenik.

## <a name="next-steps"></a>További lépések

* [Felhasználói beleegyezés és az adatok megosztása a Linkedinen](linkedin-user-consent.md)

* [LinkedIn-adatokat és a szolgáltatások a Microsoft-alkalmazásba](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn-súgóközpont](https://www.linkedin.com/help/linkedin)

* [Megtekintheti a jelenlegi LinkedIn-integráció beállítása az Azure Portalon](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
