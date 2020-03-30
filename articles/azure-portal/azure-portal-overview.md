---
title: Az Azure Portal áttekintése | Microsoft Docs
description: Az Azure Portal egy grafikus felhasználói felület, amely segítségével kezelheti az Azure-szolgáltatások. Megtudhatja, hogyan navigálhat és kereshet erőforrásokat az Azure Portalon.
services: azure-portal
keywords: portál
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310730"
---
# <a name="azure-portal-overview"></a>Az Azure Portal áttekintése

Ez a cikk bemutatja az Azure Portal, azonosítja a portál oldal elemeit, és segít megismerni az Azure Portal felügyeleti élményt.

## <a name="what-is-the-azure-portal"></a>Mi az az Azure Portal?

Az Azure Portal egy egységesített felületet biztosító webes konzol, amely alternatívát biztosít a parancssori eszközökkel szemben. Az Azure Portalon grafikus felhasználói felületen kezelheti Azure-előfizetését. Az egyszerű webalkalmazásoktól az összetett felhőalapú telepítésekig mindent létrehozhat, kezelhet és figyelhet. Egyéni irányítópultokat hozhat létre az erőforrások szervezett nézetéhez. Az optimális élmény érdekében konfigurálja a kisegítő lehetőségeket.

Az Azure Portal rugalmasságra és folyamatos rendelkezésre állásra lett tervezve. Minden Azure-adatközpontban jelen van. Ez a konfiguráció rugalmassá teszi az Azure Portalt az egyes adatközponti hibákkal szemben, és elkerüli a hálózati lassulásokat azáltal, hogy közel van a felhasználókhoz. Az Azure Portal folyamatosan frissül, és nem igényel állásidőt a karbantartási tevékenységekhez.

## <a name="azure-portal-menu"></a>Az Azure Portal menüje

Kiválaszthatja a portál menü alapértelmezett módját. Dokkolható, vagy úszó panelként is működhet.

Ha a portál menü úszó panel módban van, akkor a rendszer el van rejtve, amíg szükség nem lesz rá. A menü megnyitásához vagy bezárásához válassza a menü ikonját.

![Az Azure Portal menüúszó módban](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Ha a portálmenüdokkolt módját választja, az mindig látható lesz. A menü összecsukható, hogy több munkaterületet biztosítson.

![Az Azure Portal menüje dokkolt módban](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure Főoldal

Az Azure-szolgáltatások új előfizetőjeként az **Azure Home**első dolog, amelyet [a portálra való bejelentkezés](https://portal.azure.com) után lát. Ez a lap olyan erőforrásokat állít össze, amelyek segítségével a legtöbbet hozhatja ki Azure-előfizetéséből. Ingyenes online tanfolyamokra, dokumentációra, alapvető szolgáltatásokra és hasznos webhelyekre mutató linkeket is mellékeltünk a jelenlegi állapot megőrzéséhez és a változások kezeléséhez a szervezet számára. A folyamatban lévő munkához való gyors és egyszerű hozzáférés érdekében a legutóbb meglátogatott erőforrások listáját is megjelenítjük. Ezt a lapot nem szabhatja testre, de kiválaszthatja, hogy az **Azure Home** vagy az **Azure Dashboard** legyen-e az alapértelmezett nézet. Amikor először jelentkezik be, a lap tetején megjelenik egy üzenet, amelyen mentheti a kívánt beállításokat.

![Az alapértelmezett nézetválasztót ábrázoló képernyőkép](./media/azure-portal-overview/azure-portal-default-view.png)

Az Azure Portal menüje és az Azure alapértelmezett nézete is módosítható a **Portál beállításaiban.** Ha módosítja a kijelölést, a rendszer azonnal alkalmazza a módosítást.

![Az alapértelmezett nézetválasztót ábrázoló képernyőkép](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure irányítópult

Az irányítópultok az előfizetésben lévő erőforrások célzott nézetét biztosítják, amelyek a legfontosabbak az Ön számára. A kezdéshez egy alapértelmezett irányítópultot adtunk. Az irányítópult testreszabásával a gyakran használt erőforrásokat egyetlen nézetbe alakíthatja. Az alapértelmezett nézeten végzett módosítások csak a felhasználói élményre vannak hatással. Azonban létrehozhat további irányítópultokat saját használatra, vagy közzéteheti a testreszabott irányítópultokat, és megoszthatja őket a szervezet más felhasználóival. További információ: [Irányítópultok létrehozása és megosztása az Azure Portalon.](../azure-portal/azure-portal-dashboards.md)

## <a name="getting-around-the-portal"></a>A portál megkerülése

Hasznos lehet megérteni a portál alapvető elrendezését és annak interakcióját. Itt bemutatjuk a felhasználói felület összetevőit és néhány terminológiát, amelyeket az utasítások hoz. A portál részletesebb bemutatóját a [Navigálás a portálon](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)című tanfolyami leckében veheti át.

Az Azure Portal menü és az oldal fejléce olyan globális elemek, amelyek mindig jelen vannak. Ezek az állandó funkciók az egyes szolgáltatásokhoz vagy szolgáltatásokhoz társított felhasználói felület "rendszerhéja", és a fejléc hozzáférést biztosít a globális vezérlőkhöz. Egy erőforrás konfigurációs lapja (más néven "panel") rendelkezhet egy erőforrásmenüvel is, amely segít a szolgáltatások közötti váltásban.

Az alábbi ábra az Azure Portal alapvető elemeit címkézi, amelyek mindegyike az alábbi táblázatban található.

![Képernyőkép a teljes képernyős portálnézetről és a felhasználói felület elemeinek kulcsa](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Képernyőkép a kibontott portál menüjével](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Kulcs|Leírás
|:---:|---|
|1|Oldalfejléc. Minden portáloldal tetején megjelenik, és globális elemeket tartalmaz.|
|2| Globális keresés. A keresősáv segítségével gyorsan megtalálhatja az adott erőforrást, szolgáltatást vagy dokumentációt.|
|3|Globális irányítás. Mint minden globális elem, ezek a funkciók is megmaradnak a portálon, és a következőket tartalmazzák: Cloud Shell, előfizetésszűrő, értesítések, portálbeállítások, súgó és támogatás, és visszajelzést küldhet nekünk.|
|4|Az Ön számlájára. Megtekintheti a fiókkal kapcsolatos információkat, könyvtárat válthat, kijelentkezhet vagy másik fiókkal jelentkezhet be.|
|5|Portál menü. A portál menü egy globális elem, amely segít a szolgáltatások közötti navigálásban. Néha az oldalsávnak is nevezik, a portál menümód módosítható a **Portál beállításai ban.**|
|6|Erőforrás menü. Számos szolgáltatás tartalmaz egy erőforrás menüt, amely segít a szolgáltatás kezelésében. Ez az elem a bal oldali ablaktábla.|
|7|Parancssáv. A parancssáv vezérlői az aktuális fókuszhoz képest kontextust jelentenek.|
|8|Munkaablak.  Megjeleníti az aktuálisan fókuszban lévő erőforrás részleteit.|
|9|Morzsa. A zsemlemorzsahivatkozásokkal visszahelyezhet egy szintet a munkafolyamatban.|
|10|Főkiszolgáló vezérlő új erőforrás létrehozásához az aktuális előfizetésben. Bontsa ki vagy nyissa meg a portál menüt a **+ Erőforrás létrehozása elemkereséshez**. Keresse meg vagy böngésszen az Azure Piactéren a létrehozni kívánt erőforrástípushoz.|
|11|A kedvencek listája. A lista testreszabásáról a [Kedvencek hozzáadása, eltávolítása és rendezése](../azure-portal/azure-portal-add-remove-sort-favorites.md) című témakörben olvashat.|

## <a name="get-started-with-services"></a>A szolgáltatások kalkulát

Ha Ön új előfizető, akkor létre kell hoznia egy erőforrást, mielőtt bármit is kezelnie kell. Válassza **a + Erőforrás létrehozása** lehetőséget az Azure Marketplace-en elérhető szolgáltatások megtekintéséhez. Itt több száz szolgáltató alkalmazásait és szolgáltatásait találja, amelyek mindegyike az Azure-on futtatható.

Az oldalsávon előre kitöltöttük a Kedvenceket a gyakran használt szolgáltatásokra mutató hivatkozásokkal.  Az összes elérhető szolgáltatás megtekintéséhez válassza a **Minden szolgáltatás** lehetőséget az oldalsávról.

> [!TIP]
> Az erőforrás, szolgáltatás vagy dokumentáció megkeresésének leggyorsabb módja a *Keresés* használata a globális fejlécben. A zsemlemorzsa hivatkozásokkal visszatérhet az előző oldalakra.
>
Tekintse meg ezt a videót a globális keresés azure-portálon való használatáról szóló bemutatóról.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[A globális keresés használata az Azure Portalon](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>További lépések

* További információ arról, hogy hol futtatható az Azure Portal [a támogatott böngészőkben és eszközökön](../azure-portal/azure-portal-supported-browsers-devices.md)
* Maradjon kapcsolatban útközben az [Azure mobilalkalmazással](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* A felhőkörnyezet bedolgozása és beállítása az [Azure Quickstart Center rel](../azure-portal/azure-portal-quickstart-center.md)
