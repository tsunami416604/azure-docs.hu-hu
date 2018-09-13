---
title: Önkiszolgáló regisztrációs portál az Azure Active Directory B2B együttműködés |} A Microsoft Docs
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 94001b005a883c172cab279029b47ac1ad0c0de5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645048"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Az Azure AD B2B együttműködés regisztráció, önkiszolgáló portál

Ügyfelek a beépített funkciókkal keresztül elérhetővé tett sokkal teheti a [az Azure portal](https://portal.azure.com) és a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) a végfelhasználók számára. Azonban szüksége lehet a regisztrációs munkafolyamat a B2B-felhasználók a szervezet igényeinek megfelelően testre szabhatja. Az elvégezhető [a meghívó API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

A meghívó szervezetet, talán nem tudja időben az egyes külső közreműködők számára, akiknek szükség van az erőforrásokhoz való hozzáférés. Le kell a felhasználókat a partnervállalatokból regisztráció magukat, mint a meghívó szervezetet szabályozott házirendekről vannak beállítva. Ez a forgatókönyv akkor lehetséges, az API-kon keresztül. Van egy [mintaprojektet a Githubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) does, amely éppen ezt.

A GitHub-projekt bemutatja a szervezetek használatát az API-k csoportházirend-alapú, önkiszolgáló regisztrációs képességet biztosít a megbízható partnerekkel, a szabályok, amelyek meghatározzák az alkalmazások eléréséhez. Partner felhasználók erőforrásokhoz való hozzáférést kaphat, amikor szükségük van rájuk. Ehhez biztonságos, anélkül, hogy a meghívó szervezetet, hogy manuálisan előkészítheti őket. A projektet egyszerűen üzembe helyezhető az Ön által választott Azure-előfizetéssel.

## <a name="as-is-code"></a>As-kód

Ez a kód lehetőségként elérhetővé tegyen egy minta használatát az Azure Active Directory B2B meghívó API bemutatása. A fejlesztői csapat vagy egy partner által testre kell, és a egy éles helyzetben való telepítése előtt kell vizsgálni.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B-együttműködés licencelése](licensing-guidance.md)
* [Az Azure Active Directory B2B-együttműködés – gyakori kérdések (GYIK)](faq.md)