---
title: Hosted Test Drive | Azure Piactér
description: Hogyan kell beállítani egy fenntartott Marketplace Hosted Test Drive
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278263"
---
# <a name="hosted-test-drive"></a>Szolgáltatott tesztverzió

A üzemeltetett tesztmeghajtó eltávolítja a Microsoft hosting által végzett telepítés összetettségét, és fenntartja a Test Drive felhasználói kiépítést és megszüntetést végző szolgáltatást. Ez a cikk azoknak a kiadóknak szól, akik ajánlattal rendelkeznek az AppSource-on, vagy újat építenek, és egy hosted test drive-ot szeretnének kínálni, amely összeköti a Dynamics 365 for Customer Engagement, a Dynamics 365 for Finance and Operations vagy a Dynamics 365 Business Central alkalmazást. Példány.

## <a name="how-to-publish-a-test-drive"></a>Tesztvezetés közzététele

Keresse meg a meglévő ajánlatot, vagy hozzon létre egy új ajánlatot.

Válassza a Tesztvezetés lehetőséget az oldalsó menüből.

Válassza \'\' az \'Igen lehetőséget\' a Tesztvezetés engedélyezése beállításhoz.

Adja meg a \'következő\' mezőket a Részletek szakaszban.

- **Leírás**: Áttekintést nyújt a tesztvezetésről. Ez a szöveg jelenik meg a felhasználó számára, miközben a Test Drive kiépítése folyamatban van. Ez a mező támogatja a HTML-kódot, ha formázott tartalmat szeretne megadni.
- **Használati utasítás:** Töltsön fel egy részletes felhasználói kézikönyvet (.pdf típusú fájlt), amely segít a Test Drive-felhasználóknak megérteni, hogyan kell használni az alkalmazást.
- **Test Drive Demo Video**: Tetszés szerint tölts fel egy videót, amely bemutatja az alkalmazást.

Adjon appsource-engedélyt a testmeghajtó-felhasználók bérlőben való kiépítésére és megszüntetésére az [itt](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)található utasítások használatával.

Ebben a lépésben az \'Azure AD\' App \'Id és\' az Azure AD App Key értékek et az alábbiakban említett.

Adja meg a \'műszaki\' konfiguráció szakasz következő mezőit:

- **Tesztmeghajtó típusa:** \'Válassza a Microsoft Hosted (például Dynamics 365 for Customer Engagement) lehetőséget. Ez azt jelzi, hogy a Microsoft üzemelteti és karbantartja a Test Drive felhasználói kiépítést és megszüntetést végző szolgáltatást.
- **Egyidejű tesztmeghajtók**maximális száma: Állítsa be ezt a mezőt azon egyidejű felhasználók számára, akik egy adott időpontban aktív tesztvezetést használhatnak. Minden felhasználó egy Dynamics-licencet használ fel, amíg a Tesztmeghajtó aktív, ezért gondoskodnia kell arról, hogy legalább ennyi Dynamics-licenc áll-e rendelkezésre a Test Drive-felhasználók számára. Ajánlott értéke 3-5.
- **Tesztvezetés időtartama (óra)**: Állítsa be ezt a mezőt arra, hogy a felhasználók tesztmeghajtója hány órát fog aktív. Ennyi óra elteltével a felhasználó lesz deprovisioned a bérlőtől. Az alkalmazás összetettségétől függően 2-24 órás ajánlott érték. A felhasználó bármikor kérhet egy másik tesztmeghajtót, ha kifut az időből, és újra hozzá szeretne férni a tesztmeghajtóhoz.
- **Példány URL-címe**: Adjon meg egy URL-címet, amelyre a Tesztmeghajtó felhasználója először a tesztmeghajtó indításakor navigál. Ez általában a Dynamics 365-példány URL-címe, amelyre az alkalmazás és a mintaadatok telepítve vannak. Példa Érték:\/https: /testdrive.crm.dynamics.com
- **Azure AD-bérlői azonosító:** Adja meg az Azure Tenant azonosítóját a Dynamics 365-példányhoz. Ennek az értéknek a beolvasásához \'jelentkezzen be\'  - \> az Azure Portalra, és keresse meg az Azure Active Directory kijelölési tulajdonságokat a menüpanelről –\> Másolja a címtárazonosítót. Példa érték: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD App ID:** A 7.
- **Azure AD alkalmazáskulcs:** Titkos a 7.
- **Azure AD-bérlő neve:** Adja meg az Azure Tenant nevét a Dynamics 365-példányhoz. Használja a \<bérlőnév formátumát. \>onmicrosoft.com. Példa Érték: testdrive.onmicrosoft.com
- **Példány webes API-címe:** Adja meg a Webes API URL-címét a Dynamics 365-példányhoz. Ezt az értéket a Microsoft Dynamics 365 példányba való\> bejelentkezéssel és a Setting - Customization -\> Developer Resources -\> Instance Web API (URL másolása) című lapra való navigálással szerezheti be. Példa érték:\/https: /testdrive.crm.dynamics.com/api/data/v9.0
- **Szerepkör neve**: Adja meg a Tesztmeghajtóhoz létrehozott egyéni Dynamics 365 biztonsági szerepkör nevét. Ez az a szerepkör, amelyet a rendszer a tesztvezetés során a felhasználókhoz rendel. Példa Érték: testdriverole

## <a name="next-steps"></a>További lépések

Amikor készen áll az ajánlat **közzétételére,** miután az alkalmazás megfelelt a minősítésnek, megtekintheti az ajánlat **előnézetét.** Indítsa el a tesztmeghajtót a felhasználói felületen, és ellenőrizze, hogy a tesztmeghajtók megfelelően futnak-e. Ha már jól érzi magát az előnézeti ajánlatával, most itt az ideje, hogy **élőben induljon!**
