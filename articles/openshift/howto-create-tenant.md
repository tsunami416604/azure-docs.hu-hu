---
title: Azure AD-bérlő létrehozása az Azure Red Hat OpenShift számára
description: Az alábbiakban bemutatjuk, hogyan hozhat létre egy Azure Active Directory (Azure AD) bérlőt a Microsoft Azure Red Hat OpenShift fürt üzemeltetéséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243691"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure AD-bérlő létrehozása az Azure Red Hat OpenShift számára

A Microsoft Azure Red Hat OpenShift egy [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) bérlőt igényel, amelyben létre kell hoznia a fürtöt. A *bérlő* az Azure AD egy dedikált példánya, amelyet egy szervezet vagy alkalmazásfejlesztő kap, amikor kapcsolatot hoz létre a Microsofttal az Azure, a Microsoft Intune vagy a Microsoft 365 regisztrálásával. Minden Azure AD-bérlő különbözik, és elkülönül a többi Azure AD-bérlőktől, és saját munkahelyi és iskolai identitásokkal és alkalmazásregisztrációkkal rendelkezik.

Ha még nem rendelkezik egy Azure AD-bérlő, kövesse az alábbi utasításokat hozzon létre egyet.

## <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Bérlő létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure Red Hat OpenShift-fürthöz társítani kívánt fiókhasználatával.
2. Nyissa meg az [Azure Active Directory panelt](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) egy új bérlő (más néven új *Azure Active Directory)* létrehozásához.
3. Adja meg **a szervezet nevét**.
4. Adja meg **a kezdeti tartománynevet**. Ez *lesz onmicrosoft.com* csatolni hozzá. Itt újra felhasználhatja a *Szervezet nevének* értékét.
5. Válassza ki azt az országot vagy régiót, ahol a bérlő létre jön.
6. Kattintson **a Létrehozás gombra.**
7. Az Azure AD-bérlő létrehozása után válassza **a Kattintson ide az új címtárkapcsolat kezeléséhez.** Az új bérlői névnek meg kell jelennie az Azure Portal jobb felső részén:  

    ![Képernyőkép a portálról, amely a bérlő nevét mutatja a jobb felső sarokban][tenantcallout]  

8. Jegyezze fel a *bérlői azonosítót,* így később megadhatja, hogy hol hozza létre az Azure Red Hat OpenShift-fürtjét. A portálon most meg kell jelennie az Azure Active Directory áttekintése panel az új bérlőhöz. Válassza a **Tulajdonságok lehetőséget,** és másolja a **címtárazonosító**értékét. Ezt az értéket az `TENANT` [Azure Red Hat OpenShift fürt létrehozása](tutorial-create-cluster.md) oktatóanyagban fogjuk hivatkozni.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Források

Az [Azure AD-bérlőkről](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory/) olvashat bővebben.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hozhat létre egyszerű szolgáltatást, hozhat létre egy ügyféltitkos és hitelesítési visszahívási URL-címet, és hogyan hozhat létre egy új Active Directory-felhasználót az Azure Red Hat OpenShift-fürtalkalmazások teszteléséhez.

[Azure AD-alkalmazásobjektum és -felhasználó létrehozása](howto-aad-app-configuration.md)
