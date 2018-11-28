---
title: Alkalmazás eltávolítása az Azure Active Directoryból
description: Megismerheti, hogyan távolíthat el egy alkalmazást az Azure Active Directoryból (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: ddc04d32f76515cb93d2cde3fe4eccaab7e4c291
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245358"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Rövid útmutató: Alkalmazás eltávolítása az Azure Active Directoryból

Az alkalmazásokat az Azure Active Directoryban (Azure AD) regisztráló nagyvállalati fejlesztőknek és SaaS-szolgáltatóknak (szoftverszolgáltatók) esetenként el kell távolítaniuk az alkalmazás regisztrációját az Azure AD-bérlőből.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* [A saját szervezet által kifejlesztett alkalmazás eltávolítása](#removing-an-application-authored-by-your-organization)
* [Egy másik szervezet által engedélyezett több-bérlős alkalmazás eltávolítása](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége lesz egy olyan Azure AD-bérlőre, amelyen vannak alkalmazások regisztrálva.

* Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).
* Ha szeretné megtudni, hogyan vehet fel és regisztrálhat alkalmazásokat a bérlőn, tekintse meg az [Alkalmazás felvétele az Azure AD-be](quickstart-v1-integrate-apps-with-azure-ad.md) című szakaszt.

## <a name="removing-an-application-authored-by-your-organization"></a>A saját szervezet által kifejlesztett alkalmazás eltávolítása

A saját szervezet által regisztrált alkalmazások a **Saját alkalmazások** szűrő alatt találhatók a bérlő **Alkalmazásregisztrációk** főoldalán. Ezeket az alkalmazásokat regisztrálta manuálisan az Azure Portalon, vagy programozott módon a PowerShellen vagy a Microsoft Graph API-n keresztül. Még pontosabban, ezeket az alkalmazásokat egy alkalmazás- és szolgáltatásnév-objektum is jelöli a bérlőben. Ezekről az objektumokról az [Alkalmazásobjektumok és szolgáltatásnév-objektumok](app-objects-and-service-principals.md) című szakaszban találhat további információt.

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Egybérlős alkalmazás eltávolítása a könyvtárból

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást és az **Alkalmazásregisztrációk** lehetőséget, majd keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
    Ekkor az alkalmazás regisztrációjának főoldalára kerül, ahol megnyílik az alkalmazás **Beállítások** lapja.
1. Az alkalmazás regisztrációjának főoldalán válassza a **Törlés** lehetőséget.
1. Kattintson az **Igen** gombra az alkalmazás törlésének megerősítéséhez.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Több-bérlős alkalmazás eltávolítása annak kezdőkönyvtárából

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást és az **Alkalmazásregisztrációk** lehetőséget, majd keresse meg és válassza ki a konfigurálni kívánt alkalmazást.
    Ekkor az alkalmazás regisztrációjának főoldalára kerül, ahol megnyílik az alkalmazás **Beállítások** lapja.
1. A **Beállítások** lapon válassza a **Tulajdonságok** lehetőséget, majd állítsa a **Több-bérlős** kapcsolót a **Nem** állásba. Ezzel az alkalmazás egybérlősre módosul. Végül kattintson a **Mentés** lehetőségre.
    Az alkalmazás szolgáltatásnév-objektumai minden olyan szervezet bérlőiben megmaradnak, amelyek már hozzájárulásukat adták.
1. Az alkalmazás regisztrációjának főoldalán válassza a **Törlés** lehetőséget.
1. Kattintson az **Igen** gombra az alkalmazás törlésének megerősítéséhez.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Egy másik szervezet által engedélyezett több-bérlős alkalmazás eltávolítása

A bérlő **Alkalmazásregisztrációk** főoldalán az **Összes alkalmazás** szűrő alatt megjelenő alkalmazások egy része (kivéve a **Saját alkalmazások** regisztrációit) több-bérlős alkalmazás.

Technikai szempontból ezek a több-bérlős alkalmazások más bérlőtől származnak, és a hozzájárulási folyamat során lettek regisztrálva a bérlőjében. Pontosabban ezeket az alkalmazásokat csak egy szolgáltatásnév-objektum jelöli a bérlőben, és nem tartozik hozzájuk alkalmazásobjektum. Az alkalmazás- és szolgáltatásnév-objektumok különbségeivel kapcsolatos további információt az [Alkalmazás- és szolgáltatásnév-objektumok az Azure AD-ben](app-objects-and-service-principals.md) című szakaszban találhat.

Egy több-bérlős alkalmazás az Ön könyvtárához való hozzáférésének eltávolításához (a hozzájárulás megadása után) a vállalati rendszergazdának el kell távolítania az alkalmazás szolgáltatásnevét. A rendszergazdának globális rendszergazdai hozzáféréssel kell rendelkeznie, és az Azure Portalon keresztül vagy az [Azure AD PowerShell-parancsmagok](https://go.microsoft.com/fwlink/?LinkId=294151) használatával végezheti el az eltávolítási műveletet.

## <a name="next-steps"></a>További lépések

Tekintse át az Azure AD 1.0-s verziójú végpontját használó alkalmazásokra vonatkozó alábbi alkalmazásfelügyeleti útmutatókat:

- [Alkalmazás felvétele az Azure AD-be](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Alkalmazás frissítése az Azure AD-ben](quickstart-v1-update-azure-ad-app.md)
