---
title: HSM fizikai biztonság - Azure dedikált HSM | Microsoft dokumentumok
description: Információk az Azure dedikált HSM-eszközeinek adatközpontokban való fizikai biztonságáról
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881023"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Az Azure dedikált HSM fizikai biztonsága

Az Azure dedikált HSM segítségével teljesítheti a kulcstárolás speciális biztonsági követelményeit. A teljes életciklusa során szigorú biztonsági eljárásokat alkalmaznak, hogy megfeleljenek az ügyfelek igényeinek.

## <a name="security-through-procurement"></a>Biztonság beszerzés útján

A Microsoft biztonságos beszerzési folyamatot követ. Mi kezeljük a felügyeleti láncot, és biztosítjuk, hogy az adott eszköz megrendelt és szállított eszköz az az eszköz, amely az adatközpontjainkba érkezik. Az eszközök szabotázs-esemény műanyag háton. Ezek biztonságos tárolóhelyen tárolódnak, amíg meg nem rendelik őket az adatközpont adattárában.  A HSM-eszközöket tartalmazó állványok nagy üzleti hatásnak (HBI) minősülnek. Az eszközök mindig le vannak zárva és videó megfigyelés alatt állnak elöl és hátul.

## <a name="security-through-deployment"></a>Biztonság telepítéssel

A HSM-ek a kapcsolódó hálózati összetevőkkel együtt az állványokba vannak telepítve. A telepítés után konfigurálni kell őket, mielőtt elérhetővé válnak az Azure dedikált HSM-szolgáltatás részeként. Ezt a konfigurációs tevékenységet a háttérellenőrzésen átesett Microsoft-alkalmazottak végzik. A Just In Time (JIT) adminisztrációja csak a megfelelő alkalmazottak hozzáférésének korlátozására szolgál, és csak a hozzáférés szükséges idejére. Az alkalmazott eljárások és rendszerek azt is biztosítják, hogy a HSM-eszközökkel kapcsolatos minden tevékenység et naplózza.

## <a name="security-in-operations"></a>A műveletek biztonsága

A HSM-ek hardveres készülékek (a tényleges HSM pci kártya a készüléken belül), így lehetséges, hogy alkatrészszintű problémák merülhetnek fel. A lehetséges problémák közé tartozik, de nem kizárólagosan a ventilátor és a tápegység meghibásodása. Az ilyen típusú esemény karbantartást vagy törési/javítási tevékenységeket igényel a cserélhető összetevők cseréjéhez.

### <a name="component-replacement"></a>Komponens cseréje

Az eszköz üzembe helyezése és az ügyfélfelügyelet alatt történő kiépítése után a rendszer csak azokat az összetevőket cseréli ki, amelyeket lecserélne. Ez az összetevő kívül esik a biztonsági határon, és nem okoz illetéktelen beavatkozási eseményt. A jegyrendszer arra szolgál, hogy a Microsoft mérnökei hozzáférjenek a HBI rack hátuljához. A jegy feldolgozásakor egy ideiglenes fizikai kulcs kerül kiadásra. Ez a kulcs hozzáférést biztosít a mérnöknek az eszközhöz, és lehetővé teszi számukra az érintett alkatrész cseréjét. Minden más hozzáférés (azaz a szabotázseseményt okozó) akkor történik, ha egy eszköz nincs lefoglalva az ügyfél számára, így minimalizálva a biztonsági és rendelkezésre állási kockázatot.  

### <a name="device-replacement"></a>Eszközcsere

Teljes eszközhiba esetén az összetevő meghibásodása során használthoz hasonló folyamat következik. Ha az ügyfél nem tudja nullázni az eszközt, vagy az eszköz ismeretlen állapotban van, az adatcsapágy-eszközök törlődnek, és egy rack-megsemmisítési tárolóba kerülnek. A kukába helyezett eszközök ellenőrzött és biztonságos módon semmisülnek meg. A HBI-állványról származó adathordozó eszközök nem hagyják el a Microsoft adatközpontját.

### <a name="other-rack-access-activities"></a>Egyéb rack-hozzáférési tevékenységek

Ha egy Microsoft-mérnöknek hozzá kell férnie a HSM-eszközök által használt állványhoz (például a hálózati eszközök karbantartásához), a rendszer szabványos biztonsági eljárásokat alkalmaz a HBI biztonságos állványhoz való hozzáféréshez. Minden belépés videó megfigyelés alatt lesz. A HSM-eszközök [FIPS 140-2 Level 3-ra](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) vannak érvényesítve, így a HSM-eszközökhöz való jogosulatlan hozzáférés az ügyfélnek lesz jelezve, és az adatok nullázva lesznek.

## <a name="logical-level-security-considerations"></a>Logikai szintű biztonsági szempontok

HSM-ek vannak kiépítve egy virtuális hálózat az ügyfél által létrehozott. Ez az ügyfél privát IUP-címtere.  Ez a konfiguráció értékes logikai hálózati szintelkülönítést biztosít, és csak az ügyfél számára biztosítja a hozzáférést. Ez azt jelenti, hogy minden logikai szintű biztonsági ellenőrzés az ügyfél felelőssége.

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás minden kulcsfontosságú fogalma, például a magas rendelkezésre állás és a biztonság és a támogathatóság, jól érthető az eszköz kiépítése, az alkalmazás tervezése vagy üzembe helyezése előtt.

* [Magas rendelkezésre állás](high-availability.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Megfigyelő](monitoring.md)
* [Telepítési architektúra](deployment-architecture.md)