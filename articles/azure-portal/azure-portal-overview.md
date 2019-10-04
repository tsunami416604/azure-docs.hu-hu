---
title: Az Azure Portal áttekintése | Microsoft Docs
description: Megtudhatja, hogyan navigálhat a Azure Portal, és hogyan használhatja a szolgáltatásokat a szolgáltatások kezeléséhez
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000344"
---
# <a name="azure-portal-overview"></a>Az Azure portál áttekintése

Ez a cikk bemutatja a Azure Portal, azonosítja a portál oldal elemeit, és segítséget nyújt a Azure Portal felügyeleti felületének megismeréséhez.

## <a name="what-is-the-azure-portal"></a>Mi a Azure Portal?

A Azure Portal egy webalapú, egységesített konzol, amely alternatívát biztosít a parancssori eszközökhöz. A Azure Portal segítségével az Azure-előfizetését grafikus felhasználói felületen kezelheti. Az egyszerű webalkalmazásokból az összetett felhőbe történő üzembe helyezéshez, az egyéni irányítópultok létrehozásához, az erőforrások rendszerezéséhez és a kisegítő lehetőségek a legjobb élményhez való konfigurálásához létrehozhat, kezelhet és felügyelhet mindent.

A Azure Portal a rugalmasság és a folyamatos rendelkezésre állás érdekében lett kialakítva. Minden Azure-adatközpontban jelenléttel rendelkezik, így az egyes adatközpontok hibáira rugalmasan biztosítható, és elkerülheti a hálózat lelassulását azáltal, hogy a felhasználókhoz közeledik. A Azure Portal folyamatosan frissül, és nem igényel állásidőt a karbantartási tevékenységekhez.

## <a name="azure-home"></a>Azure Kezdőlap

Az Azure-szolgáltatások új előfizetői a [portálra való bejelentkezés](https://portal.azure.com) után elsőként láthatják az **Azure kezdőlapját**. Ez az oldal olyan erőforrásokat fordít le, amelyek segítenek az Azure-előfizetések legtöbbje számára. Az ingyenes online tanfolyamokra, dokumentációra, alapszolgáltatásokra és hasznos webhelyekre mutató hivatkozásokat tartalmaz, amelyekkel naprakészen maradhat, és kezelheti a szervezete változásait. A munkához való gyors és könnyű hozzáférés érdekében a legutóbb felkeresett erőforrások listáját is megjeleníti. Ezt a lapot nem szabhatja testre, de kiválaszthatja, hogy az **Azure Home** vagy az **Azure irányítópultot** szeretné-e látni alapértelmezett nézetként. Amikor először jelentkezik be, az oldal tetején megjelenik egy üzenet, amelyen mentheti a beállításokat.

![Az alapértelmezett nézet-választót ábrázoló képernyőfelvétel](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Azure Dashboard

Az irányítópultok célzott áttekintést nyújtanak az előfizetésben található azon erőforrásokról, amelyek a leginkább az Ön számára fontosak. A kezdéshez egy alapértelmezett irányítópultot kaptunk. Ezt az irányítópultot testreszabhatja úgy, hogy a leggyakrabban használt erőforrásokat egyetlen nézetbe hozza. Az alapértelmezett nézetben végrehajtott módosítások csak a felhasználói élményt érintik. Létrehozhat azonban további irányítópultokat saját használatra, vagy közzéteheti a testreszabott irányítópultokat, és megoszthatja azokat a szervezet más felhasználóival. További információ: [irányítópultok létrehozása és megosztása a Azure Portalban](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Ismerkedés a portál körül

Hasznos megérteni az alapszintű portál elrendezését és a velük való interakciót. Itt bemutatjuk a felhasználói felület összetevőit és az utasításokhoz használt terminológiát. A portál részletes ismertetését lásd a tanfolyamot bemutató leckében. [](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)

A Azure Portal oldalsáv és az oldal fejléce mindig megtalálható globális elemek. Ezek az állandó funkciók az egyes szolgáltatásokhoz vagy szolgáltatásokhoz társított felhasználói felület "rendszerhéja", a fejléc pedig hozzáférést biztosít a globális vezérlőkhöz. Egy erőforráshoz tartozó konfigurációs lap (más néven "panel") is rendelkezhet egy bal oldali ablaktáblával, amely segítséget nyújt a szolgáltatások közötti váltáshoz.

Az alábbi ábra a Azure Portal alapvető elemeit címkézi, amelyek mindegyike az alábbi táblázatban látható.

![A teljes képernyős portál nézetét és a felhasználói felület elemeinek kulcsát bemutató képernyőfelvétel](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Kulcs|Leírás
|:---:|---|
|1|Oldal fejléce Megjelenik az egyes portálok oldal tetején, és globális elemeket tart fenn.|
|2| Globális keresés. A keresősáv használatával gyorsan megtalálhatja az adott erőforrást, szolgáltatást vagy dokumentációt.|
|3|Globális vezérlők. Az összes globális elemhez hasonlóan ezek a funkciók is megmaradnak a portálon, és többek között a következők: Cloud Shell, előfizetési szűrő, értesítések, portál beállításai, Súgó és támogatás, és küldjön nekünk visszajelzést.|
|4|Az Ön fiókja. Megtekintheti a fiókjával kapcsolatos információkat, átválthatja a címtárakat, kijelentkezhet vagy bejelentkezhet egy másik fiókkal.|
|5|Oldalsáv. A Sidebar egy globális elem, amely segít a szolgáltatások közötti váltásban. Az oldalsáv összecsukható úgy, hogy jobban összpontosítsanak a munkaablakra.|
|6|A fő vezérlővel új erőforrás hozható létre a jelenlegi előfizetésben. Keresse meg vagy tallózással keresse meg az Azure piactéren a létrehozni kívánt erőforrás típusát.|
|7|A Kedvencek listája. Kedvenceket adhat hozzá vagy távolíthat el a **minden szolgáltatás** lapról.|
|8|Bal oldali ablaktábla Számos szolgáltatás tartalmaz egy bal oldali panel menüt, amely segít a szolgáltatás kezelésében.|
|9|Parancssáv. A parancssáv vezérlői az aktuális fókusz kontextusában jelennek meg.|
|10|Morzsa. A navigációs hivatkozások segítségével egy szint visszahelyezhető a munkafolyamatba.|
|11|Munkaablakban.  A jelenleg fókuszban lévő erőforrás részleteit jeleníti meg.|

## <a name="get-started-with-services"></a>Ismerkedés a szolgáltatásokkal

Ha Ön új előfizető, létre kell hoznia egy erőforrást, mielőtt bármit kezelnie kellene. Válassza az **+ erőforrás létrehozása** lehetőséget az Azure piactéren elérhető szolgáltatások megtekintéséhez. Itt több száz szolgáltatótól származó alkalmazásokat és szolgáltatásokat találhat, amelyek mindegyike az Azure-ban fut.

A gyakran használt szolgáltatásokra mutató hivatkozásokkal előre kitöltöttük kedvenceit az oldalsávon.  Az összes elérhető szolgáltatás megtekintéséhez válassza a **minden szolgáltatás** lehetőséget az oldalsávon.

> [!TIP]
> Az erőforrás, szolgáltatás vagy dokumentáció keresésének leggyorsabb módja a *Keresés* használata a globális fejlécben. A navigációs hivatkozások használatával visszatérhet az előző lapokra.
>
Tekintse meg ezt a videót, amely bemutatja, hogyan használható a globális keresés a Azure Portalban.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[A globális keresés használata a Azure Portalban](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>További lépések

* További információ a Azure Portal futtatásáról a [támogatott böngészőkben és eszközökön](../azure-portal/azure-portal-supported-browsers-devices.md)

* Útközben is kapcsolódhat [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)
