---
title: Delegálás és szerepkörök a jogosultságkezelésben – Azure AD
description: Megtudhatja, hogy miként delegálhatja a hozzáférés-irányítást az informatikai rendszergazdáktól a részlegvezetőknek és a projektmenedzsereknek, hogy ők maguk is kezelhessék a hozzáférést.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261839"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegálás és szerepkörök az Azure AD jogosultságkezelésében

Alapértelmezés szerint a globális rendszergazdák és a felhasználói rendszergazdák hozhat létre és kezelheti az Azure AD jogosultságkezelés minden aspektusát. Előfordulhat azonban, hogy a szerepkörökben lévő felhasználók nem ismerik az összes olyan helyzetet, amelyben hozzáférési csomagokra van szükség. Általában a megfelelő részlegek, csoportok vagy projektek felhasználói tudják, kivel dolgoznak együtt, milyen erőforrásokat és mennyi ideig használnak. Ahelyett, hogy korlátlan engedélyeket adna a nem rendszergazdáknak, a felhasználóknak a legkevesebb engedélyt biztosíthatja a feladat elvégzéséhez, és elkerülheti az ütköző vagy nem megfelelő hozzáférési jogok létrehozását.

Ez a videó áttekintést nyújt arról, hogyan delegálhatja a hozzáférés-szabályozást a rendszergazda tól a nem rendszergazdáknak.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Példa delegálása

Annak megértéséhez, hogy hogyan delegálhatja a hozzáférés-szabályozást a jogosultságkezelésben, segít egy példát figyelembe venni. Tegyük fel, hogy a szervezet rendelkezik a következő rendszergazda és a vezetők.

![Delegálás az informatikai rendszergazdától a vezetőknek](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Az informatikai rendszergazdaként Hana minden részlegen – a Mamta in Marketingben, a Mark in Finance és a Joe in Legal – kapcsolattartókkal rendelkezik, akik felelősek az osztály erőforrásaiért és az üzleti legkritikusabb tartalmakért.

A jogosultságkezeléssel hozzáférést delegálhat ezeknek a nem rendszergazdáknak, mert ők azok, akik tudják, hogy mely felhasználóknak van szükségük hozzáférésre, mennyi ideig és milyen erőforrásokhoz. Ez biztosítja, hogy a megfelelő emberek kezelik a hozzáférést a részlegeikhez.

Itt van egy módja annak, hogy Hana is delegálhat hozzáférést irányítás a marketing, pénzügyi és jogi osztályok.

1. Hana létrehoz egy új Azure AD biztonsági csoportot, és hozzáadja mamta, Mark és Joe a csoport tagjaként.

1. Hana hozzáadja ezt a csoportot a katalóguslétrehozók szerepkörhöz.

    Mamta, Mark és Joe most már katalógusokat hozhat létre a részlegeikszámára, hozzáadhat olyan erőforrásokat, amelyekre a részlegeiknek szükségük van, és további delegálást tehet a katalóguson belül.

    Ne feledje, hogy Mamta, Mark és Joe nem láthatja egymás katalógusait.

1. A Mamta **Marketing** létrehoz egy marketingkatalógust, amely az erőforrások tárolója.

1. Mamta hozzáadja a marketingosztálya által birtokolt erőforrásokat ehhez a katalógushoz.

1. A Mamta további személyeket is hozzáadhat a részlegéből katalógustulajdonosként ehhez a katalógushoz. Ez segít megosztani a katalóguskezelési feladatokat.

1. A Mamta tovább delegálhatja a Marketing katalógusban lévő hozzáférési csomagok létrehozását és kezelését a marketingosztály projektvezetőinek. Ezt úgy teheti meg, hogy hozzárendeli őket a hozzáférési csomagkezelő szerepkörhöz. A hozzáférési csomagkezelő hozzáférési csomagokat hozhat létre és kezelhet. 

Az alábbi ábra a marketing-, pénzügyi és jogi részlegek erőforrásait beamelyekből álló katalógusokat mutat be. Ezekkel a katalógusokkal a projektmenedzserek hozzáférési csomagokat hozhatnak létre a csapataik vagy projektjeik számára.

![Példa jogosultságkezelési meghatalmazottra](./media/entitlement-management-delegate/elm-delegate.png)

A delegálás t követően a marketingosztály az alábbi táblázathoz hasonló szerepkörökkel rendelkezhet.

| Felhasználó | Feladat szerepkör | Azure AD szerepkör | Jogosultságkezelési szerepkör |
| --- | --- | --- | --- |
| Hana | INFORMATIKAI rendszergazda | Globális rendszergazda vagy felhasználó |  |
| Mamta között | Marketing menedzser | Felhasználó | A katalógus létrehozója és a katalógus tulajdonosa |
| Bob | Marketing vezető | Felhasználó | Katalógus tulajdonosa |
| Jessica | Marketing projektmenedzser | Felhasználó | Hozzáférés csomagkezelő |

## <a name="entitlement-management-roles"></a>Jogosultságkezelési szerepkörök

A jogosultságkezelés a következő, a jogosultságkezelésre jellemző szerepkörökkel rendelkezik.

| Jogosultságkezelési szerepkör | Leírás |
| --- | --- |
| Katalógus létrehozója | Katalógusok létrehozása és kezelése. Általában olyan rendszergazda, aki nem globális rendszergazda, vagy erőforrás-gyűjtemény ének tulajdonosa. A katalógust létrehozó személy automatikusan a katalógus első katalógustulajdonosává válik, és további katalógustulajdonosokat vehet fel. A katalógus létrehozója nem kezelheti és nem láthatja azokat a katalógusokat, amelyek nem a saját tulajdonukban vannak, és nem adhatnak hozzá olyan erőforrásokat, amelyek nem a katalógusban vannak. Ha a katalógus készítőjének egy másik katalógust kell kezelnie, vagy olyan erőforrásokat kell hozzáadnia, amelyek nem a tulajdonukban vannak, kérheti, hogy társtulajdonosa legyen a katalógusnak vagy erőforrásnak. |
| Katalógus tulajdonosa | Meglévő katalógusok szerkesztése és kezelése. Általában egy informatikai rendszergazda vagy erőforrás-tulajdonosok, vagy egy felhasználó, aki a katalógus tulajdonosa kijelölt. |
| Hozzáférés csomagkezelő | A katalógusban lévő összes meglévő hozzáférési csomag szerkesztése és kezelése. |

Emellett egy kijelölt jóváhagyó és egy hozzáférési csomag kérelmezője is rendelkezik jogokkal, bár nem szerepkörök.

| Jobb | Leírás |
| --- | --- |
| Approver | Szabályzat engedélyezi a csomagok elérésére vonatkozó kérelmek jóváhagyására vagy elutasítására, bár nem módosíthatják a hozzáférési csomag definícióit. |
| Requestor (Kérelmező) | Egy hozzáférési csomag házirendje engedélyezte a hozzáférési csomag kérésére. |

Az alábbi táblázat azokat a feladatokat sorolja fel, amelyeket a jogosultságkezelési szerepkörök el tudnak végezni.

| Tevékenység | Rendszergazda | Katalógus létrehozója | Katalógus tulajdonosa | Hozzáférés csomagkezelő |
| --- | :---: | :---: | :---: | :---: |
| [Delegálás katalóguskészítőnek](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Csatlakoztatott szervezet hozzáadása](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Új katalógus létrehozása](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Erőforrás hozzáadása katalógushoz](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Katalógustulajdonos hozzáadása](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Katalógus szerkesztése](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Katalógus törlése](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegálás hozzáférési csomagkezelőre](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Hozzáférés-kezelő eltávolítása](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Új hozzáférési csomag létrehozása katalógusban](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Erőforrás-szerepkörök módosítása hozzáférési csomagban](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Házirendek létrehozása és szerkesztése](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Felhasználó közvetlen hozzárendelése hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Annak megtekintése, hogy ki rendelkezik hozzárendeléssel egy hozzáférési csomaghoz](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Hozzáférési csomag kéréseinek megtekintése](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [A kérelem kézbesítési hibáinak megtekintése](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Kérelem újrafeldolgozása](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Függőben lévő kérelem visszavonása](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Hozzáférési csomag elrejtése](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Hozzáférési csomag törlése](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Erőforrások katalógushoz való hozzáadásához szükséges szerepkörök

A globális rendszergazda bármely csoportot (felhőalapú biztonsági csoportokat vagy felhőalapú Office 365-csoportokat), alkalmazást vagy SharePoint Online-webhelyet vehet fel vagy távolíthat el egy katalógusból. A felhasználó rendszergazda bármely csoportot vagy alkalmazást hozzáadhat vagy eltávolíthat a katalógusban.

Ahhoz, hogy egy felhasználó, aki nem globális rendszergazda vagy felhasználó rendszergazda, csoportokat, alkalmazásokat vagy SharePoint Online-webhelyeket adjon hozzá egy katalógushoz, a felhasználónak *rendelkeznie* kell a szükséges Azure AD címtárszerepkörrel és a katalógustulajdonosi jogosultságkezelési szerepkörrel. Az alábbi táblázat felsorolja azokat a szerepkörkombinációkat, amelyek az erőforrások katalógushoz való hozzáadásához szükségesek. Az erőforrások katalógusból való eltávolításához ugyanazokat a szerepköröket kell elválasztania.

| Azure AD címtárszerepkör | Jogosultságkezelési szerepkör | Biztonsági csoport hozzáadása | Az Office 365-csoport hozzáadása | Hozzáadhat alkalmazást | SharePoint Online-webhely hozzáadása |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globális rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [A Teams szolgáltatás rendszergazdája](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  |  |
| [SharePoint-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Alkalmazás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| [Felhőalkalmazás-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md) | Katalógus tulajdonosa |  |  | :heavy_check_mark: |  |
| Felhasználó | Katalógus tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha a csoport tulajdonosa | Csak akkor, ha az alkalmazás tulajdonosa |  |

A feladat legkevésbé kiemelt szerepkörének meghatározásához rendszergazdai feladatok alapján is hivatkozhat [rendszergazdai feladatokra az Azure Active Directoryban.](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)

## <a name="next-steps"></a>További lépések

- [Hozzáférés-irányítás delegálása a katalóguskészítőknek](entitlement-management-delegate-catalog.md)
- [Erőforrások katalógusának létrehozása és kezelése](entitlement-management-catalog-create.md)
