---
title: Az Azure AD többtényezős hitelesítésének adattárolása
description: Ismerje meg, hogy milyen személyes és szervezeti adatai vannak az Azure AD többtényezős hitelesítési tárolói az Ön és a felhasználók számára, és hogy milyen információk maradnak a származási országban vagy régióban.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208352"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítésének adattárolási és ügyféladatok

A vásárlói adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft Online szolgáltatásra (például Microsoft 365 és az Azure-ra való feliratkozáskor) alapuló címen. További információ a vásárlói adatok tárolásáról: a Microsoft adatvédelmi központjának hol található az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

A felhőalapú Azure AD többtényezős hitelesítés és az Azure AD többtényezős hitelesítési kiszolgáló feldolgozása, és a személyes és a szervezeti adatmennyiség tárolása. Ez a cikk azt ismerteti, hogy mit és hol tárolja a rendszer az adattárolást.

Az Azure AD többtényezős hitelesítési szolgáltatás adatközpontokkal rendelkezik az USA-ban, Európában és Ázsia és a Csendes-óceáni térség. A következő tevékenységek a regionális adatközpontokból származnak:

* A telefonhívásokat használó többtényezős hitelesítés az Egyesült államokbeli adatközpontokból származik, és a globális szolgáltatók irányítják.
* Az egyéb régiókból (például Európából vagy Ausztráliából) származó általános célú felhasználói hitelesítési kérelmeket jelenleg a felhasználó helye alapján dolgozzák fel a rendszer.
* A Microsoft Authenticator alkalmazást használó leküldéses értesítések jelenleg a felhasználó helyétől függően a regionális adatközpontokban lesznek feldolgozva.
    * Az eszköz gyártója által meghatározott szolgáltatások, például az Apple leküldéses értesítések, a felhasználó tartózkodási helyétől eltérőek lehetnek.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítése által tárolt személyes adattárolás

A személyes adatok egy adott személyhez kapcsolódó felhasználói szintű információk. A következő adattárak személyes adatokat tartalmaznak:

* Letiltott felhasználók
* Megkerülő felhasználók
* Microsoft Authenticator eszköz jogkivonat-módosítási kérelmek
* Többtényezős hitelesítési tevékenységgel kapcsolatos jelentések
* Microsoft Authenticator aktiválása

Ezeket az információkat 90 napig őrzi meg a rendszer.

Az Azure AD többtényezős hitelesítése nem naplózza a személyes, például a felhasználónevet, a telefonszámot vagy az IP-címet, de van egy *UserObjectId* , amely azonosítja a többtényezős hitelesítési kísérleteket a felhasználók számára. A naplófájlok tárolása 30 napig történik.

### <a name="azure-ad-multifactor-authentication"></a>Azure AD többtényezős hitelesítés

Az Azure nyilvános felhők esetében, az Azure B2C hitelesítés, az NPS-bővítmény és a Windows Server 2016 vagy a 2019 AD FS adapter kivételével a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók     |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók     |
| Hanghívás                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure 21Vianet, Azure B2C hitelesítés, NPS-bővítmény és Windows Server 2016 vagy 2019 AD FS adapter esetében a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Hanghívás                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

### <a name="multifactor-authentication-server"></a>Többtényezős hitelesítési kiszolgáló

Ha az Azure AD többtényezős hitelesítési kiszolgálót telepíti és futtatja, a rendszer a következő személyes adattárolást tárolja:

> [!IMPORTANT]
> 2019. július 1-től a Microsoft nem nyújt többtényezős hitelesítési kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, akik többtényezős hitelesítést kívánnak megkövetelni a felhasználóktól, felhőalapú Azure AD többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik a július 1. előtt aktiválták a többtényezős hitelesítési kiszolgálót, le tudják tölteni a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Hanghívás                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítéssel tárolt szervezeti adatszolgáltatások

A szervezeti adatok olyan bérlői szintű információk, amelyek lehetővé tehetik a konfiguráció vagy a környezet beállítását. A következő Azure Portal a többtényezős hitelesítési lapok bérlői beállításai tárolhatnak olyan szervezeti adatokat, mint például a zárolási küszöbértékek vagy a hívó azonosító adatai a bejövő telefonos hitelesítési kérésekhez:

* Fiókzárolási
* Csalási riasztás
* Értesítések
* Telefonhívás beállításai

Az Azure AD többtényezős hitelesítési kiszolgálója esetében az alábbi Azure Portal-lapok szervezeti adattartalmakat is tartalmazhatnak:

* Kiszolgáló beállításai
* Egyszeri Mellőzés
* Gyorsítótárazási szabályok
* Többtényezős hitelesítési kiszolgáló állapota

## <a name="multifactor-authentication-logs-location"></a>Többtényezős hitelesítési naplók helye

A következő táblázat a nyilvános felhők szolgáltatási naplóinak helyét mutatja be.

| Nyilvános felhő| Bejelentkezési naplók | Többtényezős hitelesítési tevékenység jelentés        | Többtényezős hitelesítési szolgáltatás naplói       |
|-------------|--------------|----------------------------------------|------------------------|
| USA          | USA           | USA                                     | USA                     |
| Európa      | Európa       | USA                                     | Európa <sup>2</sup>    |
| Ausztrália   | Ausztrália    | USA<sup>1</sup>                         | Ausztrália <sup>2</sup> |

<sup>1</sup> Az eskü kód naplói Ausztráliában tárolódnak

<sup>2</sup> Hanghívások a többtényezős hitelesítési szolgáltatás naplói az USA-ban tárolódnak

A következő táblázat a szuverén felhők szolgáltatási naplóinak helyét mutatja be.

| Szuverén felhő                      | Bejelentkezési naplók                         | Többtényezős hitelesítési tevékenységről szóló jelentés (személyes adatbevitelt is tartalmaz)| Többtényezős hitelesítési szolgáltatás naplói |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | Németország                              | USA                            | USA               |
| A 21Vianet által üzemeltetett Microsoft Azure | Kína                                | USA                            | USA               |
| Microsoft Government Cloud           | USA                                   | USA                            | USA               |

A többtényezős hitelesítési tevékenység jelentési adattartalma személyes, például egyszerű felhasználónevet (UPN) és teljes telefonszámot tartalmaz.

A többtényezős hitelesítési szolgáltatás naplói a szolgáltatás működtetésére szolgálnak.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a felhőalapú Azure AD többtényezős hitelesítés és az Azure AD többtényezős hitelesítési kiszolgáló milyen felhasználói adatokat gyűjt, lásd: [Azure ad többtényezős hitelesítés felhasználói adatok gyűjtése](howto-mfa-reporting-datacollection.md).
