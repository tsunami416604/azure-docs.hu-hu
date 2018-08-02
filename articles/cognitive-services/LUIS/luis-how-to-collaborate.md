---
title: Együttműködhet más közreműködőkkel párhozamosan LUIS-alkalmazások az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan lehet másokkal együttműködve segítségével más közreműködőkkel párhozamosan Language Understanding (LUIS) alkalmazások.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397788"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Szerzők és a közreműködők kezelése 

Akkor is működhet együtt másokkal a LUIS-alkalmazás a együtt. 

## <a name="owner-and-collaborators"></a>Tulajdonos és közreműködő

Egy alkalmazás egyetlen Szerző, a tulajdonos, rendelkezik, de sok közreműködők is. 

## <a name="add-collaborator"></a>Adja hozzá a közreműködő

Hogy a LUIS-alkalmazás szerkesztése a közreműködők a **beállítások** lapon adja meg az e-mailt, a közreműködő, majd kattintson a LUIS-alkalmazás **Hozzáadás közreműködő**. Közreműködők bejelentkezhet és a LUIS-alkalmazás szerkesztése az alkalmazás éppen dolgozik egy időben.

![Adja hozzá a közreműködő](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>A tulajdonjog átruházása

Közben a LUIS jelenleg nem támogatja a tulajdonjog átruházása, exportálhatja az alkalmazás, és a egy másik LUIS felhasználó importálhatja az alkalmazás. Előfordulhat, hogy a két alkalmazás közötti LUIS pontszámok kisebb különbségek. 

## <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlői felhasználó

A LUIS a standard szintű Azure Active Directory (Azure AD) jóváhagyási folyamatot használ. 

A bérlői rendszergazda közvetlenül a felhasználó, aki a LUIS használatát az Azure AD-ben biztosított hozzáférést kell működnie. 

A felhasználó először jelentkezik be a LUIS, és megtekinti a rendszergazdai jóváhagyást igénylő felugró párbeszédpanel. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 

A bérlői rendszergazda második, LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazda engedélyt kell adnia a felhasználó számára. A rendszergazda fogad az engedélyt, ha a felhasználó nem tudja folytatni az intelligens hangfelismerési szolgáltatással.

Ha a bérlői rendszergazda nem fog bejelentkezni a LUIS, a rendszergazda hozzáférhet [hozzájárulás](https://account.activedirectory.windowsazure.com/r#/applications) a LUIS. 

![Alkalmazás webhelye által az Azure active directory-engedély](./media/luis-how-to-account-settings/tenant-permissions.png)

Ha a bérlői rendszergazda csak meghatározott felhasználóknak, hogy a LUIS használatát szeretné, tekintse meg a [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>A közreműködők több e-mailt a felhasználói fiókok

Ha közreműködők ad hozzá egy LUIS-alkalmazásokon, adja meg a pontos e-mail-cím, LUIS közreműködőként használandó kell közreműködő. Az Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó felcserélhetők egynél több e-mail-fiókot, amíg a LUIS a felhasználót, hogy jelentkezzen be a közreműködő listában megadott e-mail-cím szükséges.