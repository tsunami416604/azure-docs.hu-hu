---
title: Azure Multi-Factor Authentication-beli adattárolás
description: Megtudhatja, hogy milyen személyes és szervezeti adatai vannak az Azure Multi-Factor Authentication az Ön és a felhasználók adatait, valamint azt, hogy mely információk maradnak a származási országban vagy régióban.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4b15311dfefecd9a533add9c5a028a9b7b22fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051160"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Adattárolási és ügyféladatok az Azure Multi-Factor Authentication

A vásárlói adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft online szolgáltatásokra, például az Office 365-re és az Azure-ra való feliratkozáskor. További információ a vásárlói adatok tárolásáról: a Microsoft adatvédelmi központjának hol található az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

A felhőalapú Azure Multi-Factor Authentication és az Azure Multi-Factor Authentication-kiszolgáló dolgozza fel és tárolja a személyes és a szervezeti adatmennyiséget. Ez a cikk azt ismerteti, hogy mit és hol tárolja a rendszer az adattárolást.

A következő Multi-Factor Authentication tevékenységek jelenleg az Egyesült államokbeli adatközpontokból származnak, kivéve a következőket:

* A kétfaktoros hitelesítés telefonhívásokkal vagy SMS-sel általában az Egyesült államokbeli adatközpontokból származik, és a globális szolgáltatók irányítják.
    * A más régiókból, például Európából vagy Ausztráliából származó általános célú felhasználói hitelesítési kérelmeket jelenleg az adott régióban lévő adatközpontok dolgozzák fel. Az Egyesült államokbeli adatközpontok jelenleg minden olyan eseményt feldolgoznak, mint például az önkiszolgáló jelszavak alaphelyzetbe állítása, az Azure B2C-események vagy az NPS-bővítményt vagy AD FS adaptert használó hibrid forgatókönyvek.
* Az Microsoft Authenticator alkalmazást használó leküldéses értesítések az Egyesült államokbeli adatközpontokból származnak. Emellett az eszköz gyártó-specifikus szolgáltatásai is különböző régiókban is előfordulhatnak.
* Az eskü kódok általában jelenleg az Egyesült Államokban vannak érvényesítve
    * A más régiókban (például Európa vagy Ausztrália) származó általános célú felhasználói hitelesítési eseményeket az adott régióban lévő adatközpontok dolgozzák fel. Az Egyesült államokbeli adatközpontok jelenleg további eseményeket dolgoznak fel.

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

## <a name="next-steps"></a>További lépések

További információ arról, hogy a felhőalapú Azure-Multi-Factor Authentication és az Azure-Multi-Factor Authentication-kiszolgáló milyen felhasználói adatokat gyűjtenek be: [azure multi-Factor Authentication felhasználói adatok gyűjtése](howto-mfa-reporting-datacollection.md).
