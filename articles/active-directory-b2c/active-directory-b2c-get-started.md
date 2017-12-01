---
title: "Azure Active Directory B2C-bérlő létrehozása |} Microsoft Docs"
description: "Témakör: Azure Active Directory B2C-bérlő létrehozása"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: parja
ms.openlocfilehash: 0a81b8717f9cd78b9e5c39267ff187656b0b2827
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Azure Active Directory B2C-bérlő létrehozása az Azure-portálon

A gyors üzembe helyezés segítséget nyújt a Microsoft Azure Active Directory (Azure AD) B2C-bérlő létrehozása néhány perc múlva. Amikor végzett, hogy a B2C-bérlő (más néven címtár) regisztrálása a B2C-alkalmazásokhoz használandó.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

B2C funkciók nem engedélyezhetők a a meglévő bérlők számára. Azure AD B2C-bérlő létrehozásához szükséges.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Gratulálunk, az Azure Active Directory B2C-bérlő hozott létre. A bérlő globális rendszergazdája áll. Szükség szerint más globális rendszergazdákat is hozzáadhat. Az új bérlőjéhez tartozik, kattintson a *kezelése az új bérlő hivatkozás*.

![Az új bérlő hivatkozás kezelése](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Ha azt tervezi, hogy a B2C-bérlő éles alkalmazások használja, olvassa el a cikk a [éles méretű és a B2C előzetes bérlők](active-directory-b2c-reference-tenant-type.md). Vannak ismert problémák egy meglévő B2C-bérlő törölje és hozza létre újból az azonos nevű tartomány. Egy másik tartomány nevét a B2C-bérlő létrehozásához szükséges.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>A bérlő csatolása az előfizetéshez

Az Azure AD B2C-bérlő csatolása az Azure-előfizetés engedélyezése a B2C-funkciókat és használati díjat kell fizetnie kell. További tudnivalókért olvassa el a [Ez a cikk](active-directory-b2c-how-to-enable-billing.md). Ha az Azure AD B2C-bérlő nem kapcsolódik az Azure-előfizetéssel, néhány funkció le van tiltva, és megjelenik egy figyelmeztető üzenet ("Nincs előfizetés csatolva a B2C-bérlő vagy az előfizetés kell a figyelmet.") a B2C beállítások. Fontos, hogy ezzel a lépéssel előtt az alkalmazások éles környezetben.

## <a name="easy-access-to-settings"></a>Egyszerű hozzáférést beállításai

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Emellett a blade megadásával `Azure AD B2C` a **keresési erőforrások** a portál felső. Az eredmények listájában válassza **az Azure AD B2C** a B2C beállítások panel eléréséhez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A B2C alkalmazás regisztrálása a B2C-bérlőben](active-directory-b2c-app-registration.md)