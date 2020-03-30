---
title: Bejelentkezés az Azure Notebookelőzetes verzióba
description: Jelentkezzen be gyorsan az Azure Notebookelőzetes verzióba, és állítson be egy felhasználói azonosítót, amellyel elérheti a mentett projekteket, és megoszthatja a jegyzetfüzeteket másokkal.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75647016"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Rövid útmutató: Jelentkezzen be, és állítson be egy felhasználói azonosítót az Azure Notebookok előzetes verziójához

Bár az Azure Notebookok bejelentkezés nélkül mindig megtekintheti, be kell jelentkeznie a jegyzetfüzetek futtatásához, hozzáférhet a mentett projektekhez és jegyzetfüzetekhez, és megoszthatja jegyzetfüzeteit másokkal.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Bejelentkezés

1. Válassza a **Bejelentkezés** lehetőséget [a notebooks.azure.com](https://notebooks.azure.com/)jobb felső részén.

    ![A bejelentkezési parancs helye az Azure-jegyzetfüzetekben](media/accounts/sign-in-command.png)

1. Amikor a rendszer kéri, adja meg egy Microsoft-fiók, illetve egy munkahelyi vagy iskolai fiók e-mail címét, és válassza a **Tovább**gombot. A fióktípusok leírása az [Azure Notebookok felhasználói fiókjában](azure-notebooks-user-account.md)található. Ha nem rendelkezik Microsoft-fiókkal, vagy kifejezetten az Azure-jegyzetfüzetekhez szeretné használni, válassza **a Létrehozás lehetőséget:**

    ![Új Microsoft-fiók parancs létrehozása bejelentkezési parancsban](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Ha olyan e-mail címmel próbál új fiókot létrehozni, amelyhez már tartozik fiók, a következő üzenet jelenhet meg: "Itt nem regisztrálhat munkahelyi vagy iskolai e-mail címmel. Használjon személyes e-mailt, például a Gmailt vagy a Yahoo!-t, vagy szerezzen be egy új Outlook-e-mailt." Ebben az esetben próbáljon meg új fiók létrehozása nélkül bejelentkezni a munkahelyi e-mail címmel.

1. Amikor a rendszer kéri, adja meg a jelszót.

1. Ha első alkalommal jelentkezik be, az Azure Notebooks engedélyt kér a fiók eléréséhez. A folytatáshoz válassza az **Igen** lehetőséget:

    ![Fiókengedélyek – kérdés](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Felhasználói azonosító beállítása

1. Az első bejelentkezéskor egy ideiglenes felhasználói azonosítót kap, például "anon-idrca3".In First sign in, you're assigned a temporary user ID like "anon-idrca3". Ha egy "anon-" (anon-" típusú felhasználói azonosítóval rendelkezik, az Azure Notebooks kéri, hogy hozzon létre egy saját azonosítót. A felhasználói azonosítót a rendszer minden beszerzett URL-címben használja a projektek és jegyzetfüzetek megosztásához, ezért válasszon valamit, ami egyedi és értelmezhető az Ön számára.

    ![Az Azure-jegyzetfüzetek felhasználói azonosítójának megadására vonatkozó kérdés](media/accounts/create-user-id.png)

    Ha a **Nincs köszönet**lehetőséget választja, az Azure Notebooks továbbra is kéri a felhasználói azonosítót minden bejelentkezéskor. A felhasználói azonosító bármikor beállítható a [felhasználói profilban.](azure-notebooks-user-profile.md)

1. A sikeres bejelentkezés tkövetően az Azure Notebooks a nyilvános profil lapra lép, amelyen a **Profiladatok szerkesztése** lehetőséget választva kitöltheti a többi adatot (további információkért lásd [a profil- és felhasználói azonosítóját):](azure-notebooks-user-profile.md)

    ![Az Azure Notebooks profillap kezdeti nézete](media/accounts/profile-page-new.png)

> [!NOTE]
> Ha a következő üzenet jelenik meg: "A felhasználói azonosító már használatban van", próbálkozzon egy másik azonosítóval. A felhasználói azonosítók az összes Azure Notebook-fiókban egyediek, és az Azure Notebooks bizonyos felhasználói azonosítókat is fenntart, például a Microsoft márkaneveket.

## <a name="sign-out"></a>Kijelentkezés

A kijelentkezéshez válassza ki a felhasználónevét a lap jobb felső részén, majd válassza a **Kijelentkezés**lehetőséget:

![A kijelentkezési parancs helye az Azure Notebookokon](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Jegyzetfüzet létrehozása és megosztása](quickstart-create-share-jupyter-notebook.md)
