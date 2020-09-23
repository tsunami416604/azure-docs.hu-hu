---
title: Azure Multi-Factor Authentication-beli adattárolás
description: Megtudhatja, hogy milyen személyes és szervezeti adatai vannak az Azure Multi-Factor Authentication az Ön és a felhasználók adatait, valamint azt, hogy mely információk maradnak a származási országban vagy régióban.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5d8dc60ee0a1f4742382b1cec8ef3ed60e8fb3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970662"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Adattárolási és ügyféladatok az Azure Multi-Factor Authentication

A vásárlói adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft Online szolgáltatásra (például Microsoft 365 és az Azure-ra való feliratkozáskor) alapuló címen. További információ a vásárlói adatok tárolásáról: a Microsoft adatvédelmi központjának hol található az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

A felhőalapú Azure Multi-Factor Authentication és az Azure Multi-Factor Authentication-kiszolgáló dolgozza fel és tárolja a személyes és a szervezeti adatmennyiséget. Ez a cikk azt ismerteti, hogy mit és hol tárolja a rendszer az adattárolást.

Az Azure Multi-Factor Authentication szolgáltatás az USA-ban, Európában és Ázsia és a Csendes-óceáni térség adatközpontokkal rendelkezik. A következő tevékenységek a regionális adatközpontokból származnak:

* A többtényezős hitelesítés telefonos hívásokat használ az Egyesült államokbeli adatközpontokból, és a globális szolgáltatók irányítják.
* Az egyéb régiókból (például Európából vagy Ausztráliából) származó általános célú felhasználói hitelesítési kérelmeket jelenleg a felhasználó helye alapján dolgozzák fel a rendszer.
* A Microsoft Authenticator alkalmazást használó leküldéses értesítések a felhasználó helyétől függően a regionális adatközpontokat jelentik.
    * Az eszköz gyártója által meghatározott szolgáltatások, például az Apple leküldéses értesítések, a felhasználó tartózkodási helyétől eltérőek lehetnek.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication által tárolt személyes adattárolás

A személyes adatok egy adott személyhez kapcsolódó felhasználói szintű információk. A következő adattárak személyes adatokat tartalmaznak:

* Letiltott felhasználók
* Megkerülő felhasználók
* Microsoft Authenticator eszköz jogkivonat-módosítási kérelmek
* Multi-Factor Authentication tevékenységgel kapcsolatos jelentések
* Microsoft Authenticator aktiválása

Ezeket az információkat 90 napig őrzi meg a rendszer.

Az Azure Multi-Factor Authentication nem naplózza a személyes adatait, például a felhasználónevet, a telefonszámot vagy az IP-címet, de van egy *UserObjectId* , amely azonosítja a felhasználók multi-Factor Authentication kísérleteit. A naplófájlok tárolása 30 napig történik.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Az Azure nyilvános felhők esetében, az Azure B2C hitelesítés, az NPS-bővítmény és a Windows Server 2016 vagy a 2019 AD FS adapter kivételével a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Multi-Factor Authentication naplókban     |
| Egyirányú SMS                          | Multi-Factor Authentication naplókban     |
| Hanghívás                           | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

> [!NOTE]
> A Multi-Factor Authentication tevékenység jelentés adattárat az összes felhők Egyesült Államok tárolja, a hitelesítési kérést feldolgozó régiótól függetlenül. Microsoft Azure Germany, a 21Vianet által üzemeltetett Microsoft Azure és a Microsoft Government Cloud saját független adattárakat használ a nyilvános Felhőbeli adattáraktól, azonban ezeket az adatok mindig a Egyesült Államok tárolják.

Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure 21Vianet, Azure B2C hitelesítés, NPS-bővítmény és Windows Server 2016 vagy 2019 AD FS adapter esetében a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár |
| Egyirányú SMS                          | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár |
| Hanghívás                           | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication-kiszolgáló

Ha az Azure Multi-Factor Authentication-kiszolgáló telepítését és futtatását végzi, a rendszer a következő személyes adattárolást tárolja:

> [!IMPORTANT]
> 2019. július 1-től a Microsoft nem nyújt Multi-Factor Authentication-kiszolgáló az új üzemelő példányokhoz. Azok a felhasználók, akik a többtényezős hitelesítést szeretnék megkövetelni a felhasználóknak, felhőalapú Azure-Multi-Factor Authentication kell használniuk. A (z) július 1. előtti Multi-Factor Authentication-kiszolgáló rendelkező meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és az aktiválási hitelesítő adatokat a szokásos módon létrehozzák.

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár |
| Egyirányú SMS                          | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár |
| Hanghívás                           | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett |
| Értesítés Microsoft Authenticator | Multi-Factor Authentication naplókban<br />Multi-Factor Authentication tevékenység jelentés adattár<br />Letiltott felhasználók, ha csalás jelentett<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication által tárolt szervezeti adatszolgáltatások

A szervezeti adatok olyan bérlői szintű információk, amelyek lehetővé tehetik a konfiguráció vagy a környezet beállítását. A bérlői beállítások a következő Azure Portal Multi-Factor Authentication lapok olyan szervezeti adatokat tárolhatnak, mint például a zárolási küszöbértékek vagy a hívó azonosító adatai a bejövő telefonos hitelesítési kérésekhez:

* Fiókzárolási
* Csalási riasztás
* Értesítések
* Telefonhívás beállításai

Az Azure Multi-Factor Authentication-kiszolgáló esetében az alábbi Azure Portal-lapok szervezeti adatkezelést is tartalmazhatnak:

* Kiszolgáló beállításai
* Egyszeri Mellőzés
* Gyorsítótárazási szabályok
* Multi-Factor Authentication-kiszolgáló állapota

## <a name="log-data-location"></a>Az adatnapló helye

A naplófájlok tárolási helyétől függ, hogy a rendszer melyik régióból dolgozza fel az adatokat. A legtöbb földrajzi hely natív Azure Multi-Factor Authentication képességekkel rendelkezik, ezért a naplózási adattárolást ugyanabban a régióban tárolja a rendszer, amely feldolgozza a Multi-Factor Authentication kérelmet. A natív Azure Multi-Factor Authentication-támogatás nélküli földrajzi területeken a Egyesült Államok-vagy Európa-régiók és a naplózási adathalmazok szolgáltatásait az Multi-Factor Authentication kérelmet feldolgozó régió tárolja.

Néhány alapszintű hitelesítési naplót csak a Egyesült Államok tárol. A 21Vianet által üzemeltetett Microsoft Azure Germany és Microsoft Azure mindig a saját felhőben tárolódnak. A Microsoft Government Cloud log-adatkészleteket a rendszer mindig a Egyesült Államok tárolja.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a felhőalapú Azure-Multi-Factor Authentication és az Azure-Multi-Factor Authentication-kiszolgáló milyen felhasználói adatokat gyűjtenek be: [azure multi-Factor Authentication felhasználói adatok gyűjtése](howto-mfa-reporting-datacollection.md).
