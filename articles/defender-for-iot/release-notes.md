---
title: Az Azure Defender IoT újdonságai
description: Ebből a cikkből megtudhatja, hogy a Defender legújabb kiadásának újdonságai a IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/06/2021
ms.author: shhazam
ms.openlocfilehash: 72a6e50134647194679055a886b50b01f42e212d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629979"
---
# <a name="whats-new"></a>Újdonságok

A IoT 10,0 Defender a biztonságot, a felügyeletet és a használhatóságot javító funkciókat biztosít.

## <a name="security"></a>Biztonság

Ehhez a kiadáshoz a tanúsítvány-és jelszó-helyreállítási fejlesztések történtek.

### <a name="certificates"></a>Tanúsítványok
  
Ez a verzió a következőket teszi lehetővé:

- Az SSL-tanúsítványok feltöltése közvetlenül az érzékelőkre és a helyszíni felügyeleti konzolokra.
- Ellenőrzés végrehajtása a helyszíni felügyeleti konzol és a csatlakoztatott érzékelők között, valamint egy felügyeleti konzol és egy magas rendelkezésre állású felügyeleti konzol között. Az érvényesítés a lejárati dátumok, a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ hitelessége és a visszavont tanúsítványok listája alapján történik.  Ha az érvényesítés sikertelen, a munkamenet nem folytatódik.

Frissítések esetén:

- A frissítés során nem módosul az SSL-tanúsítvány vagy az érvényesítési funkció.
- A frissítés után az érzékelő és a helyszíni felügyeleti konzol rendszergazda felhasználók lecserélhetik az SSL-tanúsítványokat, vagy aktiválják az SSL-tanúsítvány érvényesítését a rendszerbeállítások, az SSL-tanúsítvány ablakában.  

Új telepítések esetén:

- Az első bejelentkezés során a felhasználóknak SSL-tanúsítványt (ajánlott) vagy helyileg generált önaláírt tanúsítványt kell használniuk (nem ajánlott)
- A tanúsítvány érvényesítése alapértelmezés szerint be van kapcsolva a friss telepítések esetében.

### <a name="password-recovery"></a>Jelszó-helyreállítás
  
Az érzékelő és a helyszíni felügyeleti konzol a rendszergazda felhasználók mostantól helyreállíthatók az Azure Defender for IoT portálon elérhető jelszavak. Korábban a jelszó-helyreállításhoz a támogatási csapat beavatkozás szükséges.

## <a name="onboarding"></a>Előkészítés

### <a name="on-premises-management-console---committed-devices"></a>Helyszíni felügyeleti konzol – véglegesített eszközök

A helyszíni felügyeleti konzolra való első bejelentkezés után a felhasználóknak most már szükségük van egy aktiválási fájl feltöltésére. A fájl tartalmazza a szervezeti hálózaton figyelni kívánt eszközök összesített számát. Ezt a számot a véglegesített eszközök számának nevezzük.
A véglegesített eszközök a bevezetési folyamat során vannak meghatározva az Azure Defender for IoT portálon, ahol létrejön az aktiválási fájl.
Az aktiválási fájl feltöltéséhez először a felhasználók és a felhasználók frissítésére van szükség.
A kezdeti aktiválás után a hálózaton észlelt eszközök száma túllépheti a véglegesített eszközök számát. Ez az esemény például akkor fordulhat elő, ha több érzékelőt csatlakozik a felügyeleti konzolhoz. Ha az észlelt eszközök száma és a véglegesített eszközök száma között eltérés tapasztalható, a felügyeleti konzolon megjelenik egy figyelmeztetés. Ha ez az esemény történik, fel kell töltenie egy új aktiválási fájlt.

### <a name="pricing-page-options"></a>Díjszabási oldal beállításai

A díjszabási oldalon új előfizetéseket hozhat létre az Azure Defender IoT, és meghatározhatja a hálózatban lévő véglegesített eszközöket.  
Emellett a díjszabási oldal segítségével kezelheti az érzékelőhöz társított meglévő előfizetéseket, és frissítheti az eszközre vonatkozó kötelezettségvállalást.

### <a name="view-and-manage-onboarded-sensors"></a>Beépített érzékelők megtekintése és kezelése

Az új hely és érzékelők portál oldalon A következőket teheti:

- Leíró információk hozzáadása az érzékelőhöz. Például az érzékelőhöz társított zóna, vagy a szabad szöveges címkék.
- Az érzékelő adatainak megtekintése és szűrése. Például megtekintheti a felhőhöz csatlakoztatott vagy helyileg felügyelt érzékelők adatait, vagy megtekintheti az érzékelőkre vonatkozó információkat egy adott zónában.  

## <a name="usability"></a>Használhatóság

### <a name="azure-sentinel-new-connector-page"></a>Az Azure Sentinel új összekötő lapja

Az Azure Defender for IoT adatösszekötő lapja az Azure Sentinelben újratervezve. Az adatösszekötő mostantól a IoT hubok helyett előfizetéseken alapul. lehetővé teszi, hogy az ügyfelek jobban kezeljék az Azure Sentinelhez való konfigurációs kapcsolataikat.

### <a name="azure-portal-permission-updates"></a>Azure Portal engedélyek frissítései  

A biztonsági olvasó és a biztonsági rendszergazdai támogatás hozzá lett adva.

## <a name="other-updates"></a>Egyéb frissítések

### <a name="access-group---zone-permissions"></a>Hozzáférési csoport – zóna engedélyei
  
A helyszíni felügyeleti konzol hozzáférési csoportjának szabályai nem tartalmazzák az adott zónához való hozzáférés engedélyezésének lehetőségét. Nem változik a helyek, régiók és üzleti egységek használatát használó szabályok meghatározása.   A frissítés után a megadott zónákhoz hozzáférést biztosító szabályokat tartalmazó hozzáférési csoportok módosulnak, hogy engedélyezze a hozzáférést a fölérendelt helyéhez, beleértve az összes zónáját.

### <a name="terminology-changes"></a>Terminológiai változások

Az eszköz kifejezése átnevezve lett az eszközre az érzékelő és a helyszíni felügyeleti konzolon, a jelentésekben és az egyéb megoldási felületeken.
Az érzékelő és a helyszíni felügyeleti konzol riasztásai esetében az eseményt kezelő kifejezés szervizelési lépések elnevezésű.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés a Defender IoT-vel való használatába](getting-started.md)
