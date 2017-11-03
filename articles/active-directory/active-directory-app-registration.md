---
title: "Az Azure Active Directory-alkalmazás regisztrációja |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan használható az Azure-portál alkalmazás regisztrálása az Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Az alkalmazás regisztrálása az Azure Active Directory-bérlő

Az Azure portál segítségével regisztrálhatja alkalmazását az Azure Active Directory (Azure AD) bérlő. Az alkalmazás Azonosítóját hoz létre, és lehetővé válik a jogkivonatokat fogadni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki az Azure AD-bérlő fiókja kiválasztja az oldal jobb felső sarkában.
3. A bal oldali navigációs ablaktábláján válassza **több szolgáltatások**, kattintson a **App regisztrációk**, és kattintson a **hozzáadása**.
4. Kövesse az utasításokat az új alkalmazás létrehozásához. Ha szeretné, hogy a webes alkalmazások és natív alkalmazások példák, tekintse meg a [quickstarts](active-directory-developers-guide.md).
  * A webes alkalmazásokhoz, adja meg a **bejelentkezési URL-cím**, az alap URL-CÍMÉT az alkalmazásához. Ez az adott felhasználó tud egyszerre bejelentkezni például `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Natív alkalmazások esetén adja meg a **átirányítási URI-**, az Azure AD vissza a token válaszok használja. Adjon meg egy, az alkalmazáshoz tartozó értéket, például: `http://MyFirstAADApp`
5. Miután végrehajtotta a regisztráció, az Azure AD rendeli hozzá az alkalmazás egyedi ügyfél-azonosítókra, az alkalmazás azonosítóját.

## <a name="update-application-settings-from-the-azure-portal"></a>Azure-portálról Alkalmazásbeállítások frissítése

Könnyen módosíthatja egy meglévő alkalmazás beállításait az Azure portál használatával. Érdemes lehet például egy válasz URL-CÍMEN, amely van, ahol az Azure AD kibocsát token válaszok konfigurálásához. Is érdemes lehet például konfigurálja az egyéb alkalmazások engedélyei ahhoz, hogy az alkalmazás a Microsoft Graph API eléréséhez. Ez az alkalmazás beállításait tartalmazó lap keresztül teheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki az Azure AD-bérlő fiókja kiválasztja az oldal jobb felső sarkában.
3. A bal oldali navigációs ablaktábláján válassza **több szolgáltatások**, kattintson a **App regisztrációk**, és válassza ki az alkalmazást a listából.
4. Kattintson a **beállítások** kattintva nyissa meg az alkalmazás beállításait tartalmazó oldalt.
  * A **tulajdonságok** lap lehetővé teszi, hogy módosítsa az alkalmazás általános adatokat. Ez magában foglalja az alkalmazás nevét, a bejelentkezési URL-cím és a kijelentkezési URL-CÍMÉT.
  * A **válasz URL-címek** lap lehetővé teszi egy válasz URL-CÍMEN, amely ahol küld az Azure AD a token válaszokat.
  * A **tulajdonosok** lap lehetővé teszi az alkalmazástulajdonosok.
  * A **engedélyek** lap lehetővé teszi az alkalmazás-engedélyek konfigurálása. Például a Microsoft Graph API eléréséhez kattintson **Hozzáadás** válassza **Microsoft Graph** az API-választó, majd válassza az engedély szükséges, például **címtáradatok olvasása**.
  * A **kulcsok** lap lehetővé teszi az alkalmazás titkos kulcsok. A titkos kulcs csak jelenik meg, miután a létrehozás után azonnal Igen ügyeljen rá, hogy másolja a további használja.

## <a name="use-the-inline-manifest-editor"></a>A beágyazott jegyzék szerkesztő használata

A beágyazott jegyzék szerkesztő segítségével bizonyos alkalmazás tulajdonságait, amelyek nem érhetők el közvetlenül az Azure-portálon módosíthatja. Például használhatja az alkalmazás App ID URI módosítására, vagy ahhoz, hogy a OAuth2.0 implicit engedélyezési folyamat helyett az alapértelmezett hitelesítésikód-támogatás folyamata.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki az Azure AD-bérlő fiókja kiválasztja az oldal jobb felső sarkában.
3. A bal oldali navigációs ablaktábláján válassza **több szolgáltatások**, kattintson a **App regisztrációk**, és válassza ki az alkalmazást a listából.
4. Kattintson a **Manifest** az alkalmazás oldalról a jegyzék beágyazott-szerkesztő megnyitásához.
5. Közvetlenül a jegyzékfájl módosítja, és mentheti, ha készen áll. Alternatív megoldásként letöltheti a a jegyzékfájl nyissa meg a kedvenc szerkesztőben, és a frissített jegyzékfájl feltöltéséhez.

## <a name="next-steps"></a>Következő lépések

1. Tekintse meg a [Quickstarts](active-directory-developers-guide.md) részletes forgatókönyvek végrehajtása az Azure AD hitelesítési kérelmek számára.
2. Tekintse meg a teljes listáját a mintakódok [GitHub](https://github.com/azure-samples).
