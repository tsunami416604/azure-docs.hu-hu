---
title: B2B-együttműködés felhasználói jogcímtársítások az Azure Active Directoryban |} A Microsoft Docs
description: Testre szabhatja a felhasználói jogcímeket, az Azure Active Directory (Azure AD) B2B-felhasználók az SAML-jogkivonatban kiadott.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 7a06afd7e6e295510f393177d9828fe6dd7a3c0e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428903"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-együttműködés felhasználói jogcímtársítások az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a B2B együttműködési felhasználókat az SAML-jogkivonatban kiadott jogcímek testreszabása. Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD SAML-jogkivonat az alkalmazást, amely tartalmazza az adatokat (vagy jogcímek), amely egyedileg azonosítja a felhasználóval kapcsolatos problémák. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználói név, e-mail címét, Utónév és Vezetéknév.

Az a [az Azure portal](https://portal.azure.com), megtekintheti és szerkesztheti a jogcímek, az alkalmazás SAML-jogkivonatban elküldött. A beállítások eléréséhez, válassza **Azure Active Directory** > **vállalati alkalmazások** > az alkalmazás egyszeri bejelentkezésre konfigurált > **egyszeri bejelentkezés** . Tekintse meg az SAML-jogkivonat beállítások a **felhasználói attribútumok** szakaszban.

![A felhasználói felületen jeleníti meg az SAML-jogkivonat attribútumai](media/claims-mapping/view-claims-in-saml-token.png)

Előfordulhat, hogy miért a SAML-jogkivonatban kiadott jogcímek szerkesztése kell két lehetséges oka van:

1. Az alkalmazáshoz szükséges különböző jogcím-URI-k vagy a jogcímértékek.

2. Az alkalmazás használatához a NameIdentifier jogcím verziójáétól eltérő az egyszerű felhasználónév (UPN) az Azure ad-ben tárolt.

Adja hozzá, és a jogcímek szerkesztése kapcsolatos információkért lásd: [az Azure Active Directory vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md).

B2B-együttműködés a felhasználók, leképezés NameID és UPN több-bérlős ebben az esetben a biztonsági okokból.

## <a name="next-steps"></a>További lépések

- B2B együttműködés felhasználói tulajdonságokkal kapcsolatos információkért lásd: [egy Azure Active Directory B2B-együttműködés felhasználói tulajdonságainak](user-properties.md).
- B2B-együttműködés felhasználók a felhasználói jogkivonatok kapcsolatos információkért lásd: [megismerheti az Azure AD B2B együttműködés felhasználói jogkivonatok](user-token.md).

