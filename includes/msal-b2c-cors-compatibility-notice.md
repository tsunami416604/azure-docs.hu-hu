---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311541"
---
> [!IMPORTANT]
> A MSAL.js 2,0 jelenleg nem támogatja Azure AD B2C használatát a PKCE-engedélyezési kód folyamatával való használatra. Jelenleg Azure AD B2C javasolja az implicit folyamat használatát az [oktatóanyag: alkalmazás regisztrálása][implicit-flow]című témakörben leírtak szerint. A probléma előrehaladásának követéséhez tekintse meg a [MSAL.js wikit][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue
