<properties 
   pageTitle="Szerepköralapú hozzáférés-vezérlés az Azure Automationben | Microsoft Azure"
   description="A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Ez a cikk ismerteti az RBAC beállítását az Azure Automationben."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn"
   keywords="automation rbac, role based access control, azure rbac" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte;sngun"/>

# Szerepköralapú hozzáférés-vezérlés az Azure Automationben

## Szerepköralapú hozzáférés-vezérlés

A Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz. Az [RBAC](../active-directory/role-based-access-control-configure.md) használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amely a felhasználóknak a feladataik elvégzéséhez szükséges. A szerepköralapú hozzáférés az Azure portál, az Azure parancssori segédeszközök vagy az Azure felügyeleti API-k segítségével adható a felhasználóknak.

## RBAC Automation-fiókokban

Az Azure Automationben a hozzáférés biztosításához a megfelelő RBAC-szerepkörök rendelhetők az Automation-fiók tartományában lévő felhasználókhoz, csoportokhoz és alkalmazásokhoz. Alább láthatók az Automation-fiók által támogatott beépített szerepkörök:  

|**Szerepkör** | **Leírás** |
|:--- |:---|
| Tulajdonos | A tulajdonosi szerepkör segítségével hozzáférhet minden erőforráshoz és művelethez egy Automation-fiókon belül, beleértve azt is, hogy hozzáférést biztosíthat más felhasználók, csoportok és alkalmazások számára az Automation-fiók kezeléséhez. |
| Közreműködő | A közreműködő szerepkör segítségével kezelhet mindent, csak más felhasználók Automation-fiókokra vonatkozó hozzáférési jogosultságait nem módosíthatja. |
| Olvasó | Az olvasó szerep lehetővé teszi az összes erőforrás megtekintését egy Automation-fiókban, de módosítás nem hajtható vele végre. |
| Automation-operátor | Az Automation-operátor szerepkör lehetővé teszi olyan operatív feladatok végrehajtását, mint az indítás, a leállítás, a felfüggesztés, a folytatás és az ütemezés. Ez a szerepkör akkor hasznos, ha meg szeretné akadályozni, hogy az Automation-fiók erőforrásokat, például a hitelesítő adategységeket és a forgatókönyveket megtekintsék vagy módosítsák, de továbbra is engedélyezi a szervezet tagjai számára a forgatókönyvek végrehajtását. Az [Automation-operátori műveletek](../active-directory/role-based-access-built-in-roles.md#automation-operator) felsorolja az Automation-operátori szerepkör által támogatott műveleteket az Automation-fiókon és az erőforrásokon. |
| Felhasználói hozzáférés rendszergazdája | A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy kezelje a felhasználói hozzáférést az Azure Automation-fiókokhoz. |

Ebben a cikkben bemutatjuk az RBAC beállítását az Azure Automationben. 

## Az Automation-fiókhoz tartozó RBAC konfigurálása az Azure portál segítségével

1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com/), és nyissa meg az Automation-fiókját az Automation-fiókok panelről.  

2.  Kattintson a jobb felső sarokban található **Hozzáférés** vezérlőre. Ezzel megnyitja a **Felhasználók** panelt, ahol hozzáadhat új felhasználókat, csoportokat vagy alkalmazásokat az Automation-fiók kezeléséhez, és megtekintheti a meglévő szerepköröket, amelyek konfigurálhatók az Automation-fiókhoz.  

    ![Hozzáférés gomb](media/automation-role-based-access-control/automation-01-access-button.png)  

>[AZURE.NOTE]  Az **Előfizetési rendszergazdák** már létezik alapértelmezett felhasználóként. Az előfizetési rendszergazdák Active Directory-csoportja tartalmazza az Azure-előfizetés szolgáltatás-rendszergazdáit és társrendszergazdáit. A szolgáltatás-rendszergazda az Azure-előfizetés és erőforrásainak tulajdonosa, és örökli az Automation-fiókok tulajdonosi szerepkörét is. Ez azt jelenti, hogy a **szolgáltatás-rendszergazdák és a társrendszergazdák** számára **Örökölt** a hozzáférés egy előfizetéshez, az összes többi felhasználó számára pedig **Hozzárendelt**. Kattintson az **Előfizetési rendszergazdák** csoportjára az engedélyeik részleteinek megtekintéséhez.  

### Új felhasználó hozzáadása és szerepkör hozzárendelése

1.  A Felhasználók panelen a **Hozzáadás** gombra kattintva nyissa meg a **Hozzáférés felvétele panelt**, ahol hozzáadhat egy felhasználót, csoportot vagy alkalmazást, majd hozzájuk rendelhet egy szerepkört.  

    ![Felhasználó hozzáadása](media/automation-role-based-access-control/automation-02-add-user.png)  

2.  Válasszon egy szerepkört az elérhető szerepkörök listájáról. Az **Olvasó** szerepkört választjuk ki, de választhatja az Automation-fiók által támogatott bármelyik elérhető szerepkört, illetve bármely egyéni szerepkört, amelyet korábban definiált.  

    ![Szerepkör kiválasztása](media/automation-role-based-access-control/automation-03-select-role.png)  

3.  Kattintson a **Felhasználók hozzáadása** lehetőségre a **Felhasználók hozzáadása** panel megnyitásához. Ha hozzáadott felhasználókat, csoportokat vagy alkalmazásokat az előfizetés kezeléséhez, akkor azok a felhasználók megjelennek a listában, és kiválaszthatja őket hozzáférés hozzáadására. Ha nincs felhasználó a listán, vagy ha a hozzáadni kívánt felhasználó nem szerepel a listán, kattintson a **meghívás** lehetőségre a **Vendég meghívása** panel megnyitásához, ahol meghívhat egy érvényes Microsoft-fiókhoz tartozó e-mail-címmel (Outlook.com, OneDrive vagy Xbox Live Id) rendelkező felhasználót Ha beírta a felhasználó e-mail-címét, kattintson a **Kiválasztás** gombra a felhasználó hozzáadásához, majd kattintson az **OK** gombra. 

    ![Felhasználók hozzáadása](media/automation-role-based-access-control/automation-04-add-users.png)  
 
Most meg kell jelennie a **Felhasználók** panelhez hozzáadott felhasználónak, mégpedig a hozzá rendelt **Olvasó** szerepkörrel.  

![Felhasználók listázása](media/automation-role-based-access-control/automation-05-list-users.png)  

A felhasználóhoz a **Szerepkörök** panelről is hozzárendelhet szerepkört. A **Szerepkörök panel** megnyitásához kattintson a Felhasználók panelről a **Szerepkörök** elemre. Erről a panelről megtekintheti a szerepkör nevét, és az adott szerepkörhöz rendelt felhasználók és csoportok számát.

![Szerepkör hozzárendelése a Felhasználók panelről](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
>[AZURE.NOTE] A szerepköralapú vezérlés csak az Automation-fiók szintjén állítható be, az Automation-fiók alatti erőforrásoknál nem.

Hozzárendelhet egynél több szerepkört egy felhasználóhoz, csoporthoz vagy alkalmazáshoz. Ha például hozzáadjuk az **Automation operátor** szerepkört az **Olvasó** szerepkör mellett a felhasználóhoz, akkor megtekinthetik az összes Automation erőforrást, valamint végrehajthatják a forgatókönyv feladatokat. A legördülő listát kibontva megtekintheti a felhasználóhoz hozzárendelt szerepkörök listáját.  

![Több szerepkör megtekintése](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  
 
### Felhasználó eltávolítása

Eltávolíthatja az Automation-fiókot nem kezelő, illetve már nem a szervezetnek dolgozó felhasználó engedélyeit. Alább láthatók egy felhasználó eltávolításának lépései: 

1.  A **Felhasználók** panelen válassza ki az eltávolítani kívánt szerepkört.

2.  A hozzárendelés részleteit megjelenítő panelen kattintson az **Eltávolítás** gombra.

3.  Az **Igen** gombra kattintva erősítse meg az eltávolítást. 

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

• A [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) felsorolja az Azure Active Directory RBAC-ban elérhető összes szerepkört. Ezt a parancsot használhatja a **Name** tulajdonsággal, ez esetben felsorolja az adott szerepkörrel rendelkező összes felhasználót.  
    **Példa:**  
    ![Szerepkör-definíció lekérése](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• A [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) felsorolja az Azure RBAC szerepkör-hozzárendelést a megadott tartományban. Paraméterek nélkül ez a parancs az előfizetéshez tartozó összes szerepkör-hozzárendelést visszaadja. Az **ExpandPrincipalGroups** paraméter segítségével felsorolhatja egy adott felhasználó vagy a csoportja összes kijelölt hozzáférési engedélyét.  
    **Példa:** Az alábbi paranccsal felsorolhatja az összes felhasználót és azok szerepkörét egy Automation-fiókon belül.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Szerepkör-kijelölés lekérése](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• A [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) segítségével hozzáférés biztosítható felhasználóknak, csoportoknak és alkalmazásoknak egy adott tartományban.  
    **Példa:** Használja az alábbi parancsot egy új „Automation-operátor” szerepkör létrehozásához egy Automation-fiók hatókörében lévő felhasználóhoz.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Új szerepkör hozzárendelése](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• A [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) segítségével távolítsa el a hozzáférést a megadott felhasználóhoz, csoporthoz vagy alkalmazáshoz egy adott hatókörben.
    **Példa:** Használja az alábbi parancsot egy új „Automation-operátor” szerepkör létrehozásához egy Automation-fiók hatókörében lévő felhasználóhoz.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

A fenti parancsmagokban cserélje ki a bejelentkezési nevet, az előfizetés azonosítóját és az Automation-fiók nevét a saját fiókja adataira. Ha a rendszer rákérdez, hogy tényleg szeretné-e törölni a szerepkör-hozzárendelést, kattintson az **Igen** gombra.   


## Következő lépések
-  Az RBAC Azure Automationre való konfigurálásának különböző módjairól további információ: [Az RBAC Azure PowerShellel való kezelése](../active-directory/role-based-access-control-manage-access-powershell.md).
- További információk a forgatókönyvek elindításának különböző módjairól: [Forgatókönyv elindítása](automation-starting-a-runbook.md).
- További információk a különböző típusokról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)




<!--HONumber=jun16_HO2-->


