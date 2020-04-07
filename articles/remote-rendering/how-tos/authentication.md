---
title: Hitelesítés
description: A hitelesítés működésének elmagyarázása
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681635"
---
# <a name="configure-authentication"></a>Hitelesítés konfigurálása

Az Azure Remote Rendering ugyanazt a hitelesítési mechanizmust használja, mint az [Azure Spatial Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) Az ügyfeleknek be kell állítaniuk *az AccountKey*, *AuthenticationToken*vagy *AccessToken kulcsot* a REST API-k sikeres hívásához. *AccountKey* beszerezhető a "Kulcsok" lapon a távoli leképezési fiók az Azure Portalon. *Az AuthenticationToken* egy Azure AD-token, amely az [ADAL-kódtár](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)használatával szerezhető be. *AccessToken* egy MR-jogkivonat, amely beszerezhető az Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Telepített alkalmazások hitelesítése

 A fiókkulcsok használata a gyors beszálláshoz ajánlott, de csak fejlesztés/prototípus készítés során. Erősen ajánlott, hogy ne szállítsa az alkalmazást éles környezetbe egy beágyazott fiókkulcs használatával, és ehelyett a felhasználó-alapú vagy szolgáltatásalapú Azure AD-hitelesítési megközelítések használata.

 Az Azure AD-hitelesítés az [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) szolgáltatás [Azure AD-felhasználó hitelesítési](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) szakaszában található.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szolgáltatás alkalmazások, szolgáltatások vagy Az Azure AD-felhasználók számára biztosított hozzáférési szint szabályozása érdekében a következő szerepköröket hozták létre, amelyeket szükség szerint hozzárendelhet az Azure remote-leadási fiókjaihoz:

* **Távoli renderelési rendszergazda:** Konverziós, munkamenet-, renderelési és diagnosztikai lehetőségeket biztosít a felhasználónak az Azure távoli rendereléshez.
* **Távoli leképezési ügyfél:** Biztosítja a felhasználó számára a munkamenet, a renderelés és a diagnosztikai képességek azure távoli leképezési képességek kezelése.

## <a name="next-steps"></a>További lépések

* [Fiók létrehozása](create-an-account.md)
* [Az Azure előtér-API-k használata a hitelesítéshez](frontend-apis.md)
* [Példa PowerShell-parancsfájlokra](../samples/powershell-example-scripts.md)
