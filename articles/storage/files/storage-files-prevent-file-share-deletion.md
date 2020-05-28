---
title: Véletlen törlés megakadályozása – Azure-fájlmegosztás
description: Ismerje meg az Azure-fájlmegosztás helyreállítható törlését, valamint azt, hogy miként használható az adathelyreállításhoz és a véletlen törlés megakadályozásához.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 9ffc065cb877c7f87cd38671f586f0754a42b2b8
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141585"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Azure-fájlmegosztás véletlen törlésének megakadályozása

Az Azure Storage mostantól helyreállítható törlést biztosít a fájlmegosztás (előzetes verzió) számára. A Soft delete lehetővé teszi az adatok helyreállítását, ha azt egy alkalmazás vagy más Storage-fiók felhasználója véletlenül törölte.

## <a name="how-soft-delete-preview-works"></a>A Soft Delete (előzetes verzió) működése

Ha engedélyezve van, a Soft delete lehetővé teszi a fájlmegosztás mentését és helyreállítását a törlésük után. Ha az adat törlődik, a rendszer véglegesen törli a törölt állapotot a végleges törlés helyett. Beállíthatja, hogy a rendszer a véglegesen törölt adatmennyiséget a végleges törlés előtt helyreállítsa.

A helyreállítható törlés engedélyezhető új vagy meglévő fájlmegosztás esetén. A helyreállítható törlés visszafelé is kompatibilis, így nem kell módosítania az alkalmazásokat, hogy kihasználhassa a Soft delete védelmét. 

A prémium szintű törölt fájlmegosztás esetén a fájlmegosztás kvótáját (a fájlmegosztás kiosztott méretét) a rendszer a teljes Storage-fiók kvótájának kiszámításához használja, amíg a rendszer a megosztást teljesen törli.

### <a name="availability"></a>Rendelkezésre állás

Az Azure-fájlmegosztás (előzetes verzió) Soft delete szolgáltatás minden tárolási szinten elérhető, minden tárolási fiók típusa és minden olyan régióban, amely Azure Files elérhető.

## <a name="configuration-settings"></a>Konfigurációs beállítások

A fájlmegosztás helyreállítható törlésének engedélyezése a Storage-fiók szintjén történik, a helyreállítható törlési beállítások a Storage-fiókban lévő összes fájlmegosztás esetében érvényesek. Amikor új Storage-fiókot hoz létre, a Soft delete alapértelmezés szerint ki van kapcsolva. A meglévő Storage-fiókok esetében a Soft delete is alapértelmezés szerint ki van kapcsolva. Bármikor válthat a Soft delete szolgáltatás be-és kikapcsolásával.

Ha engedélyezi a fájlmegosztás törlését, töröljön néhány fájlmegosztást, majd tiltsa le a helyreállítható törlést, ha a megosztások ebben az időszakban lettek mentve, akkor továbbra is elérheti és helyreállíthatja a fájlmegosztást. Ha engedélyezi a helyreállítható törlést, a megőrzési időtartamot is konfigurálnia kell.

A megőrzési időtartam azt jelzi, hogy a rendszer mennyi időt tárol és biztosít a helyreállítható fájlmegosztás tárolásához. A kifejezetten törölt fájlmegosztás esetén a megőrzési időszak órája az adatok törlésekor kezdődik. A törölt megosztások jelenleg 1 és 365 nap között maradhatnak.

Bármikor módosíthatja a helyreállítható törlés megőrzési időtartamát. A rendszer csak a megőrzési időszak frissítése után törölt megosztásokra alkalmazza a frissített megőrzési időtartamot. A rendszer a megőrzési idő frissítése előtt törölte a megosztásokat, és az adatok törlésekor beállított megőrzési időtartam alapján lejár.

Ha véglegesen törölni szeretne egy fájlmegosztást a lejárati idő előtt, törölje a törölt törlési állapotot, törölje a megosztást, tiltsa le a Soft delete parancsot, majd törölje újra a megosztást. Ezután engedélyezze újra a helyreállítható törlést, mivel az adott Storage-fiókban lévő más fájlmegosztás is ki lesz téve a véletlen törlésnek, miközben a Soft delete kikapcsol.

## <a name="pricing-and-billing"></a>Árak és számlázás

A standard és a prémium szintű fájlmegosztást a rendszer a felhasznált kapacitás esetén is felszámítja, nem pedig kiosztott kapacitással. Emellett a prémium szintű fájlmegosztást a pillanatképek díjszabása alapján számítjuk fel, a Soft delete állapotában. A normál fájlmegosztás számlázása a normál díjszabással történik a helyreállítható törlési állapotban. A beállított megőrzési időtartam után véglegesen törölt adatokért nem számítunk fel díjat.

Az Azure File Storage általános díjszabásával kapcsolatos további információkért tekintse meg az [azure file Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/files/).

Amikor először engedélyezte a Soft delete használatát, javasoljuk, hogy használjon egy kis megőrzési időtartamot, hogy jobban megértse, hogyan befolyásolja a szolgáltatás a számlát.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan engedélyezheti és használhatja a Soft deletet, folytassa a [Soft delete engedélyezésével](storage-files-enable-soft-delete.md)
