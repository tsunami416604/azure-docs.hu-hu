---
title: Együttműködés másokkal a
titleSuffix: Language Understanding - Azure Cognitive Services
description: Az alkalmazások tulajdonosai közreműködők adhat hozzá az alkalmazást. A közreműködők módosíthatja a modell betanítását és tegye közzé az alkalmazást.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 4ccf703ffde0d4a5a0961135e81259519b0a0e41
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878969"
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

Ha a szervezete Azure Active Directory (Azure AD) használja, a LUIS a LUIS használni szeretne a felhasználókra vonatkozó az adatok eléréséhez engedélyre van szüksége. A LUIS igénylő erőforrások minimálisak. 

Amikor egy olyan fiókkal, amely rendelkezik rendszergazdai jóváhagyás, vagy nincs szükség rendszergazdai jóváhagyás, például a rendszergazdai jóváhagyást bejelentkezés jelenik meg a részletes leírása:

* Lehetővé teszi, hogy jelentkezzen be az alkalmazásba a szervezeti fiókjával, és hogy az alkalmazás olvassa a profilját. Azt is lehetővé teszi, hogy az alkalmazásnak a főbb munkahelyi adatai olvasását.
* Itt engedélyezheti az alkalmazásnak, és frissítheti az adatokat, akkor is, ha jelenleg nem használja az alkalmazást.

Az első engedély lehetővé teszi a LUIS alapvető profiladatok, például a felhasználói azonosító, az e-mailt, a név olvasási joga. A második engedély szükséges a felhasználó a hozzáférési jogkivonat frissítéséhez.

## <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlői felhasználó

A LUIS a standard szintű Azure Active Directory (Azure AD) jóváhagyási folyamatot használ. 

A bérlői rendszergazda közvetlenül a felhasználó, aki a LUIS használatát az Azure AD-ben biztosított hozzáférést kell működnie. 

A felhasználó először jelentkezik be a LUIS, és megtekinti a rendszergazdai jóváhagyást igénylő felugró párbeszédpanel. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 

A bérlői rendszergazda második, LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazda engedélyt kell adnia a felhasználó számára. A rendszergazda fogad az engedélyt, ha a felhasználó nem tudja folytatni az intelligens hangfelismerési szolgáltatással.

Ha a bérlői rendszergazda nem fog bejelentkezni a LUIS, a rendszergazda hozzáférhet [hozzájárulás](https://account.activedirectory.windowsazure.com/r#/applications) a LUIS. 

![Alkalmazás webhelye által az Azure active directory-engedély](./media/luis-how-to-collaborate/tenant-permissions.png)

Ha a bérlői rendszergazda csak meghatározott felhasználóknak, hogy a LUIS használatát fájlvédelemre, van néhány lehetséges megoldások:
* Engedélyezi az "admin" (beleegyezik abba, hogy minden felhasználó, az Azure AD), azonban az "Igen" a "felhasználó-hozzárendelés szükséges" Vállalati alkalmazás Tulajdonságok területen, majd állítsa be, és végül hozzárendelése/csak a kívánt felhasználók hozzáadása az alkalmazáshoz. Ezzel a módszerrel a rendszergazda "rendszergazdai jóváhagyás" továbbra is biztosít az alkalmazásnak, azonban lehetséges szabályozhatja a felhasználókat, hogy hozzá tud férni.
* A második megoldás használatával van [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) történő minden egyes adott felhasználónak. 

További információ az Azure active directory – felhasználók és engedélyt: 
* [Az alkalmazás korlátozása](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) , a felhasználók egy csoportja

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>A közreműködők több e-mailt a felhasználói fiókok

Ha közreműködők ad hozzá egy LUIS-alkalmazásokon, adja meg a pontos e-mail-cím, LUIS közreműködőként használandó kell közreműködő. Az Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó felcserélhetők egynél több e-mail-fiókot, amíg a LUIS a felhasználót, hogy jelentkezzen be a közreműködő listában megadott e-mail-cím szükséges.

