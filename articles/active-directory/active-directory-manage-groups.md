---
title: "Csoportok használata az Azure Active Directoryban erőforrásokhoz való hozzáférés kezelése |} Microsoft Docs"
description: "Hogyan csoportok használata az Azure Active Directoryban felügyelheti a felhasználók hozzáférését a helyszíni és felhőalapú alkalmazásokhoz és erőforrásokhoz."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 714120d0-cdf9-465d-afee-39bef591c6b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 3a4764ddf2dea68bf25bd46b9691429ad3341678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Az Azure Active Directoryval erőforrásokhoz való hozzáférés kezelése
Azure Active Directory (Azure AD) egy átfogó identitás- és hozzáférés felügyeleti megoldás, amely egy robusztus több funkciót biztosít hozzáférést a helyszíni és felhőalapú alkalmazásokhoz és erőforrásokhoz, beleértve az Office 365-höz hasonló Microsoft online szolgáltatások kezelése és egy a globális Microsoft SaaS-alkalmazásokhoz. Ez a cikk áttekintést nyújt, de ha Elindítja most csoportosítja az Azure AD, kövesse az utasításokat a [biztonsági csoportok kezelése az Azure AD](active-directory-accessmanagement-manage-groups.md). Ha meg szeretné tekinteni, hogyan használhatja PowerShell kezelése az Azure Active Directoryban további a [eszközcsoport-kezelés Azure Active Directory-parancsmagjai](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Azure Active Directory használatához Azure-fiókot kell létrehoznia. Ha nincs fiókja, akkor [regisztráljon egy ingyenes Azure-fiók](https://azure.microsoft.com/pricing/free-trial/).
>
>

Az Azure AD belül fő szolgáltatásainak egyik képes kezelni az erőforrásokhoz való hozzáférést. Ezeket az erőforrásokat a könyvtár objektumok keresztüli szerepkörök a könyvtár, vagy az, hogy a címtárban, például az SaaS-alkalmazásokhoz, Azure-szolgáltatásokat, és a SharePoint-webhelyek vagy a helyszíni erőforrások a külső erőforrások kezeléséhez szükséges jogokat gazdabuszadaptereken része lehet. A felhasználó hozzáférési jogosultsága ahhoz, hogy egy erőforrás rendelhető négy módja van:

1. Közvetlen hozzárendelés

    Felhasználók rendelhet közvetlenül egy erőforrás tulajdonosa erőforrás.
2. Csoporttagság

    Egy csoport rendelhet egy erőforrás erőforrás tulajdonosa, és ezzel a módszerrel, biztosítása, hogy az erőforrás elérésére csoport tagjai. A csoport tagsága felügyelhető legyen a csoport tulajdonosa. Az erőforrás tulajdonosa hatékony, a felhasználók hozzárendelése az erőforrást a csoport tulajdonosa engedélyt ad.
3. Szabály alapú

    Az erőforrás tulajdonosa segítségével megadhat egy szabályt mely felhasználók hozzá kell rendelni hozzáférést egy erőforráshoz. A szabály eredményeit függ az adott felhasználókra vonatkozóan, hogy a szabály és azok értékei használt attribútum, és ezzel az erőforrás tulajdonosa hatékonyan ad a jogot, hogy az attribútumok, amelyek szerepelnek az erőforrás a mérvadó forráshoz való hozzáférés kezelése a szabály. Az erőforrás tulajdonosa továbbra is maga a szabály kezeli, és meghatározza, hogy mely attribútumok és értékeket adja meg az erőforrás elérésére.
4. Külső hatóság

    Az erőforráshoz való hozzáférés származtatott külső forrásból; Ha például egy csoportot, amely egy mérvadó forrás, például egy helyszíni directory vagy egy SaaS-alkalmazást például a WorkDay szinkronizálva. Az erőforrás tulajdonosa hozzárendeli a csoportot, így biztosítva az erőforrás elérésére, és a külső forrás kezeli a csoport tagjai.

   ![Access management diagram áttekintése](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Bemutató videó ismerteti a hozzáférés-kezelés
Egy rövid videót a magyarázó figyelemmel követheti:

**Az Azure AD: Dinamikus csoporttagság csoportok bemutatása**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Hogyan hozzáférés-kezelés az Azure Active Directory munkahelyi?
Az Azure AD középpontjába, hozzáférés-kezelési megoldás a biztonsági csoport. Biztonsági csoport segítségével erőforrásokhoz való hozzáférés kezelése a jól ismert összeállítást, ami lehetővé teszi a felhasználók adott csoportja számára erőforráshoz való hozzáférés biztosításához rugalmas és könnyen érthető úgy. Az erőforrás tulajdonosa (vagy a rendszergazda a címtár) rendelhet egy csoportot adjon meg egy bizonyos hozzáférési jogosultsággal a saját erőforrásokat. A csoportnak a tagjai a rendszer a hozzáférést, és az erőforrás tulajdonosa delegálhatja a jogot másnak, például a részleg vezetője vagy a segélyszolgálat rendszergazdák csoport tagjai listájának kezelése.

![Az Azure Active Directory elérés felügyeleti diagramja](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

A csoport tulajdonosa is elérhetővé teheti, hogy a csoport a önkiszolgáló kéréseket. Ennek során a felhasználó is keresse meg és található csoport és egy kérelem való csatlakozáshoz, hatékony keresést a kezelt erőforrások hozzáférése a csoport révén. A csoport tulajdonosa a csoport állíthat be, hogy a csatlakozási kérelmek automatikusan jóváhagyja-e, vagy a csoport tulajdonosa jóváhagyás szükséges. Amikor egy felhasználó egy kérést egy csoporthoz való csatlakoztatáshoz, az illesztési kérelmet továbbítja a csoport tulajdonosainak. Ha a kérelmet jóváhagyja a tulajdonosok közül, a kérelmező felhasználó értesítést kap, és a felhasználó tagja a csoportnak. A tulajdonosok egyik visszautasítja a kérelmet, ha a kérelmező felhasználó értesítést kap de nincs csatlakoztatva a csoporthoz.

## <a name="getting-started-with-access-management"></a>Ismerkedés a hozzáférés-kezelés
Próbálja ki. Használja ki az Azure AD-csoportok elvégezhető alapvető feladatokat. Ezek a képességek segítségével speciális hozzáférést adhat a különböző csoportok tagjai a szervezet különböző erőforrások. Alapszintű első lépések listáját alább láthatók.

* [A csoport dinamikus csoporttagságok konfigurálása egyszerű szabályt hoz létre](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)
* [SaaS-alkalmazásokhoz való hozzáférés kezelése csoport segítségével](active-directory-accessmanagement-group-saasapps.md)
* [Csoport elérhetővé tétele a felhasználók önkiszolgáló](active-directory-accessmanagement-self-service-group-management.md)
* [Egy helyi csoport az Azure-bA az Azure AD Connect használatával szinkronizálása](active-directory-aadconnect.md)
* [Egy csoport tulajdonosainak kezelése](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Következő lépések
Most, hogy a kezelési alapjait rendelkezik megértését, az alábbiakban néhány további speciális funkciókat az Azure Active Directoryban érhető el az alkalmazások és az erőforrásokhoz való hozzáférés.

* [Attribútumok használata speciális szabályok létrehozásához](active-directory-accessmanagement-groups-with-advanced-rules.md)
* [Biztonsági csoportok kezelése az Azure ad-ben](active-directory-accessmanagement-manage-groups.md)
* [Dedikált csoportok beállítása az Azure ad-ben](active-directory-accessmanagement-dedicated-groups.md)
* [A csoportok Graph API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](active-directory-accessmanagement-groups-settings-cmdlets.md)
