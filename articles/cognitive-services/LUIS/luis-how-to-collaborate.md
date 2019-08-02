---
title: Együttműködés másokkal – LUIS
titleSuffix: Azure Cognitive Services
description: Az alkalmazások tulajdonosai közreműködők adhat hozzá az alkalmazást. A közreműködők módosíthatja a modell betanítását és tegye közzé az alkalmazást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dbc27176e7a300c0799e326acb10b99ac663fa89
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638117"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Szerzők és a közreműködők kezelése 

Az alkalmazások tulajdonosai közreműködők adhat hozzá az alkalmazást. A közreműködők módosíthatja a modell betanítását és tegye közzé az alkalmazást. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Adja hozzá a közreműködő

Egy alkalmazás egyetlen Szerző, a tulajdonos, rendelkezik, de sok közreműködők is. Ahhoz, hogy a LUIS-alkalmazás szerkesztéséhez közreműködő, hozzá kell adnia az e-mailt a közreműködők listája a LUIS-portál eléréséhez használnak. Hozzáadás után az alkalmazás pedig a LUIS-portálon jeleníti meg.

1. Válassza ki **kezelés** jobb felső menüben, majd válassza **közreműködők** a bal oldali menüben.

2. Válassza ki **adja hozzá a közreműködő** az eszköztáron.

    [![Hozzáadás közreműködő](./media/luis-how-to-collaborate/add-collaborator.png "Hozzáadás közreműködő")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Adja meg az e-mail-cím, a közreműködő segítségével jelentkezzen be a LUIS-portálon.

    ![Adja meg a közreműködő e-mail-címet](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>A tulajdonjog átruházása

Közben a LUIS jelenleg nem támogatja a tulajdonjog átruházása, exportálhatja az alkalmazás, és a egy másik LUIS felhasználó importálhatja az alkalmazás. Előfordulhat, hogy a két alkalmazás közötti LUIS pontszámok kisebb különbségek. 

## <a name="azure-active-directory-resources"></a>Az Azure Active Directory-erőforrások

Ha Azure Active Directoryt [](https://docs.microsoft.com/azure/active-directory/) (Azure ad) használ a szervezetben, Language UNDERSTANDING (Luis) engedélyre van szüksége a felhasználók hozzáférésére vonatkozó információkhoz, amikor a Luis-t szeretnék használni. A LUIS igénylő erőforrások minimálisak. 

Amikor egy olyan fiókkal, amely rendelkezik rendszergazdai jóváhagyás, vagy nincs szükség rendszergazdai jóváhagyás, például a rendszergazdai jóváhagyást bejelentkezés jelenik meg a részletes leírása:

* Lehetővé teszi, hogy jelentkezzen be az alkalmazásba a szervezeti fiókjával, és hogy az alkalmazás olvassa a profilját. Azt is lehetővé teszi, hogy az alkalmazásnak a főbb munkahelyi adatai olvasását. Ez LUIS engedélyt ad az alapszintű profilokra vonatkozó adatok olvasására, például felhasználói azonosító, e-mail, név
* Itt engedélyezheti az alkalmazásnak, és frissítheti az adatokat, akkor is, ha jelenleg nem használja az alkalmazást. A felhasználó hozzáférési jogkivonatának frissítéséhez engedély szükséges.


## <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlői felhasználó

A LUIS a standard szintű Azure Active Directory (Azure AD) jóváhagyási folyamatot használ. 

A bérlői rendszergazda közvetlenül a felhasználó, aki a LUIS használatát az Azure AD-ben biztosított hozzáférést kell működnie. 

* A felhasználó először jelentkezik be a LUIS, és megtekinti a rendszergazdai jóváhagyást igénylő felugró párbeszédpanel. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 
* A bérlői rendszergazda második, LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazda engedélyt kell adnia a felhasználó számára. A rendszergazda fogad az engedélyt, ha a felhasználó nem tudja folytatni az intelligens hangfelismerési szolgáltatással. Ha a bérlői rendszergazda nem tud bejelentkezni a LUIS-be, a [rendszergazda hozzáférhet a](https://account.activedirectory.windowsazure.com/r#/applications) luishoz, amely az alábbi képernyőfelvételen látható. Figyelje meg, hogy a lista a nevet `LUIS`tartalmazó elemekre van szűrve.

![Alkalmazás webhelye által az Azure active directory-engedély](./media/luis-how-to-collaborate/tenant-permissions.png)

Ha a bérlői rendszergazda csak bizonyos felhasználók számára szeretne LUIS-t használni, néhány lehetséges megoldás létezik:
* Adja meg a "rendszergazdai beleegyezett" (beleegyezett az Azure AD összes felhasználójára), de az "igen" értékre állítsa a "felhasználói hozzárendelés szükséges" értéket a vállalati alkalmazás tulajdonságai területen, végül pedig csak a kívánt felhasználókat rendeli hozzá az alkalmazáshoz. Ezzel a módszerrel a rendszergazda továbbra is "rendszergazdai beleegyezett" az alkalmazáshoz, de megadhatja azokat a felhasználókat, akik hozzáférhetnek hozzá.
* Egy második megoldás az [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) használatával biztosítja az egyes felhasználók beleegyezett. 

További információ az Azure Active Directory-felhasználókról és-engedélyekről: 
* [Alkalmazás korlátozása](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) felhasználói csoportra

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>A közreműködők több e-mailt a felhasználói fiókok

Ha közreműködők ad hozzá egy LUIS-alkalmazásokon, adja meg a pontos e-mail-cím, LUIS közreműködőként használandó kell közreműködő. Az Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó felcserélhetők egynél több e-mail-fiókot, amíg a LUIS a felhasználót, hogy jelentkezzen be a közreműködő listában megadott e-mail-cím szükséges.

