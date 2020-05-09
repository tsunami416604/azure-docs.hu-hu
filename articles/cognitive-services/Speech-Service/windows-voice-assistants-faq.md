---
title: Hangsegédek Windows rendszeren – gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Gyakori kérdések, amelyek gyakran a Windows Voice Agent fejlesztése során jönnek létre.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: aa71057d3819acb335153ee5b4b65960320405be
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997413"
---
# <a name="samples-and-faqs"></a>Minták és gyakori kérdések

## <a name="the-uwp-voice-assistant-sample"></a>A UWP hangsegéd mintája

A UWP hangsegéd minta egy hangsegéd a Windows rendszerben, amely a következőt kínálja

- a Windows ConversationalAgent API-k használatának bemutatása
- ajánlott eljárások megjelenítése egy hangügynökhöz Windows rendszeren
- az MVP-ügynök alkalmazásai számára biztosítson egy alkalmazkodóképes alkalmazás-és újrafelhasználható összetevőket
- megtudhatja, hogyan használhatók a közvetlen vonalas beszédek a bot Framework vagy az egyéni parancsok használatával, valamint a Windows ConversationalAgent API-k és a teljes körű hangügynök felhasználói felülete

A mintául szolgáló alkalmazás dokumentációja végigvezeti a hangalapú aktiválás és az ügynök felügyeletének kódján, az indítási előfeltételektől a megfelelő tisztításon keresztül.

> [!div class="nextstepaction"]
> [A UWP minta GitHub-tárházának meglátogatása](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Hogyan vegye fel a kapcsolatot a Microsofttal olyan erőforrásokhoz, mint a korlátozott hozzáférésű szolgáltatás-tokenek és a kulcsszavas modellek

Vegye winvoiceassistants@microsoft.com fel a kapcsolatot az ilyen erőforrások igényléséhez.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>Az alkalmazásom egy kis ablakban jelenik meg, amikor a hangom aktiválja. Hogyan lehet áttérni a kompakt nézetből egy teljes alkalmazási ablakra?

Ha az alkalmazást a hang első aktiválása után aktiválja, a rendszer egy kompakt nézetben indítja el. A hangvezérelt [aktiválás előzetes kialakításával](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) kapcsolatos útmutatásért olvassa el a Windows rendszerű hangasszisztensek közötti különböző nézeteket és átmeneteket.

Az appView API `TryEnterViewModeAsync`-val a kompakt nézetből teljes alkalmazás nézetre való áttérést végezheti el:

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Miért van engedélyezve a Windows hangsegéd funkciói a UWP-alkalmazásokhoz?

Az olyan funkciókkal kapcsolatos adatvédelmi kockázatok miatt, mint a Hangaktiválás, a UWP platform funkciói szükségesek ahhoz, hogy a hangsegéd funkciói a Windowsban megfelelően biztonságosak legyenek.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>A UWP hangsegéd minta közvetlen sortörést használ. Kell-e közvetlen sortörést használni a hangvezérelt asszisztensem Windows rendszeren?

A UWP-minta alkalmazás a Direct line Speech és a Speech Services SDK használatával lett kifejlesztve, amely bemutatja, hogyan használható a párbeszéd-szolgáltatás a Windows beszélgetési ügynök képességgel. Azonban bármilyen szolgáltatást használhat helyi és Felhőbeli kulcsszavak ellenőrzéséhez, beszéd – szöveg átalakításhoz, bot-párbeszédpanelekhez és szöveg-beszéd átalakításhoz.