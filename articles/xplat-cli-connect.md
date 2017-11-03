---
title: Jelentkezzen be az Azure a parancssori |} Microsoft Docs
description: "Csatlakozás az Azure-előfizetéshez az Azure parancssori felület (CLI) Mac, Linux és Windows rendszerekhez"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Jelentkezzen be az Azure az az Azure parancssori felület
Az Azure parancssori felület nyílt forráskódú, platformok közötti parancsokat az Azure-erőforrások használata. Ez a cikk ismerteti a különböző módokon csatlakozni az Azure-előfizetéshez az Azure CLI Azure-fiók hitelesítő adatainak megadását:

* Futtassa a `azure login` CLI paranccsal próbál hitelesítést Azure Active Directoryn keresztül. Ez a módszer hozzáférést biztosító parancssori felület parancsait mindkét [módok parancs](#cli-command-modes). További beállítások nélkül parancs futtatásakor `azure login` kéri, hogy folytassa a naplózást interaktív webes portálon keresztül. A további `azure login` beállítások parancsot, tekintse meg a cikket, vagy a típus forgatókönyvek `azure login --help`.
* Ha csak szeretné használni az Azure szolgáltatásfelügyelet módban parancssori felület parancsait (nem ajánlott az új telepítések esetén), töltse le, és telepítse a közzétételi beállítások fájlja a számítógépen.

Ha még nem telepítette a CLI-t, tekintse meg [az Azure parancssori felület telepítése](cli-install-nodejs.md). Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/free/).

Másik fiók identitások és az Azure-előfizetések kapcsolatos háttér, lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>1. forgatókönyv: azure bejelentkezési interaktív bejelentkezéskor
Az egyes fiókok számára, a parancssori felület ehhez futtatnia kell `azure login` majd folytassa a bejelentkezési folyamat egy webes portál nevezett folyamat használatával böngészővel *interaktív bejelentkezési*. A leggyakoribb oka, hogy munkahelyi vagy iskolai fiókkal (más néven egy *szervezeti fiók*), amely be van állítva a többtényezős hitelesítés szükséges. Is használhatja, interaktív bejelentkezés Microsoft-fiókjával, ha szeretné használni az erőforrás-kezelő módban parancsok.

Interaktív bejelentkezési adatai könnyen: típus `azure login` --nélkül bármely beállításai – a következő példában látható módon:

```
azure login
```                                                                                             

Az eredmény jelenik meg a következő hasonlót:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Másolja a kódot, a parancs kimenetében felkínált, és nyissa meg a böngészőben http://aka.ms/devicelogin vagy más lap, ha meg van adva. (Is nyisson meg egy böngészőt, ugyanazon a számítógépen, vagy egy másik számítógépen vagy eszközön.) Írja be a kódot, és ezután kéri a felhasználónevet és jelszót adja meg a használni kívánt azonosító. A folyamat befejezése után a parancs-rendszerhéj befejezi a bejelentkezés. Ez lehet hasonlót:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Az interaktív bejelentkezés hitelesítés és engedélyezés történik az Azure Active Directoryval. Ha egy Microsoft-fiók identitást használja, a bejelentkezési folyamat az Azure Active Directory alapértelmezett tartomány fér hozzá. (Ha volt egy ingyenes Azure-fiókot, Azure Active Directory automatikusan létrejön egy alapértelmezett tartományt a fiókhoz.)
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>2. forgatókönyv: azure jelentkezzen be egy felhasználónevet és jelszót
Használja a `azure login` parancsot a felhasználónév (`-u`) paraméter hitelesítésére, miközben szeretné használni a munkahelyi vagy iskolai fiókkal, amely nem igényel a többtényezős hitelesítést. Kéri a jelszót a parancssorból (vagy opcionálisan átadhatók a jelszót a további paraméterként a `azure login` parancs). A következő példa egy szervezeti fiók felhasználónevét adja át:

    azure login -u myUserName@contoso.onmicrosoft.com

Ezután a rendszer kéri a jelszót:

    info:    Executing command login
    Password: *********

A bejelentkezési folyamat majd befejeződik.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Ha ezeket a hitelesítő adatokat az első alkalommal bejelentkezik, a program győződjön meg arról, hogy szeretné-e a gyorsítótár egy hitelesítési jogkivonatot kérni. Ez a kérdés is akkor fordul elő, ha korábban már használta a `azure logout` (a cikk későbbi részében leírt) parancsot. Az automatizálási esetekben kérése megkerülése, futtassa a `azure login` rendelkező a `-q` paraméter.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>3. forgatókönyv: azure jelentkezzen be egy egyszerű szolgáltatásnév
Ha az Active Directory-alkalmazás szolgáltatásnevet létrehozni, és a szolgáltatás egyszerű megfelelő jogosultságokkal rendelkezik az előfizetés, használhatja a `azure login` paranccsal próbál hitelesítést a szolgáltatásnevet. A forgatókönyvtől függően biztosíthatja az egyszerű szolgáltatás hitelesítő adatait az explicit paraméterként a `azure login` parancsot. A következő parancs például az egyszerű szolgáltatásnév és az Active Directory-bérlőazonosító beszerzése továbbítja:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Majd kéri a jelszót. Adja meg egy parancssori parancsprogram vagy alkalmazás kód, vagy tanúsítványt használnak a nem interaktív automatizálási esetekben az egyszerű hitelesítést is. Részletek és példák: [hitelesítéséhez az Azure Resource Manager szolgáltatásnevet](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>4. forgatókönyv: Használja a közzétételi beállítások fájlja
Ha csak szeretné használni az Azure szolgáltatásfelügyelet módban parancssori felület parancsait (például az Azure virtuális gépek telepítése a klasszikus üzembe helyezési modellel), akkor csatlakozhatnak a közzétételi beállítások fájlja. Ez a módszer tanúsítványt telepít a helyi számítógépen, amely lehetővé teszi a végrehajtását kapcsolatos felügyeleti feladatok, ha az előfizetés és a tanúsítvány érvényes.

* **A közzétételi beállítások fájlja letöltése** a fiókot, győződjön meg arról, hogy a CLI szolgáltatásfelügyelet módban írja be a `azure config mode asm`. Ezután futtassa a következő parancsot:

        azure account download

Ez megnyitja az alapértelmezett böngészőt, és megkéri, hogy jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com). Miután bejelentkezik, egy `.publishsettings` fájl letöltését. Jegyezze fel a letöltött fájl elérési útját.

> [!NOTE]
> Ha a fiók több Azure Active Directory-bérlő tartozik, a program kérheti töltse le a közzétételi beállítások fájlja a kívánt Active Directory kiválasztása.
>
>

A kijelölt a letöltési oldal használatával, vagy a klasszikus Azure portál felkeresésével a kijelölt Active Directory válik a klasszikus portál és a letöltési oldal által használt alapértelmezett érték. Egy alapértelmezett létrehozása után megjelenik a szöveges "**kattintson ide a kijelölt lapra való visszatéréshez**" a letöltési oldal tetején. A megadott hivatkozás segítségével a kijelölés laphoz való visszatéréshez.

* **A közzétételi beállítások fájlja importálandó**, a következő parancsot:

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Importálás után a közzétételi beállítások, törölje a `.publishsettings` fájlt. Az Azure parancssori felület már nem szükséges, és biztonsági kockázatot jelent, felhasználhatók az előfizetés eléréséhez.
>
>

## <a name="cli-command-modes"></a>Parancssori felület üzemmódok
Az Azure parancssori felület két parancs üzemmódot biztosít az Azure-erőforrások, különböző parancs készletekkel használata:

* **Resource Manager módra** – az Azure-erőforrások a Resource Manager üzembe helyezési modellel működik. Ebben a módban, hogy futtassa `azure config mode arm`.
* **Szolgáltatásfelügyeleti módban** – az Azure-erőforrások a klasszikus üzembe helyezési modellel működik. Ebben a módban, hogy futtassa `azure config mode asm`.

Első telepítésekor a CLI jelenlegi kiadásában erőforrás-kezelő módban van.

> [!NOTE]
> A Resource Manager módra és a szolgáltatásfelügyeleti módban kölcsönösen kizárják egymást. Ez azt jelenti, hogy egy módban létrehozott erőforrások nem tudja felügyelni a üzemmódról.
>
>

## <a name="multiple-subscriptions"></a>Több előfizetés
Ha több Azure-előfizetéssel rendelkezik, csatlakozás Azure hozzáférést a hitelesítő adatok társított előfizetéseket. Egy előfizetés alapértelmezés szerint kiválasztva, és használja az Azure parancssori felület műveletek végrehajtása során. Megtekintheti az előfizetéseket, beleértve a jelenlegi alapértelmezett előfizetés, használja a `azure account list` parancsot. Ez a parancs visszaadja a következő információkat:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

A fenti listában a **aktuális** oszlopban látható, hogy az Azure-al-1 alapértelmezett előfizetésben. Az alapértelmezett előfizetés módosításához használja a `azure account set` parancsot, és adja meg az alapértelmezettként használni kívánt előfizetést. Példa:

    azure account set Azure-sub-2

Az alapértelmezett előfizetés az Azure-al-2 értékre változik.

> [!NOTE]
> Az alapértelmezett előfizetés módosítása azonnal érvénybe lép, és globális módosítva; új Azure parancssori felület parancsait, hogy parancssori ugyanazon vagy másik példányt, futtassa az új alapértelmezett előfizetés használatára.
>
>

Ha szeretné használni az Azure parancssori felület nem alapértelmezett előfizetés, de nem szeretné módosítani a jelenlegi alapértelmezett, használhatja a `--subscription` ahhoz, hogy a parancs lehetőséget, majd adja meg a művelethez használni kívánt előfizetés nevét.

Miután csatlakozott az Azure-előfizetéshez, indítsa el az Azure parancssori felület parancsait használata Azure-erőforrások.

## <a name="storage-of-cli-settings"></a>Parancssori beállítások tárolására
Hogy jelentkezzen be a `azure login` parancs vagy importálása a közzétételi beállítások, a CLI-profil és a naplók tárolja egy `.azure` könyvtárban található a `user` könyvtár. A `user` könyvtár az operációs rendszer által védett. Javasoljuk azonban, hogy Ön további lépésekkel titkosíthatja a `user` könyvtár. Ezt a következő módokon teheti meg:

* Windows-könyvtár tulajdonságainak módosítása vagy a BitLocker használja.
* A Mac kapcsolja be a FileVault könyvtár.
* Ubuntu: Használjon titkosított kezdőmappát. Más Linux terjesztésekről hasonló funkciókat kínál.

## <a name="logging-out"></a>Kijelentkezés
Jelentkezzen ki, használja a következő parancsot:

    azure logout -u <username>

Ha a fiókjához társított előfizetéseket csak hitelesítése az Active Directory kijelentkezés törli az előfizetési adatok a helyi profilból. Azonban ha egy közzétételi beállítások fájlja is importálta a előfizetésekhez, kijelentkezés csak törli az Active Directory kapcsolatos információkat a helyi profilt.

## <a name="next-steps"></a>Következő lépések
* Azure parancssori felület parancsai használni, lásd: [Azure parancssori felület parancsait erőforrás-kezelő módban](virtual-machines/azure-cli-arm-commands.md) és [Azure parancssori felület parancsait szolgáltatásfelügyelet módban](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* További tudnivalók az Azure parancssori felület, letöltési forráskódját, kapcsolatos problémákat, vagy a projekt hozzájárul, látogasson el a [az Azure parancssori felület GitHub-tárházban](https://github.com/azure/azure-xplat-cli).
* Ha az Azure CLI vagy Azure problémákat tapasztal, keresse fel a [Azure fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
