---
title: A Tudásbázis - Microsoft kognitív szolgáltatások fejlesztési életciklus során |} Microsoft Docs
titleSuffix: Azure
description: A Tudásbázis fejlesztési életciklus-során
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 9ecdd2c7823eed145621b214690eff7681065507
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35349929"
---
# <a name="knowledge-base-lifecycle"></a>Tudásbázis életciklusa
Kérdések és válaszok készítő megtanulja a legjobb az ismétlődő ciklusának modellmódosításokkal, utterance példák, közzétételét és adatgyűjtés végpont lekérdezésből. 

![Szerzői ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>A kérdések és válaszok készítő Tudásbázis létrehozása
Kérdések és válaszok készítő Tudásbázis (KB) végpontja biztosítja a legjobb egyezést választ a felhasználó lekérdezése a KB-os tartalma. A Tudásbázis létrehozása a tartalomtár kérdéseket, válasz és kapcsolódó metaadatok beállítására egyszeri művelet. A Tudásbázis bejárási már meglévő tartalmat, például gyakran ismételt kérdések lap, a termék laborútmutatókkal vagy a strukturált Q-A párok hozhat létre. Megtudhatja, hogyan [hozzon létre egy Tudásbázis](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Tesztelése és frissítése a Tudásbázis
A Tudásbázis készen áll a tesztelés után a telepítéskor tartalom, besorolást vagy automatikus kiolvasásához. Tesztelési végezhető el a **teszt** általános felhasználói lekérdezések beírásával, és annak ellenőrzése, hogy a visszaadott válaszok várt módon, és abban, hogy elegendő pontszám panel. Alacsony abban, hogy pontszámok kijavításához alternatív kérdéseket is hozzáadhat. Hozzáadhat új válaszok is, ha a lekérdezés visszaadja a "nincs egyezés a KB-ban" alapértelmezett válasz. Ez a teszt-frissítés szoros ciklus továbbra is fennáll, addig, amíg elégedett az eredményeket. Megtudhatja, hogyan [tesztelése a Tudásbázis](../How-To/test-knowledge-base.md).

A nagy KB a tesztelés automatizálható generateAnswer API-k használatával. 

## <a name="publish-the-knowledge-base"></a>A Tudásbázis közzététele
Ha elkészült a Tudásbázis tesztelése, közzéteheti azt. Leküldéses értesítések a legújabb verzióját a tesztelt Tudásbázis egy dedikált Azure Search index képviselő közzététele a **közzétett** Tudásbázis. Az alkalmazás vagy a Csevegés az olyan végponttal, amely hívható is létrehoz.

Ezzel a módszerrel a Tudásbázis teszt verziója alatt végzett módosítások nem befolyásolják egy éles alkalmazásban élő elképzelhető, hogy a közzétett verziót.

A Tudásbázis körrel mindegyikének tesztelési külön telepíthető. Az API-kkal célba a Tudásbázis a teszt verziójának `isTest=true` generateAnswer hívásában jelzőt.

Megtudhatja, hogyan [közzététele a Tudásbázis](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Használat monitorozása
Fog tudni bejelentkezni a Csevegés naplókat, a szolgáltatás, akkor szüksége ahhoz, hogy az Application Insights amikor Ön [a kérdések és válaszok készítő szolgáltatás létrehozása](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használati különböző analytics kérheti le. További tudnivalók az application insights eléréséhez használandó [analytics a kérdések és válaszok készítő szolgáltatás](../How-To/get-analytics-knowledge-base.md).

Amiről tanulni az elemzés az alapján, ellenőrizze a megfelelő [frissítéseit a Tudásbázis](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)

## <a name="see-also"></a>Lásd még 

[Tudásbázis](./knowledge-base.md)
[kérdések és válaszok készítő áttekintése](../Overview/overview.md)
