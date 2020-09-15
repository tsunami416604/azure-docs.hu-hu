---
title: Élő videó-elemzés IoT Edge kvóták és korlátozások – Azure
description: Ez a cikk az élő videók elemzését ismerteti IoT Edge kvóták és korlátozások alapján.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529230"
---
# <a name="quotas-and-limitations"></a>Kvóták és korlátozások

Ez a cikk a IoT Edge modul élő videós elemzésének kvótáit és korlátozásait sorolja fel.

## <a name="maximum-period-of-disconnected-use"></a>Leválasztott használat maximális időtartama

Az Edge-modul képes fenntartani a hálózati kapcsolat átmeneti elvesztését. Ha a modul több mint 36 órával továbbra is megszakadt, akkor a rendszer inaktiválja a futó Graph-példányokat, és a további közvetlen metódusok hívása le lesz tiltva.

Ha a peremhálózati modult működési állapotba kívánja folytatni, akkor vissza kell állítania a hálózati kapcsolatot, és a modulnak képesnek kell lennie az Azure Media Service-fiókkal való sikeres kommunikációra.

## <a name="maximum-number-of-graph-instances"></a>Graph-példányok maximális száma

A modulban legfeljebb 1000 gráf példány adható meg (GraphInstanceSet használatával létrehozva).

## <a name="maximum-number-of-graph-topologies"></a>Gráf-topológiák maximális száma

Modulon legfeljebb 50 gráf-topológiát lehet létrehozni (GraphTopologySet-on keresztül létrehozva).

## <a name="limitations-on-graph-topologies-at-preview"></a>A Graph-topológiák korlátozásai az előzetes verzióban

Az előzetes kiadásban a különböző csomópontokra vonatkozó korlátozások is csatlakoztathatók a Media Graph-topológiában.

* RTSP-forrás
   * Graph-topológia esetében csak egy RTSP-forrás engedélyezett.
* Frame rate Filter processzor
   * Közvetlenül az RTSP forrás-vagy mozgásészlelési processzorról kell, hogy legyen.
   * HTTP-vagy gRPC-bővítményi processzor esetén nem használható.
   * Mozgásészlelési processzorról nem lehet upstream.
* HTTP-bővítmény processzora
   * Graph-topológiában legfeljebb egy ilyen processzor lehet.
* gRPC-bővítmény processzora
   * Graph-topológiában legfeljebb egy ilyen processzor lehet.
* Mozgásészlelési processzor
   * Közvetlenül az RTSP-forrástól kell, hogy legyen.
   * Graph-topológiában legfeljebb egy ilyen processzor lehet.
   * HTTP-vagy gRPC-bővítményi processzor esetén nem használható.
* Signal Gate processzor
   * Közvetlenül az RTSP-forrástól kell, hogy legyen.
* Eszköz fogadója 
   * Közvetlenül az RTSP-forrás vagy a Signal Gate-processzor között kell lennie.
* File mosogató
   * Közvetlenül a Signal Gate-processzorról kell, hogy legyen.
   * Nem lehet közvetlenül a HTTP-vagy gRPC-bővítmény processzora, vagy a mozgásérzékelő processzora
* IoT Hub fogadó
   * Nem lehet közvetlenül a IoT Hub forrásának vége.

Ha a mozgásészlelési és a szűrési sebességű processzor-csomópontok is szerepelnek, akkor az RTSP-forrás csomóponthoz vezető csomópontok láncában kell lenniük.

## <a name="limitations-on-media-service-operations-at-preview"></a>A Media Service-műveletekre vonatkozó korlátozások előzetes verzióban

Az előzetes kiadás időpontjában a IoT Edge Live Video Analytics nem támogatja a következőket:

* A Media Service-fiók egyik előfizetésből a másikba való migrálása megszakítás nélkül.
* Több Storage-fiók is használható a Media Service-fiókkal.
* Az egyszerű szolgáltatásnév adatait dinamikusan, újraindítás nélkül módosíthatja a modul kívánt tulajdonságaiban.

Csak az RTSP protokollt támogató IP-kamerákat lehet használni. Az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőségi termékek](https://www.onvif.org/conformant-products) lapon találja. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.

Emellett ezeket a kamerákat a H. 264 videó és az AAC hang használatára kell beállítani. Más kodekek jelenleg nem támogatottak. 

## <a name="next-steps"></a>Következő lépések

[Áttekintés](overview.md)
