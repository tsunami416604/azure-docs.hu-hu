---
title: Azure Migrate-projektek hibaelhárítása
description: Segít az Azure Migrate-projektek létrehozásával és kezelésével kapcsolatos problémák elhárítása.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535400"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate-projektek hibaelhárítása

Ez a cikk segítséget nyújt az [Azure Migrate-projektek](migrate-services-overview.md) létrehozása és kezelése során felmerülő problémák elhárításához.

## <a name="how-to-add-new-project"></a>Hogyan adjunk új projektet?

Több Azure Migrate-projektet is létrehozhat egy előfizetésben. [Megtudhatja, hogyan](how-to-add-tool-first-time.md) hozhat létre projektet első alkalommal, illetve hogyan [vehet fel további](create-manage-projects.md#create-additional-projects) projekteket.

## <a name="what-azure-permissions-are-needed"></a>Milyen Azure-engedélyekre van szükség?

Az Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekre van szüksége az előfizetésben.

## <a name="cant-find-a-project"></a>Nem található projekt

Egy meglévő Azure Migrate projekt keresése attól függ, hogy az Azure Migrate jelenlegi vagy régi verzióját használja-e. [Kövesse](create-manage-projects.md#find-a-project)a .


## <a name="cant-find-a-geography"></a>Nem találom a földrajzot

Létrehozhat egy Azure Migrate projektet támogatott földrajzi területeken [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)számára.

## <a name="what-are-vm-limits"></a>Mik azok a virtuális gép-korlátozások?

Egyetlen projektben legfeljebb 35 000 VMware virtuális gépet vagy akár 35 000 Hyper-V virtuális gépet értékelhet. A projekt tartalmazhat VMware virtuális gépek és hyper-V virtuális gépek, az értékelési korlátokig.

## <a name="can-i-upgrade-old-project"></a>Frissíthetem a régi projektet?

Az Azure Migrate korábbi verziójából származó projektek nem frissíthetők. Új [projektet](how-to-add-tool-first-time.md)kell létrehoznia, és eszközöket kell hozzáadnia hozzá.

## <a name="cant-create-a-project"></a>Nem lehet projektet létrehozni

Ha projektet próbál létrehozni, és telepítési hibába ütközik:

- Próbálja meg újra létrehozni a projektet, ha átmeneti hiba lenne. A **Központi telepítések**területen kattintson az **Újratelepítés gombra,** hogy újra próbálkozzon.
- Ellenőrizze, hogy rendelkezik-e közreműködői vagy tulajdonosi engedélyekkel az előfizetésben.
- Ha egy újonnan hozzáadott földrajzi területen telepíti, várjon egy rövid ideig, majd próbálkozzon újra.
- Ha megjelenik a "Kérelmek felhasználói identitásfejléceket" hibaüzenet jelenik meg, ez azt jelezheti, hogy nem rendelkezik hozzáféréssel a szervezet Azure Active Directory (Azure AD) bérlőjéhez. Ebben az esetben:
    - Amikor első alkalommal kerül hozzá egy Azure AD-bérlőhöz, e-mailben meghívást kap a bérlőhöz való csatlakozásra.
    - Fogadja el a bérlőhöz hozzáadni szeretne meghívót.
    - Ha nem látja az e-mailt, lépjen kapcsolatba a bérlőhöz hozzáféréssel rendelkező felhasználóval, és kérje meg, hogy [küldje el újra a meghívót.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Miután megkapta a meghívó e-mailt, nyissa meg, és válassza ki a hivatkozást, hogy elfogadja a meghívást. Ezután jelentkezzen ki az Azure Portalon, és jelentkezzen be újra. (a böngésző frissítése nem fog működni.) Ezután megkezdheti az áttelepítési projekt létrehozását.

## <a name="how-do-i-delete-a-project"></a>Hogyan törölhetek egy projektet?

A projekt törléséhez [kövesse az alábbi utasításokat.](create-manage-projects.md#delete-a-project) Vegye figyelembe, hogy a projekt törlésekor a projekt és a projektben felderített gépek metaadatai is törlődnek.

## <a name="added-tools-dont-show"></a>A hozzáadott eszközök nem jelennek meg

Győződjön meg arról, hogy a megfelelő projektet választotta ki. Az Azure Migrate hub **>-kiszolgálók** vagy **adatbázisok**ban kattintson a **Változás** a **projekt áttelepítése (változás)** elemre a képernyő jobb felső sarkában. Válassza ki a megfelelő előfizetési és projektnevet > **OK gombra**. A lapnak a kijelölt projekt hozzáadott eszközeivel kell frissülnie.

## <a name="next-steps"></a>További lépések

[Értékelési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása az Azure Áttelepítési projektekhez.