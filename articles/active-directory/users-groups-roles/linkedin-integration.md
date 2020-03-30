---
title: Rendszergazdai hozzájárulás linkedin-fiókkapcsolatokhoz – Azure AD | Microsoft dokumentumok
description: A LinkedIn-integrációs fiókkapcsolatok engedélyezése és letiltása az Azure Active Directoryban a Microsoft-alkalmazásokban
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025415"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>LinkedIn-fiókkapcsolatok integrálása az Azure Active Directoryban

Engedélyezheti a szervezet felhasználóinak, hogy egyes Microsoft-alkalmazásokban hozzáférjenek a LinkedIn-kapcsolataikhoz. A rendszer mindaddig nem oszt meg adatokat, amíg a felhasználók nem járulnak hozzá a fiókok összekapcsolásához. Integrálhatja a szervezetet az Azure Active Directory (Azure AD) [felügyeleti központjában.](https://aad.portal.azure.com)

> [!IMPORTANT]
> A LinkedIn-fiók kapcsolatok beállítása jelenleg bevezetés alatt áll az Azure AD-szervezetek számára. Amikor a szervezetben bevan vezetve, alapértelmezés szerint engedélyezve van.
> 
> Kivételek:
> * A beállítás nem érhető el a Microsoft Cloud for US Government, a Microsoft Cloud Germany, illetve a 21Vianet által kínában üzemeltetett Azure és Office 365 szolgáltatást használó ügyfelek számára.
> * A beállítás alapértelmezés szerint ki van kapcsolva a Németországban kiépített bérlők esetében. Ne feledje, hogy a beállítás nem érhető el a Microsoft Cloud Germany szolgáltatást használó ügyfelek számára.
> * A beállítás alapértelmezés szerint ki van kapcsolva a Franciaországban kiépített bérlők számára.
>
> Miután a LinkedIn-fiókkapcsolatok engedélyezve vannak a szervezetszámára, a fiókkapcsolatok akkor működnek, ha a felhasználók hozzájárulnak ahhoz, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások hozzáférjenek. A felhasználói hozzájárulási beállításról a [Felhasználó alkalmazáshoz való hozzáférésének eltávolítása](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)című témakörben talál további információt.

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>LinkedIn-fiókkapcsolatok engedélyezése az Azure Portalon

A LinkedIn-fiókkapcsolatokat csak a hozzá kívánt felhasználók számára engedélyezheti, a teljes szervezettől a szervezet csak a kijelölt felhasználókig.

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com/) egy olyan fiókkal, amely az Azure AD-szervezet globális rendszergazdája.
1. Válassza a **Felhasználók** lehetőséget.
1. A **Felhasználók** panelen válassza a **Felhasználói beállítások lehetőséget.**
1. A **LinkedIn-fiókkapcsolatok**csoportban engedélyezheti a felhasználóknak, hogy egyes Microsoft-alkalmazásokban összekapcsolják a fiókjukat a LinkedIn-kapcsolataik eléréséhez. A rendszer mindaddig nem oszt meg adatokat, amíg a felhasználók nem járulnak hozzá a fiókok összekapcsolásához.

    * Válassza az **Igen** lehetőséget a szolgáltatás engedélyezéséhez a szervezet összes felhasználója számára.
    * Válassza a **Kijelölt csoport** lehetőséget, ha a szolgáltatást csak a szervezet kijelölt felhasználóinak egy csoportjának szeretné engedélyezni.
    * Válassza a **Nem** lehetőséget a szervezet összes felhasználójának beleegyezésének visszavonásához.

    ![LinkedIn-fiókkapcsolatok integrálása a szervezetben](./media/linkedin-integration/linkedin-integration.png)

1. Ha elkészült, a Beállítások mentéséhez válassza a **Mentés** gombot.

> [!Important]
> A LinkedIn-integráció nem teljesen engedélyezett a felhasználók számára, amíg nem járulnak hozzá a fiókjuk csatlakoztatásához. Ha engedélyezi a fiókkapcsolatokat a felhasználók számára, a rendszer nem oszt meg adatokat.

### <a name="assign-selected-users-with-a-group"></a>Kijelölt felhasználók hozzárendelése csoporthoz
Felváltottuk a "Kiválasztott" lehetőséget, amely meghatározza a felhasználók listáját azzal a lehetőséggel, hogy kiválasszon egy felhasználói csoportot, így lehetővé teheti a LinkedIn és a Microsoft-fiókok csatlakoztatását egyetlen csoporthoz, nem pedig sok egyéni felhasználóhoz. Ha a kijelölt egyéni felhasználókszámára nincs engedélyezve a LinkedIn-fiókkapcsolatok, akkor semmit sem kell tennie. Ha korábban már engedélyezte a LinkedIn-fiókkapcsolatokat a kiválasztott egyéni felhasználók számára, a következőket kell tennie:

1. Az egyes felhasználók aktuális listájának beszereznie
1. A jelenleg engedélyezett felhasználók áthelyezése egy csoportba
1. Használja az előző csoport, mint a kijelölt csoport a LinkedIn-fiók kapcsolatok beállítása az Azure AD felügyeleti központ.

> [!NOTE]
> Még ha nem is mozgatja a jelenleg kijelölt egyéni felhasználókat egy csoportba, akkor is láthatják a LinkedIn-adatokat a Microsoft-alkalmazásokban.

### <a name="get-the-current-list-of-selected-users"></a>A kijelölt felhasználók aktuális listájának beszereznie

1. Jelentkezzen be a Microsoft 365-be rendszergazdai fiókjával.
1. Nyissa meg a következőt: https://linkedinselectedusermigration.azurewebsites.net/. Megjelenik azoknak a felhasználóknak a listája, akik a LinkedIn-fiókkapcsolatokra vannak kiválasztva.
1. Exportálja a listát CSV-fájlba.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Az aktuálisan kijelölt felhasználók áthelyezése egy csoportba

1. A PowerShell indítása
1. Az Azure AD modul telepítése a futtatással`Install-Module AzureAD`
1. Futtassa a következő parancsfájlt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Ha a második lépésből származó csoportot szeretné a LinkedIn-fiókkapcsolatok beállításában az Azure AD felügyeleti központban kiválasztott csoportként használni, olvassa el [a LinkedIn-fiókkapcsolatok engedélyezése az Azure Portalon című témakört.](#enable-linkedin-account-connections-in-the-azure-portal)

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>LinkedIn-fiókkapcsolatok engedélyezése csoportházirenddel

1. Az [Office 2016 felügyeleti sablonfájljainak (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) letöltése
1. Bontsa ki az **ADMX** fájlokat, és másolja őket a központi áruházba.
1. Nyissa meg a Csoportházirend kezelése eszközt.
1. Csoportházirend-objektum létrehozása a következő beállítással: **Felhasználói konfiguráció** > **felügyeleti sablonjai** > **Microsoft Office 2016** > **Egyéb** > **LinkedIn-szolgáltatások megjelenítése az Office-alkalmazásokban**.
1. Válassza **az Engedélyezve** vagy **a Letiltva**lehetőséget.
  
   Állapot | Hatás
   ------ | ------
   **Engedélyezve** | Az Office 2016 beállításaiban az **Office-alkalmazások LinkedIn-szolgáltatásainak megjelenítése** beállítás engedélyezve van. A szervezet felhasználói használhatják a LinkedIn-szolgáltatásokat az Office 2016-alkalmazásokban.
   **Letiltva** | Az Office 2016 beállításainak **LinkedIn-funkciók megjelenítése** beállításle van tiltva, és a végfelhasználók nem módosíthatják ezt a beállítást. A szervezet felhasználói nem használhatják a LinkedIn-szolgáltatásokat az Office 2016-alkalmazásokban.

Ez a csoportházirend csak a helyi számítógép Office 2016-os alkalmazásait érinti. Ha a felhasználók letiltják a LinkedInt az Office 2016-alkalmazásokban, továbbra is láthatják a LinkedIn-funkciókat az Office 365-ben.

## <a name="next-steps"></a>További lépések

* [A LinkedIn felhasználói hozzájárulása és adatmegosztása](linkedin-user-consent.md)

* [A LinkedIn adatai és szolgáltatásai a Microsoft-alkalmazásokban](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn súgó](https://www.linkedin.com/help/linkedin)

* [Az aktuális LinkedIn-integrációs beállítás megtekintése az Azure Portalon](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
