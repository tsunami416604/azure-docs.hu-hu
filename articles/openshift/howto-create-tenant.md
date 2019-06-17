---
title: Az Azure Red Hat OpenShift az Azure AD-bérlő létrehozása |} A Microsoft Docs
description: Itt látható, hogyan hozhat létre egy Azure Active Directory (Azure AD) bérlőt a Microsoft Azure Red Hat OpenShift-fürt üzemeltetéséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306446"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Az Azure Red Hat OpenShift az Azure AD-bérlő létrehozása

A Microsoft Azure Red Hat OpenShift van szükség egy [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) bérlő, amelyben a fürt létrehozásához. A *bérlői* egy dedikált példánya, amely egy szervezet vagy az alkalmazás fejlesztői kap, amikor azok hozhat létre kapcsolatot a Microsoft Azure, Microsoft Intune vagy a Microsoft 365 regisztrál az Azure AD. Minden egyes Azure AD-bérlő nem azonos azzal, és külön, és más Azure AD-bérlők és a saját munkahelyi és iskolai identitások és alkalmazásregisztrációk.

Ha még nem rendelkezik Azure AD-bérlővel, hozzon létre egyet az alábbi lépésekkel.

## <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Bérlő létrehozása:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a fiókkal az Azure Red Hat OpenShift fürthöz társítani kívánja.
2. Nyissa meg a [Azure Active Directory panel](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) hozhat létre egy új bérlőt (más néven egy új *Azure Active Directory*).
3. Adjon meg egy **szervezetnevet**.
4. Adjon meg egy **kezdeti tartománynév**. Ez lesz *onmicrosoft.com* rá hozzáfűzve. Újból felhasználhatja a értéke *szervezetnevet* itt.
5. Válassza ki az országot vagy régiót, ahol a bérlői létrejön.
6. Kattintson a **Create** (Létrehozás) gombra.
7. Az Azure AD-bérlő létrehozása után válassza ki a **kattintson ide az új címtár kezeléséhez** hivatkozásra. A jobb felső sarokban az Azure Portal az új bérlő nevét kell megjelennie:  

    ![A portálon, a jobb felső sarokban a bérlő nevét megjelenítő képernyőkép][tenantcallout]  

8. Jegyezze fel a *bérlőazonosító* így később megadhatja, hová hozza létre az Azure Red Hat OpenShift fürt. A portálon meg kell jelennie az Azure Active Directory áttekintés panelen az új bérlő. Válassza ki **tulajdonságok** , és másolja az értéket a **címtár-azonosító**. Ezt az értéket hivatkozni fogunk `TENANT` a a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>További források

Tekintse meg [Azure Active Directory dokumentációjának](https://docs.microsoft.com/azure/active-directory/) kapcsolatos további információért [Azure AD-bérlőt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozzon létre egy egyszerű szolgáltatást, hozzon létre egy ügyfél titkos kulcs és a hitelesítési visszahívási URL-címe és egy új Active Directory-felhasználó létrehozása az Azure Red Hat OpenShift fürtön lévő alkalmazások teszteléséhez.

[Az Azure AD alkalmazás-objektum és a felhasználó létrehozása](howto-aad-app-configuration.md)