---
title: Azure VMware-megoldás a CloudSimple által – áttekintés
description: Ismerje meg az Azure-beli VMware-megoldások funkcióit, forgatókönyveit és előnyeit a CloudSimple szolgáltatásban.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 01fd132b3e43d15c5f2eee0114ef09dbac6df2a6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812453"
---
# <a name="what-is-vmware-solution-on-azure-by-cloudsimple"></a>Mi az Azure-beli VMware-megoldás a CloudSimple

Az Azure-beli **VMware-megoldás a CloudSimple** egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a VMware platform futtatását az Azure-ban. Ez a megoldás magában foglalja az vSphere, a vCenter, a vSAN, a NSX-T és a megfelelő eszközöket.
A VMware-környezet natív módon fut az Azure-beli operációs rendszer nélküli infrastruktúrában az Azure felhőalapú helyein. A szolgáltatás minden olyan funkciót magában foglal, amely a VMware-platformok hatékony és biztonságos felhasználásához szükséges.

![VMware-megoldás az Azure-ban a CloudSimple áttekintése](media/azure-vmware-solution-by-cloudsimple.png)

## <a name="features"></a>Szolgáltatások

* VMware felhőalapú környezetek igény szerinti, önkiszolgáló üzembe helyezése. Igény szerinti kapacitás hozzáadásának és eltávolításának lehetősége
* VMware platform üzembe helyezése, frissítés, felügyeleti sík biztonsági mentése, állapot/kapacitás figyelése, riasztások, hibaelhárítás és szervizelés.
* A VMware engedélyezéséhez szükséges hálózati szolgáltatások, beleértve az L2/L3-szolgáltatásokat és a tűzfalszabályok kezelését.
* Edge típusú hálózati szolgáltatások, beleértve a VPN-t, a nyilvános IP-címet és az internetes átjárókat. Ezek a peremhálózati szolgáltatások az Azure-ban futnak, és az Azure megfelelő biztonsági és DDoS-védelmét végzik.
* Kapacitás foglalása alacsonyabb költségek mellett.
* Nagy sebességű, kis késleltetésű kapcsolat az Azure-ban és a helyszínen.
* Megoldási architektúrák az ügyfelek számára, hogy integrált módon használják az Azure-szolgáltatásokat, kihasználhatja ezt az egyedi "VMware Cloud in a Public Cloud" architektúrát. Ezek az Azure-szolgáltatások közé tartoznak az Azure AD, a Storage, az Application Gateway és mások.
* Az infrastruktúra teljes mértékben dedikált, és fizikailag el van különítve a többi ügyfél infrastruktúráján.
* Felügyeleti funkciók, például a tevékenységek kezelése, a használat, a számlázás/mérés és a felhasználói felügyelet.
* nonstop ügyfélszolgálata.

## <a name="benefits"></a>Előnyök

* **Működési folytonosság:** A CloudSimple natív hozzáférést biztosít a VMware platformokhoz. A CloudSimple architektúra kompatibilis a meglévővel:
  * Alkalmazások
  * Műveletek
  * Hálózat
  * Biztonság
  * Tartalék
  * Vészhelyreállítás
  * Naplózás
  * Megfelelőségi eszközök
  * Folyamatok
* **Nincs újraképzés:** A VMware platform Compatibility lehetővé teszi a meglévő képességek és ismeretek használatát.
* **Infrastruktúra-agilitás:** Már nem kell előre megjósolnia az összes kapacitási igényt, és végül az elpazarolt kapacitással vagy az infrastruktúra hiányával kell foglalkoznia. A CloudSimple felhőalapú szolgáltatásként érhető el, és bármikor felveheti vagy csökkentheti a kapacitást.
* **Biztonsági** Az Azure-on keresztül elérhető CloudSimple-környezet beépített DDoS-védelmet és biztonsági monitorozást biztosít.
* **Alacsonyabb díj:** A CloudSimple platform nagy mértékben lett tervezve, és magas szintű automatizálást, működési hatékonyságot és méretgazdaságosságot biztosít. A CloudSimple emellett olyan megoldási architektúrákat tesz közzé, amelyek kihasználják a VMware nyilvános felhőben való jelenlétét a költségek csökkentése érdekében. Ilyenek például az Azure AD, az Azure Storage-ba való biztonsági mentés, az Application Gateway, a Load Balancer és egyebek.
* **Egy új hibrid platform:** A szolgáltatás lehetővé teszi a nagy sebességű, kis késésű hozzáférést az Azure többi részénél. Emellett a CloudSimple-kezelés lehetővé teszi a VMware Virtual Machines és az Azure többi részének egységes kezelését ugyanazzal a felhasználói felülettel és API-val. A fejlesztői csapatok integrált, egységes módon vehetik igénybe a nyilvános és a privát platformokat is.
* **Infrastruktúra-figyelés, hibaelhárítás és támogatás:** A CloudSimple szolgáltatásként működteti a mögöttes infrastruktúrát. A meghiúsult hardver automatikusan lecserélve. Koncentrálhat a felhasználásra, miközben a CloudSimple biztosítja, hogy a környezet zökkenőmentesen fusson.
* **Szabályzatok kompatibilitása:** Megtarthatja VMware-alapú eszközeit, biztonsági eljárásait, auditálási gyakorlatait és megfelelőségi tanúsítványait.

## <a name="scenarios"></a>Forgatókönyvek

* **Adatközpont-kivonulás vagy áttelepítés:** További kapacitást érhet el, ha eléri a meglévő adatközpont korlátait, vagy frissíti a hardvert. Könnyedén felveheti a szükséges kapacitást a felhőbe, és megkerülheti a hardveres frissítések kezelésének fejfájását. Csökkentse a felhőbe való Migrálás kockázatát és költségeit az időigényes átalakítások vagy az újraarchitektúrák összehasonlításával. A felhőbe történő Migrálás felgyorsításához használja a jól ismert VMware-eszközöket és-képességeket. A felhőben az Azure-szolgáltatások segítségével modernizálhatja alkalmazásait a tempóban.
* **Igény szerinti Kibontás:** Bontsa ki a felhőt a nem várt igények kielégítéséhez, például az új fejlesztési környezetekhez vagy a szezonális kapacitások kitöréséhez. Igény szerint egyszerűen hozhat létre új kapacitást, és megtarthatja, amíg szüksége van rá. Csökkentse a kezdeti befektetéseket, gyorsítsa fel a kiépítés sebességét, és csökkentse a bonyolultságot ugyanazzal az architektúrával és házirenddel mind a helyszínen, mind a felhőben.
* **Vész-helyreállítási és virtuális asztalok az Azure-felhőben:** Távoli hozzáférés létrehozása az Azure-felhőben tárolt adatelemekhez, alkalmazásokhoz és asztali gépekhez. A nagy sávszélességű kapcsolatokkal gyorsan tölthet le/tölthet le adatokat az incidensekről. Az alacsony késleltetésű hálózatok gyors válaszidőt biztosítanak a felhasználók számára egy asztali alkalmazástól. A CloudSimple segítségével egyszerűen replikálhatja az összes házirendjét és hálózatát a felhőben a CloudSimple-portál és az ismerős VMware-eszközök használatával. A könnyű helyreállítás és a replikálás nagy mértékben csökkenti a DR-és VDI-implementációk létrehozásának és kezelésének erőfeszítéseit és kockázatait.
* **Nagy teljesítményű alkalmazások és adatbázisok:** A CloudSimple egy hiperkonvergens-architektúrát biztosít, amely a legigényesebb VMware-alapú számítási feladatok futtatására szolgál. Az Oracle, a Microsoft SQL Server, a köztes rendszerek és a nagy teljesítményű, nem SQL-adatbázisok futtatása. A felhőt saját adatközpontként használhatja nagysebességű, 25 GB/s-os hálózati kapcsolatokkal, amelyek lehetővé teszik a helyszíni, VMware Azure-beli és Azure-beli privát számítási feladatokon alapuló hibrid alkalmazások futtatását a teljesítmény veszélyeztetése nélkül.
* **Valódi hibrid:** Egyesítse a DevOps a VMware és az Azure között. Optimalizálja a VMware-felügyeletet az Azure-szolgáltatásokhoz és-megoldásokhoz, amelyek az összes számítási feladatra alkalmazhatók. A nyilvános felhőalapú szolgáltatásokat anélkül érheti el, hogy kibővíti az adatközpontot, vagy újratervezi az alkalmazásokat. Az Azure-beli VMware-alkalmazások identitásának, hozzáférés-vezérlési házirendjeinek, naplózásának és figyelésének központosítása.

![Forgatókönyvek](media/cloudsimple-scenarios.png)

## <a name="next-steps"></a>További lépések

* [CloudSimple szolgáltatás létrehozása](quickstart-create-cloudsimple-service.md)
* [Privát felhő létrehozása](quickstart-create-private-cloud.md)