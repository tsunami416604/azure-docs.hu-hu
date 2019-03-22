---
title: Test Drive üzemeltetett |} A Microsoft Docs
description: Egy karbantartása a Marketplace-en üzemeltetett Test Drive-telepítés
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5057c0c781cb9ec60ecde7dd3f4bf96089b902df
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312682"
---
# <a name="hosted-test-drive"></a>Üzemeltetett kipróbálása

Egy üzemeltetett Test Drive megszűnnek a telepítő által a Microsoft üzemeltetési és karbantartását hajtja végre a Test Drive felhasználói kiépítésének és megszüntetésének biztosítása. Ez a cikk a kiadók, akik az ajánlatot az appsource-on vagy hoz létre egy újat, és kíván kíván felajánlani üzemeltetett próbálja ki, amely csatlakozik egy Dynamics 365 for Customer Engagement, Dynamics 365 for Finance és Operations vagy a Dynamics 365 Business Central a példány.

## <a name="how-to-publish-a-test-drive"></a>A Test Drive közzététele

Keresse meg az ajánlat meglévő vagy új ajánlat létrehozása.

Az oldalsó menüben válassza ki a Test Drive lehetőséget.

Válassza ki \'Igen\' a \'engedélyezése egy Test Drive\' lehetőséget.

Adja meg a következő mezőket a \'részletek\' szakaszban.

- **Leírás**: A Test Drive áttekintést nyújtanak. Ez a szöveg a felhasználó nem jelenik meg, amíg a Test Drive kiépítése folyamatban van. Ez a mező támogatja a HTML, ha lehetővé szeretné tenni formátumú tartalmat.
- **Felhasználó manuális**: Töltse fel a részletes felhasználói manuális (.pdf típusú fájl), amely segít a Test Drive felhasználóknak az alkalmazás használatához.
- **Tesztelje a meghajtó – bemutató videó**: Szükség esetén töltse fel egy videót, amely bemutatja az alkalmazást.

Támogatás az AppSource engedély kiépítése és a Test Drive felhasználókat a bérlőben található utasításokat követve megszüntetése [Itt](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Ebben a lépésben fog létrehozni a \'Azure AD App Id\' és \'Azure AD Alkalmazáskulcs\' alább felsorolt értékeket.

Adja meg a következő mezőket a \'műszaki konfigurációs\' szakaszban:

- **Test Drive típusú**: Válasszon \'Microsoft Hosted (például a Dynamics 365 for Customer Engagement) "lehetőséget. Ez jelzi, hogy a Microsoft üzemeltetése és karbantartása hajtja végre a Test Drive felhasználói kiépítésének és megszüntetésének biztosítása.
- **Max. egyidejű Tesztverzió**: Adja meg a mezőben, amelyeken egy aktív Test Drive time lekérdezhet az egyidejű felhasználók száma. Minden felhasználó fog felhasználni a Dynamics-licenc, amíg a Test Drive aktív, így biztosíthatja, hogy a felhasználók a Test Drive legalább ennyi Dynamics licenceket kell. Ajánlott érték 3 – 5.
- **Tesztelje a meghajtó időtartama (óra)**: Adja meg a mezőben órák száma, a Test Drive lesz az aktív felhasználókat. Ennyi óra elteltével a felhasználó fogja vonni az üzemből bérlőjéből. Javasolt érték 2 – 24 óra alkalmazásához összetettségétől függően. A felhasználó mindig kérheti egy másik Test Drive, ha azok ideje elfogyott és a Test Drive újból elérhető.
- **Példány URL-cím**: Adjon meg egy URL-címet, amely a Test Drive felhasználói kezdetben a felhasználóregisztráció, amikor elindítja a Test Drive. Ez általában a Dynamics 365-példány, amelyen az alkalmazás URL-CÍMÉT és mintaadatok telepítve. Példaérték: https:\//testdrive.crm.dynamics.com
- **Az Azure AD-bérlő azonosítója**: Adja meg a Dynamics 365-példány az Azure-bérlő azonosítója. Lekérje ezt az értéket, jelentkezzen be az Azure Portalon, és keresse meg a \'Azure Active Directory\'  - \> menü panelről – tulajdonságok kiválasztása\> másolja a címtár-azonosító. Példaérték: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD-alkalmazás azonosítója**: A 7. lépésben létrehozott Azure AD-alkalmazás Csomagazonosítóját. \ Példaérték: 53852862-a2ae-4e43-9461-faa49650a096
- **Az Azure AD-Alkalmazáskulcs**: A létrehozott Azure AD-alkalmazás titkos a 7. lépés. \ Példaérték: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Az Azure AD-bérlő neve**: Adja meg az Azure-bérlő nevét, a Dynamics 365-példány. A formátumot használja \<tenantname.\> onmicrosoft.com. Példaérték: testdrive.onmicrosoft.com
- **Webes API URL-cím példány**: Adja meg a Dynamics 365-példány a webes API URL-címe. Ezt az értéket lekérheti azokat a Microsoft Dynamics 365-példány, és lépjen a beállítások -\> testreszabása –\> fejlesztői erőforrások –\> példány webes API-t (az URL-cím másolása). Példaérték: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Szerepkör neve**: Adja meg az egyéni Dynamics 365 biztonsági szerepkör Test Drive létrehozott nevét. Ez olyan, amely hozzá lesz rendelve a Test Drive felhasználói szerepkör. Példaérték: testdriverole

## <a name="next-steps"></a>További lépések

Ha készen áll **közzététele** az ajánlatot, az alkalmazás lejárt tanúsítvány, után egy **előzetes** az Ön ajánlatát. Indítsa el a Test Drive a felhasználói felületen, és ellenőrizze, hogy a Tesztverzió e megfelelően. Ha úgy gondolja, hogy tisztában az előzetes verzió ajánlathoz, most már, ideje **élesben!**
