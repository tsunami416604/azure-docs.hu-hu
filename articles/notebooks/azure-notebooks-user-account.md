---
title: Jelentkezzen be az Azure notebookok
description: Konfigurálja a felhasználói fiók Azure notebookokhoz Microsoft-fiókkal vagy egy munkahelyi vagy iskolai fiókot.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f95edde6a501cf33c45daccc2dbd2605c077217d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092608"
---
# <a name="your-user-account-for-azure-notebooks"></a>A felhasználói fiók Azure-notebookokhoz

Az Azure-jegyzetfüzeteket is használhatja, vagy egy felhasználói fiókkal történő bejelentkezés nélkül:

- Bejelentkezés nélkül is létrehozhat és futtathat jegyzetfüzeteket, de nem ürítheti a jegyzetfüzet vagy adatfájlok projektek részeként. Felhasználók, akik az Azure-jegyzetfüzetnek hivatkozást kap például élvezheti a notebook anélkül, hogy jelentkezzen be.
- Miután bejelentkezett, Azure notebookok megőrzi a fiókjához a projektek. Bejelentkezett felhasználók is egy felhasználói Azonosítót, amely lehetővé teszi, hogy a projektek és jegyzetfüzetek oszthatja meg másokkal.
  - Azure notebookokhoz használt fiókot is az Azure-előfizetéssel társítva, révén a további előnyökkel, például a hatékonyabb kiszolgálókon, jegyzetfüzetek futtatása személyes jegyzetfüzet létrehozása és ad engedélyt a notebookok egyéni felhasználók számára.

Notebookok Azure-ba történő bejelentkezéskor kell a Microsoft Account vagy egy "Munkahelyi vagy iskolai" fiók. Kér a fiók kiválasztásakor a **bejelentkezés** a notebookok lap jobb felső parancsot:

![Jelentkezzen be a parancs Azure notebookokhoz](media/accounts/sign-in-command.png)

Az Azure-jegyzetfüzetek teheti meg az összes munkahelyi társítva a bejelentkezéshez használt fióknak. Minden fiók is rendelkeznie kell egy egyedi felhasználói Azonosítót a [felhasználói profil](azure-notebooks-user-profile.md). Ennek eredményeképpen jelentkezhet be Azure-jegyzetfüzetek különböző fiókokkal Ha külön csoportok projektek és különálló biztosítani kell a. Egy data science csapat minden tagjának például előfordulhat mellett egyéni fiókot is használnak munkahelyi jelenleg a vállalat kívüli személyeknek megosztott csoport fiókként. Oktatók, hasonlóan egy olyan fiókkal, amely eltér a külső helyét, vagy a nyílt forráskódú használt fiók oktatási szerepkörük előfordulhat, hogy fenn.

## <a name="microsoft-accounts"></a>A Microsoft-fiókok

Jelentkezzen be Microsoft-termékek és szolgáltatások, például Windows, Azure, outlook.com, onedrive vállalati verzió és az XBox Live tetszőleges számú Microsoft-fiókok segítségével. Ha használja ezeket a szolgáltatásokat, valószínű, hogy már rendelkezik egy Microsoft Account, amely az Azure-jegyzetfüzeteket is használhatja.

Ha Ön nem tudja biztosan, válassza ki a **hozzon létre egy** parancsot a fiók használatával. Létrehozhat egy új Microsoft-fiók összes e-mail-címmel a bármely szolgáltatónál.

![A parancs egy új Microsoft-fiók létrehozása](media/accounts/create-new-microsoft-account.png)

Gyermek-fiókok esetében a hozzáférést az Azure-jegyzetfüzetek alapértelmezés szerint le van tiltva. A gyermek fiókkal történő bejelentkezés alább látható hibaüzenetet jeleníti meg:

![Hiba történt, amikor gyermek fiókkal próbál: Hiba történt, szülője letiltotta a hozzáférést](media/accounts/child-account-error.png)

Hozzáférés engedélyezése egy szülő a következő lépéseket kell tennie:

1. Látogasson el `https://account.live.com/mk` , és jelentkezzen be egy szülő-fiókkal.
1. A szóban forgó gyermek részén válassza **kezelése a gyermeke hozzáférhessen harmadik felek alkalmazásaihoz**.
1. A következő oldalon válassza ki a **hozzáférés engedélyezése**.
1. Használatakor a Gyermekfiók ezután notebookok Azure-ba való bejelentkezéshez, válassza ki a **Igen** az engedélyeket kér, amely akkor jelenik meg.

> [!Warning]
> Harmadik felek alkalmazásaihoz való hozzáférés engedélyezése az Azure-jegyzetfüzeteket is lehetővé teszi, hogy minden más külső alkalmazás hozzáférését. Szülők javasolja, hogy saját belátása szerint értékeli használja, amikor engedélyezi az eléréséhez, és előfordulhat, hogy azok gyermektevékenységet jobban figyelni szeretné.

## <a name="work-or-school-accounts"></a>Munkahelyi vagy iskolai fiókok

Munkahelyi vagy iskolai fiók engedélyezése a Microsoft felhőszolgáltatásai, például az Office 365, valamint olyan fiókkal jelentkezzen be Windows-tartományhoz csatlakoztatott számítógépen való eléréséhez a szervezet tagja a szervezet rendszergazdája által jön létre. Munkahelyi vagy iskolai fiók általában használ a szervezeti e-mail-címmel, például any-user@contoso.com.

Azure notebookok bejelentkezik egy munkahelyi vagy iskolai fiókkal lehet szükség rendszergazdai jóváhagyást, mert Azure notebookok gyűjti, vagy használja (de nem hozza nyilvánosságra) például a fiókhoz tartozó e-mail cím és a felhasználó böngészőben információkat. (Böngészési adatok segítségével optimalizálhatják a funkciókat népszerű használat alapján.)

Szervezeti fiók rendszergazdája kell megadnia a jóváhagyás a felhasználó nevében, ha a felhasználók korlátozva vannak a küldőnek külön-külön. Ebben az esetben felhasználók az "Ez az alkalmazás nem tud hozzáférni" üzenet jelenik meg:

!["Az alkalmazás nem tud hozzáférni" jelenik meg, amikor egy munkahelyi vagy iskolai fiókkal](media/accounts/consent-permissions-denied.png)

Rendszergazdai jóváhagyás megadásához használja a [rendszergazda hozzájárulást kérő lap](https://notebooks.azure.com/account/adminConsent), amely végigvezeti a folyamatot.

## <a name="next-steps"></a>További lépések  

> [!div class="nextstepaction"]
> [Szerkessze a profil és a felhasználói azonosító](azure-notebooks-user-profile.md)
