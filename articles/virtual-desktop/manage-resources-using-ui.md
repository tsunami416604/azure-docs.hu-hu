---
title: Felügyeleti eszköz üzembe helyezése Azure Resource Manager-sablonnal – Azure
description: Felhasználói felületeszköz telepítése egy Azure Resource Manager-sablonnal a Windows virtuális asztali erőforrások kezeléséhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dac17da4aa3d3d73ccbbc101cba4326a17fec9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127783"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Felügyeleti eszköz üzembe helyezése Azure Resource Manager-sablonnal

Ebben a cikkben található utasításokat ismerteti, hogyan telepítheti a felhasználói felület et egy Azure Resource Manager-sablon használatával.

## <a name="important-considerations"></a>Fontos szempontok

Mivel az alkalmazásnak beleegyezésre van szüksége a Windows virtuális asztal használatához, ez az eszköz nem támogatja a vállalkozások között (B2B) forgatókönyveket. Minden Egyes Azure Active Directory (AAD) bérlői előfizetés szüksége lesz a felügyeleti eszköz saját külön üzembe helyezéséhez.

Ez a felügyeleti eszköz egy példa. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. A [forráskód a GitHubon érhető el.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Az ügyfeleket és a partnereket arra ösztönzik, hogy az eszközt az üzleti igényeiknek megfelelően szabják testre.

A következő böngészők kompatibilisek a felügyeleti eszközzel:
- Google Chrome 68 vagy újabb
- Microsoft Edge 40.15063 vagy újabb
- Mozilla Firefox 52.0 vagy újabb
- Safari 10 vagy újabb verzió (csak macOS esetén)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Mire van szükség a felügyeleti eszköz telepítéséhez?

A felügyeleti eszköz üzembe helyezése előtt szüksége lesz egy Azure Active Directory (Azure AD) felhasználó ra egy alkalmazás regisztrációjának létrehozásához és a felügyeleti felhasználói felület üzembe helyezéséhez. Ennek a felhasználónak:

- Az Azure többtényezős hitelesítés (MFA) letiltása
- Engedéllyel rendelkezik erőforrások létrehozására az Azure-előfizetésben
- Engedéllyel rendelkezik egy Azure AD-alkalmazás létrehozásához. Az alábbi lépésekkel ellenőrizheti, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a [Szükséges engedélyek](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)című részben található utasításokat követve.

A felügyeleti eszköz telepítése és konfigurálása után javasoljuk, hogy kérje meg a felhasználót, hogy indítsa el a felügyeleti felhasználói felületet, hogy minden rendben legyen. A felügyeleti felhasználói felületet elvezető felhasználónak rendelkeznie kell egy szerepkör-hozzárendeléssel, amely lehetővé teszi számára a Windows virtuális asztal bérlőjének megtekintését vagy szerkesztését.

## <a name="deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezése

Mielőtt elkezdené, győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazások hozzájárulása a [Windows virtuális asztali hozzájárulási lapján](https://rdweb.wvd.microsoft.com) az Azure Active Directory (AAD) képviselt.

Az Azure Resource Management sablon telepítéséhez kövesse az alábbi utasításokat:

1. Nyissa meg a [GitHub Azure RDS-sablonok lapját.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)
2. Telepítse a sablont az Azure-ba.
    - Ha nagyvállalati előfizetésben telepít, görgessen le, és válassza **a Telepítés az Azure-ba**lehetőséget. 
    - Ha felhőszolgáltatói előfizetésben telepít, kövesse az alábbi utasításokat az Azure-ba való üzembe helyezéshez:
        1. Görgessen le, és kattintson a jobb gombbal **az Üzembe helyezés az Azure-ba**elemre, majd válassza a Hivatkozás **helyének másolása parancsot.**
        2. Nyisson meg egy szövegszerkesztőt, például a Jegyzettömböt, és illessze be oda a hivatkozást.
        3. Közvetlenül <https://portal.azure.com/> a hashtag (#) után és előtt adjon meg egy at jelet (@), amelyet a bérlői tartománynév követ. Íme egy példa a formátumra: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Jelentkezzen be az Azure Portalon rendszergazdai/közreműködői engedélyekkel rendelkező felhasználóként a Felhőszolgáltató-előfizetésbe.
        5. Illessze be a szövegszerkesztőbe másolt hivatkozást a címsorba.
3. A paraméterek megadásakor tegye a következőket:
    - Az **isServicePrincipal** paraméterhez válassza a **false**lehetőséget.
    - A hitelesítő adatok, adja meg az Azure AD hitelesítő adatok többtényezős hitelesítés le van tiltva. Ezek a hitelesítő adatok az Azure AD-alkalmazás és az Azure-erőforrások létrehozásához lesznek használva. További információ: [A Felügyeleti eszköz üzembe helyezéséhez szükséges témakörben](#what-you-need-to-deploy-the-management-tool)olvashat.
    - Az **applicationName,** használjon egyedi nevet az alkalmazás, amely regisztrálva lesz az Azure Active Directoryban. Ez a név a webalkalmazás URL-címéhez is használható lesz. Használhat például egy "Apr3UX" nevet.
4. Miután megadta a paramétereket, fogadja el a feltételeket, és válassza **a Vásárlás**lehetőséget.

## <a name="provide-consent-for-the-management-tool"></a>Hozzájárulás a kezelőeszközhöz

Miután a GitHub Azure Resource Manager sablon befejeződött, talál egy erőforráscsoportot, amely két alkalmazás-szolgáltatást és egy alkalmazásszolgáltatási csomagot tartalmaz az Azure Portalon.

Mielőtt bejelentkezne, és használja a felügyeleti eszközt, meg kell adnia a felügyeleti eszközhöz társított új Azure AD-alkalmazás beleegyezését. A hozzájárulás megadása lehetővé teszi, hogy a felügyeleti eszköz a Windows virtuális asztal kezelése hívásokat kezdeményezzen az eszközbe jelenleg bejelentkezett felhasználó nevében.

![Képernyőkép, amelyen a felhasználói felület kezelőeszközének beleegyezése esetén megadott engedélyek láthatók.](media/management-ui-delegated-permissions.png)

Annak meghatározásához, hogy melyik felhasználót használhatja az eszközbe való bejelentkezéshez, lépjen az [Azure Active Directory felhasználói beállítások lapjára,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) és vegye figyelembe, hogy a felhasználók milyen értéket **használhatnak a vállalati adatokhoz való hozzáféréshez a nevükben.**

![Képernyőkép, amely megmutatja, hogy a felhasználók csak a felhasználójuknak adhatnak-e hozzájárulást az alkalmazásokhoz.](media/management-ui-user-consent-allowed.png)

- Ha az érték **Értéke Igen,** az Azure Active Directorybármely felhasználói fiókjával bejelentkezhet, és csak az adott felhasználó beleegyezését adhatja meg. Ha azonban később egy másik felhasználóval jelentkezik be a felügyeleti eszközbe, újra végre kell hajtania ugyanazt a hozzájárulást.
- Ha az érték **Nem,** globális rendszergazdaként kell bejelentkeznie az Azure Active Directoryban, és rendszergazdai jóváhagyást kell megadnia a címtárban lévő összes felhasználónak. Más felhasználók nem szembesülnek hozzájárulási kéréssel.


Miután eldöntötte, hogy melyik felhasználót fogja használni a hozzájárulás megadásához, kövesse az alábbi utasításokat az eszközhöz való hozzájárulásmegadásához:

1. Nyissa meg az Azure-erőforrásokat, válassza ki az Azure App Services-erőforrást a sablonban megadott névvel (például Apr3UX), és keresse meg a hozzá társított URL-címet; például <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Jelentkezzen be a megfelelő Azure Active Directory felhasználói fiókkal.
3. Ha globális rendszergazdával hitelesített, most bejelölheti a **hozzájárulás jelölőnégyzetet a szervezet nevében.** A hozzájárulás megadásához válassza az **Elfogadás** lehetőséget.
   
   ![A felhasználó vagy a rendszergazda által látott teljes hozzájárulási oldalt megjelenítő képernyőkép.](media/management-ui-consent-page.png)

Ez most a felügyeleti eszközhöz vezet.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Miután megadta a szervezet vagy egy adott felhasználó beleegyezését, bármikor hozzáférhet a felügyeleti eszközhöz.

Az eszköz elindításához kövesse az alábbi utasításokat:

1. Válassza ki az Azure App Services-erőforrást a sablonban megadott névvel (például Apr3UX), és keresse meg a hozzá társított URL-címet; például <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Jelentkezzen be a Windows virtuális asztal hitelesítő adataival.
3. Amikor a rendszer kéri, hogy válasszon egy bérlői csoportot, válassza az **alapértelmezett bérlői csoport** lehetőséget a legördülő listából.
4. Ha az **Alapértelmezett bérlői csoport**lehetőséget választja, egy menünek kell megjelennie az ablak bal oldalán. Ebben a menüben keresse meg a bérlői csoport nevét, és jelölje ki.
  
  > [!NOTE]
  > Ha egyéni bérlői csoporttal rendelkezik, adja meg a nevet manuálisan, ahelyett, hogy a legördülő listából választana.

## <a name="report-issues"></a>Problémák bejelentése

Ha bármilyen probléma merül fel a felügyeleti eszközzel vagy más Windows virtuális asztali eszközökkel kapcsolatban, kövesse az [Azure Resource Manager távoli asztali szolgáltatásokhoz készült sablonjainak](https://github.com/Azure/RDS-Templates/blob/master/README.md) utasításait, és jelentse azokat a GitHubon.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan telepítheti és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja az Azure Service súgóját a szolgáltatási problémák és az egészségügyi tanácsok figyelésére. További információ: [A Szolgáltatásriasztások beállítása oktatóanyag.](./set-up-service-alerts.md)