---
title: A Microsoft Azure Data Box biztonsági áttekintése | Microsoft Docs in data
description: Az eszköz, a szolgáltatás és a Data Boxban lévő adatok Azure Data Box biztonsági szolgáltatásainak ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 3e871b8d4de8ff83e260aded89e09ad5b17b3297
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799230"
---
# <a name="azure-data-box-security-and-data-protection"></a>Az Azure Data Box biztonsági és adatvédelmi szolgáltatásai

A Data Box biztonságos megoldást kínál az adatok védelmére, mivel garantálja, hogy az adatokat csak a jogosult entitások tekinthetik meg, módosíthatják vagy törölhetik. A cikk az Azure Data Box biztonsági szolgáltatásait ismerteti, amelyekkel biztosítható a Data Box megoldás összetevőinek és a bennük tárolt adatoknak a védelme. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Az adatok áramlása az összetevők között

A Microsoft Azure Data Box megoldást négy, egymással együttműködő fő összetevő alkotja:

- **Az Azure-ban üzemeltetett Azure Data Box szolgáltatás** – a felügyeleti szolgáltatás, amellyel eszközmegrendeléseket hozhat létre, konfigurálhatja az eszközöket, majd nyomon követheti a rendelést a teljesítésig.
- **Data Box-eszköz** – az átviteli eszköz, amelyet helyszíni adatai az Azure-ba való importálásához kiküldünk Önnek. 
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek** – az infrastruktúra védeni kívánt adatokat tartalmazó, a Data Box-eszközhöz csatlakozó ügyfelei.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez általában a létrehozott Azure Data Box-erőforráshoz csatolt tárfiók.

A következő ábra az adatok áramlását mutatja az Azure Data Box megoldásban a helyszíni rendszerekről az Azure-ba.

![A Data Box biztonsága](media/data-box-security/data-box-security-2.png)

Az adatáramlás a megoldáson keresztül, mert eseményeket naplózza, és a naplók jönnek létre. További információért ugorjon [Tracking és az eseménynaplózás az Azure Data Box](data-box-logs.md).

## <a name="security-features"></a>Biztonsági funkciók

A Data Box biztonságos megoldást kínál az adatok védelmére, mivel garantálja, hogy az adatokat csak a jogosult entitások tekinthetik meg, módosíthatják vagy törölhetik. A megoldás biztonsági szolgáltatásai a lemezekre és a rajtuk tárolt adatokat védő szolgáltatásra is vonatkoznak. 

### <a name="data-box-device-protection"></a>A Data Box-eszköz védelme

A Data Box-eszköz védelmét az alábbi funkciók biztosítják:

- Az eszköz strapabíró tokkal rendelkezik, amely ellenáll az ütéseknek, valamint a kedvezőtlen szállítási és időjárási körülményeknek. 
- Eredetiséget jelző pecsétek, amelyek jelzik, ha az eszközhöz a szállítás során illetéktelenek fértek hozzá.
- A hardverhez vagy a szoftverekhez való illetéktelen hozzáférés észlelése esetén az eszköz nem működtethető.
- Csak Data Box-kompatibilis szoftvert futtat.
- A rendszerindítás zárolt állapotban történik.
- Az eszközhöz való hozzáférés védelmét zárolásfeloldási jelszó biztosítja.
- Az adatok külső helyre és helyről történő másolásához hozzáférési hitelesítő adatokat kell megadni. Az összes hozzáférést a **hitelesítő adatai** oldal az Azure portal van bejelentkezve a [tevékenységeket tartalmazó naplók](data-box-logs.md#query-activity-logs-during-setup).

### <a name="data-box-data-protection"></a>A Data Box-adatok védelme

A Data Box szolgáltatás bejövő és kimenő adatainak biztonságát az alábbi szolgáltatások biztosítják:

- Az inaktív adatok 256 bites AES-titkosítással védettek.
- A titkosított protokollok átvitel közben is biztosítják az adatok védelmét.
- Az adatokat biztonságosan törölheti az eszközről, miután az Azure-ba való feltöltés befejeződött. Az adatok törlése a NIST 800-88r1 szabványoknak megfelelően történik. Az adatok törlését esemény rögzíti a [rendelési előzmények](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>A Data Box szolgáltatás védelme

A Data Box szolgáltatás biztonságát az alábbi funkciók biztosítják.

- A Data Box szolgáltatáshoz való hozzáféréshez a cégnek olyan Azure-előfizetéssel kell rendelkeznie, amely tartalmazza a Data Boxot. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el.
- A Data Box szolgáltatás az Azure-ban üzemel, így az Azure biztonsági funkciói védik. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://www.microsoft.com/TrustCenter/Security/default.aspx) talál további információt.
- A Data Box-rendelés hozzáférést keresztül szerepköreit, szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható. További információkért lásd: [hozzáférés-vezérlés a Data Box-rendelését beállítása](data-box-logs.md#set-up-access-control-on-the-order)
- A Data Box szolgáltatás tárolja a zárolásfeloldási jelszót, amellyel az eszköz zárolása a szolgáltatásban feloldható.
- A Data Box szolgáltatás tárolja a megrendelések adatait és állapotát a szolgáltatásban. A megrendelés törlésekor ezek az információk is törlődnek.

## <a name="managing-personal-data"></a>Személyes adatok kezelése

Az Azure Data Box a szolgáltatás alábbi főbb példányaiban gyűjt és jelenít meg személyes adatokat:

- **Értesítési beállítások** – Amikor létrehoz egy megrendelést, a felhasználók e-mail-címeit az értesítési beállítások alatt konfigurálhatja. Ezeket az adatokat az adminisztrátor megtekintheti. A szolgáltatás törli az adatokat, amikor a feladat eléri a végállapotot, illetve ha Ön törli a megrendelést.

- **Megrendelés részletei** – miután létrejött a megrendelés, a felhasználók szállítási címét, e-mail-címét és kapcsolattartási adatait az Azure Portal tárolja. A mentett információk a következők:

  - Kapcsolattartó neve
  - Telefonszám
  - E-mail-cím
  - Utca, házszám
  - Város
  - Irányítószám
  - Állapot
  - Ország/tartomány/régió
  - Szállítmányozó fiókszáma
  - Szállítmány nyomkövetési száma

    A Data Box a feladat befejezésekor vagy törlésekor törli a rendelés részleteit.

- **Szállítási cím** – A rendelés feladása után a Data Box szolgáltatás kiadja a szállítási címet külső szállítmányozóknak, például a UPS-nek vagy a DHL-nek. 

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Biztonsági irányelvek – referencia

A Data Box szolgáltatásra a következő biztonsági irányelvek vonatkoznak: 

|Irányelv   |Leírás   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Víz és por elleni védelem         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Ellenállás a kedvezőtlen szállítási körülményeknek          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Biztonságos belsővezérlőprogram-frissítés         |
|[2. szintű FIPS 140-2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Adatvédelem         |
|[NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Adattisztítás         |

## <a name="next-steps"></a>További lépések

- A [Data Box használatára vonatkozó előfeltételek](data-box-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-limits.md) értelmezése.
- Az [Azure Data Box](data-box-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
