---
title: Felhasználók meghívása videoindexelőbe - Azure
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan hívhatja meg a felhasználókat a Video Indexer be.
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: e9a4575ce2f353d61654263aea8d968c650a689e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499745"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Rövid útmutató: Felhasználók meghívása a Video Indexerbe

A munkatársakkal való együttműködéshez meghívhatja őket a Video Indexer-fiókjába. 

> [!NOTE]
> Felhasználókat csak a fiók rendszergazdája vehet fel vagy távolíthat el.

## <a name="invite-new-users"></a>Új felhasználók meghívása

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre. Győződjön meg arról, hogy rendszergazdai fiókkal van kapcsolatban.
1. Kattintson a felső menü **Mások meghívása** gombjára:

   ![Új felhasználók meghívása](./media/invite-users/invite-users.png)

1. Adja hozzá azoknak az e-mail címeit, akiket hozzá szeretne adni a Video Indexer-fiókjához:

    ![Felhasználók meghívása erre a fiókra](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > Minden meghívott felhasználó nak olvasási és írási engedélye lesz a fiókjában lévő összes videóhoz.
1. A meghívott felhasználók kapnak egy e-mailt egy hivatkozást, és képes lesz elérni a fiókot, ha rákattintanak a **Join Video Indexer** linkre:

    ![Megerősítés](./media/invite-users/invite-msg.png)

    A felhasználónak a linkre kell kattintania, hogy csatlakozzon a fiókhoz való hozzáféréshez. 

## <a name="removing-existing-users"></a>Meglévő felhasználók eltávolítása

Ha el szeretné távolítani azon felhasználókat, akik hozzáférnek a fiókjához, kattintson a nevük melletti **X** jelre:

![Felhasználók eltávolítása](./media/invite-users/remove-users.png)

A felhasználók nem kapnak értesítést az eltávolításkor. Az eltávolítást követően a felhasználók nem jelentkezhetnek be.

## <a name="next-steps"></a>További lépések

Most már használhatja a [Videó Indexer webhely](video-indexer-view-edit.md) vagy [videoindexelő fejlesztői portál,](video-indexer-use-apis.md) hogy az elemzési a videó.

## <a name="see-also"></a>Lásd még

- [A Video Indexer áttekintése](video-indexer-overview.md)
- [Hogyan regisztrálj és töltsd fel az első videódat?](video-indexer-get-started.md)
- [API-k használatának megkezdése](video-indexer-use-apis.md)
