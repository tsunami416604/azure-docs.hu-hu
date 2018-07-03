---
title: Az Azure-ban a különféle szerepkörök ismertetése |} A Microsoft Docs
description: Ismerteti a különféle szerepkörök az Azure-ban – klasszikus előfizetés rendszergazdai szerepköröket, az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szerepkört és az Azure Active Directory (Azure AD) rendszergazdai szerepkörök
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 6285614b42b469feeb63b880976e79828838e675
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346972"
---
# <a name="understand-the-different-roles-in-azure"></a>Az Azure-ban a különféle szerepkörök ismertetése

Ha most ismerkedik az Azure-ba, azt tapasztalhatja, kihívást megérteni a különféle szerepkörök az Azure-ban. Ez a cikk segít a következő szerepköröket és mikor érdemes használni mindegyik ismertetik:
- Klasszikus előfizetés rendszergazdai szerepkörök
- Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök
- Az Azure Active Directory (Azure AD) rendszergazdai szerepkörök

## <a name="how-the-roles-are-related"></a>A szerepkörök kapcsolódó hogyan

Szerepkörök az Azure-ban segít jobban megérteni, megismeréséről néhány az előzményeket. Azure jelent, ha mindössze három rendszergazdai szerepkörök erőforrásokhoz való hozzáférés lett felügyelt: a fiók rendszergazdája, szolgáltatás-rendszergazda és a társ-rendszergazdaként. Később a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások lett hozzáadva. Az Azure RBAC egy rendszer újabb engedélyt épül [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) , amely az Azure-erőforrások részletes hozzáférés-vezérlést biztosít. RBAC számos beépített szerepköröket tartalmaz, különféle hatókörökben lehet hozzárendelni, és lehetővé teszi, hogy a saját egyéni szerepkörök létrehozása. Erőforrások kezelése az Azure AD-ben például felhasználók, csoportok és tartományok, nincsenek számos Azure ad-ben rendszergazdai szerepkörrel.

Az alábbi ábrán egy átfogó képet hogyan kapcsolódnak a hagyományos előfizetés rendszergazdai szerepköröket, Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket.

![A különféle szerepkörök az Azure-ban](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrators"></a>Hagyományos előfizetés-rendszergazda

A fiók rendszergazdája, szolgáltatás-rendszergazda és a társ-rendszergazdaként a három hagyományos előfizetés rendszergazdai szerepkörök az Azure-ban. Hagyományos előfizetés-rendszergazda teljes hozzáférése az Azure-előfizetést. Felügyelheti az erőforrásokat az Azure Portalon, az Azure Resource Manager API-k és a klasszikus üzemi modell API-k használatával. A fiók, amellyel regisztrálhat az Azure automatikusan a felhasználóifiók-adminisztrátor és a szolgáltatás-rendszergazda állítja. Ezt követően további Társrendszergazdákat is hozzáadhatók. A szolgáltatás-rendszergazda és a Társadminisztrátorok hozzáférése a megfelelő felhasználók, akik az előfizetések szintjén (az Azure RBAC-szerepkör) tulajdonosi szerepkört rendeltek. A következő táblázat ismerteti ezen három hagyományos előfizetés rendszergazdai szerepkörök közötti különbségeket.

| Hagyományos előfizetés-adminisztrátor | Korlát | Engedélyek | Megjegyzések |
| --- | --- | --- | --- |
| A fiók rendszergazdája | az Azure-fiókonként 1 | <ul><li>Hozzáférés a [Azure fiókkezelési központjába](https://account.azure.com/Subscriptions)</li><li>-Fiókban lévő összes előfizetés kezelése</li><li>Új előfizetések létrehozása</li><li>Előfizetések megszakítása</li><li>A számlázási előfizetés módosítása</li><li>A szolgáltatás-rendszergazda módosítása</li></ul> | Elméleti szinten számlázási az előfizetés tulajdonosa.|
| Szolgáltatás-rendszergazda | 1 Azure-előfizetésenként | <ul><li>A szolgáltatások kezeléséhez a [Azure Portalon](https://portal.azure.com)</li><li>Felhasználók hozzárendelése a Társadminisztrátori szerepkörhöz</li></ul> | Alapértelmezés szerint egy új előfizetést a fiók rendszergazdája a is a szolgáltatás-rendszergazda.<br>A szolgáltatás-rendszergazda egy előfizetésre a tulajdonosi szerepkörrel felruházott felhasználó a megfelelő hozzáféréssel rendelkezik. |
| Társ-rendszergazdaként | 200-as előfizetésenként | <ul><li>Azonos hozzáférési jogosultságokkal a szolgáltatás-rendszergazdaként, de nem módosíthatja az előfizetések közötti társítás, és az Azure-címtárak</li><li>Felhasználók hozzárendelése a Társadminisztrátori szerepköréhez, de nem módosíthatja a szolgáltatás-rendszergazda</li></ul> | A társ-rendszergazdaként az egyenértékű hozzáfér az előfizetések szintjén a tulajdonosi szerepkörrel felruházott felhasználó. |

Az Azure Portalon láthatja, akik a felhasználóifiók-adminisztrátor és a szolgáltatás-rendszergazda által hozzárendelt az előfizetés tulajdonságainak megtekintése.

![Felhasználóifiók-adminisztrátor és a szolgáltatás-rendszergazda az Azure Portalon](./media/rbac-and-directory-admin-roles/account-admin-service-admin.png)

Hozzáadása vagy módosítása az előfizetés adminisztrátorainak kapcsolatos információkért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) az Azure-számlázásról dokumentációjában.

### <a name="azure-account-and-azure-subscriptions"></a>Azure-fiók és az Azure-előfizetések

Az Azure-fiók számlázási kapcsolatot jelöl. Az Azure-fiók a felhasználói azonosítót, egy vagy több Azure-előfizetések és egy társított Azure-erőforrások készletét. A személy, aki a fiókot hoz létre az adott fiókban létrehozott összes előfizetés Fiókadminisztrátori. Adott személy egyben az alapértelmezett szolgáltatás-rendszergazda az előfizetés.

Azure-előfizetés segít rendszerezni a Azure-erőforrásokhoz való hozzáférést. Ezenfelül az előfizetés révén azt is megszabhatja, hogy hogyan szeretne jelentést készíteni az erőforrások használatáról, hogy hogyan számlázzák ki azt Önnek, illetve, hogy hogyan szeretne fizetni érte. Az egyes előfizetésekhez eltérő számlázási és fizetési telepítő, van így is rendelkezik különböző előfizetési és az office, részleg, projekt és így tovább. Mindegyik szolgáltatás egy előfizetéshez tartozik, és lehet, hogy az előfizetés-azonosító programozott műveletekhez szükséges.

Fiókok és előfizetések felügyelete, a [Azure Account Center](https://account.azure.com/Subscriptions).
## <a name="azure-rbac-roles"></a>Az Azure RBAC-szerepkörök

Az Azure RBAC egy rendszer, amely az Azure-erőforrások, például a számítási és tárolási részletes hozzáférés-vezérlést biztosít. Az Azure RBAC beépített szerepkörök több mint 60 tartalmazza. Négy alapvető RBAC-szerepkörök tartoznak. Az első három vonatkoznak az összes erőforrástípus:

| Azure-beli RBAC szerepkör | Engedélyek | Megjegyzések |
| --- | --- | --- |
| [Tulajdonos](built-in-roles.md#owner) | <ul><li>Az összes erőforrásokhoz való teljes hozzáférés</li><li>Delegált hozzáférést másoknak</li></ul> | A szolgáltatás-rendszergazda és Társrendszergazdák egy előfizetésre a tulajdonosi szerepkörhöz vannak hozzárendelve<br>Az összes erőforrástípus vonatkozik. |
| [Közreműködő](built-in-roles.md#contributor) | <ul><li>Létrehozásához és kezeléséhez az összes Azure-erőforrások típusai</li><li>Nem adhat hozzáférést másoknak</li></ul> | Az összes erőforrástípus vonatkozik. |
| [Olvasó](built-in-roles.md#reader) | <ul><li>Az Azure-erőforrások megtekintése</li></ul> | Az összes erőforrástípus vonatkozik. |
| [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) | <ul><li>Azure-erőforrásokhoz való felhasználói hozzáférés felügyelete</li></ul> |  |

A beépített szerepkörök többi adott Azure-erőforrások kezelését teszi lehetővé. Ha például a [virtuális gépek Közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör lehetővé teszi, hogy a felhasználó számára a virtuális gépek létrehozása és kezelése. A beépített szerepkörök listája: [beépített szerepkörök](built-in-roles.md).

Csak az Azure portal és az Azure Resource Manager API-k támogatják az RBAC. Felhasználók, csoportok és alkalmazások RBAC-szerepkörökhöz rendelt nem használható a [klasszikus Azure üzemi modell API-k](../azure-resource-manager/resource-manager-deployment-model.md).

Az Azure Portal használatával RBAC szerepkör-hozzárendelések megjelennek a **hozzáférés-vezérlés (IAM)** panelen. Ezen a panelen a portálon, például az előfizetések, erőforráscsoportok és különböző erőforrásokat során is található.

![Hozzáférés-vezérlés (IAM) paneljén, az Azure Portalon](./media/rbac-and-directory-admin-roles/access-control.png)

Amikor rákattint a **szerepkörök** lehetőséget, látni fogja a beépített és egyéni szerepkörök listája.

![Az Azure Portal beépített szerepkörök](./media/rbac-and-directory-admin-roles/built-in-roles.png)

## <a name="azure-ad-administrator-roles"></a>Az Azure AD-rendszergazdai szerepkörök

Az Azure AD-rendszergazda szerepkör segítségével kezelhet, mint a címtár Azure AD-erőforrások létrehozása vagy a felhasználók szerkesztése, rendszergazdai szerepköröket hozzárendelni más, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése és tartományok kezelése. Az alábbi táblázat néhány fontosabb Azure AD felügyeleti szerepkörök ismerteti.

| Az Azure AD-Rendszergazda szerepkörhöz | Engedélyek | Megjegyzések |
| --- | --- | --- |
| [Globális rendszergazda](../active-directory/active-directory-assign-admin-roles-azure-portal.md#company-administrator) | <ul><li>Az Azure Active Directoryval, valamint a szolgáltatások, amelyek az Azure Active Directoryhoz összevonni összes rendszergazdai funkciójához való hozzáférés kezelése</li><li>Rendszergazdai szerepkörök hozzárendelése mások számára</li><li>Minden felhasználó és minden más rendszergazdák jelszavát</li></ul> | A személy, aki regisztrál az Azure Active Directory-bérlő globális rendszergazdája lesz. |
| [Felhasználói adminisztrátor](../active-directory/active-directory-assign-admin-roles-azure-portal.md#user-account-administrator) | <ul><li>Hozzon létre és kezelheti a felhasználók és csoportok minden aspektusát</li><li>Támogatási jegyek kezelése</li><li>A figyelő szolgáltatásának állapota</li><li>Módosíthatja a felhasználók, az ügyfélszolgálat adminisztrátorai és egyéb felhasználói rendszergazdák jelszavát</li></ul> |  |
| [Ügyfélszolgálati adminisztrátor](../active-directory/active-directory-assign-admin-roles-azure-portal.md#helpdesk-administrator) | <ul><li>Módosíthatja a felhasználók és más segélyszolgálat rendszergazdák jelszavát</li><li>Szolgáltatáskérések kezelése</li><li>A figyelő szolgáltatásának állapota</li></ul> |  |
| [Számlázási adminisztrátor](../active-directory/active-directory-assign-admin-roles-azure-portal.md#billing-administrator) | <ul><li>Vásárlások</li><li>Előfizetések kezelése</li><li>Támogatási jegyek kezelése</li><li>Figyelők szolgáltatás állapota</li></ul> |  |

Az Azure AD felügyeleti szerepkörök listáját lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

Az Azure Portalon a az Azure AD-rendszergazdai szerepköröket rendelhet az **Azure Active Directory** panelen.

![Az Azure Portalon az Azure AD rendszergazdai szerepkörei](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök közötti különbségek

Magas szinten az Azure RBAC-szerepkörökhöz való kezelése Azure-erőforrásokat, amíg az Azure AD-rendszergazdai szerepköröket az Azure Active Directory-erőforrások kezeléséhez engedélyekkel szabályozhatja szabályozza. Az alábbi táblázat összehasonlítja az eltérések képest.

| Az Azure RBAC-szerepkörök | Az Azure AD-rendszergazdai szerepkörök |
| --- | --- |
| Azure-erőforrásokhoz való hozzáférés kezelése | Azure Active Directory-erőforrásokhoz való hozzáférés kezelése |
| Támogatja az egyéni szerepkörök | Nem hozható létre a saját szerepkörök |
| Hatókör (felügyeleti csoport, előfizetés, erőforráscsoport, erőforrás) több szinten adható meg | Bérlői szinten hatóköre |
| Szerepköradatok elérhető lesz az Azure Portalon, az Azure CLI, az Azure PowerShell, az Azure Resource Manager-sablonok, a REST API | Szerepköradatok érhetők el az Azure felügyeleti portál, Office 365 felügyeleti portálján a Microsoft Graph, Azure ad PowerShell |

### <a name="do-azure-rbac-roles-and--azure-ad-administrator-roles-overlap"></a>Hajtsa végre az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket átfedés?

Alapértelmezés szerint az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök nem terjed ki az Azure és az Azure AD. Azonban ha egy globális rendszergazda emelt hozzáférésüket kiválasztásával a **globális rendszergazda kezelheti az Azure-előfizetéseket és a felügyeleti csoportok** váltani az Azure Portalon, a globális rendszergazda megkapja a [ Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) szerepkört (RBAC szerepkör) egy adott bérlő összes előfizetés. A felhasználói hozzáférés rendszergazdájának szerepköre lehetővé teszi, hogy a felhasználót, hogy más felhasználók Azure-erőforrásokhoz való hozzáférés biztosítása. Ez a kapcsoló egy előfizetéshez való hozzáférés visszaszerzéséhez hasznos lehet. További információkért lásd: [az Azure AD-rendszergazda hozzáférési szintjének emelése](elevate-access-global-admin.md).

Több Azure AD felügyeleti szerepkörök span Azure ad-ben és a Microsoft Office 365, például a globális rendszergazda és a felhasználó rendszergazdai szerepkör. Ha Ön globális rendszergazda szerepkör tagjának, például az Azure AD globális rendszergazdai képességek rendelkezik, és Office 365-höz, így például a Microsoft Exchange és a Microsoft SharePoint változik. Azonban alapértelmezés szerint a globális rendszergazda nem rendelkezik Azure-erőforrásokhoz való hozzáférést.

![Az Azure AD-rendszergazdai szerepköröket és az Azure RBAC](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>További lépések

- [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](overview.md)
- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Adja hozzá, vagy az Azure-előfizetések rendszergazdáinak módosításáról](/azure/billing/billing-add-change-azure-subscription-administrator)
