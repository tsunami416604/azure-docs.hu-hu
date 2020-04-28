---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179734"
---
Az Azure Storage összes blobjának egy tárolóban kell lennie. A blob nevének egy részét a tároló adja meg. Például ezekben a blob URI-mintákban a `mycontainer` a tároló neve:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

A tároló nevének egy érvényes DNS-névnek kell lennie, amely megfelel az alábbi elnevezési szabályoknak:

1. A tároló nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.
2. Minden kötőjel előtt és után közvetlenül egy betűnek vagy számnak kell állnia. A tárolók nevében nem szerepelhetnek egymást követő kötőjelek.
3. A tároló nevében szereplő összes betűnek kisbetűnek kell lennie.
4. A tároló nevének 3–63 karakter hosszúságúnak kell lennie.

> [!IMPORTANT]
> Ne feledje, hogy a tároló nevének minden esetben kisbetűsnek kell lennie. Ha a tárolónévbe nagybetűt ír, vagy más módon megsérti a tároló elnevezési szabályait, akkor a rendszer 400-as hibát adhat vissza (Hibás kérés). 
> 
> 

