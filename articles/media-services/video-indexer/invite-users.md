---
title: Felhasználók meghívása Video Indexerra – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan hívhat meg felhasználókat a Video Indexer.
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 740b03179fad91e9042a54f8e74a622b3125b832
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950177"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Gyors útmutató: Felhasználók meghívása Video Indexer

A munkatársakkal való együttműködéshez meghívhatja őket a Video Indexer-fiókjába. 

> [!NOTE]
> Csak a fiók rendszergazdája adhat hozzá vagy távolíthat el felhasználókat.

## <a name="invite-new-users"></a>Új felhasználók meghívása

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre. Győződjön meg arról, hogy rendszergazdai fiókkal csatlakozik.
1. A felső menüben kattintson a **mások meghívása** gombra:

   ![Új felhasználók meghívása](./media/invite-users/invite-users.png)

1. Adja meg azoknak a személyeknek az e-mail címeit, akiket hozzá szeretne adni a Video Indexer fiókjához:

    ![Felhasználók meghívása erre a fiókra](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > Az összes meghívott felhasználó olvasási és írási engedéllyel rendelkezik a fiókjában lévő összes videóhoz.
1. A meghívott felhasználók e-mailt kapnak egy hivatkozással, és akkor férhetnek hozzá a fiókhoz, ha rákattintanak a **Join video Indexer** hivatkozásra:

    ![Megerősítés](./media/invite-users/invite-msg.png)

    A fiókhoz való hozzáféréshez a felhasználónak a hivatkozásra kell kattintania a csatlakozáshoz. 

## <a name="removing-existing-users"></a>Meglévő felhasználók eltávolítása

Ha el szeretné távolítani a fiókjához hozzáférő felhasználókat, kattintson a neve melletti **X** jelre:

![Felhasználók eltávolítása](./media/invite-users/remove-users.png)

A felhasználók nem kapnak értesítést az eltávolítás után. Az eltávolítást követően a felhasználók nem kapnak engedélyt a bejelentkezésre.

## <a name="next-steps"></a>További lépések

Most már használhatja a [video Indexer webhelyet](video-indexer-view-edit.md) , vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videó információit.

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Regisztráció és az első videó feltöltése](video-indexer-get-started.md)
- [Az API-k használatának megkezdése](video-indexer-use-apis.md)
