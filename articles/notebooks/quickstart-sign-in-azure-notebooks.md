---
title: Jelentkezzen be Azure notebookok
description: Gyorsan jelentkezzen be Azure notebookok, és állítsa be a felhasználói azonosító, amely lehetőséget nyújt a mentett projektek és a notebookok megoszthatja másokkal.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: eb8ba7f23de99d333693430d806a8d887c55a678
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608175"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>Gyors útmutató: Bejelentkezés és felhasználói azonosító beállítása

Bár az Azure-jegyzetfüzetek mindig bejelentkezés nélkül is megtekintheti, jelentkezzen be futtathat jegyzetfüzeteket, mentett projektek és jegyzetfüzetek és a notebookok megoszthatja másokkal.

## <a name="sign-in"></a>Bejelentkezés

1. Válassza ki **jelentkezzen be a** a felül, jobb [notebooks.azure.com](https://notebooks.azure.com/).

    ![Jelentkezzen be a parancs Azure notebookokban helye](media/accounts/sign-in-command.png)

1. Amikor a rendszer kéri, adja meg annak a Microsoft-Account vagy egy munkahelyi vagy iskolai fiókkal és válassza ki az e-mail címét **tovább**. Fióktípus esetében leírása [a felhasználói fiók Azure notebookokhoz](azure-notebooks-user-account.md). Ha nem rendelkezik Microsoft-Account, vagy nem szeretne tenni egy kifejezetten az Azure-jegyzetfüzetek való használatra, válassza ki a **hozzon létre egyet**:

    ![Hozzon létre új Microsoft-fiók parancs bejelentkezési kérések jelenhetnek](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Hozzon létre egy új fiókot, amely már rendelkezik fiókkal társított e-mail-címmel próbál jelenhet meg az üzenetet, a "nem itt jelentkezzen be munkahelyi vagy iskolai e-mail-címét. Egy személyes e-mail, például a Gmailes vagy Yahoo!-s, vagy szerezze be egy új Outlook e-mailt." Ebben az esetben próbáljon meg azzal bejelentkezni a munkahelyi e-mail címét, egy új fiók létrehozása nélkül.

1. Adja meg a jelszót, amikor a rendszer kéri.

1. Ha most jelentkezik be először, Azure notebookok engedélyt kér a fiókja eléréséhez. Válassza ki **Igen** folytatásához:

    ![Fiók engedélyek kérése](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Állítsa be a felhasználói azonosító

1. Első bejelentkezés után a például a "névtelen-idrca3" ideiglenes felhasználói Azonosítót kapott. Minden alkalommal, amikor egy felhasználói Azonosítót, amely a következővel kezdődik: rendelkezik "névtelen-", az Azure-jegyzetfüzetek felszólítja, hogy hozzon létre egy Azonosítót a saját. A felhasználói azonosító bármelyik URL-cím, hogy a projektek és jegyzetfüzetek, ezért válasszon egy egyedi és beszédes használatban van.

    ![Rákérdezés a felhasználói azonosító megadása Azure notebookokhoz](media/accounts/create-user-id.png)

    Ha **nem Köszönjük, hogy**, Azure notebookok kéri a felhasználói azonosító minden alkalommal, amikor bejelentkezik továbbra is. A felhasználói azonosító is megadható a bármikor a [felhasználói profil](azure-notebooks-user-profile.md).

1. Miután sikeresen bejelentkezett, az Azure-jegyzetfüzetek navigál, amelyen kiválaszthatja a nyilvános profil lapon **profiladatok szerkesztése** töltse ki az Ön adatait a többi (további információkért lásd: [profilját és felhasználói azonosítója](azure-notebooks-user-profile.md)):

    ![Egy Azure-jegyzetfüzetek profilu kezdeti megtekintése](media/accounts/profile-page-new.png)

> [!NOTE]
> Ha a "Felhasználói azonosító már használatban van," üzenetet látja, próbálja meg másik azonosítót. Felhasználói azonosítók egyediek legyenek az összes Azure-jegyzetfüzetek fiókokat, és az Azure notebookok is fenntartja az egyes felhasználói azonosítókat, például a Microsoft márkanevek.

## <a name="sign-out"></a>Kijelentkezés

Jelentkezzen ki, válassza ki, az oldal jobb felső sarokban a felhasználónevére, majd válassza ki a **Kijelentkezés**:

![Kijelentkezés parancsot Azure notebookokban helye](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Hozzon létre és osszon meg a notebook](quickstart-create-share-jupyter-notebook.md)
