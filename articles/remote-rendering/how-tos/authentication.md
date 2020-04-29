---
title: Hitelesítés
description: Elmagyarázza, hogyan működik a hitelesítés?
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681635"
---
# <a name="configure-authentication"></a>A hitelesítés konfigurálása

Az Azure távoli renderelés ugyanazt a hitelesítési mechanizmust használja, mint az [Azure térbeli horgonyok (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Az ügyfeleknek a REST API-k sikeres meghívásához be kell állítania a *AccountKey*, a *AuthenticationToken*vagy a *AccessToken* . A *AccountKey* az Azure Portal távoli renderelési fiókjához tartozó kulcsok lapon szerezhetők be. A *AuthenticationToken* egy Azure ad-jogkivonat, amelyet a [ADAL-könyvtár](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)használatával lehet beszerezni. A *AccessToken* egy Mr token, amely az Azure Mixed Reality biztonságijogkivonat-szolgáltatás (STS) használatával szerezhető be.

## <a name="authentication-for-deployed-applications"></a>Hitelesítés telepített alkalmazásokhoz

 A fiók kulcsainak használata ajánlott a gyors beléptetéshez, de csak a fejlesztés és a prototípusok során. Erősen ajánlott, hogy az alkalmazást az élesben lévő beágyazott fiók kulcsa alapján szállítsa az alkalmazásba, és Ehelyett használja a felhasználó-vagy a szolgáltatás-alapú Azure AD-hitelesítési módszereket.

 Az Azure AD-hitelesítést az Azure [térbeli horgonyok (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) szolgáltatás [Azure ad felhasználói hitelesítés](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) szakasza ismerteti.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szolgáltatás alkalmazásaihoz, szolgáltatásaihoz vagy az Azure AD felhasználóihoz biztosított hozzáférés szintjének szabályozásához a következő szerepköröket hozta létre, amelyeket szükség szerint rendelhet hozzá az Azure távoli megjelenítési fiókjaihoz:

* **Távoli renderelési rendszergazda**: lehetővé teszi a felhasználó számára a konverziót, a munkamenetek kezelését, a renderelést és a diagnosztikai képességeket az Azure távoli rendereléshez.
* **Távoli renderelési ügyfél**: a felhasználók számára biztosít munkamenet-, renderelési és diagnosztikai képességeket az Azure távoli rendereléshez.

## <a name="next-steps"></a>További lépések

* [Fiók létrehozása](create-an-account.md)
* [Az Azure frontend API-k használata a hitelesítéshez](frontend-apis.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
