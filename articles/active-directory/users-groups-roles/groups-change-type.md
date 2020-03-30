---
title: Statikus csoporttagság módosítása dinamikusra - Azure AD | Microsoft dokumentumok
description: A csoportok automatikus feltöltéséhez és a szabályhivatkozáshoz létrejön tagsági szabályok.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027314"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Statikus csoporttagság módosítása dinamikusra az Azure Active Directoryban

Módosíthatja a csoport tagságát statikusról dinamikusra (vagy fordítva) az Azure Active Directoryban (Azure AD). Az Azure AD megtartja ugyanazt a csoportnevet és azonosítót a rendszerben, így a csoportra mutató összes meglévő hivatkozás továbbra is érvényes. Ha ehelyett új csoportot hoz létre, frissítenie kell ezeket a hivatkozásokat. A dinamikus csoporttagság kiküszöböli a felhasználók hozzáadásával és eltávolításával kapcsolatos felügyeleti többletterhelést. Ez a cikk bemutatja, hogyan konvertálhatja a meglévő csoportokstatikus dinamikus tagság az Azure AD Felügyeleti központ vagy a PowerShell-parancsmagok használatával.

> [!WARNING]
> Ha egy meglévő statikus csoportot dinamikus csoportra módosít, a rendszer eltávolítja az összes meglévő tagot a csoportból, majd a tagsági szabályt feldolgozza új tagok hozzáadására. Ha a csoportot az alkalmazásokhoz vagy erőforrásokhoz való hozzáférés szabályozására használja, vegye figyelembe, hogy az eredeti tagok elveszíthetik a hozzáférést, amíg a tagsági szabály teljesen fel nem dolgozik.
>
> Javasoljuk, hogy előzetesen tesztelje az új tagsági szabályt, és győződjön meg arról, hogy a csoport új tagsága a várt módon történik.

## <a name="change-the-membership-type-for-a-group"></a>Csoport tagsági típusának módosítása

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy globális rendszergazdai vagy egy felhasználói rendszergazda a bérlőben.
2. Válassza a **Csoportok lehetőséget.**
3. A **Minden csoport** listában nyissa meg a módosítani kívánt csoportot.
4. Válassza **a Tulajdonságok lehetőséget.**
5. A **csoport Tulajdonságok** lapján a kívánt tagsági típustól függően válassza ki a Hozzárendelt (statikus), a Dinamikus felhasználó vagy a Dinamikus eszköz **tagsági típusát.** Dinamikus tagság esetén a szabályszerkesztővel megadhatja egy egyszerű szabály beállításait, vagy saját maga írhat tagsági szabályt. 

A következő lépések egy példa arra, hogy egy csoportot statikusról dinamikus tagságra módosítsunk egy felhasználói csoport számára.

1. A kijelölt csoport **Tulajdonságok** lapján jelölje ki a **dinamikus felhasználó** **tagsági típusát,** majd kattintson az Igen gombra a csoporttagság további változásait ismerő párbeszédpanelen. 
  
   ![dinamikus felhasználó tagsági típusának kiválasztása](./media/groups-change-type/select-group-to-convert.png)
  
2. Válassza **a Dinamikus lekérdezés hozzáadása**lehetőséget, majd adja meg a szabályt.
  
   ![adja meg a dinamikus csoport szabályát](./media/groups-change-type/enter-rule.png)
  
3. A szabály létrehozása után válassza a **Lekérdezés hozzáadása** lehetőséget a lap alján.
4. A módosítások mentéséhez válassza a Csoport **Tulajdonságok** lapján a **Mentés** gombot. A csoport **tagsági típusa** azonnal frissül a csoportlistában.

> [!TIP]
> A csoportkonverzió sikertelen lehet, ha a megadott tagsági szabály helytelen. A portál jobb felső sarkában megjelenik egy értesítés arról, hogy a rendszer magyarázatot ad arra, hogy a rendszer miért nem tudja elfogadni a szabályt. Olvassa el figyelmesen, hogy megértse, hogyan módosíthatja a szabályt, hogy érvényes legyen. Példák a szabály szintaxisára és a támogatott tulajdonságok, operátorok és egy tagsági szabály értékeinek teljes listájára, lásd: [Dinamikus tagsági szabályok csoportokhoz az Azure Active Directoryban.](groups-dynamic-membership.md)

## <a name="change-membership-type-for-a-group-powershell"></a>Csoport tagságtípusának módosítása (PowerShell)

> [!NOTE]
> A dinamikus csoport tulajdonságainak módosításához az [Azure AD PowerShell 2-es verziójának](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)előzetes verziójából származó parancsmagokat kell **használnia.** Az előzetes verziót a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureADPreview)telepítheti.

Íme egy példa azokkal a függvényekből, amelyek egy meglévő csoport tagságkezelését kapcsolják be. Ebben a példában a rendszer ügyel arra, hogy megfelelően manipulálja a GroupTypes tulajdonságot, és megőrizze a dinamikus tagsághoz nem kapcsolódó értékeket.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Csoport statikussá adása:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Csoport dinamikussá adása:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak az Azure Active Directory csoportokról.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
