---
title: Bejelentkezés Azure Notebooks előzetes verzióra
description: Gyorsan bejelentkezhet Azure Notebooks előzetes verzióra, és beállíthat egy felhasználói azonosítót, amely lehetővé teszi a mentett projektek elérését és a jegyzetfüzetek másokkal való megosztását.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: b6572a7c0b965b2b72916db577b47eff4f1921c0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647016"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Gyors útmutató: bejelentkezés és felhasználói azonosító beállítása Azure Notebooks előzetes verzióhoz

Habár bármikor megtekintheti Azure Notebooks bejelentkezés nélkül, be kell jelentkeznie a jegyzetfüzetek futtatásához, a mentett projektekhez és jegyzetfüzetekhez való hozzáféréshez, valamint a notebookok megosztásához.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="sign-in"></a>Bejelentkezés

1. Válassza a **Bejelentkezés** lehetőséget a [Notebooks.Azure.com](https://notebooks.azure.com/)jobb felső sarkában.

    ![Bejelentkezési parancs helye Azure Notebooks](media/accounts/sign-in-command.png)

1. Ha a rendszer kéri, adja meg egy Microsoft-fiók vagy egy munkahelyi vagy iskolai fiók e-mail-címét, és kattintson a **Tovább gombra**. A fiók típusainak ismertetését [Azure Notebooks felhasználói fiókjában](azure-notebooks-user-account.md)találja. Ha nem rendelkezik Microsoft-fiókkal, vagy kifejezetten a Azure Notebooks használatára szeretné használni az egyiket, válassza a **Létrehozás**lehetőséget:

    ![Új Microsoft-fiók parancs létrehozása a bejelentkezési kérésben](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Ha olyan e-mail-címmel próbál meg létrehozni egy új fiókot, amelyhez már tartozik fiók társítva, akkor a következő üzenet jelenhet meg: "nem lehet regisztrálni egy munkahelyi vagy iskolai e-mail-címmel. Használjon személyes e-maileket, például a Gmailt vagy a Yahoo!-t, vagy szerezzen be egy új Outlook e-mail címet. " Ebben az esetben próbáljon meg bejelentkezni a munkahelyi e-mail-címmel új fiók létrehozása nélkül.

1. Amikor a rendszer kéri, adja meg a jelszót.

1. Ha első alkalommal jelentkezik be, Azure Notebooks engedélyt kér a fiókja eléréséhez. A folytatáshoz válassza az **Igen** lehetőséget:

    ![Fiók engedélyeinek kérése](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Felhasználói azonosító beállítása

1. Az első bejelentkezéskor egy ideiglenes felhasználói azonosítót kap, például: "Anon-idrca3". Ha olyan felhasználói AZONOSÍTÓval rendelkezik, amely "Anon-" előtaggal kezdődik, Azure Notebooks kéri, hogy hozzon létre egy saját azonosítót. A felhasználói azonosítót bármely olyan URL-cím használja, amelyet a projektek és a jegyzetfüzetek megosztásához szereztek be, ezért válasszon ki egy egyedi és értelmes dolgot.

    ![Felhasználói azonosító megadásának kérése Azure Notebooks](media/accounts/create-user-id.png)

    Ha a **No Thank (nem**) lehetőséget választja, a Azure Notebooks minden bejelentkezéskor továbbra is kéri a felhasználói azonosító megadását. A felhasználói azonosító a [felhasználói profilban](azure-notebooks-user-profile.md)bármikor beállítható.

1. A sikeres bejelentkezést követően Azure Notebooks navigál a nyilvános profil oldalra, amelyen kiválaszthatja a **profil adatainak szerkesztése** lehetőséget a további információk kitöltéséhez (további információért lásd [a profilt és a felhasználói azonosítót](azure-notebooks-user-profile.md)):

    ![Azure Notebooks profil oldalának kezdeti nézete](media/accounts/profile-page-new.png)

> [!NOTE]
> Ha a "felhasználói azonosító már használatban van" üzenet jelenik meg, próbálkozzon egy másik AZONOSÍTÓval. A felhasználói azonosítók minden Azure Notebooks-fiókban egyediek, és Azure Notebooks bizonyos felhasználói azonosítókat, például a Microsoft márkaneveket is fenntartanak.

## <a name="sign-out"></a>Kijelentkezés

A kijelentkezéshez válassza ki a felhasználónevet az oldal jobb felső sarkában, majd válassza a **kijelentkezés lehetőséget:**

![Kijelentkezési parancs helye Azure Notebooks](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: jegyzetfüzet létrehozása és megosztása](quickstart-create-share-jupyter-notebook.md)
