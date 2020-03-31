---
title: Azure VMware-megoldás a CloudSimple-től – áttekintés
description: Ismerje meg a VMware-megoldás funkcióit, forgatókönyveit és előnyeit az Azure-ban a CloudSimple szolgáltatás által.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7b5017cdeade14e53778b58154456412507c5ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024874"
---
# <a name="what-is-azure-vmware-solution-by-cloudsimple"></a>Mi az Azure VMware megoldás a CloudSimple-től?

**Az Azure VMware Solution by CloudSimple** egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a VMware platform futtatását az Azure-ban. Ez a megoldás tartalmazza a vSphere, vCenter, vSAN, NSX-T és a megfelelő eszközöket. A VMware-környezet natívan fut az Azure operációs rendszer nélküli infrastruktúrán az Azure felhőbeli helységein. A szolgáltatás tartalmazza a VMware platformok hatékony és biztonságos felhasználásához szükséges összes funkciót.

![VMware-megoldás az Azure-ban a CloudSimple áttekintése](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>Szolgáltatások

* VMware felhőkörnyezetek igény szerinti önkiszolgáló kiépítése. Kapacitás hozzáadása és eltávolítása igény szerint.
* VMware platform telepítése, frissítése, felügyeleti sík biztonsági mentése, állapot/kapacitás figyelése, riasztás, hibaelhárítás és szervizelés.
* Alávetítési hálózati szolgáltatások szükségesek a VMware engedélyezéséhez, beleértve az L2/L3 szolgáltatásokat és a tűzfalszabály-kezelést.
* Peremhálózati hálózati szolgáltatások, beleértve a VPN-t, a nyilvános IP-címet és az internetes átjárókat. Ezek a szolgáltatások az Azure-on futnak, és az Azure biztonsági és DDoS-védelmét hordozzák.
* Kapacitáslefoglalás a költségek csökkentése érdekében.
* Nagy sebességű, alacsony késleltetésű kapcsolat az Azure-ral és a helyszíni kapcsolattal.
* Megoldásarchitektúrák az ügyfelek számára, hogy integrált módon használják az Azure-szolgáltatásokat, és kihasználják ezt az egyedülálló "VMware felhő nyilvános felhőben" architektúrát. Az Azure-szolgáltatások közé tartozik az Azure AD, a tárolás, az alkalmazásátjárók és mások.
* Olyan infrastruktúra, amely teljes mértékben az Ön számára van elkülönítve, és fizikailag el van szigetelve más ügyfelek infrastruktúrájától.
* A felügyeleti funkciók, például a tevékenységkezelés, a használat, a számlázás/mérés és a felhasználókezelés.
* 24x7 ügyfélszolgálat.

## <a name="benefits"></a>Előnyök

* **Működési folytonosság**. A CloudSimple natív hozzáférést biztosít a VMware platformokhoz. A CloudSimple architektúra kompatibilis a meglévő:
    * Alkalmazások
    * Műveletek
    * Biztonság
    * Backup
    * Vészhelyreállítás
    * Naplózás
    * Megfelelőségi eszközök
    * Folyamatok
* **Nincs átképzés**. A VMware platformkompatibilitás lehetővé teszi a meglévő készségek és ismeretek használatát.
* **Infrastruktúra agilitás**. Többé nem kell megjósolnia az összes kapacitásigényét, majd elpazarolt kapacitás- vagy infrastruktúrahiányt kell maga után végeznie. A CloudSimple felhőszolgáltatásként érhető el, és bármikor hozzáadhat vagy csökkentheti a kapacitást
* **Biztonság**. A CloudSimple környezethez való hozzáférés az Azure-on keresztül beépített DDoS-védelmet és biztonsági figyelést biztosít.
* **Alacsonyabb költség**. A CloudSimple platform nagy tervezésű, és magas szintű automatizálást, működési hatékonyságot és méretgazdaságosságot biztosít. Továbbá a CloudSimple olyan megoldásarchitektúrákat tesz közzé, amelyek a költségek csökkentése érdekében kihasználják a VMware nyilvános felhőben való jelenlétét. Ilyenek például az Azure AD, biztonsági mentés az Azure storage, alkalmazásátjáró, terheléselosztó és mások.
* **Egy új hibrid platform**. A szolgáltatás lehetővé teszi a nagy sebességű, alacsony késésű hozzáférést az Azure többi részéhez. Továbbá a CloudSimple-felügyelet lehetővé teszi a VMware virtuális gépek és az Azure többi részének egységes kezelését ugyanazzal a felhasználói felülettel és API-val. A fejlesztőcsapatok integrált, következetes módon is kihasználhatják a nyilvános és a privát platformok előnyeit.
* **Infrastruktúra figyelése, hibaelhárítása és támogatása**. A CloudSimple szolgáltatásként működteti az alapul szolgáló infrastruktúrát. A hibahardvert a rendszer automatikusan lecseréli. A felhasználásra összpontosíthat, míg a CloudSimple biztosítja a környezet zökkenőmentes futását.
* **Házirend-kompatibilitás**. Tartsa meg vmware-alapú eszközeit, biztonsági eljárásait, naplózási gyakorlatait és megfelelőségi tanúsítványait.

## <a name="scenarios"></a>Forgatókönyvek

* **Adatközpont-kivonás vagy áttelepítés**. További kapacitást kaphat, ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert. A szükséges kapacitást egyszerűen hozzáadhatja a felhőben, és kiküszöbölheti a hardverfrissítések kezelésével kapcsolatos fejfájást. Csökkentse a felhőbe való migrálás kockázatát és költségét az időigényes konverziókhoz vagy újraarchitektúrákhoz képest. A felhőbe való bevetés felgyorsításához használja a jól ismert VMware eszközöket és készségeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait az Ön tempójában.
* **Bővítse ki igény szerint**. Bontsa ki a felhőt a nem várt igények, például az új fejlesztési környezetek vagy a szezonális kapacitásadatlöketek kielégítésére. Igény szerint egyszerűen létrehozhat új kapacitást, és csak addig tarthatja meg, amíg szüksége van rá. Csökkentse az előzetes befektetést, gyorsítsa fel a kiépítés sebességét, és csökkentse az összetettséget ugyanazzal az architektúrával és szabályzatokkal mind a helyszíni, mind a felhőben.
* **Vészhelyreállítás és virtuális asztalok az Azure-felhőben.** Távoli hozzáférést hozhat létre az adatokhoz, alkalmazásokhoz és asztalokhoz az Azure-felhőben. A nagy sávszélességű kapcsolatokkal gyorsan tölthet fel és tölthet le adatokat az incidensek helyreállítása érdekében. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak, amelyet a felhasználók elvárnak egy asztali alkalmazástól. A CloudSimple segítségével a CloudSimple portálés a jól ismert VMware-eszközök segítségével egyszerűen replikálhatja az összes szabályzatot és hálózatot a felhőben. A helyreállítás és a replikáció egyszerűsége jelentősen csökkenti a DR és VDI implementációk létrehozásának és kezelésének erőfeszítését és kockázatát.
* **Nagy teljesítményű alkalmazások és adatbázisok**. A CloudSimple egy hiperkonvergens architektúrát biztosít, amely a legigényesebb VMware-számítási feladatok futtatására szolgál. Futtassa az Oracle, a Microsoft SQL server, a middleware rendszerek és a nagy teljesítményű, nem SQL adatbázisok futtatását. A felhőt saját adatközpontként saját adatközpontként saját adatközpontként tapasztalhatja meg a nagy sebességű, 25 gbagbói hálózati kapcsolatokkal, amelyek lehetővé teszik a helyszíni, az Azure-beli VMware és az Azure privát számítási feladatait átfogó hibrid alkalmazások futtatását a teljesítmény veszélyeztetése nélkül.
* **Igazi hibrid**. A DevOps egyesítése a VMware és az Azure területén. Optimalizálja a VMware felügyeletét az Azure-szolgáltatásokhoz és -megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók. Az adatközpont bővítése vagy az alkalmazások újratervezése nélkül is elérheti a nyilvános felhőszolgáltatásokat. Központosíthatja az identitásokat, a hozzáférés-vezérlési szabályzatokat, a naplózást és a figyelést az Azure-beli VMware-alkalmazásokhoz.

![Forgatókönyvek](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Privát felhő létrehozása](quickstart-create-private-cloud.md)
