---
title: Avere vFXT fürt hangolása - Azure
description: Egyéni beállítások áttekintése az Azure-hoz való Avere vFXT teljesítményének optimalizálása érdekében
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152935"
---
# <a name="cluster-tuning"></a>A fürtök finomhangolása

A legtöbb vFXT-fürt élvezheti a testreszabott teljesítménybeállítások előnyeit. Ezek a beállítások segítik a fürtöt abban, hogy a legjobban működjön az adott munkafolyamattal, adatkészlettel és eszközökkel.

Ezt a testreszabást egy támogatási képviselő segítségével kell elvégezni, mivel ez magában foglalhatja az Avere vezérlőpulton nem elérhető szolgáltatások konfigurálását.

Ez a szakasz néhány, elvégezhető egyéni hangolást ismertet.

## <a name="general-optimizations"></a>Általános optimalizálások

Ezek a módosítások az adatkészlet tulajdonságai vagy a munkafolyamat-stílus alapján ajánlottak lehetnek.

* Ha a munkaterhelés írási nehéz, növelje az írási gyorsítótár méretét az alapértelmezett 20%-ról.
* Ha az adatkészlet sok kis fájlt tartalmaz, növelje a fürt gyorsítótárának fájlszámlálási korlátját.
* Ha a munka két adattár közötti másolással vagy áthelyezéssel jár, módosítsa az adatok áthelyezéséhez használt szálak számát:
  * A sebesség növeléséhez növelheti a használt párhuzamos szálak számát.
  * Ha a háttértároló kötete túlterheltté válik, előfordulhat, hogy csökkentenie kell a használt párhuzamos szálak számát.
* Ha a fürt gyorsítótárazza az NFSv4 Hozzáférés-hozzáférés-hozzáférés-hozzáférés-hozzáférést használó központi fájlkezelő adatait, engedélyezze a hozzáférési módgyorsítótárazást az egyes ügyfelek fájlengedélyezésének egyszerűsítése érdekében.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Felhőbeli NAS- vagy felhőátjáró-optimalizálás

Felhőbeli NAS- vagy átjáró-forgatókönyv esetén a vFXT-fürt NAS-stílusú hozzáférést biztosít egy felhőtárolóhoz. A vFXT-fürt és a felhőalapú tárhely közötti nagyobb adatsebesség kihasználása érdekében a képviselő javasolhatja a beállítások módosítását, hogy agresszívebben leadja az adatokat a tárolókötetre a gyorsítótárból. Példa:

* A fürt és a tárolótároló közötti TCP-kapcsolatok számának növelése

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Felhőbeli bursting vagy hibrid WAN-optimalizálás

Felhőalapú bursting forgatókönyv vagy hibrid tároló WAN optimalizálási forgatókönyv esetén a vFXT-fürt integrációt biztosít a felhő és a helyszíni hardveres tárolás között. Ezek a változások hasznosak lehetnek:

* A fürt és a központi fájlkezelő között engedélyezett TCP-kapcsolatok számának növelése
* Engedélyezze a TÁVOLI magos fájlkezelő WAN-optimalizálási beállítását (Ez a beállítás távoli helyszíni fájlkezelőhöz vagy egy másik Azure-régióban lévő felhőalapú magfájl-fájlkezelőhöz használható.)
* A TCP-szoftvercsatorna pufferméretének növelése<sup>*</sup>
* A "mindig előre" beállítás engedélyezése a feleslegesen gyorsítótárazott fájlok csökkentéséhez<sup>*</sup>

<sup>*</sup>Ezek a korrekciók előfordulhat, hogy nem vonatkoznak minden rendszerre, a munkaterheléstől és a teljesítményigényektől függően.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Az Avere vFXT azure-hoz való optimalizálásának elősegítése

Ha kapcsolatba szeretne lépni a támogatási munkatársakkal ezekről az optimalizálásokról, használja a Segítség a [rendszerrel kapcsolatban](avere-vfxt-open-ticket.md)című témakörben leírt eljárást.
