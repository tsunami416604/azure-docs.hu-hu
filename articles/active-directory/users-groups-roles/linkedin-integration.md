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
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ca46d6df9c32f23d3051d1205c3c6b39e69f5a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164713"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>LinkedIn-fiókkapcsolatok az Azure Active Directory integrálása

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

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Az Azure Portalon LinkedIn-fiókkapcsolatok engedélyezése

LinkedIn-fiókkapcsolatok csak a felhasználók számára szeretné engedélyezni a hozzáférést, a teljes szervezet a szervezet csak a kijelölt felhasználókra is engedélyezheti.

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure ad-ben a szervezet globális rendszergazdája.
1. Válassza ki **felhasználók**.
1. Az a **felhasználók** panelen válassza ki **felhasználói beállítások**.
1. A **LinkedIn-fiókkapcsolatok**, engedélyezése a felhasználók számára, hogy csatlakoztathassák a fiókjukat a LinkedIn kapcsolatok bizonyos Microsoft-alkalmazások eléréséhez. Nincsenek adatok meg van osztva, amíg a felhasználók beleegyezik abba, hogy csatlakoztathassák a fiókjukat.

    * Válassza ki **Igen** a szervezet összes felhasználója számára a szolgáltatás engedélyezése
    * Válassza ki **kiválasztott csoportot** a szolgáltatás csak a kiválasztott felhasználók a szervezet csoport engedélyezése
    * Válassza ki **nem** való Jóváhagyás eltávolítása a szervezet összes felhasználója

    ![Integrálása a szervezet LinkedIn-fiókkapcsolatok](./media/linkedin-integration/linkedin-integration.png)

1. Ha elkészült, válassza ki a **mentése** a beállítások mentéséhez.

> [!Important]
> LinkedIn-integráció nem teljes mértékben áll a felhasználók mindaddig, amíg azok beleegyezik abba, hogy csatlakoztathassák a fiókjukat. Nincs adat fiókkapcsolatok engedélyezésével a felhasználók számára meg van osztva.

### <a name="assign-selected-users-with-a-group"></a>Kiválasztott felhasználók hozzárendelése egy csoporthoz
A "Kijelölt" lehetőséget, amely meghatározza a felhasználók listáját a engedélyezheti a LinkedIn- és Microsoft-fiókok számos egyéni felhasználók helyett egy adott csoportra vonatkozó csatlakozás lehetővé teszi, hogy a felhasználók egy csoportjára kívánt váltotta. Ha nincs engedélyezve a kiválasztott egyéni felhasználók LinkedIn-fiókkapcsolatok, nem kell semmit. Ha korábban engedélyezte a LinkedIn-fiókkapcsolatok a kiválasztott egyéni felhasználók számára, akkor az alábbi lépéseket kell tennie:

1. Az egyes felhasználók aktuális listájának beolvasása
1. A jelenleg engedélyezve van az egyes felhasználók csoport áthelyezése
1. A csoport az előző használja a beállítása az Azure AD felügyeleti központ az a LinkedIn-fiókkapcsolatok a kiválasztott csoporthoz.

> [!NOTE]
> Akkor is, ha a jelenleg kijelölt egyes felhasználók nem át egy csoportot, LinkedIn-adatokat a Microsoft-alkalmazások továbbra is láthatják.

### <a name="get-the-current-list-of-selected-users"></a>Kiválasztott felhasználók aktuális listájának beolvasása

1. Jelentkezzen be a Microsoft 365-rendszergazdai fiókjával.
1. Nyissa meg a következőt: https://linkedinselectedusermigration.azurewebsites.net/. Láthatja, hogy ki van választva a LinkedIn-fiókkapcsolatok felhasználók listája.
1. A lista exportálása CSV-fájlba.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>A jelenleg kijelölt egyes felhasználók csoport áthelyezése

1. A PowerShell indítása
1. Futtassa az Azure AD-modul telepítése `Install-Module AzureAD`
1. Futtassa a következő parancsfájlt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

A csoport a második lépés a kiválasztott csoport beállítása az Azure AD felügyeleti központ az a LinkedIn-fiókkapcsolatok használja, lásd: [engedélyezése LinkedIn-fiókkapcsolatok az Azure Portalon](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok engedélyezése csoportházirend használatával

1. Töltse le a [Office 2016 felügyeleti sablonfájlok (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Bontsa ki a **ADMX** fájlokat, és másolja őket a központi tárolóban.
1. Nyissa meg a Csoportházirend kezelése.
1. Hozzon létre egy csoportházirend-objektum a következő beállítást: **Felhasználói konfiguráció** > **felügyeleti sablonok** > **a Microsoft Office 2016** > **vegyes**  >  **Megjelenítése a LinkedIn-funkciókat az Office-alkalmazásokban**.
1. Válassza ki **engedélyezve** vagy **letiltott**.
  
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
