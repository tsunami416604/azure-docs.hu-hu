---
title: Azure AD-bérlő létrehozása az Azure Red Hat OpenShift
description: Ebből a témakörből megtudhatja, hogyan hozhat létre Azure Active Directory (Azure AD) bérlőt a Microsoft Azure Red Hat OpenShift-fürt üzemeltetéséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 775aa44043e9c8900bd0c040ab603736314ea34e
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469983"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure AD-bérlő létrehozása az Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift olyan [Azure Active Directory (Azure ad)](../active-directory/develop/quickstart-create-new-tenant.md) bérlőt igényel, amelyben létre kívánja hozni a fürtöt. A *bérlő* az Azure ad dedikált példánya, amelyet a szervezet vagy az alkalmazás fejlesztője kap, amikor kapcsolatot hoz létre a Microsofttal az Azure-ra, Microsoft Intunera vagy Microsoft 365re való regisztráláskor. Minden Azure AD-bérlő különálló, és elkülönül a többi Azure AD-bérlőtől, és saját munkahelyi és iskolai identitásokkal és alkalmazás-regisztrációval rendelkezik.

Ha még nem rendelkezik Azure AD-Bérlővel, az alábbi utasításokat követve hozhat létre egyet.

## <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Bérlő létrehozása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure Red Hat OpenShift-fürthöz társítandó fiók használatával.
2. A [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) panel megnyitásával hozzon létre egy új bérlőt (más néven új *Azure Active Directory*).
3. Adja meg a **szervezet nevét**.
4. Adja meg a **kezdeti tartománynevet**. Ehhez hozzá kell fűzni a *onmicrosoft.com* . Itt újra felhasználhatja a *szervezet nevének* értékét.
5. Válassza ki azt az országot vagy régiót, ahol létre kívánja hozni a bérlőt.
6. Kattintson a **Létrehozás** gombra.
7. Miután létrehozta az Azure AD-bérlőt, válassza a **kattintson ide az új címtár-hivatkozás kezeléséhez** . Az új bérlő nevét a Azure Portal jobb felső sarkában kell megjeleníteni:  

    ![Képernyőkép a bérlő nevét bemutató portálról a jobb felső sarokban][tenantcallout]  

8. Jegyezze fel a *bérlő azonosítóját* , hogy később meg tudja határozni, hová hozza létre az Azure Red Hat OpenShift-fürtöt. A portálon ekkor megjelenik az új bérlő Azure Active Directory áttekintés panelje. Válassza a **Tulajdonságok** lehetőséget, és másolja a **címtár-azonosító**értékét. Erre az értékre `TENANT` az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagban talál.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>További források

Az [Azure ad-Bérlővel](../active-directory/develop/quickstart-create-new-tenant.md)kapcsolatos további információkért tekintse meg [Azure Active Directory dokumentációját](../active-directory/index.yml) .

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhat létre egyszerű szolgáltatásnevet, hogyan hozhat létre egy ügyfél-titkos és hitelesítési visszahívási URL-címet, és hogyan hozhat létre új Active Directory felhasználót az Azure Red Hat OpenShift-fürtön futó alkalmazások teszteléséhez.

[Azure AD-alkalmazásobjektum és -felhasználó létrehozása](howto-aad-app-configuration.md)