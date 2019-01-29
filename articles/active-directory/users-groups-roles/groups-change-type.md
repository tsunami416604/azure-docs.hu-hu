---
title: Statikus csoport tagsági típusának módosítása az Azure Active Directory dinamikus |} A Microsoft Docs
description: Hogyan lehet automatikusan feltölti a csoportokat és a egy odkaz nA pravidlo tagsági szabályok létrehozásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 11175a091ad610470632b52b46270ed27c76e452
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150631"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Az Azure Active Directory dinamikus statikus csoport tagságának módosítása

Módosíthatja a csoporttagságot statikusról dinamikus (vagy fordítva) az Azure Active Directoryban (Azure AD). Az Azure AD tartja a csoport neve és azonosítója a rendszer, így a csoport összes meglévő hivatkozások még érvényesek. Inkább hozzon létre egy új csoportot, ha szeretné frissíteni szeretné ezeket a hivatkozásokat. Dinamikus csoporttagság kiküszöböli a felhasználók felügyeleti többletterhelési hozzáadása és eltávolítása. Ez a cikk bemutatja, hogyan lehet statikusról átalakítása a meglévő csoportokat vagy az Azure AD felügyeleti központban, vagy a PowerShell-parancsmagok segítségével dinamikus tagságot.

> [!WARNING]
> Egy meglévő statikus csoportot egy dinamikus csoport módosításakor összes meglévő tag törlődik a csoportból, és ezután a tagsági szabály feldolgozása új tagokat adhat. Ha alkalmazások vagy erőforrásokhoz való hozzáférés szabályozásához a csoportot használ, vegye figyelembe, hogy az eredeti tagok előfordulhat, hogy elvesznek a hozzáférés mindaddig, amíg a tagsági szabály feldolgoz.
>
> Azt javasoljuk, hogy előre az új tagsági szabályt, győződjön meg arról, hogy az új a csoporttagságot a várt módon vizsgálati.

## <a name="change-the-membership-type-for-a-group"></a>Módosítsa egy csoport tagsági típusa

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda vagy egy felhasználói fiók rendszergazdája a bérlőben.
2. Válassza ki **csoportok**.
3. Az a **összes csoport** list, nyissa meg a csoportot, amelyet módosítani szeretne.
4. Válassza ki **tulajdonságok**.
5. A a **tulajdonságok** csoportban válassza a lap egy **tagságtípusának** hozzárendelt (statikus), a dinamikus felhasználó vagy a dinamikus eszköz, a kívánt tagsági típusától függően. A dinamikus tagságot a szabály a jelentéskészítő használatával jelölje be egy egyszerű szabályt, vagy írjon egy tagsági szabályt, saját magának. 

A következő lépéseket kell egy példa egy csoport módosítása statikusról dinamikus tagsági a felhasználók egy csoportjánál.

1. Az a **tulajdonságok** a kiválasztott csoportot, válassza az oldal egy **tagság típusa** , **dinamikus felhasználói**, majd válassza az Igen lehetőséget a módosítások elmagyarázza a csoporthoz párbeszédpanelen tagság a folytatáshoz. 
  
   ![Válassza ki a felhasználó dinamikus tagsági típusa](./media/groups-change-type/select-group-to-convert.png)
  
2. Válassza ki **dinamikus lekérdezés hozzáadása**, majd adja meg a szabály.
  
   ![Adja meg a szabály](./media/groups-change-type/enter-rule.png)
  
3. A szabály létrehozása után válassza ki a **lekérdezés hozzáadása** az oldal alján.
4. Válassza ki **mentése** a a **tulajdonságok** lap a csoport a módosítások mentéséhez. A **tagságtípusának** a csoport azonnal frissül a listájából.

> [!TIP]
> Csoport konvertálása sikertelen lehet, ha a megadott tagsági szabály helytelen volt. A benne található annak magyarázatát, miért érdemes a szabályt nem fogadja el a rendszer a portál jobb felső sarkában megjelenik egy értesítés. Olvassa el, hogy alaposan megismerheti, hogyan módosíthatja a szabályt, hogy érvényes. A szabály szintaxisra vonatkozó példákat és a támogatott tulajdonságok, operátorok és a tagsági szabály értékeinek teljes listáját lásd: [dinamikus tagsági szabályok az Azure Active Directoryban csoportok](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Tagság típusa (PowerShell) csoport módosítása

> [!NOTE]
> A parancsmagok használatával kell a dinamikus csoport tulajdonságainak módosítása **előzetes verzióját** [az Azure AD PowerShell 2-es verziójú](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Az előzetes verzióját, a telepítése a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureADPreview).

Íme egy példa, amely egy meglévő csoportot a tagsági felügyeleti kapcsoló funkciók. Ebben a példában van ügyelni arra, hogy megfelelően GroupTypes tulajdonság módosítására, és megőrizheti azokat az értékeket, a dinamikus tagsági kapcsolódnak.

```
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
Egy csoport statikus tétele:

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

A csoport dinamikus tétele:

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>További lépések

E cikkekben további információk az Azure Active Directory-csoportokon.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
