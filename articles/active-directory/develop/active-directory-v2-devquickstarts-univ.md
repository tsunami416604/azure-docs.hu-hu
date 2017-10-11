---
title: "Az Azure AD v2.0 univerzális Windows-alkalmazás |} Microsoft Docs"
description: "Hogyan hozhat létre, hogy a felhasználók be mindkét személyes Microsoft-Account aláírja univerzális Windows-alkalmazások és a munkahelyi vagy iskolai fiókját."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: d2c92b65-3c1d-46d1-81c8-88f32f6b2c4b
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.date: 02/20/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 369802f1a42b8720aa730d5ac7e5576ed20eeddf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-a-windows-universal-app-using-the-v20-endpoint"></a>Bejelentkezés hozzáadása egy univerzális Windows-alkalmazást a v2.0-végpontra használatával
  A gyors üzembe helyezési oktatóanyag univerzális Windows-alkalmazásokhoz nem áll teljesen készen... Vissza hamarosan & keresse meg a elérhető frissítések ellenőrzése @AzureAD a Twitteren.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

    ## Get security updates for our products

Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.

