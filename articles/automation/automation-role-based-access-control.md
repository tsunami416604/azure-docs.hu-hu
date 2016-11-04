---
title: Szerepköralapú hozzáférés-vezérlés az Azure Automationben | Microsoft Docs
description: A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Ez a cikk ismerteti az RBAC beállítását az Azure Automationben.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: automation rbac, szerepköralapú hozzáférés-vezérlés, azure rbac

ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;sngun

---
# Szerepköralapú hozzáférés-vezérlés az Azure Automationben
## Szerepköralapú hozzáférés-vezérlés
A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Az [RBAC](../active-directory/role-based-access-control-configure.md) használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amely a felhasználóknak a feladataik elvégzéséhez szükséges. A szerepköralapú hozzáférés az Azure portál, az Azure parancssori segédeszközök vagy az Azure felügyeleti API-k segítségével adható a felhasználóknak.

## RBAC Automation-fiókokban
Az Azure Automationben a hozzáférés biztosításához a megfelelő RBAC-szerepkörök rendelhetők az Automation-fiók tartományában lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:  

| **Szerepkör** | **Leírás** |
|:--- |:--- |
| Tulajdonos |A tulajdonosi szerepkör segítségével hozzáférhet minden erőforráshoz és művelethez egy Automation-fiókon belül, beleértve azt is, hogy hozzáférést biztosíthat más felhasználók, csoportok és alkalmazások számára az Automation-fiók kezeléséhez. |
| Közreműködő |A közreműködői szerepkör segítségével minden elemet kezelhet, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó |Az olvasói szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor |Az Automation-operátori szerepkör lehetővé teszi olyan operatív feladatok végrehajtását, mint az indítás, a leállítás, a felfüggesztés, a folytatás és az ütemezés. Ez a szerepkör akkor hasznos, ha meg szeretné akadályozni, hogy az Automation-fiók erőforrásokat, például a hitelesítő adategységeket és a forgatókönyveket megtekintsék vagy módosítsák, de továbbra is engedélyezi a szervezet tagjai számára a forgatókönyvek végrehajtását. |
| Felhasználói hozzáférés rendszergazdája |A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

> [!NOTE]
> Nem biztosíthat hozzáférési jogot megadott runbookhoz vagy runbookokhoz, csak az Automation-fiókban található erőforrásokhoz és műveletekhez.  
> 
> 

Ebben a cikkben bemutatjuk az RBAC beállítását az Azure Automationben. Elsőként tekintse meg a Közreműködő, Olvasó, Automation-operátor és Felhasználói hozzáférés rendszergazdája számára biztosított egyéni engedélyeket, így elmélyítheti a tudását, mielőtt hozzáférést biztosítana másoknak az Automation-fiókhoz.  Ha ezt elmulasztja, a hozzáférés biztosítása nem tervezett és nem kívánt következményekkel járhat.     

## Közreműködői szerepkör engedélyei
A következő táblázat azokat a műveleteket mutatja be, amelyeket a Közreműködő szerepkörrel végezhet el az Automationban.

| **Erőforrás típusa** | **Olvasás** | **Írás** | **Törlés** | **Egyéb műveletek** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation-fiók |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation tanúsítvány-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation kapcsolat-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation kapcsolattípus-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation hitelesítési adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation ütemezési adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation változó-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation kívánt állapotának konfigurálása | | | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Hibrid forgatókönyv-feldolgozó erőforrástípus |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure Automation-feladat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Automation-feladatstream |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-feladatütemezés |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation-modul |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure Automation-runbook |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Automation-runbookvázlat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Automation-runbookvázlat tesztfeladat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Automation-webhook |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |

## Olvasói szerepkör engedélyei
A következő táblázat azokat a műveleteket mutatja be, amelyeket az Olvasói szerepkörrel végezhet el az Automationban.

| **Erőforrás típusa** | **Olvasás** | **Írás** | **Törlés** | **Egyéb műveletek** |
|:--- |:--- |:--- |:--- |:--- |
| Hagyományos előfizetés-adminisztrátor |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Felügyeleti zárolás |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Engedély |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Szolgáltatói műveletek |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Szerepkör-kijelölés |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Szerepkör-definíció |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |

## Automation-operátori szerepkör engedélyei
A következő táblázat azokat a műveleteket mutatja be, amelyeket az Automation-operátor szerepkörrel végezhet el az Automationban.

| **Erőforrás típusa** | **Olvasás** | **Írás** | **Törlés** | **Egyéb műveletek** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation-fiók |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation tanúsítvány-adategység | | | | |
| Automation kapcsolat-adategység | | | | |
| Automation kapcsolattípus-adategység | | | | |
| Automation hitelesítési adategység | | | | |
| Automation ütemezési adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation változó-adategység | | | | |
| Automation kívánt állapotának konfigurálása | | | | |
| Hibrid forgatókönyv-feldolgozó erőforrástípus | | | | |
| Azure Automation-feladat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |
| Automation-feladatstream |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-feladatütemezés |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation-modul | | | | |
| Azure Automation-runbook |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-runbookvázlat | | | | |
| Automation-runbookvázlat tesztfeladat | | | | |
| Automation-webhook | | | | |

Az [Automation-operátori műveletek](../active-directory/role-based-access-built-in-roles.md#automation-operator) az Automation-operátori szerepkör által az Automation-fiókon és az erőforrásokon támogatott műveleteket is felsorolja.

## Felhasználói hozzáférés rendszergazdája szerepkör engedélyei
A következő táblázat azokat a műveleteket mutatja be, amelyeket a Felhasználói hozzáférés rendszergazdája szerepkörrel végezhet el az Automationban.

| **Erőforrás típusa** | **Olvasás** | **Írás** | **Törlés** | **Egyéb műveletek** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation-fiók |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation tanúsítvány-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation kapcsolat-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation kapcsolattípus-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation hitelesítési adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation ütemezési adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation változó-adategység |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation kívánt állapotának konfigurálása | | | | |
| Hibrid forgatókönyv-feldolgozó erőforrástípus |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure Automation-feladat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-feladatstream |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-feladatütemezés |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-modul |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure Automation-runbook |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-runbookvázlat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-runbookvázlat tesztfeladat |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation-webhook |![Zöld állapot](media/automation-role-based-access-control/green-checkmark.png) | | | |

## Az Automation-fiókhoz tartozó RBAC konfigurálása az Azure portál segítségével
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok panelről.  
2. Kattintson a jobb felső sarokban található **Hozzáférés** vezérlőre. Ezzel megnyitja a **Felhasználók** panelt, ahol hozzáadhat új felhasználókat, csoportokat vagy alkalmazásokat az Automation-fiók kezeléséhez, és megtekintheti a meglévő szerepköröket, amelyek konfigurálhatók az Automation-fiókhoz.  
   
   ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> Az **Előfizetési rendszergazdák** már létezik alapértelmezett felhasználóként. Az előfizetési rendszergazdák Active Directory-csoportja tartalmazza az Azure-előfizetés szolgáltatás-rendszergazdáit és társrendszergazdáit. A szolgáltatás-rendszergazda az Azure-előfizetés és erőforrásainak tulajdonosa, és örökli az Automation-fiókok tulajdonosi szerepkörét is. Ez azt jelenti, hogy a **szolgáltatás-rendszergazdák és a társrendszergazdák** számára **Örökölt** a hozzáférés egy előfizetéshez, az összes többi felhasználó számára pedig **Hozzárendelt**. Kattintson az **Előfizetési rendszergazdák** csoportjára az engedélyeik részleteinek megtekintéséhez.  
> 
> 

### Új felhasználó hozzáadása és szerepkör hozzárendelése
1. A Felhasználók panelen a **Hozzáadás** gombra kattintva nyissa meg a **Hozzáférés felvétele panelt**, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, majd hozzájuk rendelhet egy szerepkört.  
   
   ![Felhasználó hozzáadása](media/automation-role-based-access-control/automation-02-add-user.png)  
2. Válasszon egy szerepkört az elérhető szerepkörök listájáról. Az **Olvasó** szerepkört választjuk ki, de választhatja az Automation-fiók által támogatott bármelyik elérhető szerepkört, illetve bármely egyéni szerepkört, amelyet korábban definiált.  
   
   ![Szerepkör kiválasztása](media/automation-role-based-access-control/automation-03-select-role.png)  
3. Kattintson a **Felhasználók hozzáadása** lehetőségre a **Felhasználók hozzáadása** panel megnyitásához. Ha hozzáadott felhasználókat, csoportokat vagy alkalmazásokat az előfizetés kezeléséhez, akkor azok a felhasználók megjelennek a listában, és kiválaszthatja őket hozzáférés hozzáadására. Ha nincs felhasználó a listán, vagy ha a hozzáadni kívánt felhasználó nem szerepel a listán, kattintson a **meghívás** lehetőségre a **Vendég meghívása** panel megnyitásához, ahol meghívhat egy érvényes Microsoft-fiókhoz tartozó e-mail-címmel (Outlook.com, OneDrive vagy Xbox Live Id) rendelkező felhasználót Ha beírta a felhasználó e-mail-címét, kattintson a **Kiválasztás** gombra a felhasználó hozzáadásához, majd kattintson az **OK** gombra. 
   
   ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   Most meg kell jelennie a **Felhasználók** panelhez hozzáadott felhasználónak, mégpedig a hozzá rendelt **Olvasó** szerepkörrel.  
   
   ![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   A felhasználóhoz a **Szerepkörök** panelről is hozzárendelhet szerepkört. 
4. A **Szerepkörök panel** megnyitásához kattintson a Felhasználók panelről a **Szerepkörök** elemre. Erről a panelről megtekintheti a szerepkör nevét, és az adott szerepkörhöz rendelt felhasználók és csoportok számát.
   
    ![Szerepkör hozzárendelése a Felhasználók panelről](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > A szerepköralapú vezérlés csak az Automation-fiók szintjén állítható be, az Automation-fiók alatti erőforrásoknál nem.
   > 
   > 
   
    Hozzárendelhet egynél több szerepkört egy felhasználóhoz, csoporthoz vagy alkalmazáshoz. Ha például hozzáadjuk az **Automation operátor** szerepkört az **Olvasó** szerepkör mellett a felhasználóhoz, akkor megtekinthetik az összes Automation erőforrást, valamint végrehajthatják a forgatókönyv feladatokat. A legördülő listát kibontva megtekintheti a felhasználóhoz hozzárendelt szerepkörök listáját.  
   
    ![Több szerepkör megtekintése](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### Felhasználó eltávolítása
Eltávolíthatja az Automation-fiókot nem kezelő, illetve már nem a szervezetnek dolgozó felhasználó engedélyeit. Alább láthatók egy felhasználó eltávolításának lépései: 

1. A **Felhasználók** panelen válassza ki az eltávolítani kívánt szerepkört.
2. A hozzárendelés részleteit megjelenítő panelen kattintson az **Eltávolítás** gombra.
3. Az **Igen** gombra kattintva erősítse meg az eltávolítást. 
   
   ![Felhasználók eltávolítása](media/automation-role-based-access-control/automation-08-remove-users.png)  

## Szerepkörrel ellátott felhasználó
Ha egy szerepkörhöz rendelt felhasználó bejelentkezik az Automation-fiókjába, a tulajdonos fiókja immár megjelenik az **Alapértelmezett könyvtárak** listájában. Ha meg szeretnék tekinteni az Automation-fiókot, amelyhez hozzá lettek adva, az alapértelmezett könyvtárat át kell váltani a tulajdonos alapértelmezett könyvtárára.  

![Alapértelmezett könyvtár](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### Automation-operátori szerepkör felhasználói élménye
Ha egy Automation-operátori szerepkörrel felruházott felhasználó megtekinti a számára kijelölt Automation-fiókot, csak az Automation-fiókban létrehozott forgatókönyvek, forgatókönyv-feladatok és -ütemezések listáját látja, de a definíciójukat nem. Elindíthatják, leállíthatják, felfüggeszthetik, folytathatják és ütemezhetik a forgatókönyv-feladatokat. A felhasználó nem fog hozzáférni az Automation-erőforrásokhoz, például konfigurációkhoz, hibrid munkacsoportokhoz vagy DSC-csomópontokhoz.  

![Nincs hozzáférése az erőforrásokhoz](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Ha a felhasználó rákattint a forgatókönyvre, a forrás megtekintésére vagy a forgatókönyv szerkesztésére vonatkozó parancsok nem érhetők el, mert az Automation-operátori szerepkör nem engedi a hozzáférést.  

![Nincs engedélye a forgatókönyv szerkesztésére](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

A felhasználó jogosult lesz ütemezések megtekintésére és létrehozására, de nem kap jogosultságot más adategységtípusokhoz.  

![Nincs hozzáférése az adategységekhez](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Ennek a felhasználónak nincs jogosultsága megtekinteni a forgatókönyvekhez társított webhookokat sem

![Nincs hozzáférése a webhookokhoz](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## Az Automation-fiókhoz tartozó RBAC konfigurálása az Azure PowerShellel
A szerepköralapú hozzáférés konfigurálható egy Automation-fiókra az alábbi [Azure PowerShell-parancsmagokkal](../active-directory/role-based-access-control-manage-access-powershell.md).

• A [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) felsorolja az Azure Active Directoryban elérhető összes RBAC-szerepkört. Ezt a parancsot használhatja a **Name** (Név) tulajdonsággal, ha fel szeretné sorolni az összes műveletet, amelyet egy adott szerepkörrel lehet elvégezni.  
    **Példa:**  
    ![Szerepkör-definíció lekérése](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• A [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) felsorolja a megadott tartomány Azure AD RBAC szerepkör-hozzárendeléseit. Paraméterek nélkül ez a parancs az előfizetéshez tartozó összes szerepkör-hozzárendelést visszaadja. Az **ExpandPrincipalGroups** paraméter segítségével felsorolhatja egy adott felhasználó vagy a felhasználó csoportjának összes hozzáférés-hozzárendelését.  
    **Példa:** Az alábbi paranccsal felsorolhatja az összes felhasználót és azok szerepkörét egy Automation-fiókon belül.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Szerepkör-kijelölés lekérése](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• A [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) segítségével felhasználóknak, csoportoknak és alkalmazásoknak biztosíthat hozzáférést egy adott hatókörhöz.  
    **Példa:** Használja az alábbi parancsot, hogy hozzárendelje az „Automation-operátor” szerepkört egy, az Automation-fiók hatókörébe eső felhasználóhoz.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Új szerepkör hozzárendelése](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• A [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) segítségével távolítsa el a megadott felhasználó, csoport vagy alkalmazás hozzáférését egy adott hatókörből.  
    **Példa:** Használja az alábbi parancsot a felhasználó „Automation-operátor” szerepkörből történő eltávolításához az Automation-fiók hatókörében.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

A fenti példákban cserélje ki a **bejelentkezési azonosítót**, az **előfizetés azonosítóját**, az **erőforráscsoport nevét** és az **Automation-fiók nevét** a saját fiókja adataira. Ha a rendszer rákérdez, hogy tényleg szeretné-e törölni a szerepkör-hozzárendelést, kattintson az **Igen** gombra.   

## Következő lépések
* Az RBAC for Azure Automation konfigurálásának különböző módjaira vonatkozó további információért lásd [az RBAC Azure PowerShellel folytatott kezelésével](../active-directory/role-based-access-control-manage-access-powershell.md) foglalkozó témakört.
* További információk a forgatókönyvek elindításának különböző módjairól: [Forgatókönyv elindítása](automation-starting-a-runbook.md).
* További információk a különböző forgatókönyv-típusokról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)

<!--HONumber=Sep16_HO4-->


