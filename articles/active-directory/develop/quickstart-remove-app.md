---
title: 'Útmutató: regisztrált alkalmazás eltávolítása a Microsoft Identity platformról | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a útmutatóban megtudhatja, hogyan távolíthat el egy, a Microsoft Identity platformmal regisztrált alkalmazást.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 033d5cfa5dbe5e0010bc1182f54643ec04db3ce3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017612"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>A Microsoft Identity platformmal regisztrált alkalmazások eltávolítása

Az alkalmazásokat a Microsoft identitásplatformján regisztráló nagyvállalati fejlesztőknek és SaaS-szolgáltatóknak (szoftverszolgáltatók) esetenként el kell távolítaniuk az alkalmazás regisztrációját.

A következő fejezetekben megismerheti a következőket:

* A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása
* Más szervezet által fejlesztett alkalmazás eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Az [Azure ad-bérlőben regisztrált alkalmazás](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása

A saját maga vagy a szervezete által regisztrált alkalmazásokat egy alkalmazás- és egy szolgáltatásnév-objektum is jelöli a bérlőn. További információkért tekintse meg [az alkalmazás- és szolgáltatásnév-objektumokat](./app-objects-and-service-principals.md) ismertető cikket.

Az alkalmazás törléséhez az alkalmazás tulajdonosaként kell szerepelnie, vagy rendszergazdai jogosultságokkal kell rendelkeznie.

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk** lehetőséget. Keresse meg, és válassza ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik annak **Áttekintés** lapja.
1. Az **Áttekintés** lapon válassza a **Törlés** elemet.
1. Kattintson az **Igen** gombra az alkalmazás törlésének megerősítéséhez.

## <a name="remove-an-application-authored-by-another-organization"></a>Más szervezet által fejlesztett alkalmazás eltávolítása

Ha az **alkalmazásregisztrációkat** egy bérlő kontextusában tekinti meg, a **Minden alkalmazás** lapon szereplő alkalmazások egy része más bérlőről származik, és a hozzájárulási folyamat során lettek regisztrálva a bérlőjében. Pontosabban ezeket az alkalmazásokat csak egy szolgáltatásnév-objektum jelöli a bérlőben, és nem tartozik hozzájuk alkalmazásobjektum. Az alkalmazás- és szolgáltatásnév-objektumok különbségeivel kapcsolatos további információért tekintse meg [az Azure AD-ben használt alkalmazás- és szolgáltatásnév-objektumokkal](./app-objects-and-service-principals.md) foglalkozó cikket.

Egy alkalmazás az Ön könyvtárához való hozzáférésének eltávolításához (a hozzájárulás megadása után) a vállalati rendszergazdának el kell távolítania az alkalmazás szolgáltatásnevét. A rendszergazdának globális rendszergazdai hozzáféréssel kell rendelkeznie, és az Azure Portalon keresztül távolíthatja el az alkalmazást, vagy az [Azure AD PowerShell-parancsmagok](/previous-versions/azure/jj151815(v=azure.100)) használatával törölheti a hozzáférést.

## <a name="next-steps"></a>További lépések

További információ az [alkalmazások és szolgáltatások egyszerű objektumairól](app-objects-and-service-principals.md) a Microsoft Identity platformon.
