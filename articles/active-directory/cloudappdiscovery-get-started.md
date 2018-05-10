---
title: A Cloud App Discovery szolgáltatás az Azure Active Directory beállítása |} Microsoft Docs
description: Keresse meg és kezelheti az alkalmazásokat a Cloud App Discovery felhőhasználat és árnyékmásolatok informatikai kapcsolatban nyújtanak információkat hajtható végre.
services: active-directory
keywords: a cloud app discovery alkalmazások kezelése
documentationcenter: ''
author: barbkess
manager: mtillman
tags: ignite
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 04/25/2018
ms.author: barbkess
ms.reviewer: nigu
ms.openlocfilehash: 4d1e845dee621bf284cbed91ba74177f9fc4acf3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Az Azure AD Cloud App Discovery beállítása

Az Azure AD a cloud App Discovery-adatok a Microsoft Cloud App Security integrációja a alapján. Adja meg a folyamatban lévő adatokat a felhő használatát és árnyékmásolat informatikai, a Cloud App Discovery összeveti a forgalmi naplók a Cloud App Security katalógust a több mint 15 000 felhőalapú alkalmazásokat. Ez a cikk ismerteti a telepítési folyamatot, és az egyes lépések részletes információkra mutató hivatkozásokat tartalmaz. Azt is bemutatja, tűzfal-és proxy és a naplófájlok támogatás.

## <a name="prerequisites"></a>Előfeltételek

* A szervezet a termék használatához az Azure AD Premium P1 licenccel kell rendelkeznie. További információkért lásd: [Azure Active Directory árképzési](https://azure.microsoft.com/pricing/details/active-directory/).
* A Cloud App Discovery beállításához globális rendszergazdájának vagy az Azure Active Directory biztonsági olvasó kell lennie.

## <a name="setup-steps"></a>A telepítő lépéseit

1. [Pillanatkép jelentéseket állíthat](cloudappdiscovery-set-up-snapshots.md) ellenőrizze, hogy a naplóformátumban ellenőrizze, hogy a naplók használható információkkal a Cloud App Discovery modulhoz. Ad hoc láthatósága forgalmi naplók manuális feltöltése a tűzfalak és proxykiszolgálók is is biztosítanak.

2. [Állítsa be a folyamatos reporting](https://docs.microsoft.com/cloud-app-security/discovery-docker) használatával a Cloud App Security naplógyűjtő a hálózaton továbbított összes naplók elemzése. Új alkalmazások és a használati trendek azonosításához használhatja őket.

3. A naplók jelenleg nem támogatottak, ha [állítson be egy egyéni napló elemző](https://docs.microsoft.com/cloud-app-security/custom-log-parser) , hogy a Cloud App Discovery is elemezheti őket.
  
## <a name="log-processing-flow"></a>Napló-feldolgozási folyamat

Azt is igénybe vehet néhány perctől több óráig adatok mennyiségétől függően-jelentések készítéséhez. Íme, néhány statisztikai elemzésnek:

* **Töltse fel** a hálózat webforgalomnaplók feltöltése a portálra.
* **Elemezni** Cloud App Security elemez, és egy dedikált program segítségével adatforrásonként a forgalmi naplók forgalmi adatok kivonatok.
* **Elemezze** forgalmi adatok elemzése a Cloud App Security katalógus 15 000-nél több felhőalkalmazások azonosításához. Aktív felhasználók és IP-címek is azonosítja az elemzés részeként.
* **Jelentés készítése** Cloud App Security adatokat a naplófájlokból kivonatolt adatokon jelentést hoz létre, és lehetővé teszi a Cloud App Discovery modulhoz.

> [!NOTE]
> * Folyamatos jelentés adatokat statisztikai elemzésnek naponta kétszer.
> * A naplógyűjtő tömöríti az adatokat, mielőtt feltöltése van. A kimenő adatforgalmat a naplógyűjtő körülbelül 10 %-át a kapott forgalmi naplók méretét.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Forgalmi naplók használatával a Cloud App Discovery modulra

A cloud App Discovery a forgalmi naplók adatai alapján. A további részletek is hozzáadhat a naplóban, akkor jobb látható. A cloud App Discovery megköveteli a webes forgalom az adatok a következő attribútumokkal:

* A tranzakció dátuma
* Forrás IP-címe
* Forrásfelhasználó **ajánlott**
* Cél IP-címe
* Cél URL-címe **ajánlott** (URL-címeket adjon meg IP-címek felhőalkalmazás detektálásához további pontosságot)
* Az adatok
* Mennyisége feltöltött, illetve a letöltött adatok, a cloud app használati mintáinak észrevételeket
* (Engedélyezett/letiltott) műveletről

A cloud App Discovery nem lehet megjeleníteni, vagy attribútumokat, amelyek nem szerepelnek a naplók elemzése. Például **Cisco ASA tűzfal** szabványos naplóformátumban nem tartalmaz a **tranzakciónként feltöltött bájt mennyiségű**, **felhasználónév**, vagy **cél URL-címe**  , de csak a cél IP-címe. Így lehetséges, hogy kevesebb a felhőalapú alkalmazások láthatóságát az ehhez az adatforráshoz. A Cisco ASA tűzfal 6.1-es információ szint beállítása.

Ahhoz, hogy a Cloud App Discovery jelentés készítése sikeresen, a forgalmi naplók a következő feltételeknek kell megfelelniük:

1.  Az adatforrás [egy támogatott tűzfalhoz vagy proxyhoz kiszolgáló](#supported-firewalls-and-proxies).
2.  Napló formátuma megegyezik-e a szabványos formátumúak. A feltöltés során van bejelölve. Uour naplóformátumban optimalizálása érdekében tekintse meg a [pillanatkép a Cloud App Discovery jelentéseket készíthet](cloudappdiscovery-set-up-snapshots.md).
3.  Nincsenek 90 napnál régebbi események.
4.  A naplófájl érvényes, és a kimenő forgalom információkat tartalmaz.

## <a name="supported-firewalls-and-proxy-servers"></a>Támogatott tűzfalak és proxykiszolgálók

* Barracuda - Web App tűzfal (W3C)
* Blue Coat Proxy SG – hozzáférési napló (W3C)
* Ellenőrzőpont
* Cisco ASA FirePOWER
* Cisco ASA tűzfal (Cisco ASA tűzfal információk szint beállítása 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URL-napló
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Digitális Arts i-szűrő
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee biztonságos webes átjáró
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto series tűzfal
* Sophos SG
* Sophos Cyberoam
* Kalmár (Common)
* Kalmár (natív)
* Websense - Web Security Solutions - részletes nyomozási jelentés (CSV)
* Websense - Web Security Solutions - internetes tevékenységnapló (CEF)
* Zscaler

> [!NOTE]
> A cloud App Discovery IPv4 és IPv6-címeket is támogatja.

Ha a napló nem támogatott, válassza ki a **más** , a **adatforrás** , és adja meg az eszköz és a feltölteni kívánt naplót. A Cloud App Security felhő elemző csapata vizsgálja felül a naplót. Ha támogatása, a kerül, hogy a napló típusa értesítést, de ehelyett egy egyéni elemző, amely megfelel a naplóformátumban adhat meg. További információkért lásd: [használjon egy egyéni napló elemző](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Adatok attribútumok (megfelelően a forgalmazó dokumentációja)

| Adatforrás         | Célzott alkalmazás URL-címe | Megcélzott alkalmazások IP-cím | Felhasználónév | Forrás IP-cím | Összes forgalom | Feltöltött bájtok |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Igen**        | **Igen**       | **Igen**  | **Igen**   | Nem            | Nem             |
| Blue Coat                               | **Igen**        | Nem            | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Ellenőrzőpont                              | Nem             | **Igen**       | Nem       | **Igen**   | Nem            | Nem             |
| Cisco ASA                               | Nem             | **Igen**       | Nem       | **Igen**   | **Igen**       | Nem             |
| Cisco FWSM                              | Nem             | **Igen**       | Nem       | **Igen**   | **Igen**       | Nem             |
| Cisco Ironport WSA                      | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Cisco Meraki                            | **Igen**        | **Igen**       | Nem       | **Igen**   | Nem            | Nem             |
| Clavister NGFW (Syslog)                 | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Dell SonicWall                          | **Igen**        | **Igen**       | Nem       | **Igen**   | **Igen**       | **Igen**        |
| FortiGate                               | Nem             | **Igen**       | Nem       | **Igen**   | **Igen**       | **Igen**        |
| Juniper SRX                             | Nem             | **Igen**       | Nem       | **Igen**   | **Igen**       | **Igen**        |
| Juniper SSG                             | Nem             | **Igen**       | Nem       | **Igen**   | **Igen**       | **Igen**        |
| McAfee SWG                              | **Igen**        | Nem            | Nem       | **Igen**   | **Igen**       | **Igen**        |
| MS TMG                                  | **Igen**        | Nem            | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Palo Alto Networks                      | Nem             | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Sophos                                  | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | Nem             |
| Kalmár (Common)                          | **Igen**        | Nem            | **Igen**  | **Igen**   | Nem            | **Igen**        |
| Kalmár (natív)                          | **Igen**        | Nem            | **Igen**  | **Igen**   | Nem            | **Igen**        |
| Websense - nyomozási jelentés (CSV)   | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Websense - internetes tevékenységnapló (CEF)  | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |
| Zscaler                                 | **Igen**        | **Igen**       | **Igen**  | **Igen**   | **Igen**       | **Igen**        |


## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások segítségével továbbra is az Azure AD Cloud App Discovery beállítása.

* [Pillanatkép-jelentések létrehozása](cloudappdiscovery-set-up-snapshots.md)
* [Folyamatos jelentéskészítés konfigurálása](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Egyéni naplóelemző használata](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
