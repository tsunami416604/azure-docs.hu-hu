---
title: Az Azure többtényezős hitelesítési adatok rezidenssége
description: Ismerje meg, milyen személyes és szervezeti adatokat tárol az Azure többtényezős hitelesítése Önről és a felhasználókról, és milyen adatok maradnak a származási országon belül.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309802"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Adatok tárolási és ügyféladatok az Azure többtényezős hitelesítéséhez

Az ügyféladatokat az Azure AD földrajzi helyen tárolja a szervezet által megadott cím alapján, amikor egy Microsoft Online-szolgáltatásra, például az Office 365-re és az Azure-ra iratkozik fel. Az ügyféladatok tárolási helyéről a Microsoft Adatvédelmi központ Hol találhatók az [adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

A felhőalapú Azure többtényezős hitelesítés és az Azure többtényezős hitelesítési kiszolgálója bizonyos mennyiségű személyes adatot és szervezeti adatot tárol. Ez a cikk ismerteti, hogy mit és hol tárolnak az adatok.

A következő többtényezős hitelesítési tevékenységek jelenleg az USA adatközpontjaiból származnak, kivéve, ha az a megjegyzés:

* A telefonhívások vagy SMS-sel történő kétfaktoros hitelesítés általában amerikai adatközpontokból származik, és globális szolgáltatók irányítják őket.
    * Általános célú felhasználói hitelesítési kérelmek más régiókból, például Európából vagy Ausztráliából jelenleg az adott régióadatközpontjai dolgozzák fel. Más események, például az önkiszolgáló jelszó-visszaállítás, az Azure B2C-események vagy a hálózati házirend-kiszolgáló bővítmény vagy az AD FS-adapter használatával hibrid forgatókönyvek, jelenleg az amerikai adatközpontok által feldolgozott.
* A Microsoft Authenticator alkalmazással érkező leküldéses értesítések amerikai adatközpontokból származnak. Ezenkívül az eszközgyártó-specifikus szolgáltatások is szerepet játszhatnak a különböző régiókból.
* Az OATH-kódokat általában jelenleg az Egyesült Államokban érvényesítik.
    * Ismét általános célú felhasználói hitelesítési események, amelyek más régiókból, például Európából vagy Ausztráliából származnak, az adott régió adatközpontjai dolgozzák fel. További események jelenleg az amerikai adatközpontok által feldolgozott.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítése által tárolt személyes adatok

A személyes adatok egy adott személyhez kapcsolódó felhasználói szintű információk. A következő adattárak személyes adatokat tartalmaznak:

* Letiltott felhasználók
* Megkerült felhasználók
* Microsoft Hitelesítő eszköztoken-módosítási kérelmei
* Többtényezős hitelesítési tevékenységjelentések
* Microsoft Hitelesítő aktiválása

Ezeket az információkat 90 napig őrizzük meg.

Az Azure többtényezős hitelesítés nem naplózza a személyes adatokat, például a felhasználónevet, a telefonszámot vagy az IP-címet, de van egy *UserObjectId,* amely azonosítja a többtényezős hitelesítési kísérleteket a felhasználók számára. A naplóadatok tárolása 30 napig tart.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Az Azure nyilvános felhőihez, kivéve az Azure B2C-hitelesítést, a hálózati címkiszolgáló-bővítményt és a Windows Server 2016-os vagy 2019-es AD FS-adaptert, a következő személyes adatokat tároljuk:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| OATH token                           | Többtényezős hitelesítési naplókban     |
| Egyirányú SMS                          | Többtényezős hitelesítési naplókban     |
| Hanghívás                           | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek |
| A Microsoft hitelesítő értesítése | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek<br />Kérelmek módosítása a Microsoft Authenticator eszköztokenének módosításakor |

> [!NOTE]
> A többtényezős hitelesítési tevékenység jelentés adattár tárolja az Egyesült Államokban az összes felhők, függetlenül attól, hogy a régió, amely feldolgozza a hitelesítési kérelmet. A Microsoft Azure Germany, a 21Vianet által üzemeltetett Microsoft Azure és a Microsoft Government Cloud saját, a nyilvános felhőrégióbeli adattáraktól elkülönülő, független adattárakkal rendelkeznek, azonban ezeket az adatokat mindig az Egyesült Államokban tárolják.

A Microsoft Azure Government, a Microsoft Azure Germany, a 21Vianet által üzemeltetett Microsoft Azure, az Azure B2C-hitelesítés, a hálózati címtakarékos hálózati kiszolgálóbővítmény és a Windows Server 2016-os vagy 2019-es AD FS-adapter esetében a következő személyes adatokat tároljuk:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| OATH token                           | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára |
| Hanghívás                           | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek |
| A Microsoft hitelesítő értesítése | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek<br />Kérelmek módosítása a Microsoft Authenticator eszköztokenének módosításakor |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication-kiszolgáló

Ha telepíti és futtatja az Azure multi-factor authentication server kiszolgálót, a rendszer a következő személyes adatokat tárolja:

> [!IMPORTANT]
> 2019. július 1-jéig a Microsoft a továbbiakban nem kínál többtényezős hitelesítési kiszolgálót az új telepítésekhez. Azoknak az új ügyfeleknek, akik többtényezős hitelesítést szeretnének megkövetelni a felhasználóiktól, felhőalapú Azure többtényezős hitelesítést kell használniuk. Azok a meglévő ügyfelek, akik július 1-je előtt aktiválták a többtényezős hitelesítési kiszolgálót, a szokásos módon letölthetik a legújabb verziót, a jövőbeli frissítéseket és a szokásos módon létrehozhatják az aktiválási hitelesítő adatokat.

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| OATH token                           | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára |
| Hanghívás                           | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek |
| A Microsoft hitelesítő értesítése | Többtényezős hitelesítési naplókban<br />Többtényezős hitelesítési tevékenység jelentésadattára<br />Letiltott felhasználók, ha csalást jelentettek<br />Kérelmek módosítása a Microsoft Authenticator eszköztokenének módosításakor |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Az Azure többtényezős hitelesítése által tárolt szervezeti adatok

A szervezeti adatok olyan bérlői szintű információk, amelyek felfedhetik a konfigurációt vagy a környezet beállítását. A következő Azure portal on többtényezős hitelesítési lapok bérlői beállításai tárolhatják a szervezeti adatokat, például a zárolási küszöbértékeket vagy a hívóazonosító adatait a bejövő telefonhitelesítési kérelmekhez:

* Fiók zárolása
* Csalási riasztás
* Értesítések
* Telefonhívás beállításai

Az Azure többtényezős hitelesítési kiszolgálója esetében pedig az alábbi Azure portallapok tartalmazhatnak szervezeti adatokat:

* Kiszolgáló beállításai
* Egyszeri bypass
* Gyorsítótárazási szabályok
* Többtényezős hitelesítési kiszolgáló állapota

## <a name="log-data-location"></a>Adatok helyének naplózása

A naplóadatok tárolásának helye attól függ, hogy melyik régióban dolgozzák fel őket. A legtöbb földrajzi hely natív Azure többtényezős hitelesítési képességekkel rendelkezik, így a naplóadatok ugyanabban a régióban tárolódnak, amely feldolgozza a többtényezős hitelesítési kérelmet. A natív Azure többtényezős hitelesítés támogatása nélküli földrajzi területeken az Egyesült Államok vagy Európa földrajzi adottságai szolgálják ki őket, és a naplóadatok ugyanabban a régióban vannak tárolva, amely a többtényezős hitelesítési kérelmet dolgozza fel.

Néhány alapvető hitelesítési naplóadat csak az Egyesült Államokban tárolódik. A Microsoft Azure Germany és a 21Vianet által üzemeltetett Microsoft Azure mindig a megfelelő felhőben tárolódik. A Microsoft Government Cloud naplóadatait mindig az Egyesült Államokban tároljuk.

## <a name="next-steps"></a>További lépések

A felhőalapú Azure többtényezős hitelesítés és az Azure többtényezős hitelesítési kiszolgáló által gyűjtött felhasználói adatokról az [Azure többtényezős hitelesítésfelhasználói adatgyűjtése](howto-mfa-reporting-datacollection.md)című témakörben talál további információt.
