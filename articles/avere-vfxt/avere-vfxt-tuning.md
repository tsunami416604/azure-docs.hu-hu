---
title: Avere vFXT-fürt finomhangolása – Azure
description: Az Azure-beli avere-vFXT teljesítményének optimalizálására szolgáló egyéni beállítások áttekintése
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 17e55dbe84cda87ee902c94e0024c9a3aad8b31b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698343"
---
# <a name="cluster-tuning"></a>Fürt finomhangolása


A legtöbb vFXT-fürt kihasználhatja a testreszabott teljesítmény-beállításokat. Ezek a beállítások segítenek abban, hogy a fürt a legjobban működjön az adott munkafolyamattal, adatkészlettel és eszközökkel. 

Ezt a testreszabást a támogatási szakembernek kell elvégeznie, mert általában olyan funkciókat konfigurál, amelyek nem érhetők el a avere Vezérlőpultján.

Ez a szakasz az egyéni hangolás néhány lehetséges módját ismerteti.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Általános optimalizálások

Ezeket a módosításokat az adatkészlet tulajdonságai vagy a munkafolyamat stílusa alapján lehet használni.

* Ha a számítási feladat írási nehéz, növelje az írási gyorsítótár méretét a 20%-os alapértéktől. 
* Ha az adatkészlet sok kisméretű fájlt foglal magában, növelje a fürt gyorsítótára fájlméretének számát. 
* Ha a munka két adattár közötti másolást vagy áthelyezést foglalja magában, módosítsa az adatok áthelyezéséhez használt szálak számát: 
  * A sebesség növeléséhez növelheti a használt párhuzamos szálak számát.
  * Ha a háttérbeli tároló kötete túlterhelt, előfordulhat, hogy csökkentenie kell a használt párhuzamos szálak számát.
* Ha a fürt Nfsv4 névleképezője ACL-eket használó központi Filer adatait gyorsítótárazza, engedélyezze a hozzáférési mód gyorsítótárazását az egyes ügyfelek számára a fájlok engedélyezésének egyszerűsítése érdekében.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS vagy Cloud Gateway-optimalizálás

A vFXT-fürt és a Felhőbeli tárolók közötti nagyobb adatsebességek kihasználása egy felhőalapú NAS-vagy átjáró-forgatókönyvben (ahol a vFXT-fürt egy felhőalapú tárolóhoz biztosít NAS-stílusú hozzáférést), a képviselő a következőhöz hasonló beállítások módosítását javasolja: adatok agresszív leküldése a tárolási kötetre a gyorsítótárból:

* Növelje meg a fürt és a tároló közötti TCP-kapcsolatok számát

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud burst vagy hibrid WAN-optimalizálás

A felhőalapú burst-forgatókönyv vagy a hibrid tárolás WAN-optimalizálási forgatókönyve (ahol a vFXT-fürt biztosítja a Felhőbeli és a helyszíni hardveres tárolók közötti integrációt), ezek a változások hasznosak lehetnek:

* Növelje a fürt és a Core Filer között engedélyezett TCP-kapcsolatok számát
* Engedélyezze a távoli Core Filer WAN-optimalizálási beállítását (ez a beállítás használható távoli helyszíni Filer-hez vagy egy másik Azure-régióban lévő Cloud Core Filer-hoz.)
* Növelje a TCP-szoftvercsatorna pufferének méretét (a munkaterhelés és a teljesítmény igényeitől függően)
* Engedélyezze a "mindig továbbítható" beállítást a redundánsan gyorsítótárazott fájlok csökkentése érdekében (a munkaterhelés és a teljesítmény igényeitől függően)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Az Azure-hoz készült avere-vFXT optimalizálásának elősegítése

A [Segítség kérése](avere-vfxt-open-ticket.md) a rendszerhez című témakörben ismertetett eljárással vegye fel a kapcsolatot a támogatási munkatársakkal ezekkel az optimalizálásokkal kapcsolatban. 