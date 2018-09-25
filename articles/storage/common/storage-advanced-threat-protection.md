---
title: Az Azure Storage-ban fenyegetések figyelése
description: Állítsa be az Azure Storage komplex veszélyforrások elleni védelem fióktevékenység rendellenességek észlelése és csupán a vélhetően kárt okozó kísérleteket hozzáférjen a fiókjához.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995643"
---
# <a name="azure-storage-advanced-threat-protection"></a>Az Azure Storage komplex veszélyforrások elleni védelem

Az Azure Storage komplex veszélyforrások elleni védelem a fiók tevékenység észleli a rendellenességeket, és értesítést küld a vélhetően kárt okozó kísérleteket hozzáférjen a fiókjához. A védelmi réteget teszi cím fenyegetések szakértői biztonsági szakértelem vagy biztonság monitorozási rendszerek felügyelete nélkül.

Fenyegetések illesztett biztonsági riasztásokat, melyek tevékenységek rendellenességeinek fordulhat elő, ha definiálásával. Ezek a riasztások integrálása [az Azure Security Center](https://azure.microsoft.com/services/security-center/) többek között a gyanús tevékenység és a problémák kivizsgálásához és elhárításához fenyegetésekkel kapcsolatos javaslatok részleteit. 

> [!NOTE]
> Az Azure Storage komplex veszélyforrások elleni védelem jelenleg csak a Blob szolgáltatás érhető el. Biztonsági riasztások integrálva vannak az Azure Security Center, és az előfizetés adminisztrátorainak kapnak e-mailen keresztül.

Az Azure Storage komplex veszélyforrások elleni védelem fogadnak, olvassa el a diagnosztikai naplók, írása, és törölje a fenyegetések észlelése a Blob szolgáltatáshoz érkező kérések. A komplex veszélyforrások elleni védelem riasztások vizsgálatához kell [diagnosztikai naplók konfigurálása](storage-monitor-storage-account.md#configure-logging) minden szintet a Blob szolgáltatás naplóinak engedélyezése.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>A portálon a komplex veszélyforrások elleni védelem beállítása

1. Indítsa el az Azure Portalra a [ https://portal.azure.com ](https://portal.azure.com/).

2. Keresse meg a védeni kívánt Azure Storage-fiók-konfigurációs oldalához. Az a **beállítások** lapon jelölje be **komplex veszélyforrások elleni védelem**.

3. Az a **komplex veszélyforrások elleni védelem** konfigurálása panel
    * Kapcsolja be **ON** speciális *veszélyforrások elleni védelem*
    * Kattintson a **mentése** mentse az új vagy frissített komplex veszélyforrások elleni védelem szabályzatot.

![Azure Storage komplex veszélyforrások elleni védelem bekapcsolása](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Az anomáliák

Tárolási tevékenységével kapcsolatos szabálytalanságokat fordulhat elő, ha e-mail-értesítést a gyanús biztonsági eseményről információkat kap. Az esemény részleteinek a következők:

* az anomáliadetektálási jellege
* Tárfiók neve
* Tárolás típusa
* esemény időpontja

Az e-mailben is kitérnek a lehetséges okok és ajánlott műveletek vizsgálata és enyhítése érdekében a potenciális fenyegetést.

![Az Azure Storage komplex veszélyforrások elleni védelem riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Tekintse át, és az aktuális biztonsági riasztások kezelése az Azure Security Center [biztonsági riasztások csempe](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Részletek és az aktuális fenyegetések kivizsgálása és a jövőbeli fenyegetésekkel szembeni címzés műveletek egy meghatározott riasztásra kattintva biztosít.

![Az Azure Storage komplex veszélyforrások elleni védelem riasztási e-mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Védelmi figyelmeztetések

Szokatlan és vélhetően kárt okozó kísérleteket elérni vagy kiaknázni a storage-fiókok által előállított riasztások. Ezeket az eseményeket is aktiválhatja a következő riasztásokat:

* **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, amikor megváltozik a storage-fiók hozzáférési mintájában. Például ha valaki használta a storage-fiók egy szokatlan földrajzi helyről. Bizonyos esetekben a riasztás jogszerű műveleteket (egy új alkalmazást vagy fejlesztői karbantartási művelet) észlel. Más esetekben a Riasztás kártékony műveleteket (korábbi alkalmazott, külső támadó, stb.) észlel.

* **Szokatlan adatkinyerés**: Ez a riasztás akkor aktiválódik, ha változik az adatok kinyerése módja egy storage-fiókból. Például ha fért hozzá valaki egy szokatlan adatmennyiséget a storage-fiókban. Bizonyos esetekben a riasztás jogszerű műveleteket (karbantartási tevékenység) észlel. Más esetekben a Riasztás kártékony műveleteket (adatok kiszűrése vagy illetéktelen behatolás, adatok jogosulatlan átvitelét) észlel.

* **Szokatlan névtelen hozzáférés:** Ez a riasztás akkor aktiválódik, amikor megváltozik a storage-fiók hozzáférési mintájában. Tegyük fel például, névtelenül fért hozzá valaki egy storage-fiókot. Bizonyos esetekben a riasztás jogszerű hozzáférést egy nyilvános olvasási hozzáférés használatával észlel. Más esetekben a riasztás észlel, amely kihasználja a nyilvános olvasási hozzáférés egy tárolóhoz és annak blobjaihoz jogosulatlan hozzáférés.

* **Váratlan törlése:** Ez a riasztás akkor aktiválódik, amikor egy vagy több váratlan törlési műveleteket a rendszer egy tárfiókot, a storage-fiók előzményadatok elemzése alapján. Például tegyük fel, hogy valaki végrehajtott egy *DeleteBlob* művelet egy új alkalmazással és a egy új IP-címről. Bizonyos esetekben a riasztás (a rendszergazda használt egy másik böngészőben üzleti utazás közben) jogszerű műveleteket észlel. Más esetekben a Riasztás kártékony műveleteket észlel (egy támadó adatok törlése). 
 
* **Hozzáférési engedély módosítása:** Ez a riasztás akkor aktiválódik, ha a tárfiók hozzáférési jogosultságaik váratlan változását van. Tegyük fel például, hogy valaki módosította a hozzáférési engedélyt, a storage-fiók egy új alkalmazást, és a egy új IP-címről. Bizonyos esetekben a riasztás (a rendszergazda használt egy másik böngészőben üzleti utazás közben) jogszerű műveleteket észlel. Más esetekben a Riasztás kártékony műveleteket észlel (például egy támadó-fiókhoz való hozzáférés révén jogosultságával növelésével). 

* **Azure Cloud Service-csomag feltöltéséhez:** Ez a riasztás akkor aktiválódik, ha van egy nem várt feltöltése az Azure Cloud Service-csomag (*.cspkg* fájl) egy tárfiókba. Tegyük fel például, egy *.cspkg* új IP-címről a feltöltött fájl. Egyes esetekben a riasztás jogszerű műveleteket észlel. Más esetekben a Riasztás kártékony műveleteket (például egy felhőalapú szolgáltatás egy rosszindulatú service üzemelő példánya előkészítésekor feltöltött csomag) észlel.    
   

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [naplók az Azure Storage-fiókok ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Tudjon meg többet [Azure Security Centerben](../../security-center/security-center-intro.md)