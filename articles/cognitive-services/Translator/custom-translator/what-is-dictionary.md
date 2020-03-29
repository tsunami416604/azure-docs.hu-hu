---
title: Mi az a szótár? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A szótár olyan egymáshoz igazított dokumentum, amely megadja azoknak a kifejezéseknek vagy mondatoknak (és azok fordításainak) a listáját, amelyeket a Microsoft Translator mindig ugyanúgy fordít. A szótárakat néha szószedetnek vagy kifejezésbázisoknak is nevezik.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970729"
---
# <a name="what-is-a-dictionary"></a>Mi az a szótár?

A szótár egy egymáshoz igazított dokumentumpár, amely meghatározza a kifejezések vagy mondatok listáját és a hozzájuk tartozó fordításokat. Használjon szótárt a betanításban, ha azt szeretné, hogy a Microsoft Translator mindig fordítsa le a forráskifejezés vagy mondat bármely példányát a szótárban megadott fordítás sal. A szótárakat szójegyzéknek vagy kifejezésbázisnak is nevezik. Lehet gondolni a szótárban, mint egy oktalan erő "másolás és csere" az összes kifejezést listát. Ezenkívül a Microsoft Custom Translator szolgáltatás saját általános célú szótárakat épít és használ fordítása minőségének javítása érdekében. Azonban az ügyfél által biztosított szótár precedenst teremt, és először a keresés szavakat vagy mondatokat.

A szótárak csak olyan nyelvi párokban lévő projekteknél működnek, amelyek mögött egy teljes mértékben támogatott Microsoft általános neurális hálózati modell áll. [A nyelvek teljes listájának megtekintése.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)

## <a name="phrase-dictionary"></a>Kifejezésszótár
Amikor a modell betanítása során beilleszt egy kifejezésszótárt, a felsorolt szavak vagy kifejezések a megadott módon lesznek lefordítva. A mondat többi része a szokásos módon lesz lefordítva. A szótár kifejezésszótársegítségével olyan kifejezéseket adhat meg, amelyeket nem szabad lefordítani, ha ugyanazt a le nem fordított kifejezést adja meg a forrás- és célfájlban a szótárban.

## <a name="sentence-dictionary"></a>Mondatszótár
A mondatszótár lehetővé teszi a forrásmondat pontos célfordításának megadását. Ahhoz, hogy egy mondatszótár egyezzen, a teljes elküldött mondatnak meg kell egyeznie a forrásszótár bejegyzéssel.  Ha a mondatnak csak egy része egyezik, a bejegyzés nem egyezik.  Egyezés észlelésekor a mondatszótár célbejegyzése kerül visszaadásra.

## <a name="dictionary-only-trainings"></a>Csak szótári tréningek
A modell csak szótáradatok használatával tanítható be. Ehhez csak azt a szótári dokumentumot (vagy több szótári dokumentumot) jelölje ki, amelyet be szeretne vonni, és koppintson a Modell létrehozása elemre. Mivel ez egy csak szótári képzés, nincs minimális számú képzési mondat szükséges. A modell általában a szokásos betanításnál sokkal gyorsabban fejezi be a betanítást.  Az eredményül kapott modellek a Microsoft alapmodelleket fogják használni a fordításhoz a hozzáadott szótárak hozzáadásával.  Nem kapsz tesztjelentést.

>[!Note]
>Az Egyéni fordító nem igazítja a szótárfájlokat, ezért fontos, hogy a szótári dokumentumokban azonos számú forrás- és célkifejezés/mondat legyen, és hogy pontosan igazodjanak egymáshoz.

## <a name="recommendations"></a>Javaslatok

- A szótárak nem helyettesítik a modell betanítását a betanítási adatok használatával. Javasoljuk, hogy kerülje el őket, és hagyja, hogy a rendszer tanuljon a betanítási adatokból. Ha azonban a mondatokat vagy az összetett nőket úgy kell megjeleníteni, ahogy van, használjon szótárat.
- A szótár kifejezést takarékosan kell használni. Tehát, ne feledje, hogy ha egy mondaton belül egy mondaton belül egy mondatot helyettesít, a mondaton belüli kontextus elvész vagy korlátozott a mondat többi részének fordításához. Az eredmény az, hogy míg a mondaton belüli kifejezés vagy szó a megadott szótár szerint fog lefordítani, a mondat általános fordítási minősége gyakran szenved.
- A szótár kifejezésszótár jól működik az összetett főnevek, például a terméknevek ("Microsoft SQL Server"), a megfelelő nevek ("Hamburg városa") vagy a termék jellemzői ("pivot table") esetében. Nem működik egyformán jól az igék vagy melléknevek esetében, mert ezek jellemzően erősen ragozott a forrásban vagy a célnyelven. Az ajánlott eljárás az, hogy elkerüljék kifejezés szótár bejegyzéseket semmit, de összetett nouns.
- Kifejezésszótár használataesetén fontos a nagybetűs írásmód és az írásjelek használata. A szótárbejegyzések csak azokat a szavakat és kifejezéseket egyezik meg a bemeneti mondatban, amelyek pontosan ugyanazt a nagybetűs írásjelet és írásjelet használják, mint a forrásszótárfájlban. A fordítások a célszótárfájlban megadott nagybetűket és írásjeleket is tükrözik. Ha például olyan angolból spanyolba programot tanított, amely a forrásfájlban az "USA" és az "EE" kifejezést használja. UU." a célfájlban. Ha olyan mondat fordítását kéri, amely tartalmazza a "mi" szót (nem nagybetűs), ez nem egyezik meg a szótárral. Ha azonban olyan mondat fordítását kéri, amely az "USA" szót (nagybetűs) tartalmazza, akkor az megegyezik a szótárral, és a fordítás "EE"-t tartalmaz. UU." Ne feledje, hogy a fordításban a nagybetűs írásjelek eltérhetnek a szótár célfájljában megadottaktól, és eltérhetnek a forrás nagybetűs írás- és írásjeleitól. A célnyelv szabályait követi.
- Ha egy szó egynél többször jelenik meg egy szótárfájlban, a rendszer mindig az utoljára megadott bejegyzést fogja használni. Ezért a szótár nem tartalmazhat több fordítást ugyanabból a szóból.

## <a name="next-steps"></a>További lépések

- A [dokumentumformátumokra vonatkozó irányelvekről](document-formats-naming-convention.md)olvashat.
