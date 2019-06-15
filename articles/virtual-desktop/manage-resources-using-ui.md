---
title: Felügyeleti eszköz – Azure
description: Hogyan telepítheti a Windows virtuális asztal előzetes-erőforrások kezeléséhez felhasználói felületi eszköz.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 3635cd422e4c7a064d3317401b734f3bc6ccb6c6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67136444"
---
# <a name="tutorial-deploy-a-management-tool"></a>Oktatóanyag: Felügyeleti eszköz üzembe helyezése

A felügyeleti eszköz felhasználói felület (UI) biztosít a Microsoft virtuális asztali előzetes-erőforrások kezeléséhez. Ebben az oktatóanyagban megismerheti, hogyan helyezheti üzembe, és a felügyeleti eszközzel csatlakozhat lesz.

>[!NOTE]
>Ezeket az utasításokat, amelyek a szervezet meglévő folyamatok használható Windows virtuális asztal előzetes jellemző konfiguráció esetén is.

## <a name="important-considerations"></a>Fontos szempontok

Mivel az alkalmazás engedélyt kell kapnia Windows virtuális asztal kommunikál, ez az eszköz nem támogatja a vállalatközi (B2B) forgatókönyvekhez. Minden Azure Active Directory (AAD) bérlői előfizetéshez kell a saját külön deployment kezelőeszköz.

A felügyeleti eszköze egy mintát. A Microsoft fontos biztonsági és minőségi frissítések biztosít. A [forráskódja elérhető a Githubon](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Ügyfeleink és partnereink számára javasolt, hogy az eszköz üzleti igényeinek megfelelően testre szabhatja.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Mit kell futtatnia az Azure Resource Manager-sablon

Az Azure Resource Manager-sablon üzembe helyezése előtt szüksége lesz egy Azure Active Directory-felhasználó üzembe helyezéséhez a felügyeleti felhasználói Felületét. Ez a felhasználó a következőket kell tennie:

- Letiltotta az Azure multi-factor Authentication (MFA)
- Hozhat létre erőforrásokat az Azure-előfizetésben
- Egy Azure AD-alkalmazás létrehozásához szükséges engedéllyel rendelkezik. Kövesse az alábbi lépéseket, ellenőrizze, hogy a felhasználó rendelkezik-e a [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Az Azure Resource Manager-sablon üzembe helyezése után érdemes indítsa el a felügyeleti felhasználói Felületét ellenőrzése. Ez a felhasználó a következőket kell tennie:
- Szerepkör-hozzárendelés, megtekintése és szerkesztése a Windows virtuális asztal bérlő rendelkezik

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Futtassa az Azure Resource Manager-sablonok kiépítéséhez a kezelőfelület

Mielőtt elkezdené, győződjön meg arról, a kiszolgáló és az ügyfél alkalmazások rendelkeznek a hozzájárulási meglátogatják a [Windows virtuális asztal hozzájárulás megadása lapon](https://rdweb.wvd.microsoft.com) számára az Azure Active Directory (AAD) jelöli.

Kövesse ezeket az utasításokat az Azure Resource Management-sablon üzembe helyezéséhez:

1. Nyissa meg a [GitHub Azure RDS-sablonok lap](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. A sablon üzembe helyezése az Azure-bA.
    - Ha egy vállalati-előfizetésben helyezi üzembe, görgessen lefelé, és válassza ki **üzembe helyezés az Azure**. Lásd: [Sablonparaméterek útmutatást](#guidance-for-template-parameters).
    - Ha a Cloud Solution Provider-előfizetésben helyezi üzembe, helyezze üzembe az Azure az alábbi lépésekkel:
        1. Görgessen lefelé, és kattintson a jobb gombbal **üzembe helyezés az Azure**, majd **másolási csatolás helye**.
        2. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömbben, és illessze be a hivatkozás van.
        3. Után <https://portal.azure.com/> és előtt a hashtaggel (#), adja meg egy kukacjelet (@) a bérlő tartományneve követ. Íme egy példa a formátumra: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Jelentkezzen be az Azure Portalon a Felhőszolgáltatói előfizetéshez rendszergazdai/közreműködői engedélyekkel rendelkező felhasználóként.
        5. Illessze be a címet a címsorba a szövegszerkesztőbe másolt hivatkozást.

### <a name="guidance-for-template-parameters"></a>Útmutató a sablon paraméterei
A következő adja meg a paramétereket az eszköz konfigurálásának módjáról:

- Ez a távoli asztali munkamenet-átvitelszervező URL-címe:  <https://rdbroker.wvd.microsoft.com/>
- Ez az erőforrás URL-címe:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- Az AAD-hitelesítő adatok használata a többtényezős hitelesítés le van tiltva, hogy jelentkezzen be az Azure-bA. Lásd: [kell futtatni az Azure Resource Manager-sablon](#what-you-need-to-run-the-azure-resource-manager-template).
- Használjon egy egyedi nevet, amely regisztrálva lesz az Azure Active Directory management Tool; Ha például Apr3UX.

## <a name="use-the-management-tool"></a>A felügyeleti eszközzel

Miután a GitHub Azure Resource Manager sablon befejeződik, egy erőforráscsoportot, két app services és a egy app service-csomag az Azure Portalon tartalmazó találja.

Kövesse az alábbi utasításokat, az eszköz elindításához:

1. Válassza ki az Azure App Services-erőforrás a megadott nevű (például Apr3UX) a sablonban, és keresse meg az URL-címhez társított Ha például <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Jelentkezzen be a virtuális asztali Windows hitelesítő adatok használatával.
3. Amikor a rendszer kéri, válassza ki a bérlő csoportot, válassza ki a **bérlői csoport alapértelmezett** a legördülő listából.

> [!NOTE]
> Ha egyéni bérlői csoport rendelkezik, adja meg manuálisan helyett a legördülő listából válassza a neve.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan helyezheti üzembe, és a felügyeleti eszköz csatlakoztatása, megismerheti a Azure Service Health szolgáltatással kapcsolatos problémák és állapot-tanácsadási információk figyelheti.

> [!div class="nextstepaction"]
> [Állítsa be a szolgáltatási riasztások az oktatóanyag](./set-up-service-alerts.md)
